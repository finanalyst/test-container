
# NO_TITLE

----

## Table of Contents

<a href="#Usage_with_compiler">Usage with compiler</a>   
<a href="#Table">Table</a>   
<a href="#Usage_from_a_program">Usage from a program</a>   
<a href="#More_detail_and_differences_from_Pod::To::HTML2">More detail and differences from Pod::To::HTML2</a>   



----

## Usage with compiler<div id="Usage_with_compiler"> </div>
<span class="para" id="e7a7807"></span>From the terminal: 


```
raku --doc=MarkDown2 input.raku > README.md

```
<span class="para" id="7d39c95"></span>This takes the POD in the `input.raku` file, transforms it into MarkDown. This module uses the Mustache templating engine. 

<span class="para" id="77ab369"></span>Some rendering options can be passed via the PODRENDER Environment variable. The options can be used to turn off components of the page. 


```
PODRENDER='NoTOC NoFoot' raku --doc=MarkDown input.raku > README.md

```
<span class="para" id="2194203"></span>The following regexen are applied to PODRENDER and switch off the default rendering of the respective section: 

----

## Table<div id="Table"> </div>
| **regex applied** | **if Match, then Turns off** |
| :----:  | :----: |
| /:i 'no' '-'? 'toc' / | Table of Contents |
 | /:i 'no' '-'? 'meta' / | Meta information (eg AUTHOR) |
 | /:i 'no' '-'? 'footnotes' / | Footnotes. |

<span class="para" id="5ffc409"></span>Any or all of 'NoTOC', 'NoMeta', or 'NoFoot' may be included in any order. Default is to include each section. 


----

## Usage from a program<div id="Usage_from_a_program"> </div>
<span class="para" id="5885b09"></span>The class can be used from a program, such as [raku-pod-extraction](https://github.com/finanalyst/raku-pod-extraction). 


----

## More detail and differences from Pod::To::HTML2<div id="More_detail_and_differences_from_Pod::To::HTML2"> </div>
<span class="para" id="6dd7ba1"></span>See [RenderPod](RenderPod) [PodToHTML2](PodToHTML2) for more detail. `Pod::To::MarkDown2` has templates to produce MarkDown and not HTML. In addition: 



&nbsp;&nbsp;• <span class="para" id="5e68e90"></span>A boolean `github-badge` (default: False) and an associated string `badge-path` (default: `'/actions/workflows/test.yaml/badge.svg'`) are provided. These will generate a badge at the start of a Pod6 file converted to Markdown, such as README.md, that will show the github badge. 

  
&nbsp;&nbsp;• The target rewrite function needs to be over-ridden.  
&nbsp;&nbsp;• MarkDown2 is not intended for internal links. So there is no glossary and META data is treated as paragraphs.  
&nbsp;&nbsp;• Footnotes have to be rendered at the end of the document, and there is no backward link from the footnote to the originating text.  
&nbsp;&nbsp;• <Pod::To::MarkDown2> uses the Mustache template system, not the Raku Closure Templates.  
&nbsp;&nbsp;• <span class="para" id="739aa5e"></span>If a template file called **md-templates.raku** is contained in the Current Working Directory, and that file has the same format as the default templates, then it will over-ride the default templates. See [RenderPod](RenderPod) for more detail. 

  
&nbsp;&nbsp;• <span class="para" id="2645765"></span>`Pod::To::MarkDown2` currently has no plugins 

  




----

----

Rendered from docs/docs/MarkDown2.rakudoc at 14:52 UTC on 2024-12-13

Source last modified at 14:51 UTC on 2024-12-13


