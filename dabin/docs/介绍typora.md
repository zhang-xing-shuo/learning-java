
平时，我优先使用 Typora 写各种内容。包括：

+ 随笔
+ 个人计划
+ 个人总结
+ 编程技术的笔记

##  介绍Markdown

Markdown是一种**轻量级标记语言**。简单讲，也可以说，是一种文本格式。

该标记语言，排版语法简洁，让使用者更多地关注内容本身而非排版。

基础语法：

+ 标题

  + 1-5级
+ 字体

  + 加粗
  + 倾斜
  + 删除线
+ 列表

  + 有序

  + 无序
+ 任务列表
+ 引用
+ 表格
+ 代码

##  介绍Typora

Typora 是一款文本编辑器，专门支持 Markdown。简称**Markdown 文本编辑器**。

2021年11月23日，Typora 正式发布 1.0 版本，正式版开始收费了，定价14.99美元。不过，Beta版本还是可以继续免费使用的。

### 所见即所得

输入`Markdown`标记后，会即时渲染成相应格式。

+ 市面上，大部分的`Markdown`编辑器，包括VSCode、Idea内嵌的编辑器：
  一半是编辑窗口，一半是实时的预览窗口
+ Typora合二为一，更为简洁

###  生成目录

1. 使用`[TOC]`

   在文档的任意位置， 使用`[TOC]`标记：目录将自动生成并显示。该目录的每项，带页内超链接。
   某些平台不支持，如掘金。

2. 使用插件 doctoc

   1. 安装：`npm install doctoc`
   2. 运行：在文档当前目录，执行`doctoc xxx.md`命令，即可生成目录、标题
      （如果修改了标题，需要再次执行命令更新目录）

###  插入图片

语法：

```markdown
![](URL)
```

图片的链接地址（URL），可以是本地的，也可以是网络的。

我使用的是，图床工具PicGo，具体配置方法如下：

1、下载：`https://github.com/Molunerfinn/PicGo/releases`

2、选择图床，设置相关参数。PicGo 支持多个图床平台，如七牛、阿里云OSS等。

![](http://img.topjavaer.cn/img/图床1.png)

3、设置 PicGo server。

![](http://img.topjavaer.cn/img/图床2.png)

4、打开 Typora 中的「文件-偏好设置-图像」选项，配置上传服务为 PicGo 和 PicGo 的路径。

![](http://img.topjavaer.cn/img/图床3.png)

配置完成之后，当你在 Typora 中插入本地图片时，PicGo 会自动将图片上传图床并使用 Markdown 语法替换图片地址。

###  插入数学公式

在电子文档中，书写数学公式，最常见地，按照 LaTeX 语法。

在Typora文档中，插入这样的数学公式，有两种方式：

- 行内（inline）

  + 语法：

    ```markdown
    $LaTeX code$
    ```

  + 源代码：

    ````
    $y^{(x)}$
    ````
    
  + 显示效果：

    $y^{(x)}$

- 块（display），独立成行

  + 语法：

    ```markdown
    $$
    LaTeX code
    $$
    ```
  
  + 举例：
  
    + 源代码：
  
      ````
      $$
      \sum_{k=1}^nkx
      $$
      ````
  
    + 显示效果：
      $$
      \sum_{k=1}^nkx
      $$
  
  LaTeX 常用语法：

![](http://img.topjavaer.cn/img/latex语法.png)

下面举几个例子：

+ 分段函数

  + 源代码：

    ```
    $$
    f(n)=
    	\begin{cases}
    		n/2, & \text{if $n$ is even}\\
    		3n+1,& \text{if $n$ is odd}
    	\end{cases}
    $$
    ```

  + 显示效果：
    $$
    f(n)=
    	\begin{cases}
    		n/2, & \text{if $n$ is even}\\
    		3n+1,& \text{if $n$ is odd}
    	\end{cases}
    $$

+ 矩阵

  + 源代码：

    ```
    $$
    X=\left|
    	\begin{matrix}
    		x_{11} & x_{12} & \cdots & x_{1d}\\
    		x_{21} & x_{22} & \cdots & x_{2d}\\
    		\vdots & \vdots & \ddots & \vdots \\
    		x_{11} & x_{12} & \cdots & x_{1d}\\
    	\end{matrix}
    \right|
    $$
    ```

  + 显示效果：
    $$
    X=\left|
    	\begin{matrix}
    		x_{11} & x_{12} & \cdots & x_{1d}\\
    		x_{21} & x_{22} & \cdots & x_{2d}\\
    		\vdots & \vdots & \ddots & \vdots \\
    		x_{11} & x_{12} & \cdots & x_{1d}\\
    	\end{matrix}
    \right|
    $$

+ 偏导数和微分

  + 源代码：

    ```
    $$
    \frac{\partial z}{\partial x_1} + \frac{\partial z}{\partial x_2} \\
    \frac{\mathrm{d}z}{\mathrm{d}x_1}+\frac{\mathrm{d}z}{\mathrm{d}x_2}
    $$
    ```

  + 显示效果：
    $$
    \frac{\partial z}{\partial x_1} + \frac{\partial z}{\partial x_2} \\
    \frac{\mathrm{d}z}{\mathrm{d}x_1}+\frac{\mathrm{d}z}{\mathrm{d}x_2}
    $$



###  定制化主题

> 可以通过 CSS 文件，设置Markdown文档的渲染(显示)效果。这个原理，和HTML文档是一样的。

在 Typora 中，将定制化的一套渲染效果，称为主题。这类似于游戏中，人物模型的皮肤。

在 `文件 - 偏好设置 - 主题 - 打开主题文件夹` 看到这些 CSS 文件。

我们可以，在官网的主题页面，或其他地方，挑选自己喜欢的主题，并下载、使用。

如果我们具备CSS的知识，可以自己定义、修改 CSS 文件，从而自己编写主题。



我们可以在工具栏里，点击主题，从下拉菜单中展示的已安装的主题中，更换主题。

### Mermaid

`Mermaid`是一个用于画流程图、状态图、时序图、甘特图的库，使用 JavaScript 进行本地渲染，广泛集成于许多 Markdown 编辑器中。Typora也支持`Mermaid`语法。

下面举几个例子。

+ 流程图

  + 源代码：

    ````
    ```mermaid
    graph TD;
    A-->B;
    A-->C; 
    B-->D;
    C-->D;
    ```
    ````

  + 显示效果：

    ```mermaid
    graph TD;
    A-->B;
    A-->C; 
    B-->D;
    C-->D;
    ```

+ 时序图

  + 源代码：

    ````
    ```mermaid
    sequenceDiagram
        Alice->>+John: Hello John, how are you?
        Alice->>+John: John, can you hear me?
        John-->>-Alice: Hi Alice, I can hear you!
        John-->>-Alice: I feel great!
    ```
    ````

    

  + 显示效果：

    ```mermaid
    sequenceDiagram
        Alice->>+John: Hello John, how are you?
        Alice->>+John: John, can you hear me?
        John-->>-Alice: Hi Alice, I can hear you!
        John-->>-Alice: I feel great!
    ```

    

    

###  导入导出

Typora 支持导入和导出的文件格式：`html`、`pdf`、`docx`、`epub`和`latex`等。

其中导出`docx`、`epub`和`latex`需要安装 `Pandoc` 插件。

###  其他功能

文件、编辑、段落、格式、视图、主题、帮助

视图

+ ……

+ 源代码模式 Ctrl + /                ⟹ 直接查看&编辑Markdown文件的原始代码

  ---

+ 专注模式 F8                            ⟹ 使你正在编辑的那一行保留颜色，而其他行的字体呈灰色。
+ 打字机模式 F9                        ⟹ 使得你所编辑的那一行永远处于屏幕正中。
+ ……



**代码高亮**：输入 ``` 后并输入语言名，换行，开始写代码，Typora 会自动实现代码高亮的效果

