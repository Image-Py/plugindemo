# Widget 插件

Widget插件加载为一块面板，其实是继承与`wx.Panel`的一个ui对象，这给了我们非常大的自由空间，但代价是，我们无法隔绝`UI`，必须直接编写`wxpython`代码。例如右侧的导航栏，鹰眼就是`widget`扩展出的，而之前见到过的宏录制器同样也是`widget`。



## 桌面组件演示

```python
from imagepy import IPy
import wx

class Plugin ( wx.Panel ):
    title = 'Widget Demo'
    def __init__( self, parent ):
        wx.Panel.__init__ ( self, parent)
        sizer = wx.BoxSizer( wx.VERTICAL )
        self.lable = wx.StaticText( self, wx.ID_ANY, 'This is a widgets demo')
        self.lable.Wrap( -1 )
        sizer.Add( self.lable, 0, wx.ALIGN_CENTER|wx.ALL, 5 )
        self.btn_invert = wx.Button( self, wx.ID_ANY, 'Invert curent image')
        sizer.Add( self.btn_invert, 0, wx.ALIGN_CENTER|wx.ALL, 5 )
        
        self.SetSizer( sizer )
        self.Layout()
        self.Fit()
        # Connect Events
        self.btn_invert.Bind( wx.EVT_BUTTON, self.on_invert)

    def on_invert(self, event):
        ips = IPy.get_ips()
        if ips is None: return
        ips.img[:] = 255-ips.img
        ips.update()
```

由于`wxpython`的内容本身比较多，如有必要自己编写`widget`，那么免不了对`wxpython`进行系统性学习，这里仅仅给出一个最简单的例子。

![14](http://idoc.imagepy.org/demoplugin/27.png)

<div align=center>widget</div><br>

**widget的加载方式**

1. 文件必须以`_wgt.py`结尾，类名必须叫`Plugin`，继承于`wx.Panel`（一个文件只能实现一个widget）
2. 文件可以位于`widgets`目录下的一级子菜单内，启动时会按照层级加载到右侧组件栏。也可以位于menus或其子菜单下，当用户点击菜单时以浮动窗口形式加载面板。

![14](http://idoc.imagepy.org/demoplugin/26.png)

<div align=center>Widget Demo</div><br>

## widget 的运行机制

widgets本质上就是一个`wx.Panel`的子类，必须有`title`字段作为插件名称，其他的，就是`wxpython`编程，通过编写`UI`，添加事件实现，这里不做详细讨论。

