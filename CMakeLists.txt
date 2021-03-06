project (io.elementary.appcenter C)

cmake_minimum_required (VERSION 2.8)

include (GNUInstallDirs)

set (DATADIR "${CMAKE_INSTALL_PREFIX}/share")
set (PKGDATADIR "${DATADIR}/${CMAKE_PROJECT_NAME}")
set (CONFIGDIR "${CMAKE_INSTALL_FULL_SYSCONFDIR}/${CMAKE_PROJECT_NAME}")
set (GETTEXT_PACKAGE ${CMAKE_PROJECT_NAME})
set (EXEC_NAME ${CMAKE_PROJECT_NAME})
set (APP_NAME "AppCenter")
set (BLACKLIST "appcenter.blacklist")
set (RELEASE_NAME "Browse and manage apps")
set (DESKTOP_FILE ${CMAKE_PROJECT_NAME}.desktop)
set (DESKTOP_ICON "system-software-install")
set (VERSION "0.2.9")
set (VERSION_INFO "Alpha")
set (DOLLAR "$")

option (ENABLE_CURATED "Differentiate between apps created for elementary and not" ON)
set (CURATED_FLAG "")
if (ENABLE_CURATED)
    set (CURATED_FLAG "--define=CURATED")
endif (ENABLE_CURATED)

# CTest
include (CTest)
if (BUILD_TESTING)
    add_test (NAME blacklist-exists COMMAND test -e ${CMAKE_SOURCE_DIR}/data/${BLACKLIST})
endif ()

configure_file (${CMAKE_SOURCE_DIR}/config.vala.in ${CMAKE_BINARY_DIR}/src/config.vala)
add_definitions (-DGETTEXT_PACKAGE=\"${GETTEXT_PACKAGE}\")
add_definitions (-w)
add_definitions (-DI_KNOW_THE_PACKAGEKIT_GLIB2_API_IS_SUBJECT_TO_CHANGE)

find_package (PkgConfig)
pkg_check_modules (DEPS REQUIRED gthread-2.0 gee-0.8 gtk+-3.0>=3.10 granite packagekit-glib2 appstream>=0.10.0 libsoup-2.4 json-glib-1.0)

add_definitions (${DEPS_CFLAGS})
link_directories (${DEPS_LIBRARY_DIRS})

pkg_check_modules (UNITY unity>=4.0.0)
if (UNITY_FOUND)
    set (UNITY_OPTIONS -D HAVE_UNITY)
    set (UNITY_PACKAGES unity)
    add_definitions(${UNITY_CFLAGS})
    link_directories(${UNITY_LIBRARY_DIRS})
endif ()

find_package (Vala REQUIRED)
include (ValaVersion)
ensure_vala_version ("0.26" MINIMUM)

include (ValaPrecompile)

add_subdirectory (data)
add_subdirectory (po)
add_subdirectory (src)
add_subdirectory (schemas)
