
# Raku Closure Templates

	Templates are Raku Subroutines

----

## Table of Contents

<a href="#Template_structure">Template structure</a>   
<a href="#The_C&lt;%prm&gt;_keys"><span class="para" id="7886d2f"></span>The `%prm` keys 

</a>   
<a href="#Elements_of_%prm_hash">Elements of %prm hash</a>   
<a href="#The_C&lt;%tml&gt;_Hash"><span class="para" id="cdb24cb"></span>The `%tml` Hash 

</a>   


<span class="para" id="ea18731"></span>In order to increase the speed of Pod6 Renderering, it was necessary for the templates to be compiled directly into subroutines. On one hand, this offers the opportunity for the templates to become much more flexible, as data can be manipulated, but on the other hand, the templates become more difficult to understand. 

<span class="para" id="8c9562b"></span>This distribution provides a tool for testing any set of templates against the [minimum template set](PodTemplates) and against custom templates whose structure is passed to it. 


----

## Template structure<div id="Template_structure"> </div>
<span class="para" id="85b49fe"></span>The templates are collected together into a hash of subroutines. With the sole exceptions of 'raw' and 'escaped', every subroutine has the form 


```
'template-name' => sub (%prm, %tml) { ... };
# the exceptions are
'escaped' => sub ( $s ) { $s.trans( ... , ... ); }
'raw' => sub ( $s ) { $s };


```
<span class="para" id="d45b366"></span>In fact, 'escaped' and 'raw' are treated specially by the `RakuClosureTemplater` Role. `GenericPod` provides the information to both as a hash with the text in `contents`. This text is extracted and sent to the `escaped` template as a string, and simply returned without template processing for `raw`. 

<span class="para" id="4605069"></span>The hash `%prm` contains the parameters to be included in the template, `%tml` contains the hash of the templates. 


----

## <span class="para" id="7886d2f"></span>The `%prm` keys 

<div id="The_C&lt;%prm&gt;_keys"> </div>
<span class="para" id="fd04163"></span>Each key may point to a `Str` scalar or a `Hash`. Any other structure will be rejected by the `test-templates.raku` tool, and will most probably throw an exception. In any case, `GenericPod` does not generate template requests with any other structure. 

<span class="para" id="581fe8a"></span>Within the top-level `Hash`, the keys may point to a `Str`, a `Hash` or an `Array`. The elements of these are in the table: 

----

## Elements of %prm hash<div id="Elements_of_%prm_hash"> </div>
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


----

## <span class="para" id="cdb24cb"></span>The `%tml` Hash 

<div id="The_C&lt;%tml&gt;_Hash"> </div>
<span class="para" id="fd9b073"></span>This is the hash of all the templates, except `raw` since it is anticipated `raw` will only be the same as the characters in the string themselves. `raw` is there to be an alternative to `escaped`. 

<span class="para" id="c7fc440"></span>The `escaped` template should be called as 


```
%tml<escaped>( ｢some string｣ )


```
<span class="para" id="90040ca"></span>Another template typically will be called as 


```
%tml<toc>( %prm, %tml)


```
<span class="para" id="2e256ca"></span>However, since a template is a normal **Raku** subroutine, `%prm` could be substituted with any `Hash`, eg., 


```
%tml<title>( { :title(｢Some arbitrary string｣) }, %tml ).


```
<span class="para" id="29e7173"></span>The presence of %tml is to allow for templates to call other templates. 





----

----

Rendered from docs/docs/RakuClosureTemplates.rakudoc at 14:52 UTC on 2024-12-13

Source last modified at 14:51 UTC on 2024-12-13


