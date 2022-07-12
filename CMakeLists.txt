cmake_minimum_required(VERSION 3.20)
project("imgui"
        LANGUAGES CXX
)

# Variables
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)
set(CMAKE_DEBUG_POSTFIX d)
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    set(WARNING_FLAGS -Werror -Wall -Wextra -Wfatal-errors -Wno-cast-function-type)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    set(WARNING_FLAGS /W3 /WX)
endif()

find_package(Vulkan REQUIRED)

add_library(imgui
    STATIC
        ${PROJECT_SOURCE_DIR}/imgui.cpp
        ${PROJECT_SOURCE_DIR}/imgui_draw.cpp
        ${PROJECT_SOURCE_DIR}/imgui_widgets.cpp
        ${PROJECT_SOURCE_DIR}/imgui_tables.cpp
        ${PROJECT_SOURCE_DIR}/backends/imgui_impl_vulkan.cpp
        ${PROJECT_SOURCE_DIR}/backends/imgui_impl_win32.cpp
)

target_compile_definitions(imgui
    PRIVATE
        WIN32_LEAN_AND_MEAN
)

target_include_directories(imgui
    PUBLIC
        $<INSTALL_INTERFACE:include/>
    PRIVATE
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}>
)

target_link_libraries(imgui
    PUBLIC
        Vulkan::Vulkan
)

# Install
install(TARGETS     imgui
        EXPORT      imgui-export
        DESTINATION lib
)

install(EXPORT      imgui-export
        FILE        imguiTargets.cmake
        NAMESPACE   imgui::
        DESTINATION lib/cmake/imgui
)

install(FILES       ${PROJECT_SOURCE_DIR}/imguiConfig.cmake
        DESTINATION lib/cmake/imgui
)

install(FILES       ${PROJECT_SOURCE_DIR}/imgui.h
                    ${PROJECT_SOURCE_DIR}/imconfig.h
                    ${PROJECT_SOURCE_DIR}/imgui_internal.h
                    ${PROJECT_SOURCE_DIR}/imstb_rectpack.h
                    ${PROJECT_SOURCE_DIR}/imstb_textedit.h
                    ${PROJECT_SOURCE_DIR}/imstb_truetype.h
                    ${PROJECT_SOURCE_DIR}/backends/imgui_impl_vulkan.h
                    ${PROJECT_SOURCE_DIR}/backends/imgui_impl_win32.h
        DESTINATION include
)
