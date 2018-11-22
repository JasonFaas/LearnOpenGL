cmake_minimum_required (VERSION 3.11)
cmake_policy(VERSION 3.11)

project (LearnOpenGL)

#IF(NOT CMAKE_BUILD_TYPE)
#  SET(CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build (Debug or Release)" FORCE)
#ENDIF(NOT CMAKE_BUILD_TYPE)

#set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules/")


set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)








set(Glfw_Main "C:/Users/jason/Desktop/libraries/glfw/glfw-3.2.1.bin.WIN32")
set(Glfw_DIR "${Glfw_Main}/lib-vc2015")

set(Glad_Main "C:/Users/jason/Desktop/libraries/glad")
set(Glad_DIR "${Glad_Main}")

include_directories(LearnOpenGL "${Glad_Main}/include")
include_directories("glad" PRIVATE "${Glfw_Main}/include")
include_directories(LearnOpenGL "${Glfw_Main}/include")

add_library("glad" "${Glad_DIR}/src/glad.c")






#link_directories(${CMAKE_SOURCE_DIR}/lib)

#list(APPEND CMAKE_CXX_FLAGS "-std=c++11")

# find the required packages
#find_package(GLM REQUIRED)
#message(STATUS "GLM included at ${GLM_INCLUDE_DIR}")
#find_package(GLFW3 REQUIRED)
#message(STATUS "Found GLFW3 in ${GLFW3_INCLUDE_DIR}")
#find_package(ASSIMP REQUIRED)
#message(STATUS "Found ASSIMP in ${ASSIMP_INCLUDE_DIR}")

#if(WIN32)
#  set(LIBS glfw3 opengl32 assimp)
#else()
#  set(LIBS )
#endif(WIN32)

set(CHAPTERS
    1.getting_started
)

set(1.getting_started
    1.1.hello_window
    1.2.hello_window_clear
    2.1.hello_triangle
    2.2.hello_triangle_indexed
    2.3.hello_triangle_exercise1
    2.4.hello_triangle_exercise2
    2.5.hello_triangle_exercise3
    3.1.shaders_uniform
    3.2.shaders_interpolation
    3.3.shaders_class
#    4.1.textures
#    4.2.textures_combined
#    4.3.textures_exercise2
#    4.4.textures_exercise3
#    4.5.textures_exercise4
#    5.1.transformations
#    5.2.transformations_exercise2
#    6.1.coordinate_systems
#    6.2.coordinate_systems_depth
#    6.3.coordinate_systems_multiple
#    7.1.camera_circle
#    7.2.camera_keyboard_dt
#    7.3.camera_mouse_zoom
#    7.4.camera_class
)




#configure_file(configuration/root_directory.h.in configuration/root_directory.h)
#include_directories(${CMAKE_BINARY_DIR}/configuration)

# first create relevant static libraries requried for other projects
#add_library(STB_IMAGE "src/stb_image.cpp")
#set(LIBS ${LIBS} STB_IMAGE)

#add_library(GLAD "src/glad.c")
#set(LIBS ${LIBS} GLAD)

#macro(makeLink src dest target)
#  add_custom_command(TARGET ${target} POST_BUILD COMMAND ${CMAKE_COMMAND} -E create_symlink ${src} ${dest}  DEPENDS  ${dest} COMMENT "mklink ${src} -> ${dest}")
#endmacro()

# then create a project file per tutorial
foreach(CHAPTER ${CHAPTERS})
    foreach(DEMO ${${CHAPTER}})
        file(GLOB SOURCE
            "src/${CHAPTER}/${DEMO}/*.h"
            "src/${CHAPTER}/${DEMO}/*.cpp"
            "src/${CHAPTER}/${DEMO}/*.vs"
            "src/${CHAPTER}/${DEMO}/*.fs"
            "src/${CHAPTER}/${DEMO}/*.gs"
        )
        set(NAME "${CHAPTER}__${DEMO}")
        add_executable(${NAME} ${SOURCE})
        target_link_libraries(${NAME} ${LIBS})
		
		target_link_libraries (${NAME} "${Glfw_DIR}/glfw3.lib")
		target_link_libraries (${NAME} "glad")

        set_target_properties(${NAME} PROPERTIES RUNTIME_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/bin/${CHAPTER}")
        # copy shader files to build directory
        file(GLOB SHADERS
                 "src/${CHAPTER}/${DEMO}/*.vs"
                 # "src/${CHAPTER}/${DEMO}/*.frag"
                 "src/${CHAPTER}/${DEMO}/*.fs"
                 "src/${CHAPTER}/${DEMO}/*.gs"
        )
        foreach(SHADER ${SHADERS})
             # configure_file(${SHADER} "test")
            add_custom_command(TARGET ${NAME} PRE_BUILD COMMAND ${CMAKE_COMMAND} -E copy ${SHADER} $<TARGET_FILE_DIR:${NAME}>)
        endforeach(SHADER)
        # if compiling for visual studio, also use configure file for each project (specifically to set up working directory)
        if(MSVC)
            configure_file(${CMAKE_SOURCE_DIR}/configuration/visualstudio.vcxproj.user.in ${CMAKE_CURRENT_BINARY_DIR}/${NAME}.vcxproj.user @ONLY)
        endif(MSVC)
    endforeach(DEMO)
endforeach(CHAPTER)




#target_link_libraries (LearnOpenGL "${Glfw_DIR}/glfw3.lib")
#target_link_libraries (LearnOpenGL "glad")

include_directories(${CMAKE_SOURCE_DIR}/includes)
