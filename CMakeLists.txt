cmake_minimum_required(VERSION 3.12)

project("ImGui" C CXX)
set(PREFIX "/usr")

set(BIN "${PREFIX}/bin")
set(LIB "${PREFIX}/lib")
set(INCLUDE "${PREFIX}/include")
set(SHARE "${PREFIX}/share")
set(PATH "${BIN}" "${PATH}")
set(CMAKE_COMMAND "${BIN}/cmake")
set(CMAKE_ROOT "${SHARE}/cmake")
set(CMAKE_CXX_COMPILER "${BIN}/clang++")
set(CMAKE_C_COMPILER "${BIN}/clang")
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)
set(CMAKE_C_STANDARD_REQUIRED TRUE)
set(CMAKE_EXPORT_COMPILE_COMMANDS TRUE)
set(ZLIB_LIBRARY "${LIB}/zlib1.so")
set(ZLIB_INCLUDE_DIR "${INCLUDE}/zlib")
set(zstd_INCLUDE_DIR "${INCLUDE}/zstd")
set(CMAKE_MODULE_PATH "${LIB}/cmake" "${CMAKE_MODULE_PATH}")
set(CMAKE_PREFIX_PATH
  "${LIB}/cmake"
  "${LIB}/cmake/llvm"
  "${LIB}/cmake/zstd"
  "${LIB}/cmake/glfw3"
  "${LIB}/cmake/freetype2"
  "${CMAKE_PREFIX_PATH}")
if(MSVC)
  add_compile_options(/W3 /WX)
else()
  add_compile_options(-Wall -pedantic)
endif()

set(SOURCES
"examples/example_glfw_opengl3/main.cpp"
"imgui_demo.cpp"
  "imgui_tables.cpp"
  "imgui_widgets.cpp"
  "imgui_draw.cpp"
  "imgui.cpp"
  "imgui.h"
  "imgui_internal.h"
  "imconfig.h"
  "imstb_rectpack.h"
  "imstb_textedit.h"
  "imstb_truetype.h"
  "misc/cpp/imgui_stdlib.cpp"
  "misc/cpp/imgui_stdlib.h"
  "misc/freetype/imgui_freetype.cpp"
  "misc/freetype/imgui_freetype.h"
  "misc/single_file/imgui_single_file.h"
  "backends/imgui_impl_glfw.cpp"
  "backends/imgui_impl_glfw.h"
  "backends/imgui_impl_opengl3.cpp"
  "backends/imgui_impl_opengl3.h"
  "backends/imgui_impl_opengl3_loader.h"
  "backends/imgui_impl_vulkan.cpp"
  "backends/imgui_impl_vulkan.h"
  "backends/imgui_impl_sdl2.cpp"
  "backends/imgui_impl_sdl2.h"
  )
set(FREETYPE2_INCLUDE "${PREFIX}/include/freetype2")
find_package(PkgConfig REQUIRED)
find_package(glfw3 REQUIRED)
find_package(OpenGL REQUIRED)
find_package(Vulkan REQUIRED)
find_package(Freetype REQUIRED)
find_package(SDL2 REQUIRED)
# Add source to this project's executable.
add_executable(ImGui ${SOURCES})
target_include_directories(ImGui PUBLIC "./" "backends" "misc/freetype" "misc/single_file misc/cpp" "${FREETYPE2_INCLUDE}" "${FREETYPE2_INCLUDE}/freetype")
pkg_search_module(GLFW REQUIRED glfw3)
pkg_search_module(Vulkan REQUIRED vulkan)
pkg_search_module(Freetype REQUIRED freetype2)
pkg_search_module(OpenGL REQUIRED opengl)
pkg_search_module(SDL2 REQUIRED sdl2)
include_directories(${GLFW_INCLUDE_DIRS})
include_directories(${Vulkan_INCLUDE_DIRS})
include_directories(${FREETYPE_INCLUDE_DIRS})
include_directories(${OPENGL_INCLUDE_DIR})
include_directories(${SDL2_INCLUDE_DIRS})
target_link_libraries(ImGui ${GLFW_LIBRARIES})
target_link_libraries(ImGui ${Vulkan_LIBRARIES})
target_link_libraries(ImGui ${FREETYPE_LIBRARIES})
target_link_libraries(ImGui ${OPENGL_LIBRARIES})
target_link_libraries(ImGui ${SDL2_LIBRARIES})
option(ImGui_USE_CLANG_TIDY "Use clang tidy for static analysis" ON)

if(ImGui_USE_CLANG_TIDY)
  find_program(CLANG_TIDY_EXE NAMES "${BIN}/clang-tidy"
    DOC "Path to clang-tidy executable")

  if(NOT CLANG_TIDY_EXE)
    message(STATUS "clang-tidy not found.")
  else()
    message(STATUS "clang-tidy found: ${CLANG_TIDY_EXE}")
    set_target_properties(ImGui PROPERTIES CXX_CLANG_TIDY "${CLANG_TIDY_EXE}")
  endif()
endif()