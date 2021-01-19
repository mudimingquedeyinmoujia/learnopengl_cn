# introduction
## hello window

- `hello.cpp` 绘制一个窗口

## hello triangle
- `triangle_01.cpp` 绘制一个三角形
- `rectangle_02.cpp` 绘制一个正方形，使用了EBO
- `two_triangles_03.cpp` 绘制两个三角形
- `triangles_two_diff_04.cpp` 绘制两个三角形，使用两个VAO
- `triangles_two_diff_color_05.cpp` 绘制两个三角形，使用两个VAO和两个shader
## shader
- `shader_uniform_06.cpp` shader与uniform的简单操作,使得三角形颜色随时间变化
- `shader_complex_vao_07.cpp` 针对同一个vao元素里面有不同的属性的示例，使用了location值作为区分，重点在于顶点属性的处理，绘制一个彩色三角形
- `shader_separate_08.cpp` 引入了提供的shader头文件，将shaders文件夹里面的shader作为了输入，提高了封装性，绘制了一个彩色三角形
- `shader_change_10.cpp` 一个随着时间变化x坐标的三角形，使用了封装的shader，使用了uniform从内存中接收一个float参数，改变uniform的值的时候使用了封装的shader函数