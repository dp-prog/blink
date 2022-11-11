# Разработано для подключения из файла CMakeLists.txt приложения IDF
cmake_minimum_required(VERSION 3.5)

set(EXTRA_COMPONENT_DIRS $ENV{IDF_PATH}/examples/common_components/led_strip)

#
# Получение версии проекта либо из файла version, либо из ревизии Git.
# Это передается в вызов idf_build_process. Здесь также устанавливаются
# зависимости, когда меняется файл version (если он используется).
#
function(__project_get_revision var)
    set(_project_path "${CMAKE_CURRENT_LIST_DIR}")
    if(NOT DEFINED PROJECT_VER)
        if(EXISTS "${_project_path}/version.txt")
            file(STRINGS "${_project_path}/version.txt" PROJECT_VER)
            set_property(DIRECTORY APPEND PROPERTY CMAKE_CONFIGURE_DEPENDS "${_project_path}/version.txt")
        else()
            git_describe(PROJECT_VER_GIT "${_project_path}")
            if(PROJECT_VER_GIT)
                set(PROJECT_VER ${PROJECT_VER_GIT})
            else()
                message(STATUS "Project is not inside a git repository, or git repository has no commits;"
                        " will not use 'git describe' to determine PROJECT_VER.")
                set(PROJECT_VER 1)
            endif()
        endif()
    endif()
    set(${var} "${PROJECT_VER}" PARENT_SCOPE)
endfunction()

#Включение в имя файла прошивки версии проекта
#set(PROJECT_VER "0.0.1")
set(PROJECT_VER "4.0.1")
set(PROJECT_VERU ${PROJECT_VER})
set(PROJECT_PREF "udz")
string (REPLACE "\." "_" PROJECT_VERU ${PROJECT_VERU})
set(PROJECT_BIN "${PROJECT_PREF}_${PROJECT_VERU}")


include($ENV{IDF_PATH}/tools/cmake/project.cmake)
get_filename_component(ProjectId ${CMAKE_CURRENT_LIST_DIR} NAME)
string(REPLACE " " "_" ProjectId ${ProjectId})

#Включение в имя файла прошивки версии проекта
#project(${ProjectId})
project("${PROJECT_BIN}")
