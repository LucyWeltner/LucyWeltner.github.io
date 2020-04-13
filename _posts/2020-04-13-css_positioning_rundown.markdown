---
layout: post
title:      "CSS Positioning Rundown"
date:       2020-04-13 17:52:04 +0000
permalink:  css_positioning_rundown
---

When I first started coding, I treated CSS as an afterthought, something I stumbled through at the end of the project to make the result look presentable. Because I didn't have a great conceptual understanding of CSS, I ended up learning about concepts like positioning through trial and error, a messy process that often left me frustrated. A couple months ago, I finally decided to put in the time upfront to learn CSS fully and deeply. Partially to solidify my own understanding and partially to help out anyone else trying to make sense of CSS fundamentals, I've constructed a kind of "cheat sheet" that reviews all the possible values of the "position" property: static, relative, absolute, fixed and sticky.

When I started to learn about element positioning, it helped to think about the a block of space assigned to each element on a page. For instance, if a page has four divs floating next to each other, each div will occupy a rectangle of space (you can change the width and height of each div using – you guessed it – the width and height properties). Each rectangle of space gets lined up so the divs do not overlap. Run this code to see an example. Each div is colored in so you can see exactly how much space it takes up: 

HTML:
```<html>
	<body>
		<div class="floats"></div>
		<div class="floats"></div>
		<div class="floats"></div>
		<div class="floats"></div>
	</body>
</html>
```


CSS: 

```div.floats {
	background: blue;
	border: 3px solid black;
	display: inline-block;
	float: left;
	height: 100px;
	width: 70px;
}
```

Image (Result of Code):

![Statically Positioned Divs](https://flic.kr/p/2iQ1nbq)

<html><h3>Relative Positioning</h3></html>

By default, the position property of every element is "static", which means the element will stay in its original rectangle and cannot be moved around. However, when we set the position property of one of the divs to relative, we can now move the div around with that original block as a reference point. Using the “right”, “left”, “top” and “bottom” properties, we can now position the element right, left, above and below the space it originally occupied.

(Importantly, if you do not set the position property, the position will be "static" and the top, bottom, left and right properties will not have any effect. I like to think of elements as automatically locked into place until we specify that their position can change.)

To clarify: an element with position: relative and right: 40px does not mean the element was moved 40 pixels to the right. The “right” property does not move elements to the right; instead, it specifies how many pixels away the element is from the right-hand edge of it’s original block (or, in the case of absolute positioning, a parent element…more on that later). Setting a larger value of “right” means your relatively positioned element will be farther away from the right hand side its original position - effectively moving the element left. The same is true for the “left”, “top” and “bottom” properties. If you want to actually move the element, say, 40 px right, you can either move it 40 px away from the left hand side of its original position:
	left: 40px
Or -40 pixels away from the right hand side of its original position:
	right: -40px
(When I set “right” to a negative value, I imagine number line passing through the element, with 0 placed at the right hand edge of the element’s original position.)

Try this code to see an example; it’s very similar to the previous code, but we’ve moved the fourth div (colored red) 20 px to the left and 50 px below its original position: 
HTML:

```
<html>
	<body>
		<div class="floats"></div>
		<div class="floats"></div>
		<div class="floats"></div>
		<div class="floats" id="rel"></div>
		<div class="floats"></div>
	</body>
</html>
```

CSS:

```
div.floats {
	background: blue;
	border: 3px solid black;
	display: inline-block;
	float: left;
	height: 100px;
	width: 70px;
}

div#rel{
	background: red;
	position: relative;
	right: 20px;
	top: 50px;
}
```

Image of Result: 

![4 Statically Positioned Divs, 1 Relatively Positioned Div](https://flic.kr/p/2iQ2ZPp)

It’s key to remember that relatively positioned elements don’t effect the static elements on the page. If you shift a relatively positioned element to the right, the other, static elements won’t move aside in response. Instead, the relatively positioned element will overlap any static elements on its right, and leave a gap where it was originally positioned.

<html><h3>Absolute Positioning</h3></html>

While relative positioning positions an element relative to the window, absolute positioning allows you to position an element relative to another element. 
A static element with “right: 50px” will be placed 50 px away from its parent element, instead of 50 px away from the right hand side of the screen. There’s one small catch: elements can’t be positioned relative to parent elements with position static. In order to use absolute positioning, the parent element must have position set to relative, absolute, fixed or sticky (more on fixed and sticky in a second).

Let’s go back to our code, and add a purple absolutely positioned div inside our red, relatively positioned div. In this case, the absolutely positioned div is 30 px away from the right hand side of its parent element (the red div) and 10 px away from the bottom of its parent element: 

HTML:

```
<html>
	<body>
		<div class="floats"></div>
		<div class="floats"></div>
		<div class="floats"></div>
		<div class="floats" id="rel">
			<div id="abs"></div>
		</div>
		<div class="floats"></div>
	</body>
</html>
```

CSS: 

```
div.floats {
	background: blue;
	border: 3px solid black;
	display: inline-block;
	float: left;
	height: 100px;
	width: 70px;
}

div#rel{
	background: red;
	position: relative;
	right: 20px;
	top: 50px;
}

div#abs{
	position: absolute;
	bottom: 10px;
	left: 30px;
	background: purple;
	height: 50px;
	width: 50px;
	border: 3px solid black;
}
```

Image Result: 

![Statically, Relatively and Absolutely Positioned Divs](https://flic.kr/p/2iQ1mdU)

<html><h3>Fixed Positioning</h3></html>

While relative positioning places an element relative to itself, and absolutely positioning places an element relative to its parent, fixed position places the element relative to the browser window. The bottom, top, left and right properties now specify how far away the element is from the bottom, top, left side and right side of the browser window. If a user scrolls down, the fixed elements will remain “stuck” a certain distance from the top of the user’s current window, and appear not to move. For example, a header that remains at the top of your browser window – no matter how far you scroll down – has position: fixed; top: 0px to ensure that the element always displays a the top of the window.

<html><h3>Sticky Positioning</h3></html>

Initially, a sticky element looks and behaves like a relatively positioned element. Like all unfixed elements, sticky elements will, at first, move up towards the top of the page as the user scrolls down. However, once the sticky element is a certain number of pixels below the top of the page, the element will “stick” there and stop moving. The “top” property dictates when the element sticks in place; for example, a sticky element with top: 30px will stop moving once the element’s 30 px away from the top of the window. At that point, the sticky element will behave like a fixed element with top: 30px; the user can continue to scroll down, but the sticky element will not move.

Sticky elements are somewhat confusing to discuss without visuals, so I’ll give you some sample code to mess around with. Notice how the sticky element stops moving as soon it gets 30 px below the top of the window.

HTML: 

```
<html>
	<body>
		<div class="scroll">
			<span>. content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>. content</span>
			<br />
			<span>. content</span>
			<br />
			<span>. content</span>
			<br />
			<span>.  content</span>
			<br />
			<div id="sticky">Sticky Div</div>
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
			<br />
			<span>.  content</span>
		</div>
	</body>
</html>
```

CSS: 

```
div.scroll {
	width: 150px;
	height: 200px;
	position: relative;
	overflow-y: auto;
	background: yellow;
	border: 2px solid black;
}

div#sticky {
	height: 30px;
	width: 100px;
	background: blue;
	position: sticky;
	top: 10px;
	left: 25px;
}
```

Image of The Page Before You Scroll: 

![Sticky div before scrolling](https://flic.kr/p/2iQ2ZVG)

Image of The Page After You Scroll (the div "sticks" 30px below the top of the page): 

![Sticky div after scrolling](https://flic.kr/p/2iQ1kg8)


