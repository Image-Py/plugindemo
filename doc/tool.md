# Tool 插件

Tool插件用来完成鼠标交互，启动时被加载为工具栏上的图标。典型的工具插件是，roi编辑，画笔，测量工具等。



## <span id = "画笔工具">画笔工具</span>

```python
from imagepy.core.engine import Tool
from skimage.draw import line, circle

def drawline(img, oldp, newp, w, value):
    if img.ndim == 2: value = sum(value)/3
    oy, ox = line(*[int(round(i)) for i in oldp+newp])
    cy, cx = circle(0, 0, w/2+1e-6)
    ys = (oy.reshape((-1,1))+cy).clip(0, img.shape[0]-1)
    xs = (ox.reshape((-1,1))+cx).clip(0, img.shape[1]-1)
    img[ys.ravel(), xs.ravel()] = value

class Plugin(Tool):
    title = 'Pencil'
    
    para = {'width':1, 'value':(255, 255, 255)}
    view = [(int, 'width', (0,30), 0,  'width', 'pix'),
            ('color', 'value', 'color', '')]
    
    def __init__(self):
        self.status = False
        self.oldp = (0,0)
        
    def mouse_down(self, ips, x, y, btn, **key):
        self.status = True
        self.oldp = (y, x)
        ips.snapshot()
    
    def mouse_up(self, ips, x, y, btn, **key):
        self.status = False
    
    def mouse_move(self, ips, x, y, btn, **key):
        if not self.status:return
        w, value = self.para['width'], self.para['value']
        drawline(ips.img, self.oldp, (y, x), w, value)
        self.oldp = (y, x)
        ips.update()
        
    def mouse_wheel(self, ips, x, y, d, **key):pass
```

通过重载`mouse_down`, `mouse_up`, `mouse_move`方法，可以实现鼠标交互，这里我们实现一个最常见的画笔工具。鼠标按下时，标记`status`为`True`，在鼠标移动过程中进行绘图，并更新显示。

![14](http://idoc.imagepy.org/demoplugin/24.png)

<div align=center>画笔工具</div><br>


**Tool的加载方式**

1. 文件必须以`_tol.py`结尾，类名必须叫`Plugin`（一个文件只能实现一个工具）
2. 必须位于`tools`目录下的一级子菜单下
3. 需要配有一个同名的，16*16的gif文件用于工具栏图标
4. 单击即可选中，并作用与画布，如有配置参数，双击可进行设置


![14](http://idoc.imagepy.org/demoplugin/25.png)

<div align=center>Tool的加载</div><br>

## Tool 的运行机制

**title:** 标题

**mouse_down:** `mouse_down(self, ips, x, y, btn, **key):` 鼠标按下时触发，`ips`是当前作用图像的`ImagePlus`封装类，可以通过`ips.img`获取当前图像，也可以`ips.lut`, `ips.roi`, `ips.unit`获取图像的索引表，roi，比例尺和单位等附加信息。x, y是当前鼠标在数据坐标系下的位置，`btn`触发事件的鼠标按键，0:无，1:左键，2:中键，3:右键。可以通过`key['alt']`, `key['ctrl']``, key['shift']`获取相应功能键是否按下，通过`key['canvas']`获取触发事件的`Canvas`对象。

**mouse_up:** `mouse_up(self, ips, x, y, btn, **key):` 鼠标抬起时触发，具体参数与`mouse_down`相同。

**mouse_move:** `mouse_move(self, ips, x, y, btn, **key):` 鼠标移动时触发，具体参数与`mouse_down`相同。

**mouse_wheel(self, ips, x, y, btn, ******key):** 鼠标滚轮滚动时触发，具体参数与`mouse_down`相同。

