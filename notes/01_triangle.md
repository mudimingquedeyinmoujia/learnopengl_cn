# 绘制一个三角形
代码为`triangle_01.cpp`,运行时请排除其他main函数文件
## 1. 大致思路
- 需要自己用GLSL语言(shader language)写shader告诉pipeline如何进行渲染
- VAO与VBO是显存的管理者，管理显存
- 告诉VAO与VBO如何解释拿到的数据

## 2. 代码流程
1. 在内存中定义若干个数组 ，这些数组代表许多点的坐标
2. `unsigned int VBO, VAO;` 在内存中定义它们，便于管理显存（注意仅仅是在内存中有了，而且VAO与VBO是数组，这里仅仅是只有一个元素的数组）
3. `glGenVertexArrays(1, &VAO);`
    `glGenBuffers(1, &VBO);`
    这里借助opengl库真的在显存中开辟空间了，并且实现了内存的引用与显存相应位置的绑定
4. `glBindVertexArray(VAO);`
    `glBindBuffer(GL_ARRAY_BUFFER, VBO);`
    这里可以将这两个函数看做是状态函数，首先跳到VAO[0]状态，之后绑定VAO[0]与VBO[0]，如果VAO与VBO是数组的话需要循环进行绑定，见`triangle_two_diff_04.cpp`
5. `glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);` 将内存中一开始定义的这个数组丢进内存，用VAO[0]进行管理
6. `glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);`
    `glEnableVertexAttribArray(0);`
    告诉VAO[0]该如何解释这些数据，并激活
7. 在更复杂的项目里，4-6步循环进行，因为内存中定义的数组有多个，VAO与VBO也不是简单的只有一个元素的数组了，见`triangle_two_diff_04.cpp`
8. `glBindVertexArray(0);`解绑VAO数组，是非必选项
9. 开始渲染循环，见代码

## 3. 小结
1. VAO的好处是：使得在不同顶点数据和属性配置之间的切换变得简单，仅仅使用一行绑定的代码就可以啦，但是VBO必须得有，因为它才是存储数据的根本
2. 不同的VAO的脚标对应着不同的VAO元素，这些不同的VAO元素包含若干坐标，而这些若干坐标对应着内存中分开定义的不同的数组，见`triangle_two_diff_04.cpp`。这样的话，不同的VAO元素可以选择不同的解读方式，在渲染循环的时候也是分开渲染的，因为opengl是一个大型状态机，当前状态只能设置一个VAO元素，见`triangle_two_diff_04.cpp`，也因此可以使用不同的shader绘制不同的VAO元素，见`triangle_two_diff_color_05.cpp`

