cmake_minimum_required(VERSION 3.15)
project(wbfs VERSION 1.0.0 LANGUAGES C)

include(FetchContent)
FetchContent_Declare(
    dclib
    GIT_REPOSITORY https://github.com/Nitr4m12/dclib
    GIT_TAG c5cca73eef5f4fd9e196d72d957a4655a4d73345
)
FetchContent_MakeAvailable(dclib)

add_library(${PROJECT_NAME} STATIC)
add_subdirectory(include)
add_subdirectory(src)

# =======================
#   INCLUDE DIRECTORIES    
# =======================

target_include_directories(${PROJECT_NAME}
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

# =======================
#     LIBRARY LINKAGE    
# =======================

target_link_libraries(${PROJECT_NAME} PUBLIC dclib)

find_package(OpenSSL REQUIRED)
target_link_libraries(${PROJECT_NAME} PUBLIC OpenSSL::SSL)

# ===========================
#     COMPILE DEFINITIONS    
# ===========================

target_compile_definitions(${PROJECT_NAME} 
PUBLIC 
    LARGE_FILES
    _FILE_OFFSET_BITS=64
    USE_SHA_LIB # Make OpenSSL required for now
)

# =======================
#     COMPILE OPTIONS        
# =======================

target_compile_options(${PROJECT_NAME}
PUBLIC
    -fomit-frame-pointer
    -fno-strict-aliasing
    -Wall
    -Wno-parentheses
    -Wno-unused-function
    -O3
)

# =================
#       TESTS           
# =================
include(CTest)
set(BUILD_TESTING ON)
if(BUILD_TESTING)
    add_subdirectory(tests)
endif(BUILD_TESTING)
