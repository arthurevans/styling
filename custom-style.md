#custom-style

`custom-style` is an extension of the native `<style>` element. To ensure that your styles behave according to the Shadow DOM v1 specifications in all browsers, use `custom-style` when you define document styles.
	
[Syntax](#Syntax)
[Why custom-style is needed](#Why-custom-style-is-needed)
[custom-style and custom properties](#custom-style-and-custom-properties)

##Syntax

###Polymer 2.x

````html
<custom-style>
<style>

</style>
</custom-style>
````

###Transitional/compatibility

``````html
<custom-style>
	<style is="custom-style">
	
	</style>
</custom-style>
``````

###Polymer 1.x

``````html
<style is="custom-style">

</style>
``````

You should only use `custom-style` to define styles for the main document. To define styles for an element's local DOM, just use a `<style>` block.

##Why custom-style is needed

Browsers that implement the current Shadow DOM v1 specifications will automatically encapsulate styles, scoping them to the elements in which they were defined.

Some browsers have not implemented the Shadow DOM v1 specifications. To make sure your apps and elements display correctly in these browsers, you'll need to use `custom-style` to ensure that styling information doesn't "leak" into the local DOM of your elements.

The custom-style extension enables a set of polyfills that ensure that styles in your apps and elements are encapsulated. When you use custom-style, the styles in your main document will behave as you would expect from the Shadow DOM v1 specifications, even in browsers that don't implement these specifications.

Examples:

https://jsbin.com/dugonihabe/edit

````html
<body>
  <style>
      p {
        color: red;
      }
    </style>
    <p>Paragraph A: I am in the main DOM. I am red.</p>
    <dom-module id="x-foo">
        <template>
            <p>Paragraph B: I am in the local DOM of x-foo. If your browser implements the Shadow DOM v1 specs, I am black; otherwise, I'm red.</p>
	    </template>
    </dom-module>
	<x-foo></x-foo>   
	<script>
      window.addEventListener('WebComponentsReady', 
                              function() {
        Polymer({is: 'x-foo'})
      });
    </script>
</body>
</html>
`````

https://jsbin.com/cupeqijika/edit

`````html
<body>
  <custom-style>
  <style>
      p {
        color: red;
      }
    </style>
  </custom-style>
    <p>Paragraph A: I am in the main DOM. I am red.</p>
    <dom-module id="x-foo">
        <template>
            <p>Paragraph B: I am in the local DOM of x-foo. I am black on all browsers.</p>
	    </template>
    </dom-module>
	<x-foo></x-foo>   
	<script>
      window.addEventListener('WebComponentsReady', 
                              function() {
        Polymer({is: 'x-foo'})
      });
    </script>
</body>
</html>
``````
##custom-style and custom properties

In a `custom-style`, you can define custom properties to be used by Polymer's shim for cross-scope styling. Then, use an `include` attribute in a `custom-style` to share style data between multiple custom-style elements.

https://jsbin.com/cupeqijika/edit?html,output

`````html
  <body>
    <dom-module id="my-colors">
      <template>
         <style>
           :root {
              --my-color: #ff0000;
           }
         </style>
      </template>
    </dom-module>
     <custom-style include="my-colors">
       <style>
         p {
           color: var(--my-color);
         }
       </style>
     </custom-style>  
     <p>I should be red</p>
	 <script>
       window.addEventListener('WebComponentsReady', 
                              function() {
        Polymer({is: 'my-colors'})
       });
     </script>
  </body>
</html>
``````

