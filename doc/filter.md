# Filter Plug-in

Filter is the most important class of plug-ins, which is used to filter two-dimensional images. And it is also the most basic image processing, the most common applications.



## Invert

```python
from imagepy.core.engine import Filter

class Invert(Filter):
    title = 'Invert Demo'
    note = ['all', 'auto_msk', 'auto_snap']

    def run(self, ips, snap, img, para = None): 
        return 255-snap
```

Invert plug-in。 The ` note ` indicates the plugin supports any type as well as  ` roi ` and the undo operation. We return  processing results to the ` run `. About ` snap ` and ` img `, the ` img ` is the current image. when ` note ` is added to   ` auto_snap ` logo,  the framework will help us to copy ` img `  to ` snap `  before ` run `. Because most of the filter need a ` buffer ` for convolution, moreover the undo operation and ` ROI ` support must also need the ` snap `.

![14](http://idoc.imagepy.org/demoplugin/13.png)

<div align=center>Invert</div><br>



## Gaussian

```python
from imagepy.core.engine import Filter
from scipy.ndimage import gaussian_filter

class Gaussian(Filter):
    title = 'Gaussian Demo'
    note = ['all', 'auto_msk', 'auto_snap','preview']
    para = {'sigma':2}
    view = [(float, 'sigma', (0,30), 1,  'sigma', 'pix')]
    
    def run(self, ips, snap, img, para = None):
        gaussian_filter(snap, para['sigma'], output=img)
```

Gaussian plug-in. The ` note ` specifies any type support, and supports the ` roi ` as well as cancellation, which provides the preview function. The ` para ` and ` view ` indicate there is a floating point parameters ` sigma `. In ` run `, we can call ` scipy. ndimage. gaussian_filter `  to filter ` snap ` with the output to ` img `. If a function without output item, we will process the results ` return `, and framework will help us to assign a value to ` img `.

![14](http://idoc.imagepy.org/demoplugin/14.png)

<div align=center>Gaussian</div><br>



## Filter operation mechanism

**note:** 

The note option is a behavior control identifier that controls the flow that the plug-in performs. For example, the framework performs type compatibility checks, and is automatic abortsed if they are not met. Set channel and sequence support settings, and whether to provide preview, roi and other support.

1. `all`：The plug-in supports any type

2. `8-bit`：The plug-in supports unsigned 8 bits

3. `16-bit`：The plug-in supports unsigned 16 bits

4. `int`：The plug-in supports 32-bit, 64-bit integers

5. `rgb`：The plug-in supports 3 channels, 24 - bit color

6. `float`：The plug-in supports 32-bit, 64-bit floating point

   ------

7. `not_channel`：When working with multiple channels, we set to not allow the framework to automatically traverse channels (Each channel is processed in turn by default)

8. `not_slice`：When processing image sequences, we set to not ask if you want to batch them (Users are asked by default)

9. `req_roi`：Whether there must be roi to handle

   ---

10. `auto_snap`：Whether it is needed for the frame to automatically buffer the current image in the processor

11. `auto_msk`：Whether roi is automatically supported or not(It must be combined with auto_snap to take effect, and the principle is to use snap to restore pixels other than roi)

12. `preview`：Whether preview is supported to adjust parameters to see the results in real time

13. `2int`：Whether to convert data lower than int32 to int32 for further processing (For example, to avoid 8-bit operation overflow)

14. `2float`：Whether to automatically convert the data lower than float32 to float32 for further processing during processing (some operations require precision)

**para, view:** Parameter dictionary, see start for details.

**run:** 

1. ` ips ` : image wrapper class.  The ` filter ` don't need often to operate it.
2. ` snap ` : when joining the ` auto_snap ` logo  in ` note `,  framework will copy current image to ` snap ` before ` run ` ( As much as possible to use snap to deal with implementations, the results will be assigned to the img)
3. ` img ` : current image. We will  assign the result to ` img `, or we ` return ` results. The framework assign  result  to ` img `, and complete refreshing the interface.

**load:** 

` def load (self, ips) ` are executed first. If result of the ` return ` is ` False `, plug-in will suspend execution. The default returns ` True `. If necessary, it can be overloaded with a series of condition inspection.If not met, ` IPy. alert ` will pop up prompts, and will return the ` False `.

**preview:**

` def preview (self, ips, para) ` can be executed when selecting preview status. The default will call ` run ` to process current image. If necessary, it can be overloaded.