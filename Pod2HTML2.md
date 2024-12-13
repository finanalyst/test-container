
# Rendering Rakudoc (aka POD6) into HTML

	Renders Rakudoc sources into HTML using templates and plugins

----

## Table of Contents

<a href="#Requirements_for_HTML">Requirements for HTML</a>   
<a href="#Simple_usage">Simple usage</a>   
<a href="#Other_templating_systems">Other templating systems</a>   
<a href="#Usage_with_compiler">Usage with compiler</a>   
&nbsp;&nbsp;- <a href="#Component_options">Component options</a>   
<a href="#Regexen_and_Page_Component">Regexen and Page Component</a>   
&nbsp;&nbsp;- <a href="#Plugins_options">Plugins options</a>   
<a href="#Rakudoc-to-html">Rakudoc-to-html</a>   
&nbsp;&nbsp;- <a href="#Examples_and_plugins">Examples and plugins</a>   
<a href="#Defaults_and_customisation">Defaults and customisation</a>   
&nbsp;&nbsp;- <a href="#Template_file">Template file</a>   
<a href="#Plugins">Plugins</a>   
<a href="#Standalone_usage_mixing_Pod_and_code">Standalone usage mixing Pod and code</a>   
<a href="#Custom_blocks">Custom blocks</a>   
&nbsp;&nbsp;- <a href="#Latex-render">Latex-render</a>   
<a href="#Graphviz">Graphviz</a>   
<a href="#Adding_customisation_programmatically">Adding customisation programmatically</a>   
<a href="#Highlighting">Highlighting</a>   
<a href="#Templates">Templates</a>   
<a href="#Exported_Subroutines">Exported Subroutines</a>   
<a href="#Why_Reinvent_the_Wheel?">Why Reinvent the Wheel?</a>   


<span class="para" id="87be7ba"></span>A Rakudoc (once called POD6) document is rendered by Raku into a structure called a Pod-tree. A Pod-tree can also be constructed from the Rakudoc used to document a Raku program. 

<span class="para" id="e6faca9"></span>Pod::To::HTML2 converts the Pod-tree into an HTML file, or a fragment of Rakudoc into a fragment of HTML. 

<span class="para" id="161f3f1"></span>Pod::To::HTML2 is a subclass of **ProcessedPod**, which contains significantly more information about the Rakudoc in a file. See [the documentation of PodRender](PodRender) for more detail. 

<span class="para" id="cf6fa0a"></span>See the sister [Pod::To::MarkDown](Markdown) class for rendering a Rakudoc file to MarkDown. 

<span class="para" id="08e820e"></span>A Pod-tree consists of Pod::Blocks. The contents and metadata associated with each block is passed to a template with the same name as the block. 

<span class="para" id="4eaf0bf"></span>Templates are stored separately from the code in `Pod::To::HTML2` and so can be tweaked by the user. 

<span class="para" id="008727d"></span>Rakudoc can be customised by creating new named blocks and FormatCodes (see Raku documentation on POD6). The `Pod::To::HTML2` class enables the creation of new blocks and FormatCodes via [plugins](Plugins). Several non-standard Blocks are included to show how this is done. 

<span class="para" id="e3dd093"></span>The rationale for re-writing the whole **Pod::To::HTML** module is in the section [Why Reinvent the Wheel?](Why Reinvent the Wheel?). 

<span class="para" id="1e06763"></span>`Pod::To::HTML2` implements three of the subs in `Pod::To::HTML`, namely: 



&nbsp;&nbsp;• node2html <pod block> # convert a string of pod to a string of html  
&nbsp;&nbsp;• pod2html <pod tree> # converts a pod block to a string of html  
&nbsp;&nbsp;• render # converts a file to html  
<span class="para" id="0ae57f7"></span>However, `pod2html` and `render` have a variety of options that are not supported by HTML2 because they depend on Mustache templates and the plugins for HTML2 have not yet been fully completed to work with Mustache templates. This is a TODO. 

<span class="para" id="05e6ddf"></span>A utility called `Rakudoc-to-html` is included in the distribution to transform a local Rakudoc file# to HTML. Try `Rakudoc-to-html Example` in an empty directory! See below for more information. 


----

## Requirements for HTML<div id="Requirements_for_HTML"> </div>
<span class="para" id="0e2af40"></span>To render a Rakudoc file into HTML, the following are needed: 



&nbsp;&nbsp;• Content in the form of a Pod-tree, OR a filename containing Rakudoc, which is then converted to a Pod-tree  
&nbsp;&nbsp;• templates that will generate HTML for each Pod Block  
&nbsp;&nbsp;• a favicon file  
&nbsp;&nbsp;• CSS content for rendering the HTML  
&nbsp;&nbsp;• images referenced in the CSS or content  
&nbsp;&nbsp;• Javascript or JQuery programs and libraries.  
<span class="para" id="bff967a"></span>The HTML file must then be served together with the CSS, favicon.ico, script and image assets. This can be done in many different ways, eg, with a Cro App, but each has a different set of dependencies. It is assumed the user will know how to serve files once generated. 


----

## Simple usage<div id="Simple_usage"> </div>
<span class="para" id="7489b84"></span>This distribution assumes a user may want simplicity in order 



&nbsp;&nbsp;• to quickly render a single Rakudoc file  
&nbsp;&nbsp;• to tweak/provide any of the templates, favicon, CSS  
&nbsp;&nbsp;• add plugins to create custom blocks  
&nbsp;&nbsp;• <span class="para" id="7fd6a1e"></span>include the `=Image` named block and reference an image. 

  
<span class="para" id="4422710"></span>Anything more complex, such as another destination directory or handling more than one Rakudoc file, needs to be done by instantiating the underlying classes. An more complex example is the `Collection` module, which adds even more plugins. **Collection** *render* plugins can be adapted for `Pod::To::HTML2`. 

<span class="para" id="fce2990"></span>The utility `Rakudoc-to-html` in this distribution will render a Rakudoc file. 

<span class="para" id="43639a1"></span>The utility creates the following in the directory where Rakudoc-to-html was called: 



&nbsp;&nbsp;• an html file with the same name as the Rakudoc file;  
&nbsp;&nbsp;• <span class="para" id="4f04ae5"></span>a sub-directory `assets_files` if not already present with: 

  
&nbsp;&nbsp;&nbsp;&nbsp;▹ <span class="para" id="063bdb8"></span>an `images` subdirectory with 

  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ <span class="para" id="003676c"></span>`favicon.ico` file, if it is **not** already present; 

  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ <span class="para" id="b41a8cd"></span>the file `Camelia.svg` if it is **not** already present; 

  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ <span class="para" id="4e9ffb6"></span>images defined by plugins or [user images](Image management). 

  
&nbsp;&nbsp;&nbsp;&nbsp;▹ a CSS subdirectory with:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ <span class="para" id="e4fcb85"></span>`rakudoc-styling.css` if it is **not** already present; 

  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ <span class="para" id="17011bb"></span>css files defined for [plugins](Plugins); 

  
&nbsp;&nbsp;&nbsp;&nbsp;▹ <span class="para" id="18ec2bd"></span>a sub-directory `js` for js and jQuery scripts defined by plugins. 

  
<span class="para" id="a38884a"></span>The default templates, favicon, plugins and rakudoc-styling css are installed when this distribution is installed into a directory called `$*HOME/.local/share/RenderPod`. (See [Defaults and customisation](Defaults and customisation) for more detail.) 

<span class="para" id="51cbb5c"></span>If files for html (See [Default and customisation](Default and customisation)) are present in the current working directory, they will be used in place of the defaults in `$*HOME/.local/share/RenderPod`. 

<span class="para" id="33888d8"></span>The recommended way to customise the templates/favicon/css/plugin files is to create an empty directory, use `Rakudoc-to-html get-local` to all plugins and templates from the default directory to the empty directory, modify them, then call `Rakudoc-to-html rakudoc-source.rakudoc` in that directory (source could include a path). The HTML file and its associated assets will be generated in the directory. 

<span class="para" id="bbdda6b"></span>Plugins that don't need to be modified can be deleted locally. 

<span class="para" id="e3c41ed"></span>If the Pod::To::HTML2 class is instantiated, or the Pod::To::HTML2 functions are used, then the default assets can be used, or taken from files in the Current Working Directory. 


----

## Other templating systems<div id="Other_templating_systems"> </div>
<span class="para" id="a260de2"></span>Three sets of the main default templates are available for the three templating engines automatically detected in this distribution: 



&nbsp;&nbsp;• <span class="para" id="6c2fe9a"></span>`RakuClosure` template system, (default choice) 

  
&nbsp;&nbsp;• <span class="para" id="1e1bff5"></span>`Mustache` templating engine, (plugins do not work with Mustache) and 

  
&nbsp;&nbsp;• <span class="para" id="2a73941"></span>`Cro::WebApp::Template` engine (the interface with Crotmp is not fully developed). 

  
<span class="para" id="a65bb32"></span>The templating engine can be selected by calling `Rakudoc-to-html` with the `type` option set to 



&nbsp;&nbsp;• <span class="para" id="71072f0"></span>**crotmp** eg., ` Rakudoc-to-html --type=crotmp` 

  
&nbsp;&nbsp;• <span class="para" id="76430c5"></span>**mustache**, eg., `Rakudoc-to-html --type='mustache'`. 

  
&nbsp;&nbsp;• <span class="para" id="3254339"></span>**rakuclosure**, eg., `Rakudoc-to-html --type='rakuclosure'`. 

  
<span class="para" id="6c4ff45"></span>It is possible to change the templates in a plugin to match the main templating engine. Plugins templates are specified for RakuClosureTemplates. But if there is a key 'RakuClosureTemplater', the templates will be taken from the Hash it points to. Then keys for other templating engines can also be included. 

<span class="para" id="384377a"></span>Todo: the plan is to include templates for each templating engine in the common plugins. 


----

## Usage with compiler<div id="Usage_with_compiler"> </div>
<span class="para" id="e7a7807"></span>From the terminal: 


```
raku --doc=HTML2 input.raku > output.html

```
<span class="para" id="a292681"></span>This takes the Rakudoc (aka POD6) in the `input.raku` file, transforms it into HTML, outputs a full HTML page including a Table of Contents, Glossary and Footnotes. As described in [Simple Usage](Simple Usage), the `favicon.ico`, `rakudoc-styling.css`, image and additional CSS files are placed in subdirectories of the Current working directory. 

<span class="para" id="cddbb5a"></span>This will only work with `rakuclosure` templates. 



### Component options<div id="Component_options"> </div>
<span class="para" id="d590a39"></span>Some rendering options can be passed via the RAKOPTS Environment variable. 



&nbsp;&nbsp;• The TOC, Glossary (Index), Meta and Footnotes can be turned off.  

```
RAKOPTS='NoTOC NoMETA NoGloss NoFoot' raku --doc=HTML2 input.raku > output.html

```
<span class="para" id="bedd39e"></span>The following regexen are applied to the contents of the RAKOPTS environment variable and if one or more matchd, they switch off the default rendering of the respective section: 

----

## Regexen and Page Component<div id="Regexen_and_Page_Component"> </div>
| **regex applied** | **if Match, then Turns off** |
| :----:  | :----: |
| /:i 'no' '-'? 'toc' / | Table of Contents |
 | /:i 'no' '-'? 'meta' / | Meta information (eg AUTHOR) |
 | /:i 'no' '-'? 'glossary' / | Glossary |
 | /:i 'no' '-'? 'footnotes' / | Footnotes. |

<span class="para" id="1bac8f4"></span>Any or all of 'NoTOC' 'NoMETA' 'NoGloss' 'NoFoot' may be included in any order. Default is to include each section. 



### Plugins options<div id="Plugins_options"> </div>
<span class="para" id="9905594"></span>Setting the Environment variable `PLUGINS` to a list of plugins will invoke only those plugins. 

<span class="para" id="56adc97"></span>For example, if only the Graphviz plugin, and no other, is required, then use 


```
PLUGINS='Graphviz' raku --doc=HTML2 input.raku > output.html

```

----

## Rakudoc-to-html<div id="Rakudoc-to-html"> </div>
<span class="para" id="5c2b82e"></span>This is invoked on the command line as 


```
Rakudoc-to-html input.rakudoc


```
<span class="para" id="f19c61a"></span>The options are mostly like those for the compiler, and are: 



&nbsp;&nbsp;• --rakopts # like RAKOPTS  
&nbsp;&nbsp;• --plugins # like PLUGINS  
&nbsp;&nbsp;• <span class="para" id="b879bfd"></span>--type # default is 'rakuclosure', see [templates](Templates)) 

  


### Examples and plugins<div id="Examples_and_plugins"> </div>
<span class="para" id="6304686"></span>`Rakudoc-to-html` can also be called to get an example Rakudoc file, and to get local versions of plugins. 


```
Rakudoc-to-html Example


```
<span class="para" id="a52dedf"></span>will bring in the default files and a document called `Samples.rakudoc`, which is then transformed to HTML. 


```
Rakudoc-to-html get-local


```
<span class="para" id="bcecefd"></span>will move the customisable (but not the core transfer plugins) to the local directory. Any sub-directory without a '_' in its name is considered a plugin (hence the name for the asset files directory `asset_files`). 

<span class="para" id="09048e5"></span>Local plugins take precedence over default plugins. 


----

## Defaults and customisation<div id="Defaults_and_customisation"> </div>
<span class="para" id="16ac726"></span>The directory `$*HOME/.local/share/RenderPod` contains the following files: 



&nbsp;&nbsp;• html-templates-rakuclosure.raku # templates in Rakuclosure form to convert Rakudoc to HTML  
&nbsp;&nbsp;• html-templates-mustache.raku # ditto for mustache  
&nbsp;&nbsp;• html-templates-crotmp.raku # ditto for crotmp  
&nbsp;&nbsp;• simple-extras/ # plugin for simple additions to Rakudoc  
&nbsp;&nbsp;• graphviz/ # plugin to allow for graphviz files  
&nbsp;&nbsp;• latex-render/ # plugin to render math equations in Latex syntax to an image using a free on-line renderer.  
&nbsp;&nbsp;• <span class="para" id="dd68008"></span>styling/ # a customisable plugin containing the source for the css files and images for styling the Rakudoc html files. The plugin directory contains the following, which are transferred using `move-assets`. 

  
&nbsp;&nbsp;&nbsp;&nbsp;▹ rakudoc-styling.css # the css to be associated with the HTML  
&nbsp;&nbsp;&nbsp;&nbsp;▹ _highlight.scss # highlights source, used by next  
&nbsp;&nbsp;&nbsp;&nbsp;▹ rakudoc-styling.scss # the scss source for the css file (this is not used by Pod::To::HTML2 but is included for ease of use). Converting scss to css can be found by internet searching. SASS is a good utility.  
&nbsp;&nbsp;&nbsp;&nbsp;▹ favicon.ico # the favicon file  
&nbsp;&nbsp;• gather-js-jq/ # a special plugin for including js and jquery scripts in plugins. The plugin has a README, which provides more information about the various js/jq config keys.  
&nbsp;&nbsp;• gather-css/ # a special plugin for including css in plugins. The plugin has a README, which provides more information about the various css config keys.  
&nbsp;&nbsp;• <span class="para" id="b0360f8"></span>move-assets/ # a core plugin for moving assets from another plugin to the `asset_files` sub-directory. 

  
&nbsp;&nbsp;• md-templates-rakuclosure.raku, etc. markdown files. They are not relevant for html rendering (see Pod::To::Markdown for more detail)  


### Template file<div id="Template_file"> </div>
<span class="para" id="8ab7924"></span>A template file must contain the minimum number of templates (see [RenderPod](RenderPod) for more detail). 

<span class="para" id="3c24998"></span>The template file must evaluate to a Raku **Associative** type, eg. Hash. 

<span class="para" id="458ef55"></span>The `_templater` key (see one of the files for an example) defines which templating system is used. 

<span class="para" id="ac8df00"></span>The plugins are not guaranteed to have template systems other than `rakuclosure`. Template systems cannot be mixed. 


----

## Plugins<div id="Plugins"> </div>
<span class="para" id="7286629"></span>A plugin is in a directory with the same name. A plugin name must start with a letter and contain any letter, number, or '-' characters. It may not contain a '_' after the first letter. 

<span class="para" id="e810442"></span>The following plugins are called by default, unless explicitly removed (by passing a plugins list without one or all of them): 



&nbsp;&nbsp;• simple-extras  
&nbsp;&nbsp;• graphviz  
&nbsp;&nbsp;• latex-render  
<span class="para" id="65d1e49"></span>Two special plugins are included in the defaults directory. Their contents are used by `Pod::To::HTML2` directly from the defaults directory. Copying them to a local directory will not have an effect. Its not a good idea to change them without reading the plugin documentation for the `Collection` module. They are: 



&nbsp;&nbsp;• gather-css  
&nbsp;&nbsp;• gather-js-jq  
<span class="para" id="6ba7c80"></span>Each plugin contains a README file, which may provide more detail. 

<span class="para" id="0fda77d"></span>A plugin must contain 



&nbsp;&nbsp;• <span class="para" id="e98d4a1"></span>the file `config.raku` 

  
&nbsp;&nbsp;• <span class="para" id="f186706"></span>files named in `config.raku` 

  
&nbsp;&nbsp;• <span class="para" id="d92d2d3"></span>The config **must** contain the following keys 

  
&nbsp;&nbsp;&nbsp;&nbsp;▹ template-raku # can be an empty string, otherwise a file for templates  
&nbsp;&nbsp;&nbsp;&nbsp;▹ custom-raku # can be an empty string, otherwise a file for custom blocks  
&nbsp;&nbsp;• <span class="para" id="4d0c6bb"></span>The config **may** contain the following 

  
&nbsp;&nbsp;&nbsp;&nbsp;▹ <span class="para" id="bb5c948"></span>css # a file containing css, which will be merged into `rakudoc-extra.css` 

  
&nbsp;&nbsp;&nbsp;&nbsp;▹ css-add # a file to be transfered to CWD  
&nbsp;&nbsp;&nbsp;&nbsp;▹ css-link # a url for a style sheet  
&nbsp;&nbsp;&nbsp;&nbsp;▹ js-script # this must be either a String, or point to a [string name, ordering] array.  
&nbsp;&nbsp;&nbsp;&nbsp;▹ jquery # ditto  
&nbsp;&nbsp;&nbsp;&nbsp;▹ js-link # ditto  
&nbsp;&nbsp;&nbsp;&nbsp;▹ jquery-link # ditto  
<span class="para" id="74c867c"></span>More information about the js/jquery keys can be found in ` <defaults>/gather-s-q` 

<span class="para" id="24ae1a9"></span>The file pointed to by the `template-raku` key is a raku program that evaluates to an Associative (eg., Hash) with keys pointing to templates. Plugins currently only use the `rakuclosure` system. More detail can be found in [RakuClosureTemplates](RakuClosureTemplates). 

<span class="para" id="dabc0c8"></span>The file pointed to by the `custom-raku` key is a raku program that evaluates to a Positional (eg., Array). This is the list of names for the custom blocks. The templates should be the lower case names provided by the custom-raku list. 

<span class="para" id="ba2fe8e"></span>The default plugins provide examples. 


----

## Standalone usage mixing Pod and code<div id="Standalone_usage_mixing_Pod_and_code"> </div>
<span class="para" id="267832e"></span>'Standalone ... mixing' means that the program itself contains pod definitions (some examples are given below). This functionality is mainly for tests, but can be adapted to render other pod sources. 

<span class="para" id="a4ab0d1"></span>`Pod::To::HTML2` is a subclass of `PodProcessed`, which contains the code for a generic Pod Render. `Pod::To::HTML2` provides a default set of templates and minimal css (see [Templates](Templates)). It also exports some routines (not documented here) to pass the tests of the legacy `Pod::To::HTML2` module (see below for the rationale for choosing a different API). 

<span class="para" id="b8a838a"></span>`Pod::To::HTML2` also allows, as covered below, for a customised css file to be included, for individual template components to be changed on the fly, and also to provide a different set of templates. 

<span class="para" id="1650a5b"></span>What is happening in this case is that the raku compiler has compiled the Pod in the file, and the code then accesses the Pod segments. In fact the order of the Pod segments is irrelavant, but it is conventional to show Pod definitions interwoven with the code that accesses it. 


```
use Pod::To::HTML2;
# for repeated pod trees to be output as a single page or html snippets (as in a test file)
my $renderer = Pod::To::HTML2.new(:name<Optional name defaults to UNNAMED>);
# later

=begin pod
some pod
=end pod

say 'The rendered pod is: ', $renderer.render-block( $=pod );

=begin pod

another fact-filled assertion

=end pod

say 'The next pod snippet is: ', $renderer.render-block( $=pod[1] );
# note that all the pod in a file is collected into a 'pod-tree', which is an array of pod blocks. Hence
# to obtain the last Pod block before a statement, as here, we need the latest addition to the pod tree.

# later and perhaps after many pod statements, each of which must be processed through pod-block

my $output-string = $renderer.source-wrap;

# will return an HTML string containing the body of all the pod items, TOC, Glossary and Footnotes.
# If there are headers in the accumulated pod, then a TOC will be generated and included
# if there are X<> type references in the accumulated pod, then a Glossary will be generated and included

$renderer.file-wrap(:output-file<some-useful-name>, :ext<html>);
# first .source-wrap is called and then output to a file.
# if ext is missing, 'html' is used
# if C<some-useful-name> is missing, C<name> is used, which defaults to C<UNNAMED>
# C<some-useful-name> could include a valid path.


```

----

## Custom blocks<div id="Custom_blocks"> </div>
<span class="para" id="8471858"></span>The Rakudoc specification allows for Pod::Blocks to be named and meta data associated with it. This allows us to leverage the standard syntax to allow for non-standard blocks and templates. 

<span class="para" id="834f5e5"></span>Briefly, the name of the Block is placed in `html-blocks` (case is not important), and a template with the same name (**must** be lower case), is placed in the `html-templates-xxx.raku` file (the **xxx** depending on which templating system is used). Note, changes need only be made to the file with the templating system the user wants and specifies with the `--type` option. 

<span class="para" id="c347960"></span>In keeping with other named blocks, *Title* case may be conventionally used for the block name and *Lower* case is required for the template. 

<span class="para" id="0256702"></span>Some examples are provided in the default templates. They are 



&nbsp;&nbsp;• <span class="para" id="4948b88"></span>`Image` custom block and provides the `image` template. The actual images should be in the `assets/images` sub-directory. It is up to the user to do this. 

  
&nbsp;&nbsp;• <span class="para" id="cffb6b6"></span>`HR`. This adds a xxxish-dots class to ` <hr> ` HTML elements. 

  
&nbsp;&nbsp;• <span class="para" id="98d607e"></span>`Quotation`. This formats content with an indentation, and adds both author and citation components. 

  
&nbsp;&nbsp;• <span class="para" id="689b4a1"></span>`Latex-render` 

  
&nbsp;&nbsp;• <span class="para" id="2a9760f"></span>`Graphviz` 

  


### Latex-render<div id="Latex-render"> </div>
<span class="para" id="8fb286b"></span>When an equation is given in a Latex syntax after a `Latex` block, the description of the equation is sent to an online renderer, and the image is inserted into the html. Eg. 


```
    =for Latex
    \begin{align*}
    \sum_{i=1}^{k+1} i^{3}
    &= \biggl(\sum_{i=1}^{n} i^{3}\biggr) +  i^3\\
    &= \frac{k^{2}(k+1)^{2}}{4} + (k+1)^3 \\
    &= \frac{k^{2}(k+1)^{2} + 4(k+1)^3}{4}\\
    &= \frac{(k+1)^{2}(k^{2} + 4k + 4)}{4}\\
    &= \frac{(k+1)^{2}(k+2)^{2}}{4}
    \end{align*}


```
<span class="para" id="ede2e55"></span>It is assumed that there is internet connectivity to the online engine. 


----

## Graphviz<div id="Graphviz"> </div>
<span class="para" id="d380b5a"></span>When a description of a digraph in the dot syntax is given between bracketing elements, then an image is generated in the html, eg. 


```
    =begin Graphviz
        digraph G {
            main -> parse -> execute;
            main -> init;
            main -> cleanup;
            execute -> make_string;
            execute -> printf
            init -> make_string;
            main -> printf;
            execute -> compare;
        }
    =end Graphviz

```
<span class="para" id="6199400"></span>The assumption is that the `dot` utility is installed. 

<span class="para" id="7640104"></span>More information on `dot` and `graphviz` can be found at [Graphviz](https://www.graphviz.org/) 

<span class="para" id="27a71ce"></span>The files <rakudoc-styling.css> and <favicon.ico> can be replaced completely. If they are present in the current directory, they will not be overwritten. 


----

## Adding customisation programmatically<div id="Adding_customisation_programmatically"> </div>
<span class="para" id="209dbbf"></span>Suppose we wish to have a diagram block with a source and to assign classes to it. We want the HTML container to be `figure`. 

<span class="para" id="920a706"></span>In the Rakudoc source code, we would have: 


```
    =for diagram :src<https://someplace.nice/fabulous.png> :class<float left>
    This is the caption.

```
<span class="para" id="8e71eae"></span>Note that the `for` takes configuration parameters to be fed to the template, and ends at the first blank line or next `Pod::Block`, that is a line begining with `=`. 

<span class="para" id="406122d"></span>Then in the rendering program we need to provide to the `ProcessedPod` class the new object name, and the corresponding template (in **this** example, the `Mustache` system is used, which means all the other templates must be in `Mustache`). 

<span class="para" id="a7b197e"></span>The Block name and template must be the same name. Thus we would have: 


```
    use v6;
    use Pod::To::HTML2;
    my Pod::To::HTML2 $r .= new;
    $r.custom = <diagram>;
    $r.modify-templates( %( diagram => '<figure source="{{ src }}" class="{{ class }}">{{ contents }}</figure>' , ) );

```

----

## Highlighting<div id="Highlighting"> </div>
<span class="para" id="a106ddb"></span>Generally it is desirable to highlight code contained in ` =code ` blocks. While perhaps this may be done in-Browser, it can be done at HTML generation, via the Templates and a highlighter function. 

<span class="para" id="f352b78"></span>This distribution, `Raku::Pod::Render`, by default sets up the atom-highlighter stack (installation dependencies can be found in [README](README)). 

<span class="para" id="87fb1a8"></span>Since highlighting generates considerably more HTML, it is turned off by default, which will affect the ` --doc=HTML ` compiler option. 

<span class="para" id="3ae67de"></span>Highlighting is handled in the Templates. The default Templates use the atom-highlighter, which is installed with `Raku::Pod::Render` by default. 

<span class="para" id="f16f2ff"></span>Highlighting is enabled at the time of HTML generation by setting `$render.highlight-code=True` after `Pod::To::HTML2` object instantiation. 

<span class="para" id="75b2ed6"></span>Another highlighter could be attached to a Pod::To::HTML2 object, in which case the following need to be done: 



&nbsp;&nbsp;• <span class="para" id="582e0be"></span>the getter and setter functions of `.highlight-code` need to be over-ridden so as to set up the code, and turn it off 

  
&nbsp;&nbsp;• <span class="para" id="87f60af"></span>a closure needs to be assigned to ` .highlight `. The closure should accept a Str and highlight it. 

  
&nbsp;&nbsp;• <span class="para" id="e2d2738"></span>the `Pod::To::HTML2` object has an attribut ` .atom-highlighter` of type `Proc` to hold an external function. 

  
<span class="para" id="b4d47fd"></span>The `atom-highlighter` automatically escapes HTML entities, but so does `GenericPod`. Consequently, if a different highlighter is used for highlighting when HTML is generated, escaping needs to be turned off within GenericPod for Pod::Code blocks. This is the default behaviour. If a different behaviour is required, then `no-code-escape` needs to be set to False. 


----

## Templates<div id="Templates"> </div>
<span class="para" id="7e6d41d"></span>The default templating system is a hash of Raku closures. More about templates can be found in [RenderPod](RenderPod). Another template engine is Template::Mustache. This can be accessed as `use Pod::To::HTML2::Mustache`. A minimal default set of templates is provided with the Module. 

<span class="para" id="87c2893"></span>Each template can be changed using the `modify-templates` method. Be careful when over-riding `head-block` to ensure the css is properly referenced. 

<span class="para" id="ca4313c"></span>A full set of new templates can be provided to ProcessedPod either by providing a path/filename to the processor method, eg., 


```
use Pod::To::HTML2;
my Pod::To::HTML2 $p .= new;

$p.templates<path/to/mytemplates.raku>;

# or if all templates known

$p.templates( %( format-b => '<b>{{ contents }}</b>' .... ) );


```
<span class="para" id="c777cd6"></span>If :templates is given a string, then it expects a file that can be compiled by raku and evaluates to a Hash containing the templates. More about the hash can be found in [RenderPod](renderpod). 


----

## Exported Subroutines<div id="Exported_Subroutines"> </div>
<span class="para" id="68aef73"></span>Two functions are exported to provide backwards compatibility with legacy Pod::To::HTML module. They map onto the methods described in `PodRender`. 

<span class="para" id="4190167"></span>Only those options actually tested will be supported. 



&nbsp;&nbsp;• node2html  

```
sub node2html( $pod ) is export


```


&nbsp;&nbsp;• pod2html  

```
sub pod2html( $pod, *%options ) is export


```

----

## Why Reinvent the Wheel?<div id="Why_Reinvent_the_Wheel?"> </div>
<span class="para" id="09c2066"></span>The two original Pod rendering modules are ` Pod::To::HTML2 ` (later **legacy P2HTML** ) and ` Pod::To::BigPage `. So why rewrite the modules and create another API? There was an attempt to rewrite the existing modules, but the problems go deeper than a rewrite. The following difficulties can be found with the legacy Modules: 



&nbsp;&nbsp;• Inside the code of legacy P2HTML there were several comments such as 'fix me'. The API provided a number of functions with different parameters that are not documented.  
&nbsp;&nbsp;• Not all of the API is tested.  
&nbsp;&nbsp;• One or two POD features are not implemented in legacy P2HTML, but are implemented in P2BigPage.  
&nbsp;&nbsp;• Fundamentally: HTML snippets for different Pod Blocks is hard-coded into the software. Changing the structure of the HTML requires rewriting the software.  
&nbsp;&nbsp;• <span class="para" id="82e288d"></span>Neither module deals with Indexes, or Glossaries. POD6 defines the **<span id="index-entry-"><span style="color:green; background-color: antiquewhite;"> </span></span>** format code to place text in a glossary (or index), but this information is not collected or used by P2HTML. The Table of Content data is not collected in the same pass through the document as the generation of the HTML code. 

  
<span class="para" id="88c8c9e"></span>This module deal with these problems as follows: 



&nbsp;&nbsp;• All Pod Blocks are associated with templates and data for the templates. So the Generic Renderer passes off generation of the output format to a Template engine. (Currently, only the Template::Mustache engine is supported, but the code has been designed to allow for other template engines to be supported by over-ridding only the template rendering methods).  
&nbsp;&nbsp;• <span class="para" id="135fead"></span>Data for **Page Components** such as `Table of Contents`, `Glossary`, `Footnotes`, and `MetaData` are collected in a single processing pass of the Generic Renderer. The subclass can provide templates for the whole document to incorporate the Page Components as desired. 

  
&nbsp;&nbsp;• All the links (both external and internal) are collected together and can be accessed after processing the Pod source, thus allowing for testing of the links separately.  
&nbsp;&nbsp;• There is a clear distinction between what is needed for a particular output format, eg., HTML or MarkDown, and what is needed to render Pod. Thus, HTML requires css and headers, etc. MarkDown requires the anchors to connect a Table of Contents to specific Headers in the text to be written in a specific way.  



----

## Index

<span style="background-color: antiquewhite; font-weight: 600;"> </span>: <a href="#index-entry-">Why Reinvent the Wheel?</a>




----

----

Rendered from docs/docs/Pod2HTML2.rakudoc at 14:52 UTC on 2024-12-13

Source last modified at 14:51 UTC on 2024-12-13


