cmake_minimum_required(VERSION 3.8 FATAL_ERROR)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)

set(RSM_SOURCES ${PROJECT_SOURCE_DIR}/src/main.cpp)

if (EMSCRIPTEN)
    set(CMAKE_EXECUTABLE_SUFFIX ".html")
endif()

if(APPLE)
    add_executable(ReflectiveShadowMaps MACOSX_BUNDLE ${RSM_SOURCES})
    set(MACOSX_BUNDLE_BUNDLE_NAME "com.dihara.vct") 
else()
    add_executable(ReflectiveShadowMaps ${RSM_SOURCES}) 
endif()

target_link_libraries(ReflectiveShadowMaps dwSampleFramework)

if (EMSCRIPTEN)
    set_target_properties(ReflectiveShadowMaps PROPERTIES LINK_FLAGS "--embed-file ${PROJECT_SOURCE_DIR}/shader/fs.glsl@shader/fs.glsl --embed-file ${PROJECT_SOURCE_DIR}/shader/fs.glsl@shader/fs.glsl -O3 -s WASM=1 -s ALLOW_MEMORY_GROWTH=1 -s USE_GLFW=3 -s USE_WEBGL2=1")
endif()

if (APPLE)
    add_custom_command(TARGET ReflectiveShadowMaps POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/src/shader $<TARGET_FILE_DIR:ReflectiveShadowMaps>/ReflectiveShadowMaps.app/Contents/Resources/shader)
else()
    add_custom_command(TARGET ReflectiveShadowMaps POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/src/shader $<TARGET_FILE_DIR:ReflectiveShadowMaps>/shader)
endif()