# 从这里开始

这里我们用Hello World的例子来开始，顺带介绍插件的加载机制，以及如何配置参数对话框，为后续的开发做好准备。



##  什么是插件

ImagePy是一个扩展性很强的图像处理框架，我们是通过插件来对ImagePy进行功能扩展的，插件是一段代码或一个文件，放在特定的位置，在ImagePy启动时自动加载，其具体形式可以体现为菜单，工具栏，桌面小部件。其实在ImagePy中一切功能皆插件，ImagePy原生功能并不享受任何特权，这些插件根据目录层级解析成对应UI元素，并在点击时触发相应功能，我们可以使用**`Plugins > Manager > Plugin Tree View`**来查看插件及其对应的源码。

![31](http://idoc.imagepy.org/demoplugin/31.png)
<div align=center>Plugins Tree View</div><br>



## Hello World

我们开始写第一个插件，实现一个简单的hello world.

```python
from imagepy.core.engine import Free
from imagepy import IPy

class Plugin(Free):
    title = 'Hello World'

    def run(self, para=None):
        IPy.alert('Hello World, I am ImagePy!')
```
这是一个最简单的插件，首先`import Free, IPy`. `Free`是一种插件类型，这种类型插件可以不依赖图像而运行，我们在`run`里面用`IPy.alert`弹出一个'Hello world, I am ImagePy!'的提示框。

![14](http://idoc.imagepy.org/demoplugin/01.png)
<div align=center>Hello World</div><br>

**如何加载**
我们将上面的脚本文件命名为 `hello_plg.py`，拷贝到ImagePy目录下的`Imagepy > menus > Plugins`目录下，重新启动ImagePy，点开`Plugins`菜单，就会看到我们的插件。一些加载原则如下：

1. `menus`及其子目录会被解析。
2. `plg.py`的文件会被解析。
3. 文件内的`Plugins`类会被解析为插件，`title`是菜单显示内容



## Who Are You

接下来我们为这个插件添加一些参数，邀请用户输入名字和年龄。

```python
from imagepy.core.engine import Free
from imagepy import IPy

class Plugin(Free):
    title = 'Who Are You'
    para = {'name':'', 'age':0}
    view = [(str, 'name', 'name', 'please'),
            (int, 'age', (0,120), 0, 'age', 'years old')]

    def run(self, para=None):
        IPy.alert('Name:\t%s\r\nAge:\t%d'%(para['name'], para['age']))
```

ImagePy框架实现了参数对话框生成机制，可以根据`para`，`view`来生成对应的交互，完成交互后，我们在`run`函数中可以通过`para`参数获取交互结果，我们在下一个例子中会更加详细的讲解各种类型的参数生成。

![14](http://idoc.imagepy.org/demoplugin/02.png)
<div align=center>Who Are You</div><br>



## Questionnaire

```python
from imagepy.core.engine import Free
from imagepy import IPy

class Plugin(Free):
    title = 'Questionnaire'

    para = {'name':'yxdragon', 'age':10, 'h':1.72, 'w':70, 'sport':True, 'sys':'Mac', 'lan':['C/C++', 'Python'], 'c':(255,0,0)} 

    view = [('lab', 'lab', 'This is a questionnaire'),
            (str, 'name', 'name', 'please'), 
            (int, 'age', (0,150), 0, 'age', 'years old'),
            (float, 'h', (0.3, 2.5), 2, 'height', 'm'),
            ('slide', 'w', (1, 150), 0, 'kg'),
            (bool, 'sport', 'do you like sport'),
            (list, 'sys', ['Windows','Mac','Linux'], str, 'favourite', 'system'),
            ('chos', 'lan', ['C/C++','Java','Python'], 'lanuage you like(multi)'),
            ('color', 'c', 'which', 'you like')]

    def run(self, para=None):
        rst = ['Questionnaire Result', 
            'Name:%s'%para['name'], 
            'Age:%s'%para['age'],
            'Height:%sm'%para['h'], 
            'Weight:%skg'%para['w'], 
            'Like Sport:%s'%para['sport'],
            'Favourite System:%s'%para['sys'],
            'Like lanuage:%s'%para['lan'],
            'Favourite Color:%s'%str(para['c'])]

        IPy.alert('\r\n'.join(rst))
```
这里我们实现一个调查问卷，让用户输入各类信息，同时也是为了向开发者展示，如何通过`para`，`view`来说配置出任何自己想要的参数对话框。


![14](http://idoc.imagepy.org/demoplugin/03.png)
<div align=center>Questionnaire</div><br>

**label:** para类型：`不需要参数`, view用法：`('lab', 'lab', 'what you want to show')`

**str:** para类型：`str`, view用法：`(str, key, prefix, suffix)`，其中`key`要和`para`中的`key`对应，`prefix`，`suffix`用作输入框前后的提示内容。

**int:** para类型：`int`，view用法：`(int, key, (lim1, lim2), accu, 'prefix', 'suffix')`，其中`key`要和`para`中的`key`对应，`limit`用于限定输入数值的范围，`accu`限定小数点位数(0)，`prefix`，`suffix`用作输入框前后的提示内容。

**float:** para类型：`float`，view用法：`(int, key, (lim1, lim2), accu, 'prefix', 'suffix')`，其中`key`要和`para`中的`key`对应，`limit`用于限定输入数值的范围，`accu`限定小数点位数，`prefix`，`suffix`用作输入框前后的提示内容。

**slider:** para类型：`int/float`，view用法：`('slide', key, (lim1, lim2), accu, 'prefix')`，其中`key`要和`para`中的`key`对应，`limit`用于限定输入数值的范围，`accu`限定小数点位数，`prefix`用作输入框前后的提示内容。

**bool:** para类型：`bool`，view用法：`(bool, 'key', 'label')`，其中`key`要和`para`中的`key`对应，`label`用作提示。

**list:** para类型：`any type`，view用法：`(list, key, [choices], type, prefix, suffix)`，其中`key`要和`para`中的`key`对应，`choices`是字符选项，`type`是期望输出类型，如`str`, `int`，`prefix`，`suffix`用作选择框前后的提示内容。

**choices:** para类型：`str list`，view用法：`('chos', key, [choices], prefix, suffix)`，与`list`类似，不同的是`choices`可以支持多选，选项以`list of string`形式记录。

**color:** para类型：`(r,g,b) 0-255`，用法：`('color', key, prefix, suffix)`，其中`key`要和`para`中的`key`对应，`prefix`，`suffix`用作输入框前后的提示内容。



*除以上基础数据类型之外，ImagePy还支持一些内部类型的参数，如接收一副图像，接收一个表格，或者对表格的字段进行单选或多选，这些我们在后续的示例中会有所展示*



## 一个文件内实现多个插件
以上我们分别实现了三个插件，而Python具有语法精简的优势，所以我们也可以在一个文件内实现多个插件，方法如下。

```python
from imagepy.core.engine import Free
from imagepy import IPy

class HelloWorld(Free):
    title = 'Hello World'
    ...
    
class WhoAreYou(Free):
    title = 'Who Are You'
    ...
    
class Questionnaire(Free):
    title = 'Questionnaire'
    ...
    
plgs = [HelloWorld, WhoAreYou, Questionnaire]
```

我们将三个类写在一个文件内，最后加上`plgs = []`，文件命名为`start_plgs.py`。框架加载原则如下：

1. `menus`目录或子目录下的`plgs.py`结尾的文件会被当作多插件解析
2. 插件内的`plgs`列表会被依次解析
3. `plgs`内可以加入`'-'`，会被解析为菜单分隔符

![14](http://idoc.imagepy.org/demoplugin/04.png)

<div align=center>parse as menus</div><br>

