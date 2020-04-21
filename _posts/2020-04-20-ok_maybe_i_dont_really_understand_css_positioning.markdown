---
layout: post
title:      "OK, Maybe I Don't Really Understand CSS Positioning"
date:       2020-04-21 01:56:43 +0000
permalink:  ok_maybe_i_dont_really_understand_css_positioning
---


After writing a long post on CSS positioning, I thought I had a good grasp of the five position types, and I was excited to apply my knowledge. One week later, I’m questioning whether I really understand positioning at all. 

The problem that introduced so many doubts seemed simple. I’d designed an app for the card game Magic: The Gathering which displayed many small card images in rows. The small text on the cards proved difficult to read, so I decided each image should grow whenever a user mouses over it. I used the hover property to accomplish this: 

```
  /* The Image Container (a div): /*
  .card {
  display: inline-block;
  float: left;
}
 /* The Image: /*
.card-img{
  height: 200px;
}
 /* The Image When You Mouse Over It: /*
.card-img:hover {
  height: 400px;
}
```

However, when the image of one card grew, the other cards all shifted aside to accommodate the larger image, leaving weird gaps on the page layout. I wanted the larger image to overlap the other cards instead of moving them to the side.

My First Plan: Relative Positioning

By default, all elements have position: static. If we want to move an element a certain number of pixels left, right, up or down, we can set that element’s position to relative and use the left, right, top and bottom properties to move it exactly where we want.

 Usually, the placement of relatively-positioned elements does not effect the placement of statically-positioned elements (for example, you can move a relatively-positioned element on top of a statically-positioned one – the static element will not move over to accommodate the relatively-positioned element). By changing an image’s position to “relative” when users hover over it, I figured I could stop the statically-positioned images from moving to accommodate the bigger element. The relatively-positioned element would now overlap the statically-positioned images around it instead of moving them around. I changed my CSS to look like this:
 
 ```
  /* Card Container (a div): /*
.card {
  display: inline-block;
  float: left;
}

 /* Image: /*
.card-img{
  height: 200px;
}

 /* Image on Hover: /*
.card-img:hover { 
   position: relative;
   height: 400px;
}
```

Did it work? 
No. When one element grew, the surrounding elements continued to move over.

Reflection:
At first, I did not understand my mistake, but after doing research and talking with a friend, I realized I had a slightly – but fundamentally – incorrect conception of relative positioning.

 When the page loads, the browser sets aside a block of space on the page for each element (including relatively positioned elements). Initially, I thought the block of space allocated for a relatively positioned element was a fixed rectangle that would not move or change size after the page loaded.

In fact, the browser re-calculates the amount of space to allocate for each element every time an element changes size. So, even though a relatively positioned element can’t directly affect static elements, it does affect the amount of empty space set aside for that element on the page. When the relatively positioned element gets bigger, the browser automatically gives the element more space; as a result, the elements next to it must move over.

New plan: Change the Z Index
Changing the z-index can move an element to a new “layer” that overlaps elements with a smaller z-index. Elements on the overlapping layer do not effect the position of elements on the layer below. I decided to change the z-index of .card-img:hover so an image that gets big overlaps the smaller surrounding images. Since the big image now occupies a different layer than the smaller images, I expected the smaller images not to move.

```
/* Card Container (a div): /*
.card {
  display: inline-block;
  float: left;
}

/* Image: /*
.card-img{
  position: relative; (Note: z-index does not work for statically positioned elements. To make sure the z-index had an effect, I made the image relatively positioned.)
  height: 200px;
  z-index: 1;
}

 /* Image on Hover: /*
.card-img:hover { 
   position: relative;
   height: 400px;
   z-index: 2;
}
```

Did it work?
No. When one element grew, the surrounding elements continued to move over.

Reflection:
I expected my plan to work, and am still surprised that changing the z index had no effect. I have only one tentative and incomplete theory: perhaps the image has the same depth as the surrounding elements while it’s growing, and only moves to a different “layer” after reaching a height of 400 px. In other words, maybe the z-index only changes when the image reaches a height of 400 px, and by that time the other elements have already moved over. However, if this was the case, I’d expect the other elements to move back to their original positions once the element reached a height of 400 px.

New Plan: Absolute Positioning

I found a stack overflow post relevant to my situation which recommended changing the position of the elements from relative to absolute. Relative positioning  places elements in reference to their position in the flow of the page, which could change as elements around them move, grow or shrink. However, absolute positioning fixes elements at a certain location in the browser window. (Note: absolute positioning can also fix elements in reference to a relatively-positioned parent element, but this does not apply here because none of the parent elements of the images are relatively positioned.) Absolute positioning ensures that if an image is initially positioned, say, 50 px left of the edge of the window, it will stay 50 px left of the edge of the window, no matter how the elements around it move or grow. I hoped that changing each image’s position to absolute would solve my problem. I changed the CSS to look like this: 

```
/* Card Container (a div): /*
.card {
  display: inline-block;
  float: left;
}
/* Image: /*
.card-img{
  position: absolute;
  height: 200px;
}
/* Image on Hover: /*
.card-img:hover {
  height: 400px;
}
```

Did it work?

Well, kind of…when one image grew, the surrounding images no longer moved. However, the positioning change had an unintended side effect: all the images stacked on top of each other and overlapped the text below them. 

My Reflections:
If all your elements are static, each element will occupy a block of space an not overlap other elements. Even relative elements, which can be placed on top of static elements, affect the flow of the page by taking up more or less space. In contrast, absolutely positioned elements do not affect each other at all. Each element is placed in reference to the window (or positioned parent element), not in reference to any other elements. As a result, only changing the window can change the arrangement of the elements; when one element changes size, it does not affect any other absolutely positioned elements. What I still don’t understand is why all the images clumped up a the top of the page. I would have expected the elements to stay in their original places (ie, where the images were placed before I set their position to a value other than static).

New Plan: Fixed Heights and Widths
I stumbled across a suggestion to change the width and height of the image containers. The height and width of the container defines exactly how much space each image takes up on the page, and prevents the cards from all overlapping each other.

Did it work? 
Again, kind of. The cards stopped overlapping each other, but still overlapped other elements on the page, such as text and buttons (even elements with clear: both).

Reflections: 
Interestingly, setting the clear property to “both” does not prevent the absolutely-positioned elements from overlapping the text, perhaps because the elements are no longer floating next to each other.

Solution: Fix the Size of the Containers 
On a whim, I decided to remove all the positioning from my elements, but keep the width and height constraints on the container. My CSS now looked like this:

```
/* Card Container (a div): /*
.card {
  display: inline-block;
  float: left;
  width: 100px;
  height: 240px;
}
/* Image: /*
.card-img{
  height: 200px;
}
/* Image on Hover: /*
.card-img:hover {
  height: 400px;
}
```

Did it work? 
Yes! The cards were now arrayed from left to right exactly as before, but when one card gets larger the others do not shift to the right.

Final reflections (…but why did it work?)
Normally, when the content inside a container grows, the container also grows to accommodate all the content. This was what happened earlier when we changed the size of the image – the enclosing div also grew to fit the new, larger image. It was the container changing size – not the image itself changing size – that caused the other elements to move over. When we set width and height constraints on the container, we stop it from growing and pushing other containers to the side. The realization that the containers were moving other containers aside (as opposed to images moving other images aside) put the problem in a new light. Once I stopped thinking about the minutae of CSS positioning and just thought about container elements moving around other container elements, the problem seemed clearer and easier to solve.  



