# Setup OpenGL Development Environment with Clion on MacOS

伴随图形学课程进入中后期，我们也不能光讲理论，不做实践。在2021年的岁月里，让我们“撸起袖子加油干”！

今天，我们从比较容易上手的OpenGL开始。

**这门课程的前置基础：**

1. 杂货铺里Danny之前写的图形学入门课程
2. C/C++编程语言

**开发环境：**

- Mac OS， Clion， XCode

或者

- Windows，Clion，Visual Studio 2016。

为了统一大家的开发环境，这门课里采用Clion。本人的工作环境是Macbook，所以接下来的内容主要会围绕Macbook+Clion环境配置OpenGL来讲解。

# 配置C/C++开发环境

## 第一步：Install Brew

Brew 是Macbook上用来安装和管理各种工具的的command工具。

安装方法如下：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## 第二步：确认自己的MacOS上安装了gcc：

```bash
~ ❯❯❯ gcc --version                                                                        prefix=/Library/Developer/CommandLineTools/usr --with-gxx-include-dir=/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/c++/4.2.1Apple clang version 12.0.0 (clang-1200.0.32.28)Target: x86_64-apple-darwin20.1.0Thread model: posixInstalledDir: /Library/Developer/CommandLineTools/usr/bin
```

如果没有安装的话会上面的指令会自动触发 `XCode` 下载并安装，根据提示安装完成之后再使用上面的指令检查一遍即可。

### 第三步: 下载Clion并安装

Clion的下载路径地址：https://www.jetbrains.com/clion/download/#section=mac

Clion并不是免费的，新下载的用户有30天试用。如果大家没有银子购买，可以参考网上一个大神提供的定期重新激活插件：https://zhile.io/2020/11/18/jetbrains-eval-reset.html。

安装完 `CLion` 之后，可以在 `Settings` 中检查 `Toolchain` 是否正确识别，如下：

![rCLZXe](https://raw.githubusercontent.com/geekdanny/images/master/blog/rCLZXe-20210112153817028.png)

通常来说只需要安装好 `gcc` 即可，因为 `cmake` 会在 `CLion` 中自带，而 `make` 会在 `MacOS` 系统中提供。

这里大家如果没听说cmake是什么，简单说一下，cmake是用来管理C/C++程序的依赖和构建工具，他可以生成makefile，并轻松的管理项目中各级文件夹下的makefile的调度关系。

好啦，上边这些都是在MacOS上C/C++开发环境的基本配置，哪怕你不做OpenGL编程，这些配置也是完成c和c++语言编程的的最基本配置。 

注意：在Macbook上，你可以用xcode来做c/c++ 甚至是OpenGL的编程，在windows操作系统上，Visual Studio用的比较多。这里，我们统一采用Clion这种可以跨平台的IDE。

接下来是重点了，我们开始配置有关OpenGL的开发环境。

# 配置有关OpenGL的开发环境

OpenGL的开发主要依赖两个庫：

- `GLFW`: 提供跨平台的 `OpenGL` 上下文初始化与窗口创建等功能
- `GLAD`: 提供跨平台的 `OpenGL` 函数指针加载等功能

两者的下载顺序没有前后，我们一个一个来吧。

## 下载并配置GLFW

地址在这：https://www.glfw.org/download.html，`GLFW` 在不同的平台上都有预编译好的包，我们可以根据我们的系统下载 `MacOS` 版本的预编译包：

![XIEQIW](https://raw.githubusercontent.com/geekdanny/images/master/blog/XIEQIW.png)

但是，这里请注意，我建议用homebrew来下载和管理GLFW。因为homebrew作为统一的工具和庫的管理工具，可以方便以后对庫的删除和升级。这里用brew安装GLFW的命令如下：

```
brew install glfw3
```

brew会默认把所有的文件安装在**/usr/local/Cellar/**的一个文件夹里。例如glfw就被安装在如下的一个目录里**/usr/local/Cellar/glfw**

![UZFz8P](https://raw.githubusercontent.com/geekdanny/images/master/blog/UZFz8P.png)

不仅如此，brew 还会在/usr/local/include的目录里建立一个目录软连接，这个soft link 指向了 /usr/local/Cellar/glfw/3.3.2/include 这个目录。这样子，GLFW的头文件就被包含在IDE默认搜索的路径下了。

## 接下来是 `GLAD` 的下载与配置

`GLAD` 作为一个加载器，根据不同的系统与 `OpenGL`，库本身都有所变化，需要灵活配置，官方提供了一个在线配置与生成库的网站：https://glad.dav1d.de/，我们根据我们的需要进行选择：

这里有两个配置要注意：

![DgI4P9](https://raw.githubusercontent.com/geekdanny/images/master/blog/DgI4P9.png)

- API我们选择3.3. 其实OpenGL到今天已经发展到4.6版本了，这里选择用3.3，是因为3.3版的OpenGL是跟它之前的版本的一个分水岭，之后其他的版本都是在3.3版本上的一个升级。
- Profile选择Core。至于为什么选择Core，这里引用LearnOpenGL CN的原作者给出的解释：

> 早期的OpenGL使用立即渲染模式（Immediate mode，也就是固定渲染管线），这个模式下绘制图形很方便。OpenGL的大多数功能都被库隐藏起来，开发者很少有控制OpenGL如何进行计算的自由。而开发者迫切希望能有更多的灵活性。随着时间推移，规范越来越灵活，开发者对绘图细节有了更多的掌控。立即渲染模式确实容易使用和理解，但是效率太低。因此从OpenGL3.2开始，规范文档开始废弃立即渲染模式，并鼓励开发者在OpenGL的核心模式(Core-profile)下进行开发，这个分支的规范完全移除了旧的特性。

那点击Generate 并下载为glad.zip。

解压下载的glad.zip会得到如下的文件夹结构：

![41XFoS](https://raw.githubusercontent.com/geekdanny/images/master/blog/41XFoS.png)

我们复制glad文件夹，并把它粘贴到/usr/local/include这个目录，glad被复制到新的目录.

好的，到这里，让我们来记录下GLFW和GLAD的位置。

| GLFW 被放置 | **/usr/local/Cellar/glfw/3.3.2** |
| ----------- | -------------------------------- |
| GLAD 被放置 | **/usr/local/include/glad**      |

接下来我们来配置两个环境变量：

```
export GLFW_HOME="/usr/local/Cellar/glfw/3.3.2"export GLAD_HOME="/usr/local/include/glad"
```

推荐将这两条命令写入你的shell profile 里， 如果你用的bash，可以写入 `~/.bash_profile`，因为我用的是zsh，我会写入~/.zprofile这个文件里。

```
vi ~/.zprofile
```

把上边的两行放到shell profile 的最末端，然后激活shell profile。

```
/u/l/i/glad ❯❯❯ source ~/.zprofile
```

# 创建与配置项目

首先使用 `CLion` 创建一个 `C/C++` 工程，

![p4ISPH](https://raw.githubusercontent.com/geekdanny/images/master/blog/p4ISPH.png)

创建完成之后应该可以看到项目根目录下有一个 `CMakeLists.txt` 文件

![0Ov6XG](https://raw.githubusercontent.com/geekdanny/images/master/blog/0Ov6XG.png)



我们现在项目里创建的lib 文件夹，然后把`glad.c` 拖进去。`glad.c`是需要参与编译的。

这时候我们可以按照如下配置修改：

```cmake
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
```

# 编写测试代码

完成项目配置后，改写 `main.cpp` 来写一个测试小程序吧，这里源代码用的是learnOpenGL 网站的代码：

```c++
#include <glad/glad.h>
#include <GLFW/glfw3.h>

#include <iostream>

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void processInput(GLFWwindow *window);

// settings
const unsigned int SCR_WIDTH = 800;
const unsigned int SCR_HEIGHT = 600;

const char *vertexShaderSource = "#version 330 core\n"
                                 "layout (location = 0) in vec3 aPos;\n"
                                 "void main()\n"
                                 "{\n"
                                 "   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
                                 "}\0";
const char *fragmentShaderSource = "#version 330 core\n"
                                   "out vec4 FragColor;\n"
                                   "void main()\n"
                                   "{\n"
                                   "   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"
                                   "}\n\0";

int main()
{
    // glfw: initialize and configure
    // ------------------------------
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

#ifdef __APPLE__
    glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
#endif

    // glfw window creation
    // --------------------
    GLFWwindow* window = glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, "LearnOpenGL", NULL, NULL);
    if (window == NULL)
    {
        std::cout << "Failed to create GLFW window" << std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    // glad: load all OpenGL function pointers
    // ---------------------------------------
    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
    {
        std::cout << "Failed to initialize GLAD" << std::endl;
        return -1;
    }


    // build and compile our shader program
    // ------------------------------------
    // vertex shader
    int vertexShader = glCreateShader(GL_VERTEX_SHADER);
    glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
    glCompileShader(vertexShader);
    // check for shader compile errors
    int success;
    char infoLog[512];
    glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
    if (!success)
    {
        glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
        std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << std::endl;
    }
    // fragment shader
    int fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
    glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
    glCompileShader(fragmentShader);
    // check for shader compile errors
    glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);
    if (!success)
    {
        glGetShaderInfoLog(fragmentShader, 512, NULL, infoLog);
        std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n" << infoLog << std::endl;
    }
    // link shaders
    int shaderProgram = glCreateProgram();
    glAttachShader(shaderProgram, vertexShader);
    glAttachShader(shaderProgram, fragmentShader);
    glLinkProgram(shaderProgram);
    // check for linking errors
    glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
    if (!success) {
        glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);
        std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n" << infoLog << std::endl;
    }
    glDeleteShader(vertexShader);
    glDeleteShader(fragmentShader);

    // set up vertex data (and buffer(s)) and configure vertex attributes
    // ------------------------------------------------------------------
    float vertices[] = {
            -0.5f, -0.5f, 0.0f, // left
            0.5f, -0.5f, 0.0f, // right
            0.0f,  0.5f, 0.0f  // top
    };

    unsigned int VBO, VAO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    // bind the Vertex Array Object first, then bind and set vertex buffer(s), and then configure vertex attributes(s).
    glBindVertexArray(VAO);

    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
    glEnableVertexAttribArray(0);

    // note that this is allowed, the call to glVertexAttribPointer registered VBO as the vertex attribute's bound vertex buffer object so afterwards we can safely unbind
    glBindBuffer(GL_ARRAY_BUFFER, 0);

    // You can unbind the VAO afterwards so other VAO calls won't accidentally modify this VAO, but this rarely happens. Modifying other
    // VAOs requires a call to glBindVertexArray anyways so we generally don't unbind VAOs (nor VBOs) when it's not directly necessary.
    glBindVertexArray(0);


    // uncomment this call to draw in wireframe polygons.
    //glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);

    // render loop
    // -----------
    while (!glfwWindowShouldClose(window))
    {
        // input
        // -----
        processInput(window);

        // render
        // ------
        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        // draw our first triangle
        glUseProgram(shaderProgram);
        glBindVertexArray(VAO); // seeing as we only have a single VAO there's no need to bind it every time, but we'll do so to keep things a bit more organized
        glDrawArrays(GL_TRIANGLES, 0, 3);
        // glBindVertexArray(0); // no need to unbind it every time

        // glfw: swap buffers and poll IO events (keys pressed/released, mouse moved etc.)
        // -------------------------------------------------------------------------------
        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    // optional: de-allocate all resources once they've outlived their purpose:
    // ------------------------------------------------------------------------
    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glDeleteProgram(shaderProgram);

    // glfw: terminate, clearing all previously allocated GLFW resources.
    // ------------------------------------------------------------------
    glfwTerminate();
    return 0;
}

// process all input: query GLFW whether relevant keys are pressed/released this frame and react accordingly
// ---------------------------------------------------------------------------------------------------------
void processInput(GLFWwindow *window)
{
    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}

// glfw: whenever the window size changed (by OS or user resize) this callback function executes
// ---------------------------------------------------------------------------------------------
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    // make sure the viewport matches the new window dimensions; note that width and
    // height will be significantly larger than specified on retina displays.
    glViewport(0, 0, width, height);
}
```

好啦，如果你也和我一样一切都配置成功了：当你在clion上运行OpenglDemo project的时候，你应该可以看到一个三角形被成功的绘制出来了。

![qiyvUd](https://raw.githubusercontent.com/geekdanny/images/master/blog/qiyvUd.png)

项目代码：https://github.com/geekdanny/LearnOpenGL/tree/main/OpenglDemo

