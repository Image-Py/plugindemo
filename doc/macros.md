#  Macros 插件

Macros是一系列的命令记录，用这些记录可以重演操作，我们一般并不会自己编写命令，而是通过宏录制器来完成记录。宏录制器在**`Plugins > Macros> Macros Recorder`**。 我们将录制的命令保存到menus或其子文件夹里，mc后缀，重启即可加载到对应位置。

![14](http://idoc.imagepy.org/demoplugin/08.png)

<div align=center>Macros Recorder</div><br>

**关于插件的加载方式**

1. menus及其子文件夹下的mc后缀文件会被解析成宏。

2. 也可以将宏文件拖拽到ImagePy最下方的状态栏执行。

   

## 高斯模糊再求反

```python
Gaussian>{'sigma': 2}
Invert>None
```

以上是两条命令，点击后会依次执行Gaussian，Invert。

![14](http://idoc.imagepy.org/demoplugin/09.png)

<div align=center>Gaussian And Invert</div><br>

1. 宏命令是`插件名称>参数字典`这种形式构成
2. 如果没有参数用 `None`占位
3. 如果有参数的命令参数给`None`，ImagePy会弹出参数对话框进行交互。



## 分割硬币

```python
coins>None
Up And Down Watershed>{'thr1': 36, 'thr2': 169, 'type': 'up area'}
Fill Holes>None
Geometry Filter>{'con': '4-connect', 'inv': False, 'area': 100.0, 'l': 0.0, 'holes': 0, 'solid': 0.0, 'e': 0.0, 'front': 255, 'back': 0}
Geometry Analysis>{'con': '8-connect', 'center': True, 'area': True, 'l': True, 'extent': False, 'cov': True, 'slice': False, 'ed': False, 'holes': False, 'ca': False, 'fa': False, 'solid': False}
```

以上是一个硬币分割的流程录制成宏，点击后会自动执行硬币分割，预处理，区域过滤，区域分析，生成表格。（当然通常我们不会将图像打开也录制到宏）

![14](http://idoc.imagepy.org/demoplugin/10.png)

<div align=center>Coins Segment Macros</div><br>