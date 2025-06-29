cmake_minimum_required(VERSION 3.15)
cmake_policy(SET CMP0091 NEW)
project(cimgui)

set (CMAKE_CXX_STANDARD 11)

if (EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/imgui/imgui_tables.cpp)
	set(TABLES_SOURCE "imgui/imgui_tables.cpp")
else()
	set(TABLES_SOURCE "")
endif()


#general settings
file(GLOB IMGUI_SOURCES
    cimgui.cpp
    imgui/imgui.cpp
    imgui/imgui_draw.cpp
    imgui/imgui_demo.cpp
    imgui/imgui_widgets.cpp
	${TABLES_SOURCE}
)

set(IMGUI_LIBRARIES )

FIND_PACKAGE(freetype CONFIG REQUIRED)
FIND_PACKAGE(plutovg CONFIG REQUIRED)
FIND_PACKAGE(plutosvg CONFIG REQUIRED)

list(APPEND IMGUI_LIBRARIES Freetype::Freetype plutosvg::plutosvg plutovg::plutovg)
list(APPEND IMGUI_SOURCES imgui/misc/freetype/imgui_freetype.cpp)

add_library(cimgui SHARED ${IMGUI_SOURCES})

target_compile_definitions(cimgui PUBLIC IMGUI_ENABLE_FREETYPE)
target_compile_definitions(cimgui PUBLIC IMGUI_ENABLE_FREETYPE_PLUTOSVG)
target_compile_definitions(cimgui PUBLIC IMGUI_ENABLE_STB_TRUETYPE)
target_compile_definitions(cimgui PUBLIC IMGUI_USE_WCHAR32)

add_definitions("-DIMGUI_USER_CONFIG=\"../cimconfig.h\"")
target_compile_definitions(cimgui PUBLIC IMGUI_DISABLE_OBSOLETE_FUNCTIONS=1)
if (WIN32)
    target_compile_definitions(cimgui PUBLIC IMGUI_IMPL_API=extern\t\"C\"\t__declspec\(dllexport\))
    list(APPEND IMGUI_LIBRARIES imm32)
else (WIN32)
    target_compile_definitions(cimgui PUBLIC IMGUI_IMPL_API=extern\t\"C\"\t)
endif (WIN32)

target_include_directories(cimgui PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories(cimgui PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/imgui)
set_target_properties(cimgui PROPERTIES PREFIX "")
target_link_libraries(cimgui ${IMGUI_LIBRARIES})

#install
install(TARGETS cimgui
    RUNTIME DESTINATION  .
    LIBRARY DESTINATION  .
    ARCHIVE DESTINATION  .
)

#test
set(CIMGUI_TEST "no" CACHE STRING "Enable compilation of a test unit based on imgui null")

if (CIMGUI_TEST)
  add_subdirectory(test)
endif ()
