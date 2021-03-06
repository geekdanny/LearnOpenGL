# cmake的版本号
cmake_minimum_required(VERSION 3.17)
# project 的名字
project(OpenglDemo)
# C++ version
set(CMAKE_CXX_STANDARD 17)

# 检查环境变量
if (NOT DEFINED ENV{GLFW_HOME})
    message(FATAL_ERROR "found no env named GLFW_HOME")
endif()
if (NOT DEFINED ENV{GLAD_HOME})
    message(FATAL_ERROR "found no env named GLAD_HOME")
endif()

# 暂存环境变量
set(GLFW_HOME $ENV{GLFW_HOME})
set(GLAD_HOME $ENV{GLAD_HOME})

# 设置头文件目录
include_directories("${GLFW_HOME}/include")
include_directories("${GLAD_HOME}/include")

# 添加 GLFW3 预编译库
add_library(glfw SHARED IMPORTED)
SET_TARGET_PROPERTIES(glfw PROPERTIES IMPORTED_LOCATION "${GLFW_HOME}/lib/libglfw.3.dylib")


# 创建可执行文件
add_executable(OpenglDemo main.cpp "lib/glad.c")

# 链接 GLFW GLAD OpenGL
target_link_libraries(OpenglDemo glfw "-framework OpenGL")