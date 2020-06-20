---
layout: post
title:      "In the CSS Weeds: Making Sense of The Flex Shrink Property"
date:       2020-06-20 18:20:12 +0000
permalink:  in_the_css_weeds_making_sense_of_the_flex_shrink_property
---


When the CSS "flex shrink" property was first explained to me, I quickly grasped the basic concept, and assumed that my general understanding would make "flex shrink" easy to implement. Basically, “flex shrink” determines whether and how fast CSS elements will shrink to fit inside the window. Simple, right? 

The flex shrink property only comes into effect in a limited set of situations. Before I could toy around with the flex shrink property, I first needed to engineer a situation in which the flex shrink property applied to multiple elements on a webpage, a task which proved tricky in subtle, but important ways.

Key Idea 1: Flex Shrink only applies to elements inside a container with display: flex.

Before creating elements with the "flex shrink" property, I first needed to construct a parent div with display: flex. That's because the “flex shrink” property only applies to elements inside a "flexbox" (a container with the display property set to "flex"). Only the elements inside a "flexbox" can grow and shrink as the window expands and contracts. Setting the "flex shrink" property for an element that's not inside a flexbox will have no effect. 

However, even when elements are arranged inside a flexbox, the "flex shrink" property still sometimes does not come into effect.

Key Idea 2: Flex Shrink only works when the elements become too big to fit in the container.

1. Flex shrink does not work with wrap.
Consider a flex container holding a row of items with the display property set to “wrap”. In this case, when the window is shrunk, items that no longer fit in the container will be “bumped down” onto the next line. The elements don’t need to shrink to fit inside the smaller window because they can simply be moved down; as a result, “flex shrink” will have no effect.

2. Flex shrink does not work if the flex basis is a percentage.
Similarly, consider two elements inside a flex box. Item 1’s flex-basis property is set to 30%, which means that item 1 will always take up 30% of the space in the flexbox. Since, by definition, item 1 only ever takes up 30% of its container, it never needs to shrink in order to fit inside the container. Since the "flex basis" property already allows the item to shrink and grow according to the size of the container, "flex shrink" never needs to come into effect. 

So, when does flex shrink matter? Flex shrink comes into effect when a) elements are arranged in rows or columns inside a flexbox (a parent element with display: flex). b) the elements do not wrap around when the container shrinks. c) each item has a set width and height. When the container becomes smaller than the set width or height, then – and only then – the item shrinks according to the value of “flex shrink.” But what, exactly, does the value of “flex shrink” specify? 

Key Idea 3: An element's Flex Shrink value is relative to the Flex Shrink values of the surrounding elements.

Setting “flex shrink” to zero prevents the element from shrinking as the window gets smaller. Instead of shrinking with the window, the content gets cropped when the window gets too small (setting “flex shrink” to zero is equivalent to not including the “flex shrink” property at all). Conversely, as long as the “flex shrink” property is set to an integer greater than zero, the element will shrink to fit inside the window.

If there is only one element inside the flex box, you might notice something strange: changing the value of “flex shrink” from 1 to 50 seems to have no effect at all. This is because the value of “flex shrink” is not an absolute value, like, say, height or width. Instead, flex shrink is a relative value: it only has meaning in comparison to the “flex shrink” value of the elements around it. If all the elements inside a flex box have the same “flex shrink” value, they will all shrink at the same rate. As a result, giving all elements the same “flex shrink” value preserves the proportions of the original webpage. In other words, if element 1 is originally twice as big as element 2, element 1 will still be twice as big as element 2 once the window is shrunk. 

Key Idea 4: Flex Shrink determines how fast each element shrinks, but does not determine the relative size of the elements.

After first learning about “flex shrink”, I assumed that if item 1’s “flex shrink” value was twice as large as item 2’s “flex shrink” value, then item 1 would eventually shrink to 1/2 the size of item 2. 

Let’s say the window shrinks by 900 px squared. If item 1’s “flex shrink” value is twice as large as item 2’s, twice as much area will be removed from item 1 than item 2. In this case, 900 px were removed in total, so item 1 would be shrunk by 600 px, while item 2 would only be shrunk by 300 px.

That said, if item 1’s initially 2000 px wide and item 2’s only 600 px wide, item 1 will still be larger than item 2 after the window contracts. (Since item 1 shrinks twice as fast as item 2, if the window keeps contracting, eventually item 1 will “out-shrink” item 2, but it will not shrink to be 1/2 the size of item 2.)

While I chose to focus this blog post on the flex property I found most difficult to grasp, many of the key points apply to other flex properties, too. For example, flex shrink's sister property, "flex grow", works almost exactly the same way as "flex shrink" but determines how elements will grow when the window is expanded. "Flexbox" is a CSS concept I initially felt overwhelmed by (there are so many properties, which can take on so many different types of values!). As I stumble towards a more complete understanding of flexbox, hope to break down the trickier concepts here, with all the nitty gritty details included.


