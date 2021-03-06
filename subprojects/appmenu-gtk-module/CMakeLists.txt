cmake_minimum_required (VERSION 3.4)
cmake_policy (VERSION 3.4)
project (appmenu-gtk-module C)
list (APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)
enable_testing ()
include(GNUInstallDirs)
include(CMakeDependentOption)
find_package (PkgConfig QUIET)
find_package(GLIB2 REQUIRED QUIET COMPONENTS GLIB GOBJECT GIO GIO_UNIX COMPILE_SCHEMAS)
include(GLibProgramHandlers)
find_package(GTK 3.22 QUIET COMPONENTS GDK GTK X11 WAYLAND)
find_package(GTK 2.24 COMPONENTS GDK GTK X11)
set(GTK2_FOUND ${GTK_FOUND})

find_path(WAYLAND_INCLUDE
    NAMES wayland-client.h
    HINTS ${CMAKE_FULL_INCLUDE_DIR}
    PATH_SUFFIXES wayland
)

cmake_dependent_option(STANDALONE "Build appmenu-gtk-module STANDALONE" ON
        "NOT CORE_FOUND" OFF)
option(ENABLE_APPMENU_GTK_MODULE "Build appmenu-gtk-module" ON)
cmake_dependent_option(ENABLE_GTK_MODULE_DEMOS "Enable demo programs and docs for gtk module" OFF
        "ENABLE_APPMENU_GTK_MODULE" OFF)

if(STANDALONE)
        set (CPACK_SOURCE_PACKAGE_FILE_NAME "${CMAKE_PROJECT_NAME}")
        set (CPACK_SOURCE_IGNORE_FILES "/build/;/.gitmodules;/.git;/.gitignore;~$;${CPACK_SOURCE_IGNORE_FILES}")
        include (CPack)
        add_custom_target (dist COMMAND ${CMAKE_MAKE_PROGRAM} package_source)
endif()

if(ENABLE_APPMENU_GTK_MODULE OR STANDALONE)
	add_subdirectory(data)
	add_subdirectory(lib)
	add_subdirectory(src)
	add_subdirectory(tests)
endif()

if(ENABLE_GTK_MODULE_DEMOS)
        find_package(GtkDoc CONFIG PATH_SUFFIXES GtkDoc)
        if(GtkDoc_FOUND)
                add_subdirectory(docs)
        endif()
endif()
