# material
此笔记根据`material_19.cpp`构建

# 物体颜色的若干决定属性：

如下表所示，其中最终的物体颜色决定为：
物体颜色=ambient+diffuse+specular
每一个组成=光线\*权重\*材质\*计算分量
| 组成 | 光线 | 权重 | 材质 | 计算分量
|:-:|:-:|:-:|:-:|:-:|
ambient |光线自身的颜色| 0.2 | 环境颜色 | 无
diffuse | 光线自身的颜色| 0.5 | 漫反射颜色 | 点的位置与光线夹角
specular | 光线自身的颜色 | 1.0 | 高光颜色 | 观察位置与反射夹角的shininess次方

1. 其中材质的环境颜色与漫反射颜色其实可以看做一样的东西
2. 通常来说，在光照系统中，纹理叫做漫反射贴图（diffuse map）
3. 光源如果用vec4(x,y,z,1.0)表示，表示光源位置；如果用vec4(x,y,z,0.0)，表示是平行光
4. 这里点光源计算的时候并没有考虑光照强度随着距离衰减