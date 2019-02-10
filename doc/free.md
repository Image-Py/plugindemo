# Free plugin

Free is a plugin that can run independently without any dependencies. We can do anything in it, such as creating images, downloading online resources, and so on.


## Create image
```python
from imagepy.core.engine import Free
from imagepy import IPy
import numpy as np

class NewImage(Free):
    title = 'New Image Demo'
    para = {'name':'new image','w':300, 'h':300}
    view = [(str, 'name', 'name',''),
            (int, 'w', (1,2048), 0,  'width', 'pix'),
            (int, 'h', (1,2048), 0,  'height', 'pix')]

    def run(self, para = None):
        imgs = [np.zeros((para['h'], para['w']), dtype=np.uint8)]
        IPy.show_img(imgs, para['name'])
```
Here we demonstrate this type of plugin through an example (create a new image). Similarly, we can interact with parameters through `para` and `view`.

![14](http://idoc.imagepy.org/demoplugin/22.png)

<div align=center>New Image</div><br>


## About dialog-box

```python
from imagepy.core.engine import Free
from imagepy import IPy

class About(Free):
    title = 'About Demo'

    def run(self, para=None):
        IPy.alert('ImagePy v0.2')
```
It has been introduced in [Hello World](doc/start.md), and it is used as a dialog-box.

![14](http://idoc.imagepy.org/demoplugin/23.png)

<div align=center>ImagePy Alert</div><br>


## Quit
```python
from imagepy.core.engine import Free
from imagepy import IPy

class Close(Free):
    title = 'Exit Program Demo'
    asyn = False

    def run(self, para = None):
        IPy.curapp.Close()
```
The **quit** is a typical application in the `Free` type plugin. It is worth noticing that if `asyn = False` is added in the plugin, this sign will tell imagepy not to asynchronous execute `run`. Because 'window close' is a `UI` operation and must be done in the main thread.



## Operating mechanism of Free
`Free` is the easiest plugin to run compared to other ones, because there is no need for preparation process for  `Free`. `run` only has one parameter `para`, obtained through interaction, which is completely open to the developer.


**para, view:** 

Parameter dictionary, for specific usage, see [start](doc/start.md).

**run:** 

Get interactive results and do whatever you want.

**load:** 

`def load(self, ips)` is executed at first，if the `False` is returned with `return` , the plugin will be ended. Default return is `True`, if neccesary, it can be overloaded for a series of condition checks. If it is not satisfied, the `IPy.alert` will popup prompt, and return`False` 


