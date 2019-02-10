#  Report Plugin

In many cases, we end up with the results of our analysis in report form, generating PDF documents, or printing them. Although now LaTex, Markdown  based on the tag language document tools are very powerful, Excel software still has the widest audience,the quickliest start to fit task , the strongest form layout ability.Moreover Excel has powerful data processing, graph generating function.Thus ImagePy selects Excel as reporting features carrier choice, the user can design their Excel template, and add tags in a particular cell and save, change the suffix to RPT, you can get the report plugin of ImagePy .



**How report plugins are loaded**

1. The RPT suffix file under the menus and its subfolders is resolved into a report template.
2. You can also drag and drop the template file into the bottom status bar of ImagePy to execute.


## Personal information

Use a personal information card to show how to make templates and tag cells. In order to facilitate printing, we need to set the paper size as A5 according to our own situation. Second, for precise layout, we switched the Excel view to the page layout, so that all cell sizes are set in cm.

---

![06](http://idoc.imagepy.org/demoplugin/33.png)

<div align=center>personal information template</div><br>

---

![06](http://idoc.imagepy.org/demoplugin/34.png)

<div align=center>personal information result</div><br>

## Coin segmentation

We continue to use the example of coin segmentation and measurement to produce reports of the analysis results. Here, we carefully designed an experiment report, including basic information, processing pictures and result tables. We also made statistics on the results in Excel, and drew a histogram for the area column.

![06](http://idoc.imagepy.org/demoplugin/37.png)

<div align=center>do coins segment in ImagePy</div><br>

![06](http://idoc.imagepy.org/demoplugin/38.png)

<div align=center>generate coins report</div><br>

## Report template design principles

When executing report plug-in, ImagePy will firstly analyze Excel template, analyze each Work Sheet, detect each cell, extract all variable markers, and interact with users in the form of dialog box. After confirmation, all information will be backfilled and saved. It is very convenient to use, but we must follow certain principles to design the template, which is described in detail here.



*In order to facilitate printing, we need to set the paper size according to our own situation, such as A4, A5. Second, for precise layout, we switched the Excel view to the page layout, so that all cell sizes are set in cm.*



**Universal syntax**

`{type Var_Name = Default Value # note}`， Variable tags of any type follow this format, with curly braces for variable identifiers and other items. Some items are required and some are optional.

**Underlying parameter**

`int,float,str,txt,bool`：these basic parameters, syntax format consistent take STR as an example, `{str Name = YX Dragon # please input your name here}`，where type and variable name are required, default value and comment are optional. The difference between `str` and `txt` is that in the ImagePy interactive dialog, `txt` can receive multiple lines of text.

**Selection parameter**

`list`： usage example`{list Favourite_System = [Windows, Linux, Mac] # please select your favourite system}`，for the list, the default value must be provided, the options are separated by commas, the Spaces are ignored, the comments are optional.

**Image parameter**

`img`：usage example `{img My_Photo = [4.8,4.8,0.9,0] # you photo here`，for img type, the default value must be provided, it is an brackets, inside four numbers, respectively represents: length (cm), height (cm), white space ratio (0.9 represents 10% around), whether to stretch (0 represents holding ratio, 1 can be stretched), annotation is optional.

**Table parameter**

`tab`：usage example `{tab Record = [1,3,0,0] # the result table}`，for tab type, a default value must be provided in a bracket, incleded four numbers respectively: spacing (1 represents not merged cells), column spacing (1 represents not merged cells), the header row relative position (1 represents the data area on a line, 0 represents don't fill the title), indexed column line of position (-1 represents a left column of the data area ,0 represents no filling index), the annotation is optional.