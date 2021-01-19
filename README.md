# learnopengl_cn
此项目基于visual studio，学习opengl而构建，学习网址为 https://learnopengl-cn.github.io/ . 依赖glfw库与glad库
# set up for the project
### 1. 新建一个空项目
### 2. 设置项目include与lib要读取的路径
![avatar](https://github.com/mudimingquedeyinmoujia/learnopengl_cn/blob/main/imgs/setup_1.png)

![avatar](https://github.com/mudimingquedeyinmoujia/learnopengl_cn/blob/main/imgs/setup_2.png)
### 3. 设置链接的时候的文件路径 (.lib 文件)
![avatar](https://github.com/mudimingquedeyinmoujia/learnopengl_cn/blob/main/imgs/setup_3.png)

### 4. 把官方提供的 glad.c 文件复制进项目，以便于 glad.h 文件所调用  

### 5. 运行 hello.cpp 即可绘制窗口

### 6. 要运行不同的章节文件，由于有多个 main函数 文件， 在 visual studio 里面选择从项目中排除其它带 main函数 的文件即可

