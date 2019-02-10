# Simple

Simple is another important addition to Filter. The difference is that Simple does not focus on single images, but treats image sequences as a whole for 3d processing. In addition, Simple is also used to manipulate relevant attributes outside of images, such as ROI, color index table, scale and unit, or image Mark.



## Gaussian3D

```python
from imagepy.core.engine import Simple
import scipy.ndimage as ndimg

class Gaussian3D(Simple):
    title = 'Gaussian 3D Demo'
    note = ['all', 'stack3d']

    para = {'sigma':2}
    view = [(float, 'sigma', (0,30), 1,  'sigma', 'pix')]

    def run(self, ips, imgs, para = None):
        imgs[:] = ndimg.gaussian_filter(imgs, para['sigma'])
```

`stack3d` in  `note` identity is refers to the plug-in processing a continuous image stack. And `run` function can get image sequence of ` ndarray ` object by ` imgs ` ,We carries on the three-dimensional gaussian filter, the results to assign a value to ` imgs `.

Image sequence of ImagePy  include ` stack2d `, ` stack3d `. And ` stack2d ` image sequence is based on ` list` .It is easy to increase or delete ` slice `. While ` stack3d ` is a continuous ` numpy ` array, and facilitate three-dimensional filter to direvtly analyze. We can tansform them through the**`Image > Type > Trans to stack/list`** .
* the 2d gaussian Filter based on Filter can also process sequences, but the processing method is to treat them as multiple 2d images, while the 3d gaussian Filter based on Simple also convolves in the z direction.*



## SetLUT

```python
from imagepy.core.engine import Simple
import numpy as np

class SetLUT(Simple):
    title = 'Set LUT Demo'
    note = ['all']
    para = {'lut':'red'}
    view = [(list, 'lut', ['red', 'green', 'blue'], str, 'look up', 'table')]

    def run(self, ips, imgs, para = None):
        cmap = [[i==para['lut']] for i in ['red', 'green', 'blue']]
        ips.lut = (cmap*np.arange(256)).astype(np.uint8).T
```

Lookup table is a 255*3 color map that does not change the pixels, only the display of the image. It can be accessed or set via ips.lut. The range is a binary tuple, which sets the dynamic range of image pixels. For 8-bit images, it is usually 0-255, but for float types, the range setting is very meaningful. In fact, when displaying, ImagePy first carries out clip on the image according to the range and scales it to 0-255, and then applies the index table.
![14](http://idoc.imagepy.org/demoplugin/15.png)

<div align=center>SetLUT</div><br>


## Inflate ROI

```python
from imagepy.core.engine import Simple

class Inflate(Simple):
    title = 'Inflate ROI Demo'
    note = ['all', 'req_roi']
    para = {'r':5}
    view = [(int, 'r', (1,100),0, 'radius', 'pix')]

    def run(self, ips, imgs, para = None):
        ips.roi = ips.roi.buffer(para['r'])
```

` ROI ` indicate what areas is what we care about, the  ` ROI `  of ImagePy based on` Shapely ` object. We can operate on the ` ROI ` , the above is to expand the current ` ROI ` example.

![14](http://idoc.imagepy.org/demoplugin/16.png)

<div align=center>Inflate ROI</div><br>

## Set Scale And Unit

```python
from imagepy.core.engine import Simple

class Unit(Simple):
    title = 'Scale And Unit Demo'
    note = ['all']
    para = {'scale':1, 'unit':'mm'}
    view = [(float, 'scale', (1e-3,1e3), 3, 'scale', ''),
            (str, 'unit', 'scale', '')]

    def run(self, ips, imgs, para = None):
        ips.unit = (para['scale'], para['unit'])
```

It is in pixels unit to everything in the default ImagePy measurement and analysis results, but we can through `ips.unit` to access and set.

![14](http://idoc.imagepy.org/demoplugin/17.png)

<div align=center>Set Scale And Unit</div><br>

## Mark

```python
from imagepy.core.engine import Simple
from imagepy.core.mark import GeometryMark
import numpy as np

class Mark(Simple):
    title = 'Random Points Demo'
    note = ['all']

    def run(self, ips, imgs, para = None):
        pts = (np.random.rand(200)*512).reshape((100,2))
        ips.mark = GeometryMark({'type':'points', 'color':(255,0,0), 'lw':1, 'body':pts})
```

` mark ` is covering on the image, the image itself does not change. ImagePy defines a set of geometric data results for drawing ` mark `, here is a brief introduction:
![14](http://idoc.imagepy.org/demoplugin/18.png)

<div align=center>Set Random Point Mark</div><br>


**Various Mark types and usages**

**point:**

```python
{'type':'point', 'color':(r,g,b), 'lw':1, 'body':(x,y)}
```

**points:** 

```python
{'type':'points', 'color':(r,g,b), 'lw':1, 'body':[(x1,y1), (x2,y2), ...]}
```

**line:** 
```python
{'type':'line', 'color':(r,g,b), 'lw':1, 'style':'-', 'body':[(x1,y1), (x2,y2), ...]}
```

**lines:** 
```python
{'type':'lines', 'color':(r,g,b), 'lw':1, 'style':'-', 'body':[[(x1,y1), (x2,y2), ...], [...]]}
```

**polygon:** 
```python
{'type':'polygon', 'color':(r,g,b), 'fcolor':(r,g,b), 'lw':1, 'style':'o', 'body':[(x1,y1), (x2,y2), ...]}
```

**polygons:** 
```python
{'type':'polygons', 'color':(r,g,b), 'fcolor':(r,g,b), 'fill':False, 'lw':1, 'style':'o', 'body':[[(x1,y1), (x2,y2), ...], [...]]}
```

**circle:** 
```python
{'type':'circle', 'color':(r,g,b), 'fcolor':(r,g,b), 'fill':False, 'body':(x,y,r)}
```

**circles:** 
```python
{'type':'circles', 'color':(r,g,b), 'fcolor':(r,g,b), 'fill':False, 'body':[(x1,y1,r1), (x2,y2,r2)]}
```

**ellipse:**
```python
{'type':'ellipse', 'color':(r,g,b), 'fcolor':(r,g,b), 'fill':False, 'body':(x,y,l1,l2,ori)}
```

**ellipses:**
```python
{'type':'ellipses', 'color':(r,g,b), 'fcolor':(r,g,b), 'fill':False, 'body':[(x1,y1,a1,b1,ori1), (x2,y2,a2,b2,ori2), ...]}
```
**rectangle:** 
```python
{'type':'rectangle', 'color':(r,g,b), 'fcolor':(r,g,b), 'fill':True, 'body':(x,y,w,h)}
```

**rectangles:**
```python
{'type':'rectangles', 'color':(r,g,b), 'fcolor':(r,g,b), 'fill':False, 'body':[(x1,y1,w1,h1),(x2,y2,w2,h2),...]}
```

**text:**
```python
{'type':'text', 'color':(r,g,b), 'fcolor':(r,g,b), 'size':8, 'pt':True, 'body':(x,y,txt)}
```

**texts:**
```python
{'type':'texts', 'color':(r,g,b), 'fcolor':(r,g,b), 'size':8, 'pt':True, 'body':[(x1,y1,txt1),(x2,y2,txt2)]}
```

---

**layer:** 
```python
{'type':'layer', 'num':-1, 'clolor':(r,g,b), 'fcolor':(r,g,b), 'fill':False, 'body':[sequence of basic element]}
```

**layers:**
```python
{'type':'layers', 'num':-1, 'clolor':(r,g,b), 'fcolor':(r,g,b), 'fill':False, 'body':{1:layer1, 2:layer2, ...}}
```



**The above basic elements are similar in form. The meaning of each parameter is explained here:**

**type:** element type

**color:** colour

**fcolor:** Fill color (only plane elements)

**fill:** Whether to fill (only plane elements)

**lw:** Line width

**style:** Line style, with '-o' for both line and node drawn, or you can choose to draw only line or node, or neither

**size:** Text height (text only)

**body:** Geometric data, depending on the type



**Point collection**

**layer:** ` layer ` can specify ` color `, ` fcolor `, ` fill `, which is different from ` body ` .Because  ` body ` of   ` layer` deposit is the  of other basis elements, actually the above all sorts of elements.In addition to ` type `, ` body ` ,other attributes are not  necessary.If the current element is not specified, it will be taken from its ` layer `.If there is no subordinate ` layer `, or ` layer ` is not specified, the default value will be used.

**layers:** ` layers ` is a set of more advanced elements than ` layer `. ` body `  of ` layers  `  is a dictionary, whose key is a layer of int. And in the image sequence, only paint layer number corresponding ` layer `, so that we can realize  each image of the image sequence a corresponding ` mark `.


## Simple operating mechanism

**note:** 

` note ` option is behavior control indicators, which is used to control the plug-in implementation process.Such as allowing framework compatible with the types of tests,if does not meet ,it can be automatical suspensed. ` note ` option include set channel and sequence support settings, and whether to provide preview, ROI and other support.

1. `all`：Plug-ins support any type

2. `8-bit`：The plug-in supports unsigned 8 bits

3. `16-bit`：The plug-in supports unsigned 16 bits

4. `int`：The plug-in supports 32-bit, 64-bit integers

5. `rgb`：Plug-ins support 3 channels 24 - bit color

6. `float`：The plug-in supports 32-bit, 64-bit floating point

   ---

7. `req_roi`：Whether there must be ROI to handle

8. `stack`：The requirement must be a sequence of images

9. `stack2d`：It has to be a list of discrete images(list)

10. `stack3d`：It has to be a continuous sequence of images.(ndarray)

11. `preview`：Whether to display preview option

**para, view:** 

Parameter dictionary, see Start for details.

**run:** 

1. ` ips ` : image wrapper class, we can operate through ` ips ` ,` ROI `, ` mark `, ` lut `, ` range `, ` unit `, etc
2. ` imgs ` : image sequence, carrying on the operation, such as three-dimensional filter, or analysis, can show the results in table form.

**load:** 

` def load (self, ips) ` are executed first, if ` return ` results for ` False `, plug-in will suspend execution. The default return ` True `,.If necessary,  it can be overloaded and design a series of condition inspection.If not meet, ` IPy. Alert ` pop-up prompts, and returns the ` False `.

**preview:**

` def preview (self, ips, para) ` Simple defines the ` preview ` option , but the default is to do nothing.Because it often requires a long operation time for the three dimensional filtering operation, therefore it is not suitable for  ` preview ` .It can be overloaded, if necessary.