cmake_minimum_required(VERSION 3.5)
project(raylib_template)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

file(GLOB_RECURSE APP_SRC "${CMAKE_SOURCE_DIR}/app/src/*.cpp")
set(APP_INCLUDE "${CMAKE_SOURCE_DIR}/app/include")

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

add_executable(${PROJECT_NAME} ${APP_SRC})
target_include_directories(${PROJECT_NAME} PRIVATE ${APP_INCLUDE})



set(FETCHCONTENT_BASE_DIR ${CMAKE_SOURCE_DIR}/external)
include(FetchContent)

FetchContent_Declare(
    raylib
    GIT_REPOSITORY https://github.com/raysan5/raylib.git
    GIT_TAG master
)
FetchContent_Declare(
    imgui
    GIT_REPOSITORY https://github.com/ocornut/imgui.git
    GIT_TAG docking
)
FetchContent_Declare(
    rlimgui
    GIT_REPOSITORY https://github.com/raylib-extras/rlImGui.git
    GIT_TAG main
)

set(BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(BUILD_GAMES OFF CACHE BOOL "" FORCE)
set(RAYLIB_BUILD_STATIC ON CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(raylib)

FetchContent_GetProperties(imgui)
if(NOT imgui_POPULATED)
    FetchContent_MakeAvailable(imgui)
    set(IMGUI_DIR ${imgui_SOURCE_DIR})
    
    add_library(imgui STATIC
        ${IMGUI_DIR}/imgui.cpp
        ${IMGUI_DIR}/imgui_draw.cpp
        ${IMGUI_DIR}/imgui_tables.cpp
        ${IMGUI_DIR}/imgui_widgets.cpp
    )
    target_include_directories(imgui PUBLIC
        ${IMGUI_DIR}
    )
endif()

FetchContent_GetProperties(rlimgui)
if(NOT rlimgui_POPULATED)
    FetchContent_MakeAvailable(rlimgui)
    set(RLIMGUI_DIR ${rlimgui_SOURCE_DIR})

    add_library(rlImGui STATIC
        ${RLIMGUI_DIR}/rlImGui.cpp
    )
    target_include_directories(rlImGui PUBLIC
        ${RLIMGUI_DIR}
        ${IMGUI_DIR}
    )
    target_link_libraries(rlImGui PUBLIC imgui raylib)
endif()

target_link_libraries(${PROJECT_NAME} PUBLIC
    rlImGui
    imgui
    raylib
)