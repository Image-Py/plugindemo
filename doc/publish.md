# 插件项目发布

以上我们讨论了每一种插件的写法，当然，我们可以任意在ImagePy的`menus`，`tools`，`widgets`下创建python脚本以扩展出某个插件功能，但当我们的插件越来越多，功能越来越系统，当然就会希望正式发布成一个插件项目，可以让其他用户方便的安装使用。这里我们讨论如何发布一个插件项目。



## 功能组织

**功能划分**

如果我们的插件并不是一个单一的功能，而是系统性很强的一系列功能，那么我们应该将这些功能进行划分和聚类，用文件夹来进行组织。例如我们之前编写的全部功能，就可以按照插件类型来进行组织。

![14](http://idoc.imagepy.org/demoplugin/28.png)

<div align=center>功能划分</div><br>
**顺序设定**

我们看到，对插件进行了功能划分，看起来就更加清晰，然而插件是以字母顺序进行排序的，我们希望指定顺序，在ImagePy中这非常容易实现，我们在init.py文件中加入`catlog`字段，用一个列表对象指明顺序。

`__init__.py`

```python
catlog = ['Start Here', '-', 'Markdown Demo', 'Macros Demo', 'Workflow Demo', '-', 'Filter Demo', 'Simple Demo', 'Table Demo', 'Free Demo', '-', 'WidgetDemo']
```

一旦设定catlog，菜单将按照指定顺序加载，`'-'`被解析成分割线。`catlog`可以出现在任何插件目录下，即可以指定插件，也可以指定文件夹的顺序，对于tools也起作用（另一种设定顺序的方式是之前介绍的一个文件实现多个插件的`plgs`方式）。

![14](http://idoc.imagepy.org/demoplugin/29.png)

<div align=center>sort by catlog</div><br>

`catlog`可以出现在任何插件目录下，即可以指定插件，也可以指定文件夹的顺序，对于`tools`也起作用。另外多插件的文件中通过`plgs`也可以指定插件的顺序。



## 插件项目创建

**创建插件项目仓库**

如果插件都分散在ImagePy主项目的文件夹下不便于管理，因而我们创建一个单独的插件项目仓库，在仓库的顶层创建`menus`，`tools`，`widgets`三个文件夹，里面存放经过功能划分和顺序设定的插件。



**编写 requirements**

Python最大的好处在于有丰富的库，作为一个插件项目，很可能会依赖其他的库，按照惯例，需要把依赖写入到`requirements.txt`中（ImagePy在安装插件时会自动调用pip解决依赖，暂时只支持pip，后续会考虑支持conda）



**编写readme**

readme原本只是用于项目介绍，但在ImagePy中将readme的前几行信息用作插件管理，所以readme前几行需要严格按照如下规范，以本项目为例：

```markdown
# Demo Plugin

Path: https://github.com/Image-Py/demoplugin

Version: 0.1

Author: YXDragon

Email: yxdragon@imagepy.org

Keyword: demo, tutorial

Description: a friendly develop tutorial

*以下可以随意书写*

```



**插件的安装**

**`Plugins > Install > Install Plugin`** 在对话框中输入插件仓库的github连接，ImagePy即开始下载插件，解决依赖，并自动加载。加载成功后我们可以看到菜单栏，工具栏，组件栏自动更新。这样，把你的插件项目地址发给其他人，就可以安装，使用了。

![14](http://idoc.imagepy.org/demoplugin/30.png)

<div align=center>插件的安装</div><br>

## 发布到 ImagePy

**给ImagePy发Pull Request**

你也可以将插件项目发布到ImagePy，如果你已经完成你的插件项目，那么发布是一个很简单的过程。只需要`fork`一份ImagePy，将你的`readme`文件拷贝到**`imagepy - Plugins - Contribute - Contributors`**下，重命名为插件项目名称（不强制，但最好能体现插件功能），然后给ImagePy提`Pull Request`。当ImagePy组织成员收到`Pull Request`，并进行测试后，会`merge`你的`Pull Request`，如有问题会通过`issue`进行沟通。一旦合并到ImagePy主分支内，用户即可通过插件管理器对插件进行检索，安装，卸载等操作。

![14](http://idoc.imagepy.org/demoplugin/06.png)

<div align=center>Plugins Manager</div><br>

插件管理器会自动解析`contributors`目录下的插件信息，因而插件项目的`readme`务必按照格式认真书写，这会影响用户的检索体验。如果有更新，请修改版本号，重新`Pull Request`，插件管理器会自动检测更新。



**关于顶级菜单**

我们看到安装插件后，插件项目中的功能也加载到了菜单栏，工具栏，组件栏中，可见插件项目中的menus，tools，widgets目录与ImagePy主项目中的目录具有等同效果。**如果不希望插件功能出现在顶级菜单，可以用文件夹结构进行控制**，比如希望出现在Plugin下，就在插件项目的menus目录下加一层Plugin，进而放我们自己的插件，启动后新功能就会出现在Plugins菜单下）

其实插件项目安装后会将项目解压到ImagePy下的`plugins`目录内，而ImagePy启动时，在解析主项目的目录后，也会解析插件项目的目录结构，这样实现了插件项目的相对独立管理。**由于顶级菜单空间稀缺，因而如果不是非常丰富，系统性很强的功能，请不要占用顶级菜单**