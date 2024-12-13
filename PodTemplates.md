
# Templates needed for a Processed Pod.

----

## Table of Contents

<a href="#Template_Engine_Autodetection">Template Engine Autodetection</a>   
<a href="#Minimum_Set">Minimum Set</a>   
<a href="#Test-Templates">Test-Templates</a>   
<a href="#Verification_utility">Verification utility</a>   
&nbsp;&nbsp;- <a href="#Usage">Usage</a>   
&nbsp;&nbsp;- <a href="#Data_structures_accepted_by_templates">Data structures accepted by templates</a>   
<a href="#Table">Table</a>   
&nbsp;&nbsp;- <a href="#Structure_specification_syntax">Structure specification syntax</a>   
<a href="#Examples">Examples</a>   
<a href="#Parameters_of_required_templates">Parameters of required templates</a>   
<a href="#Required_templates_and_their_normal_parameters">Required templates and their normal parameters</a>   
&nbsp;&nbsp;- <a href="#Notes">Notes</a>   
&nbsp;&nbsp;- <a href="#Custom_block_parameters">Custom block parameters</a>   
<a href="#Custom_Templates,_MyBlock_and_formatcode-F">Custom Templates, MyBlock and formatcode-F</a>   
&nbsp;&nbsp;&nbsp;&nbsp;- <a href="#Notes_0">Notes</a>   
&nbsp;&nbsp;- <a href="#HTML2">HTML2</a>   
<a href="#Helper_templates_in_Pod::To::HTML2">Helper templates in Pod::To::HTML2</a>   
&nbsp;&nbsp;&nbsp;&nbsp;- <a href="#Notes_0">Notes</a>   


<span class="para" id="ab6753c"></span>This describes the template set for the ProcessedPod renderer, the templating engines that can be used, the Test-Template functions, and the test-templates verification utility. 

<span class="para" id="8e12e21"></span>The templating part of Raku-Pod-Render is separated into a Templating module `RenderPod::Templating`. It is written to allow for other templating engines to be added. Currently the following Engines are available in this distribution: 



&nbsp;&nbsp;• Mustache  
&nbsp;&nbsp;• Raku Closure (a templating system designed for Raku-Pod-Render)  
&nbsp;&nbsp;• Cro Templates  

----

## Template Engine Autodetection<div id="Template_Engine_Autodetection"> </div>
<span class="para" id="9c98e7c"></span>Templates are written to be interpretted by a templating engine. The Templating module in this distribution has been written to allow for other engines to be added. It is best to look at the module to see how this is done. 

<span class="para" id="6e53676"></span>The first minimum set of templates is loaded and an autodetect function determines which engine to use. Subsequent templates added by plugins must use the same template engine. 

<span class="para" id="ad78539"></span>The template engine is determined in two ways: 



&nbsp;&nbsp;• <span class="para" id="9acbfd0"></span>The hash (normally loaded from a file) containing the minimum set of templates may contain the key `_templater`, which must have a String value that is the name of the class implementing the Templating Engine. For the default engines, these are 

  

<span style="font-weight: 600;">Mustache</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">MustacheTemplater </span>

<span style="font-weight: 600;">Raku Closure</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">RakuClosureTemplater </span>

<span style="font-weight: 600;">Cro Templates</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">CroTemplater </span>


----

## Minimum Set<div id="Minimum_Set"> </div>
<span class="para" id="0b94647"></span>The minimum set of templates is `block-code comment declarator defn dlist-end dlist-start escaped footnotes format-b format-c
format-i format-k format-l format-n format-p format-r format-t format-u format-x glossary heading
item list meta unknown-name output para pod raw source-wrap table toc`. 

<span class="para" id="1ce8609"></span>Almost all of these templates expect a [parameter to be rendered](Parameters of required templates). The test functions will check the templates for the parameters to be returned. 


----

## Test-Templates<div id="Test-Templates"> </div>
<span class="para" id="507613c"></span>These subs are intended for use in a TAP context. They can be applied as follows 


```
use Test;
use RenderPod::Test-Templates;
%templates = EVALFILE "templates/basic-templates.raku";
templates-present %templates, 'basic file contains minimum set of templates';


```

<span style="font-weight: 600;">multi sub templates-present( Hash %templates, Str $description = 'minimum templates present' )</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">Short for 'are all minimum templates present in the hash'. Takes a hash, whose keys are template names. Checks the key-names contains all of the required templates. </span>

<span style="font-weight: 600;">multi sub templates-match( Hash %templates, Str $description = 'minimum templates match the specification' )</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">Checks whether the required templates render all parameters. Fails if any parameters are not rendered. If a parameter should not appear, render it as a comment or invisible element, so that it is in the output for it to match the specification, but not be seen when finally rendered. If there are more templates in the hash than are in the specifications, they are ignored. </span>

<span style="font-weight: 600;">multi sub extra-templates-match( Hash %templates, Hash %specifications, Str $description = 'extra templates match')</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">Check that templates in %templates match the specifications in %specifications. True if the templates match, AND all the templates in %templates are specified in %specifications. </span>


----

## Verification utility<div id="Verification_utility"> </div>
<span class="para" id="5fff4d4"></span>The `test-templates` utility helps to verify whether all the parameters specified for a template are in fact rendered by the template. 

<span class="para" id="ec22cc0"></span>A template developer may decide not to render a parameter provided by ProcessedPod to the template. The verification tool will flag this with a warning. 

<span class="para" id="b78c89c"></span>If the minimum set of templates is not provided, the renderer will throw an error. 



### Usage<div id="Usage"> </div>
<span class="para" id="a21fad0"></span>This testing tool has the structure of the minimum template set coded into it. It is called as 


```
test-templates --extra='filename-of-custom-template-structure.raku' --verbosity=2 'filename-of-templates.raku'


```


&nbsp;&nbsp;• <span class="para" id="f24ec42"></span>**--extra (optional, default: `Any` )** 

<span class="para" id="1795d73"></span>This is a **Raku** compilable program that returns a `Hash` with the structure of the key, more below. 

  
&nbsp;&nbsp;&nbsp;&nbsp;▹ = 0 returns 0 if no errors, or list templates with errors  
&nbsp;&nbsp;&nbsp;&nbsp;▹ = 1 0 + parameters sent, sub-keys not returned  
&nbsp;&nbsp;&nbsp;&nbsp;▹ = 2 1 + sub-keys found in error templates  
&nbsp;&nbsp;&nbsp;&nbsp;▹ = 3 2 + full response for error templates  
&nbsp;&nbsp;&nbsp;&nbsp;▹ = 4 all templates with sub-key returns  
&nbsp;&nbsp;&nbsp;&nbsp;▹ = 5 5 + full response for all templates  
&nbsp;&nbsp;• <span class="para" id="b474a2e"></span>**--verbosity (optional, default: 0 )** 

<span class="para" id="8ddb206"></span>An integer that produces more information for higher values: 

  
&nbsp;&nbsp;• <span class="para" id="3c80720"></span>**'filename-of-templates.raku' (mandatory, string)** 

<span class="para" id="357697e"></span>A **Raku** compilable program that returns a `Hash` contains all the minimum templates as keys pointing subroutines. 

  
<span class="para" id="af151cb"></span>If the minimum set of templates is not provided in **filename-of-templates.raku**, a `MissingTemplates` exception will be thrown. 

<span class="para" id="e686dea"></span>Since all the templates are provided to all the templates, it is recommended to use sub-templates within the minimum set to simplify the code. 



### Data structures accepted by templates<div id="Data_structures_accepted_by_templates"> </div>
<span class="para" id="e5b4220"></span>All templates, whether Mustache or RakuClosure, expect PodRender to present them with parameters with one of the following data structures 

----

## Table<div id="Table"> </div>
| **level** | **allowable Type** | **element Type** |
| :----:  | :----:  | :----: |
|  top  | Str  | n/a |
 |  top  | Hash  | Str |
 |       |                | Bool |
 |       |                | Hash |
 |       |                | Array |
 | ! top | Str  | n/a |
 | ! top | Bool  | n/a |
 | ! top | Hash  | Str |
 |       |                | Bool |
 |       |                | Hash |
 |       |                | Array |
 | ! top | Array  | Str |
 | ! top | Array  | Hash |

<span class="para" id="eb7c381"></span>This specification allows for `test-templates` to create create random data for template and to test whether it is returned. 

<span class="para" id="1aa9092"></span>However, the developer has to provide information about what each template is expecting. 



### Structure specification syntax<div id="Structure_specification_syntax"> </div>
<span class="para" id="990e32b"></span>As can be seen from the table in [Data structures accepted by templates](Data structures accepted by templates), a parameter value, whether directly expected from a key, or as part of an Array or Hash, may be either 'Bool' or 'Str'. These are given as `Str` values, eg. 


```
'title' => 'Str',
'heading' => { :text('Str'), :level('Str'), :is-header('Bool') }


```
<span class="para" id="9c71ca7"></span>A `Hash` element is specified as a `Hash`, using ` { ... } ` as shown above, and an `Array` is specified using ` [ ... ] `, eg. 


```
'table' => { :rows( [ 'Str', ] ) }


```

----

## Examples<div id="Examples"> </div>
<span class="para" id="759f794"></span>Examples of a set of RakuClosureTemplates can be found in the `resources` directory of the distribution, as 



&nbsp;&nbsp;• <span class="para" id="8b9c9ab"></span>`closure-temp.raku` is an array of templates, including a custom `image` template. The templates are designed for `Pod::To::HTML2`. 

  
&nbsp;&nbsp;• <span class="para" id="051304f"></span>`extra-test.raku` is a hash with the template structure for the `image` template. 

  

----

## Parameters of required templates<div id="Parameters_of_required_templates"> </div>
<span class="para" id="bf1e7e9"></span>Each block has a default template and provides parameters to the default template. It is possible to over-ride the default template of a block by associating it with `:template<xxxx>` metadata. In this case, the same parameters are provided to the over-ridden template. 

<span class="para" id="c8b4e93"></span>It is possible to provide a custom block to ProcessedPod, the parameters are shown below. 

<span class="para" id="41870d0"></span>HTML2 has some extra templates, shown below. 

----

## Required templates and their normal parameters<div id="Required_templates_and_their_normal_parameters"> </div>
| **Key** | **Parameter** | **Sub-param** | **Type** | **Description** |
| :----:  | :----:  | :----:  | :----:  | :----: |
| escaped |            |            |          | Should be a special case  |
 |            | contents |            | String | String  |
 | raw |            |            |          | Should be a special case  |
 |            | contents  |            | String | normally should return the contents unchanged  |
 | block-code |            |            |          | template for code  |
 |            | contents  |            | String | A code body  |
 | comment |            |            |          |                 |
 |            | contents  |            | String | will be made into a comment  |
 | declarator |            |            |          | renders documentation on a sub or variable  |
 |            | target  |            | String | 'target' is used in the glossary to point to the content, like a TOC for a header  |
 |            | code |            | String | the line of code that is being documented  |
 |            | contents |            | String | the documentation  |
 | dlist-start |            |            | String | the tag or code that starts a declaration list |
 | defn |            |            |          | renders and element of a definition list  |
 |            | term |            | String | the term part of a definition  |
 |            | contents  |            | String | the definition body  |
 | dlist-end |            |            | String | the end tag of a definition list |
 | format-b |            |            |          | bold  |
 |            | contents |            | String |                 |
 | format-c |            |            |          | inline code  |
 |            | contents  |            | String |                 |
 | format-i |            |            |          | italic  |
 |            | contents |            | String |                 |
 | format-k |            |            |          | keyboard  |
 |            | contents  |            | String |                 |
 | format-r |            |            |          | replace  |
 |            | contents |            | String |                 |
 | format-t |            |            |          | terminal  |
 |            | contents  |            | String |                 |
 | format-u |            |            |          | underline  |
 |            | contents |            | String |                 |
 | para |            |            |          | The template for a normal paragraph  |
 |            | contents  |            | String | text in para block  |
 | format-l |            |            |          | renders a link to somewhere else  |
 |            | internal |            | Boolean | true if target is within document |
 |            | external |            | Boolean | true if target is not in local area, eg., an internet url |
 |            | target |            | String | The url of the link  |
 |            | local |            | String | url is local to system (perhaps with implied file extension)  |
 |            | contents |            | String | The text associated with the link, which should be read (may be empty)  |
 | format-n |            |            |          | render the footnote for the text  |
 |            | retTarget  |            | String | The anchor name the footnote will target  |
 |            | fnTarget |            | String | The target for the footnote text  |
 |            | fnNumber |            | String | The footnote number as allocated by the renderer  |
 | format-p |            |            |          | Renders arbitrary text at some url.  |
 |            | contents  |            | String | The text at the link indicated by P  |
 |            | html |            | Boolean | if True, then contents is in HTML format |
 | format-x |            |            |          |                 |
 |            | target |            | String | Anchor name the glossary item will target  |
 |            | text |            | String | The text to be included (the text to be included in the glossary is in the glossary structure)  |
 |            | header |            | Boolean | True if the glossary item is also a header |
 | heading |            |            |          | Renders a heading in the text  |
 |            | level  |            | String | The level of the heading  |
 |            | target |            | String | The anchor which TOC will target  |
 |            | top |            | String | Top of document target  |
 |            | text |            | String | Text of the header  |
 | item |            |            |          | Renders to a string an item block  |
 |            | contents  |            | String | contents of block  |
 | list |            |            |          | renders a lest of items,  |
 |            | items |            | Array | Of strings already rendered with the "item" template |
 | unknown-name |            |            |          | A named block is included in the TOC, but is not known to ProcessedPod |
 |            | level  |            | String | level of the header implied by the block = 1  |
 |            | target |            | String | The target in the text body to which the TOC entry points  |
 |            | top |            | String | The top of the document for the Header to point to  |
 |            | name |            | String | The Name of the block |
 |            | contents |            | String | The contents of the block |
 | output |            |            |          | Output block contents  |
 |            | contents  |            | String |
 | pod |            |            |          |                 |
 |            | name |            | String | Like "unknown-name" |
 |            | contents |            | String | as "unknown-name" |
 |            | tail |            | String | any remaining list at end of pod not triggered by next pod statement  |
 | table |            |            |          | renders table with hash of keys  |
 |            | caption |            | String | possibly empty caption  |
 |            | headers |            | Array | of hash with key 'cells' |
 |            |            | cells | Array | Of string elements, that are the headers |
 |            | rows |            | Array | Of cells for the table body |
 |            |            | cells | Array | Of strings |
 | source-wrap |            |            |          | Turns all content to a string for a file  |
 |            | name |            | String | Name of file  |
 |            | title |            | String | Title for top of file / header  |
 |            | subtitle |            | String | Subtitle string (if any)  |
 |            | title-target |            | String | target name in text (may be same as top target)  |
 |            | metadata |            | String | rendered metadata string  |
 |            | lang  |            | String | The language of the document (default 'en')  |
 |            | toc |            | String | rendered TOC string  |
 |            | glossary |            | String | rendered glossary string  |
 |            | body |            | String | rendered body string  |
 |            | footnotes |            | String | rendered footnotes string  |
 |            | renderedtime |            | String | rendered time  |
 |            | path  |            | String | path to source file |
 |            | page-config  |            | Hash | user data given in first pod statement |
 | footnotes |            |            |          | renders the notes structure to a string |
 |            | notes |            | Array | Of hash with the following keys |
 |            |            | fnTarget | String | target in the footnote area  |
 |            |            | text | String | text for the footnote  |
 |            |            | retTarget | String | name for the anchor that the footnote will target  |
 |            |            |  fnNumber | String | The footnote number as allocated by the renderer  |
 | glossary |            |            |          | renders the glossary structure to string  |
 |            | glossary |            | Array | Of hash with keys |
 |            |            | text | String | text to be displayed in glossary (aka index)  |
 |            |            | refs | Array | Of hash with keys |
 |            |            | (refs) target | String | target in text of one ref  |
 |            |            | (refs) place | String | description of place in text of one ref (most recent header)  |
 | toc |            |            |          | Renders the TOC structure to a string  |
 |            | toc |            | Array | Of hash with keys: |
 |            |            | level | String | level of relevant header  |
 |            |            | target | String | target in text where header is  |
 |            |            | counter | String | formatted counter corresponding to level  |
 |            |            | text | String | text of the header  |
 | meta |            |            |          | renders the meta structure to a string that is then called metadata  |
 |            | meta |            | Array | Of hash |
 |            |            | name | String | Name of meta data, eg. AUTHOR  |
 |            |            | value | String | Value of key  |



### Notes<div id="Notes"> </div>
<span class="para" id="ddf058d"></span>If a block is accompanied by metadata in the Rakudoc document, then that data is also passed to the template. 



### Custom block parameters<div id="Custom_block_parameters"> </div>
<span class="para" id="e4b3483"></span>When a custom block name is provided to an instance of ProcessedPod, a default template with the same name, but in lower-case, must also be supplied. These are the parameters provided to the custom's default template (which can be over-ridden as explained above). 

----

## Custom Templates, MyBlock and formatcode-F<div id="Custom_Templates,_MyBlock_and_formatcode-F"> </div>
| **Key** | **Parameter** | **Sub-param** | **Type** | **Description** |
| :----:  | :----:  | :----:  | :----:  | :----: |
| myblock |            |            |          | The name block is included in the TOC, unless overridden |
 |         | level  |            | String | level of the header implied by the block, unless overridden |
 |         | target |            | String | The target in the text body to which the TOC entry points |
 |         | top |            | String | The top of the document for the Header to point to |
 |         | name |            | String | The Name of the block |
 |         | contents |            | String | The processed contents of the block |
 |         | raw-contents |        | String | Unprocessed contents of the block |
 |         | myblock | custom structure | Hash | Data provided by plugin |
 | format-f |        |           |             | A custom format code |
 |          | contents |         | String  | contents |
 |          | meta  |         | String / Array | contents after the straight line |



#### Notes<div id="Notes_0"> </div>
<span class="para" id="593223b"></span>For a custom block, using 'MyBlock' as an example: 

<span class="para" id="8d7f9fa"></span>The difference between `contents` and `raw-contents` is that the string in `contents` has been processed by ProcessedPod as if it was a `para` and so if the contents contains embedded Rakudoc blocks, they will be rendered as well. 

<span class="para" id="c8c713a"></span>`raw-contents` processes the contents with context Raw. This renders PodBlocks as text and does not escape contents. 

<span class="para" id="bc6691a"></span>The parameter `myblock` is provided if data has been provided to the instance of ProcessedPod by the plugin that created the custom block and templates. Normally it is the same name as the Block name, but this can be over-ridden using the `:namespace<xxx>` metadata. 

<span class="para" id="b693d96"></span>The structure of `myblock` is determined by the plugin. 

<span class="para" id="239417f"></span>If a custom block is associated with the metadata key `:headlevel(integer)`, then `level` is given the value of `integer`. If `:!toc` is given, `level` will be 0. 

<span class="para" id="94cda4c"></span>If `integer` is 0 or `:!toc`, then the contents of the block are not included in the Table of Contents. 

<span class="para" id="26c6809"></span>For a custom format code, using format code `F` as an example: 

<span class="para" id="8630844"></span>A Formatting Code in Rakudoc has the form  F&lt;contents|metadata&gt; . See documentation about L&lt;&gt; as an example. The template is given this data. 



### HTML2<div id="HTML2"> </div>
----

## Helper templates in Pod::To::HTML2<div id="Helper_templates_in_Pod::To::HTML2"> </div>
| **Key** | **Parameter** | **Calls** | **Called-by** | **Description** |
| :----:  | :----:  | :----:  | :----:  | :----: |
| camelia-img |            |            | head-block | <span class="para" id="bfe8ef5"></span>Returns a reference to `camelia-svg` |
 | css |            |            | head-block | <span class="para" id="44ff1c0"></span>Returns a reference to `rakudoc-styling.css` |
 | favicon |            |            | head-block | <span class="para" id="73405a6"></span>Returns a reference to `favicon.ico` |
 | title |            |            | head-block | Helper template to format title for text (title also used in header-block) |
 |            | title  |            |            |            |
 |            | title-target  |            |            |            |
 | subtitle |            |            | source-wrap | Helper template to format title for text |
 |            | subtitle  |            |            |            |
 | head-block |            |            | source-wrap | Forms the text for the 'head' section |
 |            | title |            |            |            |
 |            | metadata |            |            |            |
 |            | css | css-text |            | if 'css' is empty, it calls css-text |
 |            | head |            |            |            |
 |            |            | favicon |            |            |
 | header |            |            | source-wrap | renders the header section for the body |
 |            |            | title |            |            |
 |            |            | camelia-img |            |            |
 | footer |            |            | source-wrap | renders the footer section for the body |
 |            | path |            |            | path to the source file |
 |            | renderedtime |            |            | time the source was rendered |



#### Notes<div id="Notes_0"> </div>

&nbsp;&nbsp;• All blocks pass any extra config parameters to the template, eg., 'class', as well but if a developer passes configuration data to a block, she will be able to use it in the template.  
&nbsp;&nbsp;• The template does not need to render a parameter, but the template verification tool will issue a warning if it does not.  
&nbsp;&nbsp;• required templates are called by the Renderer, but it is cleaner to break the template into sections. The templates for Pod::To::HTML2 do this, with footer, header and head-block templates, all of which are called by file-wrap. This structure is shown in the table above.  




----

----

Rendered from docs/docs/PodTemplates.rakudoc at 14:52 UTC on 2024-12-13

Source last modified at 14:51 UTC on 2024-12-13


