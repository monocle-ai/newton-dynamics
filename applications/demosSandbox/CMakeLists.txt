# Copyright (c) <2014-2017> <Newton Game Dynamics>
#
# This software is provided 'as-is', without any express or implied
# warranty. In no event will the authors be held liable for any damages
# arising from the use of this software.
#
# Permission is granted to anyone to use this software for any purpose,
# including commercial applications, and to alter it and redistribute it
# freely.

cmake_minimum_required(VERSION 3.4.0)

set (projectName "demosSandbox")
message (${projectName})

# source and header files
file(GLOB CPP_SOURCE 
	sdkDemos/*.h
	sdkDemos/*.cpp
	sdkDemos/demos/*.h
	sdkDemos/demos/*.cpp
	sdkDemos/toolBox/*.h
	sdkDemos/toolBox/*.cpp 
)

#source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}/" PREFIX source/ FILES ${CPP_SOURCE})
source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}/" FILES ${CPP_SOURCE})

include_directories(sdkDemos/)
include_directories(sdkDemos/demos/)
include_directories(sdkDemos/toolBox/)
include_directories(../../sdk/dMath/)
include_directories(../../sdk/dgCore/)
include_directories(../../sdk/dScene/)
include_directories(../../sdk/dModel/)
include_directories(../../sdk/dNewton/)
include_directories(../../sdk/dgNewton/)
include_directories(../../sdk/dVehicle/)
include_directories(../../sdk/dgPhysics/)
include_directories(../../sdk/dAnimation/)
include_directories(../../sdk/dgMeshUtil/)
include_directories(../../sdk/dContainers/)
include_directories(../../sdk/dCustomJoints/)
include_directories(../../sdk/thirdParty/tinyxml/)
include_directories(../../sdk/dVehicle/dPlayerController)
include_directories(../../sdk/dVehicle/dMultiBodyVehicle)

if (NEWTON_BUILD_PROFILER)
	include_directories(../../sdk/dProfiler/dProfiler/)
endif ()

include_directories(../../sdk/thirdParty/glfw/)
include_directories(../../sdk/thirdParty/imgui/)
include_directories(../../sdk/thirdParty/glfw/include/)
include_directories(../../sdk/thirdParty/glatter/include/glatter/)
add_executable(${projectName} ${CPP_SOURCE})

target_link_libraries (${projectName} dNewton dScene dModel dVehicle glfw imgui glatter)
if (NEWTON_BUILD_PROFILER)
    target_link_libraries (${projectName} dProfiler)
endif ()

if(MSVC OR MINGW)
    if(NEWTON_BUILD_SHARED_LIBS)
        add_definitions(-D_DSCENE_DLL)
        add_definitions(-D_DCONTAINERS_DLL)
    else(NEWTON_BUILD_SHARED_LIBS)
        add_definitions(-D_NEWTON_STATIC_LIB)
        add_definitions(-D_DVEHICLE_STATIC_LIB)
        add_definitions(-D_CNEWTON_STATIC_LIB)
        add_definitions(-D_CUSTOM_JOINTS_STATIC_LIB)
    endif(NEWTON_BUILD_SHARED_LIBS)

    target_link_libraries (${projectName} glu32 opengl32)

    if(CMAKE_VS_MSBUILD_COMMAND OR CMAKE_VS_DEVENV_COMMAND)
		set_target_properties(${projectName} PROPERTIES COMPILE_FLAGS "/Yutoolbox_stdafx.h")
		set_source_files_properties(sdkDemos/toolBox/toolbox_stdafx.cpp PROPERTIES COMPILE_FLAGS "/Yctoolbox_stdafx.h")
		set_source_files_properties(sdkDemos/toolBox/FileBrowser.cpp PROPERTIES COMPILE_FLAGS "/Y-")
    endif()
endif()

if(UNIX)
    target_link_libraries (${projectName} GL GLU dl X11 pthread)
    set (source "${CMAKE_SOURCE_DIR}/applications/media")
    set (destination "${PROJECT_BINARY_DIR}/applications/media")
    add_custom_command(
        TARGET ${projectName} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E create_symlink ${source} ${destination}
        DEPENDS ${destination}
        COMMENT "symbolic link resources folder from ${source} => ${destination}")
endif()

if(WIN32)
    file(COPY ../media DESTINATION "${PROJECT_BINARY_DIR}/applications")
endif()

install(DIRECTORY ../media DESTINATION "${PROJECT_BINARY_DIR}/applications/media")
install(TARGETS ${projectName} DESTINATION "${PROJECT_BINARY_DIR}/applications/")
