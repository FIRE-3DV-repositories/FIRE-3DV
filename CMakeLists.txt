cmake_minimum_required(VERSION 3.12)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "-O2 -Wall -Wextra -pedantic -g")
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
# set(CMAKE_CXX_FLAGS "-DNDEBUG -O3")


project(FIRE3D VERSION 1.0)

set(LIB ${CMAKE_CURRENT_SOURCE_DIR}/lib/)

## IMGUI 
file(GLOB_RECURSE IMGUI_SRC_FILES ${LIB}/imgui/*.cpp)

## MY FILES
file(GLOB_RECURSE SRC_FILES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
add_executable(FIRE3D ${CMAKE_CURRENT_SOURCE_DIR}/src/ambf-vulkan.cpp ${SRC_FILES} ${IMGUI_SRC_FILES})
target_include_directories(FIRE3D PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include ${LIB}/imgui)
target_compile_definitions(FIRE3D PUBLIC IMGUI_IMPL_VULKAN_USE_VOLK)

## INCLUDE LIBRARIES
add_subdirectory(${LIB}/fastgltf)
target_link_libraries(FIRE3D PUBLIC fastgltf::fastgltf)
target_include_directories(FIRE3D INTERFACE ${LIB}/fastgltf/include)

set(GLM_INCLUDE_DIR ${LIB}/glm/glm)
target_include_directories(FIRE3D PUBLIC ${GLM_INCLUDE_DIR})
add_definitions(-DGLM_FORCE_DEPTH_ZERO_TO_ONE)

set(STB_IMAGE_INCLUDE_DIR ${LIB}/stb_image)
target_include_directories(FIRE3D PUBLIC ${STB_IMAGE_INCLUDE_DIR})

add_subdirectory(${LIB}/vkbootstrap)
target_link_libraries(FIRE3D PUBLIC vk-bootstrap::vk-bootstrap)

set(VMA_INCLUDE_DIR ${LIB}/vma)
target_include_directories(FIRE3D PUBLIC ${VMA_INCLUDE_DIR})

set(INTERPROCESS_INCLUDE_DIR ${LIB}/boost)
target_include_directories(FIRE3D PUBLIC ${LIB} ${INTERPROCESS_INCLUDE_DIR})
target_compile_definitions(FIRE3D PUBLIC BOOST_DATE_TIME_NO_LIB)

# if (WIN32)
#     set(VOLK_STATIC_DEFINES VK_USE_PLATFORM_WIN32_KHR)
# elseif (UNIX)
#     set(VOLK_STATIC_DEFINES VK_USE_PLATFORM_XLIB_KHR)
# endif()
add_subdirectory(${LIB}/volk)
target_link_libraries(FIRE3D PRIVATE volk)

################################################################################################################################
## SDL2 
################################################################################################################################

# 1. Look for a SDL2 package, 2. look for the SDL2 component and 3. fail if none can be found
find_package(SDL2 REQUIRED CONFIG REQUIRED COMPONENTS SDL2)

# 1. Look for a SDL2 package, 2. Look for the SDL2maincomponent and 3. DO NOT fail when SDL2main is not available
find_package(SDL2 REQUIRED CONFIG COMPONENTS SDL2main)

# SDL2::SDL2main may or may not be available. It is e.g. required by Windows GUI applications
if(TARGET SDL2::SDL2main)
    # It has an implicit dependency on SDL2 functions, so it MUST be added before SDL2::SDL2 (or SDL2::SDL2-static)
    target_link_libraries(FIRE3D PRIVATE SDL2::SDL2main)
endif()

# Link to the actual SDL2 library. SDL2::SDL2 is the shared SDL library, SDL2::SDL2-static is the static SDL libarary.
target_link_libraries(FIRE3D PRIVATE SDL2::SDL2)
###############################################################################################################################

###############################################################################################################################

find_library(VULKAN vulkan)
target_link_libraries(FIRE3D PUBLIC ${VULKAN})

find_library(PTHREAD pthread)
target_link_libraries(FIRE3D PUBLIC ${PTHREAD})

find_library(XRANDR Xrandr)
target_link_libraries(FIRE3D PUBLIC ${XRANDR})

find_library(DL dl)
target_link_libraries(FIRE3D PUBLIC ${DL})

