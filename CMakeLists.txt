﻿cmake_minimum_required(VERSION 3.15)

if(POLICY CMP0091)
	cmake_policy(SET CMP0091 NEW)
endif()

project("ZHMTools")

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set(CMAKE_PDB_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})

set(CMAKE_CXX_STANDARD 23)

option(ZHM_BUILD_TOOLS "Whether or not tools should be built" ON)

if(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
	set(VCPKG_CRT_LINKAGE "static")
	set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

	string(REPLACE "/INCREMENTAL:NO" "" CMAKE_SHARED_LINKER_FLAGS_RELEASE ${CMAKE_SHARED_LINKER_FLAGS_RELEASE})
	string(REPLACE "/INCREMENTAL:NO" "" CMAKE_SHARED_LINKER_FLAGS_RELWITHDEBINFO ${CMAKE_SHARED_LINKER_FLAGS_RELWITHDEBINFO})

	string(REPLACE "/MDd" "" CMAKE_CXX_FLAGS_DEBUG ${CMAKE_CXX_FLAGS_DEBUG})
	string(REPLACE "/O2" "" CMAKE_CXX_FLAGS_RELEASE ${CMAKE_CXX_FLAGS_RELEASE})
	string(REPLACE "/O2" "" CMAKE_CXX_FLAGS_RELWITHDEBINFO ${CMAKE_CXX_FLAGS_RELWITHDEBINFO})
	string(REPLACE "/Ob1" "" CMAKE_CXX_FLAGS_RELEASE ${CMAKE_CXX_FLAGS_RELEASE})
	string(REPLACE "/Ob1" "" CMAKE_CXX_FLAGS_RELWITHDEBINFO ${CMAKE_CXX_FLAGS_RELWITHDEBINFO})
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
	# In clang we want to add -fexperimental-library so we can use new stdlib features.
	string(APPEND CMAKE_CXX_FLAGS " -fexperimental-library")
endif()

if(EMSCRIPTEN)
    set(CMAKE_CXX_FLAGS_RELEASE "")
endif()

add_subdirectory("Libraries/ResourceLib")

if(NOT EMSCRIPTEN)
	if(ZHM_BUILD_TOOLS)
		add_subdirectory("Tools/ResourceTool")
		add_subdirectory("Tools/NavTool")
		add_subdirectory("Tools/ModuleInfoParser")
		add_subdirectory("Tools/PropertyBundler")
	endif()

	add_subdirectory("Libraries/NavWeakness")
endif()

if(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
	if(ZHM_BUILD_TOOLS)
		add_subdirectory("Tools/CodeGen")
	endif()

	add_subdirectory("Libraries/CodeGenLib")
endif()
