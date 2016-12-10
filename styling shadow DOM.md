# Encapsulated style with Polymer 2.0 and shadow DOM v1

When you create a custom element with the Polymer API, Polymer attaches a shadow root to it and copies in the contents of the template you provide for it. The HTML elements in your template become children within your custom element's shadow DOM. Shadow DOM provides a mechanism for encapsulation, meaning that elements inside the shadow DOM are not accessible to styling rules external to the shadow DOM. Likewise, styling rules inside the shadow DOM can't "leak" out to affect elements outside the shadow DOM.

``````
<dom-module id="x-foo">
	<template>
		<p>I'm a shadow DOM child element of x-foo.</p>
	</template>
</dom-module>
<script>Polymer({ is: 'x-foo'})</script>
<x-foo></x-foo> 
``````

Shadow DOM permits encapsulation of styling rules for custom elements. You can freely define styling information for your element like fonts, text colors and classes, without fear of the styles or class names applying outside the scope of your element. 

````````
<style>
.myclass {
	color:blue;
}
</style>
<dom-module id="x-foo">
	<template>
		<style>
			p {
				color:green;
			}
			.myclass {
				color:red;
			}
		</style>
		<p>I'm a shadow DOM child element of x-foo.</p>
	</template>
</dom-module>
<script>Polymer({ is: 'x-foo'})</script>
<p class="myclass">I have nothing to do with x-foo. Because of encapsulation, x-foo's class names won't leak to me.</p>
``````

For a detailed explanation of shadow DOM as it applies to Polymer, see (Arthur's article).

For an exploration of the shadow DOM v1 API, see (Eric Bidelman's article).

When used in an HTML document, your element will still inherit any styling information that applies to its parent element:

``````
<head>
<style>
	p { 
		font-family: sans-serif;
		color:blue;
	}
</style>
</head>
<body>
<dom-module id="x-foo">
	<template>
		<div>I inherit styles from x-foo's parent in the light DOM. I'm also sans-serif and blue.</div>
	</template>
</dom-module>
<script>Polymer({ is: 'x-foo'})</script>
<p>I'm sans-serif and blue.</p>
<p><x-foo></x-foo></p>
</body>
````````

Styles declared inside shadow DOM will override styles declared outside of it:

````````
  <style>
    p { 
	  font-family: sans-serif;
	  color:blue;
    }
  </style>
  </head>
  <body>
    <dom-module id="x-foo">
	  <template>
        <style>
          p {
            font-family: sans-serif;
            color:green;
          }
        </style>
		<p>I'm green.</p>
	  </template>
    </dom-module>
    <p>I'm blue.</p>
	<p><x-foo></x-foo></p>    
  </body>
  <script>
  Polymer({ 
    is: 'x-foo'
  })
  </script>
  </body>
``````

## Style the host element

The element to which shadow DOM is attached is known as the host. The shadow DOM v1 API allows you to style the host itself:

````````
    <dom-module id="x-foo">
	  <template>
        <style>
          :host {
            font-family: sans-serif;
            color:green;
          }
        </style>
		<p>I'm green.</p>
		<div>I'm green too.</div>
		<span>We're all green...</span>
	  </template>
    </dom-module>
	<x-foo></x-foo>    
````````

You can use CSS selectors to determine when and how to style the host:

```````
 <dom-module id="x-foo">
  <template>
     <style>
       :host { font-family: sans-serif; }
	   :host(.blue) {color: blue;}
	   :host(.red) {color: red;}
	   :host(:hover) {color: green;}
     </style>
 	 <p>Hi, from x-foo!</p>
  </template>
 </dom-module>
<x-foo class="blue"></x-foo>
<x-foo class="red"></x-foo>
<x-foo></x-foo>
````````

The `:host-context()` syntax allows you to style the host by selecting based on qualities of its parent element:

```````
  <dom-module id="x-foo">
  <template>
     <style>
	   :host-context(.red) {color:red;}
     </style>
 	 <p>Hi, from x-foo!</p>
  </template>
 </dom-module>
 <div class="red">
 	<x-foo></x-foo>
 </div>
```````

Descendant selectors after `:host` match elements in the shadow DOM. In this example, the CSS selector applies to any `p` element in the shadow tree if the host has class "warning":

`````````
 <dom-module id="x-foo">
  <template>
     <style>
	  :host(.warning) p {
		  color: red;
	  }
     </style>
 	 <p>Hi, from x-foo!</p>
	 <div>:D</div>
  </template>
 </dom-module>
<x-foo class="warning"></x-foo>
`````````

## Style slotted content

You can create slots in an element's template that are populated at runtime using this syntax:

``````
<dom-module id="x-foo">
	<template>
		<h1>
			<slot name='heading1'></slot>
		</h1>
		<p>
			<slot name='para'></slot>
		</p>
	</template>
</dom-module>

<x-foo>
	<div slot="heading1">Heading 1</div>
	<div slot="para">Paragraph text</div>
</x-foo>
````````

To style slotted content, use the ::slotted(<compound-selector>) syntax.

::slotted(*) selects all slotted content:

````````````
<dom-module id="x-foo">
	<template>
      <style>
        ::slotted(*) {
          font-family: sans-serif;
          color:green;
        }
      </style>
		<h1>
			<div><slot name='heading1'></slot></div>
		</h1>
		<p>
			<slot name='para'></slot>
		</p>
	</template>
</dom-module>

<x-foo>
	<div slot="heading1">Heading 1. I'm green.</div>
	<div slot="para">Paragraph text. I'm green too.</div>
</x-foo>    
````````````

You can select by element type:

````````
<dom-module id="x-foo">
	<template>
      <style>
        ::slotted(h1) {
          font-family: sans-serif;
          color:green;
        }
        ::slotted(p) { 
          font-family: sans-serif;
          color:blue;
	  }
      </style>	  
			<slot name='heading1'></slot>
			<slot name='para'></slot>
	</template>
</dom-module>
<x-foo>
	<h1 slot="heading1">Heading 1. I'm green.</h1>
	<p slot="para">Paragraph text. I'm blue.</p>
</x-foo>    
````````

You can select by class:

``````
<dom-module id="x-foo">
	<template>
      <style>
        ::slotted(.green) {
          color:green;
        }
      </style>
		<p>
			<slot name='para1'></slot>
		</p>
		<p>
			<slot name='para2'></slot>
		</p>
	</template>
</dom-module>

<x-foo>
	<div slot="para1" class="green">I'm green!</div>
	<div slot="para1">I'm not green.</div>
	<div slot="para2" class="green">I'm green too.</div>
	<div slot="para2">I'm not green.</div>
</x-foo>  
``````  

And you can select by slot name:

``````````
<dom-module id="x-foo">
	<template>
      <style>
        slot[name='para1']::slotted(*) {
          color:green;
        }
      </style>
		<p>
			<slot name='para1'></slot>
		</p>
		<p>
			<slot name='para2'></slot>
		</p>
	</template>
</dom-module>

<x-foo>
	<div slot="para1">I'm green!</div>
	<div slot="para2">I'm not green.</div>
</x-foo>  
````````

For more examples, see Rob Dodson's Shadow DOM CSS Cheat Sheet.

<!-- jsbin template>


<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
  <base href="https://polygit.org//shadydom+webcomponents+:master/shadycss+webcomponents+:master/custom-elements+webcomponents+:master/webcomponentsjs+:v1/polymer+:2.0-preview/paper*+:2.0-preview/iron*+:2.0-preview/app*+:2.0-preview/components/">
  <script src="webcomponentsjs/webcomponents.js"></script>
  <link rel="import" href="polymer/polymer.html">
</head>
<body>
    <dom-module id="x-foo">
        <template>
        <style>
			
        </style>
	  </template>
    </dom-module>
	<x-foo></x-foo>   
	<script>Polymer({is: 'x-foo'})</script>
</body>
</html>

---->
