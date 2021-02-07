# learn OpenGL ES （Web 版本）

这里我将使用 `MDN` 的资源进行简明的示例编写，以便于你能够快速读懂。


## 基本原理

- 第一步

  - 基于 `canvas` 对象
  - 利用 OpenGL ES 2.0 的 API   

- 第二步
  - 用 `JavaScript` 写的控制代码
  - 执行的着色代码（`GLSL` ，注：`GLSL` 为 `OpenGL` 着色语言）  


## 准备点火

下面我们基于一个 `html` 文件结合  `Canvas` 初始化一个 `WEBGL` 上下文。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>prepare</title>
</head>
<body onload="OnDOMLoaded()">
  <canvas id="glcanvas" class="box">
    你的浏览器似乎不支持或者禁用了HTML5 <code>&lt;canvas&gt;</code> 元素.
  </canvas>
</body>
<script>
  function OnDOMLoaded(){
    const CANVAS = document.getElementById('glcanvas')
    const GL_CONTEXT = CANVAS.getContext('webgl')
    if (!GL_CONTEXT){
      alert('无法初始化WEBGL，你的浏览器或OS不支持')
      return
    }

    // 使用不透明全黑抹掉一切
    GL_CONTEXT.clearColor(0, 0, 0, 1)
    // 清除缓冲区
    GL_CONTEXT.clear(GL_CONTEXT.COLOR_BUFFER_BIT)
  }
</script>
</html>

```

屏幕截图如下所示：

![prepare](learnOpenGLES-prepare-html-1612674402226.png)

## 渲染

- 着色器
  - 本质
    - 使用着色语言编写的程序
      - [着色语言](GLSL_ES_Specification_1.00.pdf)
  - 需要的信息
    - 绘制形状的顶点信息
    - 构造绘制在屏幕上像素的所需数据
    
  - 一句话
    - 着色器负责记录像素点位置、颜色
  - 使用的函数
    - 顶点着色器
    - 片段着色器
    - 顶点着色器和片段着色器的集合我们通常称之为着色器程序。
  
### 顶点着色器

一句话可以概括：

将原始坐标系转换为WEBGL的特定坐标系

细节：

- 每个轴的坐标范围是　`－１`　到　`１`
- 不考虑纵横比，实际尺寸或任何其他因素
- 经过各种转换后，将变量保存至名为 `gl_Position`的特殊变量中并返回

他能干的活：

- 决定哪个包含 `texel` 面部纹理的坐标，可以应用于顶点；
- 通过法线来确定应用到顶点的光照因子等
- 存储的变量
  - `varyings`
  - `attributes`
  - 目的
    - 以便与片段着色器共享
  
#### 示例

定义一个顶点位置

```js
  const vsSource = `
    attribute vec4 aVertexPosition; // 顶点位置值

    uniform mat4 uModelViewMatrix;  // 4x4 矩阵
    uniform mat4 uProjectionMatrix; // 4x4 矩阵

    void main() {
    // 乘积赋值给gl_Position
      gl_Position = uProjectionMatrix * uModelViewMatrix * aVertexPosition;
    }
  `;
```

### 片段着色器

定点处理完了，每个要被绘制的像素点调用一次它

职责：

确定像素的颜色

方式：

通过指定应用到像素的纹理元素（也就是图形纹理中的像素），获取纹理元素的颜色，然后将适当的光照应用于颜色。

变量存储？

存储在特殊变量 `gl_FragColor` 中，返回到 `WebGL` 层

最终渲染：

该颜色将最终绘制到屏幕上图形对应像素的对应位置。


```js
const fsSource = `
    void main() {
    // 获取纹理元素的颜色
      gl_FragColor = vec4(1.0, 1.0, 1.0, 1.0);
    }
  `;
```
