# texture

基于`texture_mutou_11.cpp` 所记录的笔记，运行前请保留`stb_image_change.cpp`文件

# 代码流程
- 使用一个数组定义顶点数据，每一行代表一个顶点以及他的颜色，纹理坐标；使用一个数组定义绘制关系以节省运算
```cpp

    float vertices[] = {
        // positions          // colors           // texture coords
         0.5f,  0.5f, 0.0f,   1.0f, 0.0f, 0.0f,   1.0f, 1.0f, // top right
         0.5f, -0.5f, 0.0f,   0.0f, 1.0f, 0.0f,   1.0f, 0.0f, // bottom right
        -0.5f, -0.5f, 0.0f,   0.0f, 0.0f, 1.0f,   0.0f, 0.0f, // bottom left
        -0.5f,  0.5f, 0.0f,   1.0f, 1.0f, 0.0f,   0.0f, 1.0f  // top left 
    };
    unsigned int indices[] = {
        0, 1, 3, // first triangle
        1, 2, 3  // second triangle
    };
```
- 定义VAO，VBO，EBO数组，由于之前只定义了一个数组存储顶点数据，所以这里每个数组也都是只有一个元素
```cpp
    unsigned int VBO, VAO, EBO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glGenBuffers(1, &EBO);
```
- 开始设置状态，首先绑定了VAO之后，意味着当前是VAO[0]状态，紧接着接下来依次绑定VBO[0]与EBO[0],这一步结束后，内存数据进入了显存
```cpp
    glBindVertexArray(VAO);

    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
```
- 开始设置显存该如何解析这些数据
```cpp
    // position attribute
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)0);
    glEnableVertexAttribArray(0);
    // color attribute
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(3 * sizeof(float)));
    glEnableVertexAttribArray(1);
    // texture coord attribute
    glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(6 * sizeof(float)));
    glEnableVertexAttribArray(2);

```
- 开始纹理相关操作，只生成一个纹理对象
```cpp
    // load and create a texture 
    // -------------------------
    unsigned int texture;
    glGenTextures(1, &texture);
```
- 绑定纹理之后，再次开始纹理状态的设置，texture[0]状态开启，此时设置环绕属性，纹理过滤属性
```cpp
    glBindTexture(GL_TEXTURE_2D, texture); // all upcoming GL_TEXTURE_2D operations now have effect on this texture object
    // set the texture wrapping parameters
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);	// set texture wrapping to GL_REPEAT (default wrapping method)
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
    // set texture filtering parameters
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```
- 读取图片，并加载进纹理，之后释放图片内存
```cpp
    // load image, create texture and generate mipmaps
    int width, height, nrChannels;
    // The FileSystem::getPath(...) is part of the GitHub repository so we can find files on any IDE/platform; replace it with your own image path.
    std::string picpath = "textures/mutou.jpg";
    unsigned char* data = stbi_load(picpath.c_str(), &width, &height, &nrChannels, 0);
    if (data)
    {
        glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
        glGenerateMipmap(GL_TEXTURE_2D);
    }
    else
    {
        std::cout << "Failed to load texture" << std::endl;
    }
    stbi_image_free(data);
```
- 开始渲染循环,因为使用了EBO，所以使用`glDrawElements()`函数进行绘制
```cpp
    while (!glfwWindowShouldClose(window))
    {
        // input
        // -----
        processInput(window);

        // render
        // ------
        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        // bind Texture
        glBindTexture(GL_TEXTURE_2D, texture);

        // render container
        ourShader.use();
        glBindVertexArray(VAO);
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        // glfw: swap buffers and poll IO events (keys pressed/released, mouse moved etc.)
        // -------------------------------------------------------------------------------
        glfwSwapBuffers(window);
        glfwPollEvents();
    }
```

## 小结
- 此处glBindTexture看作了一个状态函数，其实在他之前还有纹理单元这一个状态，一个纹理单元对应一个纹理，总共有GL_TEXTURE0到GL_TEXTRUE15这16个纹理单元，每个纹理单元对应一个texture纹理数组的元素（上面的例子中的纹理数组只有一个元素）
- 之所以上面没有体现出纹理单元，是因为默认为GL_TEXTURE0这个纹理单元在使用，并且已激活
- 关于纹理单元，纹理，采样器之间的关系小结如下，详情请见`texture_mix_12.cpp`：
1. 在shader里面，有很多采样器，每个采样器是一个uniform，我们需要在内存里给他设置一个值，叫位置值，如`glUniform1i(glGetUniformLocation(ourShader.ID, "texture1"), 0);`或者`ourShader.setInt("texture2", 1);`
2. 而所谓的纹理单元的位置值，又叫做纹理单元，opengl提供了16个纹理单元，当我们给采样器设置好位置值之后，其实就意味着设置好了对应的纹理单元，如位置值为0就代表GL_TEXTURE0这个纹理单元，位置值为1就代表GL_TEXTURE1这个纹理单元
3. 在内存中有着若干个纹理的引用，而每个纹理的引用对应着显存里的一张图片，如果将内存中的引用绑定到对应的纹理单元，那么就能够实现shader与显存图片的交互了（注意每个纹理单元也是代表一张图片），于是在渲染循环里使用状态激活的方式设置，代码如下
```cpp
        glActiveTexture(GL_TEXTURE0);
        glBindTexture(GL_TEXTURE_2D, texture1);
        glActiveTexture(GL_TEXTURE1);
        glBindTexture(GL_TEXTURE_2D, texture2);
```