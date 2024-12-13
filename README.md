
# Generic Pod Renderer

	Transforms POD in a Raku module/pod to other format such as HTML or MarkDown.

----

## Table of Contents

<a href="#Installation">Installation</a>   
&nbsp;&nbsp;- <a href="#Dependencies">Dependencies</a>   
<a href="#Page_Components">Page Components</a>   
&nbsp;&nbsp;- <a href="#TOC">TOC</a>   
&nbsp;&nbsp;- <a href="#Glossary">Glossary</a>   
&nbsp;&nbsp;- <a href="#Footnotes">Footnotes</a>   
&nbsp;&nbsp;- <a href="#Links">Links</a>   
&nbsp;&nbsp;- <a href="#Meta_data">Meta data</a>   
<a href="#RakuClosureTemplates">RakuClosureTemplates</a>   
&nbsp;&nbsp;- <a href="#Testing_tool">Testing tool</a>   
<a href="#HTML_highlighting">HTML highlighting</a>   
<a href="#Tests">Tests</a>   
<a href="#Tutorial">Tutorial</a>   
<a href="#More_Information">More Information</a>   


<span class="para" id="cdca01f"></span>Intended 



&nbsp;&nbsp;• to provide a plugin replacement for the original (legacy) Pod::To::HTML and to pass all its tests.  
&nbsp;&nbsp;• to use templates for all output (legacy Pod::To::HTML hard codes HTML)  
&nbsp;&nbsp;• use the same API for outputting MarkDown and other output formats. Hence simply changing templates will generate new output  
&nbsp;&nbsp;• generate Glossary, TOC and Footnote structures for each set of Pod trees.  
&nbsp;&nbsp;• to generate HTML and Markdown with raku's --doc flag.  
&nbsp;&nbsp;• optionally to highlight Raku code at HTML generation time.  
<span class="para" id="2c3266e"></span>The Renderers, eg., Pod::To::HTML2, will chose the templating engine depending on the templates provided. So far only Template::Mustache and the new RakuClosureTemplates are handled, though other engines can be subclassed in. 


----

## Installation<div id="Installation"> </div>
<span class="para" id="421581b"></span>If highlighting is desired (see [Highlighting](Highlighting) below) from the start, an environment variable needs to be set (also see [Dependencies](Dependencies)). 


```
POD_RENDER_HIGHLIGHTER=1 zef install Raku::Pod::Render


```
<span class="para" id="d309f5e"></span>Since it is intended that `Raku::Pod::Render` can be used as a dependency for other modules, eg `raku-alt-documentation`, and it cannot be known whether all dependencies are installed, the default must be to prevent highlighting from being installed. If this is desired then, 


```
zef install Raku::Pod::Render


```
<span class="para" id="f3f3a61"></span>will install the module without checking or installing a highlighter. If, **after the default installation**, the highlighter is needed and *node & npm are installed*, then it can be installed by running the following in a terminal: 


```
raku-render-install-highlighter


```


### Dependencies<div id="Dependencies"> </div>
<span class="para" id="2e72906"></span>The default highlighter at present is a Node based toolstack called **atom-perl-highlighter**. In order to install it automatically, `Raku::Pod::Render` requires an uptodate version of npm. The builder is known not to work with `node.js` > ****v13.0> and `npm` > **v14.15**. 

<span class="para" id="3072918"></span>For someone who has not installed `node` or `npm` before, or for whom they are only needed for the highlighter, the installation is ... confusing. It would seem at the time of writing that the easiest method is: 


```
# Using Ubuntu
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
sudo apt-get install -y nodejs


```

----

## Page Components<div id="Page_Components"> </div>
<span class="para" id="327835b"></span>A Pod6 source will generate body text and generate information for TOC, Footnotes, Glossaries, Links and Metadata 



### TOC<div id="TOC"> </div>
<span class="para" id="051f61c"></span>A TOC or Table of Contents contains each Header text and a README.mdtarget within the document. For an HTML or MarkDown file, the target will be an anchor name and will react to a mouse click. 

<span class="para" id="190c3e4"></span>For a dead tree format, or soft equivalent, the pointer will be a page number. 

<span class="para" id="0555867"></span>ProcessedPod create as TOC structure that is an array in the order the headers appear, with the lable, destination anchor, return anchor and the level of the header. 



### Glossary<div id="Glossary"> </div>
<span class="para" id="4428f2a"></span>A Glossary, or Index, is a list of words or phrases that may be used or defined in multiple places within a document and which the author / editor considers would be useful to the reader when searching. Glossary structures are also useful when creating SEARCH type functions. 

<span class="para" id="25cd2ab"></span>The word 'index' is not used because in the HTML world, the file index.html is mostly used as the landing page for a collection of documents and in most cases is a Table of Contents rather than a Glossary. 

<span class="para" id="5ce9f1a"></span>In a POD file, glossary texts are created with the <span id="index-entry-"><span style="color:green; background-color: antiquewhite;"></span></span> formatting code. 

<span class="para" id="213c700"></span>ProcessedPod creates a structure as a hash of the entry names (a single <span id="index-entry-_0"><span style="color:green; background-color: antiquewhite;"></span></span> can have multiple entry names pointing to the same target), the destination anchors, the return anchor, and in the case where anchors are not possible, a location consisting of the most recent header text. 



### Footnotes<div id="Footnotes"> </div>
<span class="para" id="798c9b2"></span>When an author wishes to give more explanation to a phrase without interupting the logic of the text, the information is included in a footnote. In dead-tree formats, the footnotes tended to be at the end of a page (hence foot note). In HTML for PCs/Laptops, a popular format was to include text to be shown by hovering a mouse. For smartphone applications, hovering is not convenient, and other solutions are being found. 

<span class="para" id="d911074"></span>ProcessedPod creates an array in order of footnote creation with the number of the footnote, and target and return anchors. 



### Links<div id="Links"> </div>
<span class="para" id="a83ace2"></span>Links can be 



&nbsp;&nbsp;• internal to the document  
&nbsp;&nbsp;• external to the site (eg. on the internet)  
&nbsp;&nbsp;• local to the site  
<span class="para" id="f26d9fc"></span>Links should be tested. While the data is collected, verifying links is left to other modules. 



### Meta data<div id="Meta_data"> </div>
<span class="para" id="f98178d"></span>Pod6 allows for metadata such as AUTHOR or VERSION to be set. These can be included in HTML or other formats. 


----

## RakuClosureTemplates<div id="RakuClosureTemplates"> </div>
<span class="para" id="3ca4508"></span>A new templating system is introduced to speed up the rendering. The Pod::To::HTML2 renderer is now slightly faster than the legacy Pod::To::HTML renderer, whilst achieving considerably more. 



### Testing tool<div id="Testing_tool"> </div>
<span class="para" id="41df18b"></span>A testing tool is included that will test the array of RakuClosureTemplates, including the possibility of specifying the structure of a custom template and testing it against the template. 


----

## HTML highlighting<div id="HTML_highlighting"> </div>
<span class="para" id="823f627"></span>Raku code in HTML can be highlighted when HTML is generated. This requires the atom-perl6-highlighter developed by Samantha McVie. In the legacy Pod::To::HTML this is installed separately. This module will automatically install the highlighter unless specifically rejected by setting the POD_RENDER_NO_HIGHLIGHTER=1 environment variable. 

<span class="para" id="a222170"></span>The Build Module places the highlighter in the users directory at either (in order of preference) 



&nbsp;&nbsp;• .local/lib/raku-pod-render/highlights  
&nbsp;&nbsp;• .raku-pod-render/highlights  
<span class="para" id="e6cdf16"></span>If the highlighter already exists at one of these locations, further installations of Raku-Pod-Render will not rebuild the stack. 

<span class="para" id="675e64b"></span>This behaviour can be over-riden by 


```
RAKU_POD_RENDER_FORCE_HIGHLIGHTER_REFRESH zef install Raku::Pod::Render


```

----

## Tests<div id="Tests"> </div>
<span class="para" id="74b3023"></span>Only sanity tests are under the `t/` directory. Extensive tests are under `xt/`. 

<span class="para" id="5e8b5eb"></span>Some tests require an online connection. The tests can be run offline by setting the environment variable TEST_OFFLINE, eg. `TEST_OFFLINE=1 prove6 -I. xt/`. 


----

## Tutorial<div id="Tutorial"> </div>
<span class="para" id="9967c11"></span>A short tutorial is included to show how a new Format Code can be created. It was written for the Advent Calendar in 2022. See the `tutorial/` directory. 


----

## More Information<div id="More_Information"> </div>
<span class="para" id="43371f7"></span>See [RenderPod](RenderPod) for the generic module and [Pod2HTML](Pod2HTML) for information about the HTML specific module `Pod::To::HTML2`. `Pod::To::Markdown2`, see [MarkDown2](MarkDown2), follows `Pod::To::HTML2` mostly. 




----

## Index

<span style="background-color: antiquewhite; font-weight: 600;"></span>: <a href="#index-entry-">Glossary</a>, <a href="#index-entry-_0">Glossary</a>




----

----

Rendered from docs/docs/README.rakudoc at 14:52 UTC on 2024-12-13

Source last modified at 14:51 UTC on 2024-12-13


