
# The RakuDoc to HTML-Extra renderer

	Based on RakuAST-RakuDoc-Render engine, HTML with plugins and Bulma CSS

----

## Table of Contents

<a href="#SYNOPSIS">SYNOPSIS</a>   
<a href="#Overview">Overview</a>   
&nbsp;&nbsp;- <a href="#Plugins_and_their_sources">Plugins and their sources</a>   
<a href="#RakuDoc::Render_plugins">RakuDoc::Render plugins</a>   
<a href="#Customisability">Customisability</a>   
<a href="#Credits">Credits</a>   



----

## SYNOPSIS<div id="SYNOPSIS"> </div>
<span class="para" id="2c0848f"></span>In a *clean* directory containing only a RakuDoc source, eg `wp.rakudoc` use the terminal command 


```
RAKUDO_RAKUAST=1 raku -MRakuDoc::Render -MRakuDoc::To::HTML --rakudoc=HTML-Extra wp.rakudoc > wp.html


```
<span class="para" id="1fa780f"></span>Easier (once the distribution has been installed, see [README](README)) 


```
RenderDocs --html --extra --src='.' --to='.' wp


```
<span class="para" id="5162ee0"></span>Both will cause the file `wp.html` to be generated in the current working directory from `wp.rakudoc` in CWD. 

<span class="para" id="1aa8aa9"></span>To view this file (adapt according to browser), open `file://<home-path-to>/wp.html`. All the JS and CSS are packaged in the HTML file, or downloaded from a CDN. 




----

## Overview<div id="Overview"> </div>
<span class="para" id="9c40f56"></span>This renderer allows for more customisability than the [minimal HTML renderer](RakuDoc-To-HMTL) in this distribution. 

<span class="para" id="6f7fb51"></span>By default, the following plugins are provided, and more can be added by the user (see [Plugins](Extra-HTML-Plugins)). 



### Plugins and their sources<div id="Plugins_and_their_sources"> </div>
 | **Plugin** | **Source** | **Use** | **License** |
| :---: | :---: | :---: | :---: |
 | Bulma | <span class="para" id="bbdd707"></span>[Bulma Home](https://bulma.io) | Use the Bulma CSS for the page, which is styled into panels, is responsive, has themes | MIT |
 | LeafletMaps | <span class="para" id="b6acc1d"></span>[Home page](https://leafletjs.com/) and [Github repo](https://github.com/leaflet-extras/leaflet-providers) | Puts a map in a web page, with tiles from multiple providers | 2-clause BSD |
 | Latex | <span class="para" id="ec0ff3e"></span>[CodeCogs editor page](https://editor.codecogs.com/) | Uses CodeCogs online equation editor to render formulae | see website |
 | Graphviz | <span class="para" id="012f2f8"></span>[GraphViz software ](https://graphviz.org) | <span class="para" id="ef7f558"></span>Allows for figures/diagrams to be described in the `dot` language | see website |
 | FontAwesome | <span class="para" id="05940b2"></span>[FontAwesome v5-15-4](https://docs.fontawesome.com/v5/web/setup/get-started/) | <span class="para" id="311287b"></span>Include FontAwesome icons using ℱ&lt;> markup, where ℱ is U+2131 | see website |
 | ListFiles | <span class="para" id="957647d"></span>[Local documentation](plugins/ListFiles.txt) | <span class="para" id="7a28480"></span>Provides `=ListFiles` custom block | Artistic-2.0 |

----

## RakuDoc::Render plugins<div id="RakuDoc::Render_plugins"> </div>
<span class="para" id="210874c"></span>Customisation is provided by plugins. These are essentially classes of the form `RakuDoc::Plugins::XXX`, where **XXX** is the name of a plugin. 

<span class="para" id="d705461"></span>An illustrative example of the plugin class is for the Bulma CSS: 


```
use experimental :rakuast;
use RakuDoc::Templates;
use RakuDoc::PromiseStrings;
use RakuDoc::Render;

unit class RakuDoc::Plugin::Bulma;
has %.config = %(
    :name-space<bulma>,
	:license<Artistic-2.0>,
	:credit<https://https://bulma.io , MIT License>,
	:author<Richard Hainsworth, aka finanalyst>,
	:version<0.1.0>,
	:css-link(['href="https://cdn.jsdelivr.net/npm/bulma@1.0.1/css/bulma.min.css"',1],),
	:js-link(['src="https://rawgit.com/farzher/fuzzysort/master/fuzzysort.js"',1],),
    :js(['',2],), # 1st element is replaced in TWEAK
    :css([]),
);
submethod TWEAK {
    %!config<js>[0][0] = self.js-text;
    %!config<css>.append: [self.chyron-css,1], [ self.toc-css, 1];
}
method enable( RakuDoc::Processor:D $rdp ) {
    $rdp.add-templates( $.templates );
    $rdp.add-data( %!config<name-space>, %!config );
}
method templates {
    %(
        final => -> %prm, $tmpl {
            qq:to/PAGE/
            <!DOCTYPE html>
            ... <!- the rest of the template is omitted ->
            PAGE
        },
    )
}

```
<span class="para" id="4d5c451"></span>Each RP class must have a `%.config` attribute containing the information needed for the plugin. These are divided into mandatory keys and plugin-specific. 

<span class="para" id="ab374e8"></span>The mandatory key-value Pairs are: 



&nbsp;&nbsp;• <span class="para" id="c1fa930"></span>**license**, typically Artistic-2.0 (same as Raku), but may need to change if the plugin relies on software that has another license. 

  
&nbsp;&nbsp;• <span class="para" id="2ee0c4f"></span>**credits**, a source for the software, and the license its developers use. 

  
&nbsp;&nbsp;• <span class="para" id="f7ab4b4"></span>**version**, a version number for the plugin. 

  
&nbsp;&nbsp;• <span class="para" id="6505c7c"></span>**authors**, the author(s) of the plugin. 

  
&nbsp;&nbsp;• <span class="para" id="375cd71"></span>**name-space**, the name of the plugin's name-space within the RakuDoc-Processor instance. 

  
<span class="para" id="55397fa"></span>Some typical config fields are 



&nbsp;&nbsp;• block-name, the custom block that activates the plugin (a plugin may need only replace existing templates)  
&nbsp;&nbsp;• <span class="para" id="7d34ac2"></span>js-link, a list of `Str, Int $order` arrays. The string is placed in script tag in the head container of the web-page, the order is to ensure that when one js library must appear before another, that relation is created. Libraries with the same order appear in alphabetic order. 

  
&nbsp;&nbsp;• <span class="para" id="24929c6"></span>css-link, a list of `Str, Int $order` arrays. As above, but for CSS. Typically, CSS files must appear before the JS files they are associated with. All CSS files appear in head before JS files. 

  
<span class="para" id="667ac96"></span>All the elements of %.config are transferred to the RakuDoc::Processor object, and can be accessed in a template or callable, as `$tmpl.globals.data<plugin-name-space> ` (assuming that the plugin's config has `namespace =` plugin-name-space>). 

<span class="para" id="ad67172"></span>The `enable` method is mandatory. It is used to add data and templates to the `RakuDoc::Processor` object created by the `RakuDoc::To::HTML-Extra` renderer. 


----

## Customisability<div id="Customisability"> </div>
<span class="para" id="52269fb"></span>A user can create a custom plugin class (eg. `RakuDoc::Plugin::MyFunc`), which should then be installed in the environment. 

<span class="para" id="ee119fe"></span>The plugin can be enabled, for example by running the following in the repo root directory 


```
zef install -/precompile-install .


```
<span class="para" id="e804cf8"></span>Note the option `-/precompile-install` turning off the precompilation. This is because currently this distribution has some modules that depend on the RakuAST compiler, and some that use the standard Rakudo compiler. Precompilation does not handle the situation robustly. However, when a `use` or `require` is encountered, the relevant module is correctly compiled and loaded. 

<span class="para" id="6003ba3"></span>In order to enable `RakuDoc::Plugin::MyFunc`, create a file called `rakudoc-config.raku` in the current working directory. The file should be a raku program that yields a sequence or array, eg. 


```
use RakuDoc::To::HTML-Extra;
my @plugs = @HTML-Extra-plugins;
@plugs.append: 'MyFunc';

```
<span class="para" id="975b726"></span>`@HTML-Extra-plugins` contains the default plugins called by `RakuDoc::To::HTML-Extra`. 

<span class="para" id="e29ad64"></span>So the code above adds `MyFunc` to the list, and the plugin `RakuDoc::Plugin::MyFunc` will now be enabled after all of the default plugins called by `RakuDoc::To::HTML-Extra`. 

<span class="para" id="9a91532"></span>It is also possible to disable a default plugin by passing an array that does not contain the plugin to be disabled. Suppose it is desired to disable the SCSS plugin, then create (include) the following code in `rakudoc-config.raku`: 


```
use RakuDoc::To::HTML-Extra;
my @plugs = @HTML-Extra-plugins;
@plugs.grep({ $_ ne 'FontAwesome' });

```

----

## Credits<div id="Credits"> </div>
Richard Hainsworth, aka finanalyst




----

## VERSION<div id="VERSION_0"> </div>
v0.1.0





----

----

Rendered from docs/to/docs/to/HTML-Extra.rakudoc at 14:52 UTC on 2024-12-13

Source last modified at 14:51 UTC on 2024-12-13


