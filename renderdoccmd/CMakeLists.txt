set(sources renderdoccmd.cpp)
set(includes PRIVATE ${CMAKE_SOURCE_DIR}/renderdoc/api)
set(libraries PRIVATE renderdoc)

if(APPLE)
    list(APPEND sources renderdoccmd_apple.cpp)
elseif(ANDROID)
    list(APPEND sources renderdoccmd_android.cpp)
    include_directories(${ANDROID_NDK}/sources/android/native_app_glue)
    list(APPEND libraries PRIVATE -llog -landroid)
elseif(UNIX)
    list(APPEND sources renderdoccmd_linux.cpp)

    find_package(OpenGL REQUIRED)
    list(APPEND includes PRIVATE ${OPENGL_INCLUDE_DIR})
    list(APPEND libraries PRIVATE ${OPENGL_gl_LIBRARY})

    list(APPEND libraries PRIVATE -lxcb -lX11 -lX11-xcb)
endif()

if(ANDROID)
    add_library(renderdoccmd SHARED ${sources})
else()
    add_executable(renderdoccmd ${sources})
endif()
target_include_directories(renderdoccmd ${includes})
target_link_libraries(renderdoccmd ${libraries})

install (TARGETS renderdoccmd DESTINATION bin)
