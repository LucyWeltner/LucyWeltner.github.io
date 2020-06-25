---
layout: post
title:      "Diving into Data with D3"
date:       2020-06-25 16:57:33 +0000
permalink:  diving_into_data_with_d3
---


Recently, I decided to turn my birdwatching hobby into a more serious citizen science project. For the last four months, I’ve walked the same trail at the same time each week, taking note of all the birds I see and hear. By the end of May, I had notebooks full of times, dates, temperatures and lists of bird species. To turn all that raw information into a comprehensible format, I built a React application to display my data. I created bar graphs composed of SVG elements to display the number of species and individual birds seen on each day. I added two interactive dropdown menus to display the breakdown of sightings on a selected day, and the distribution of a selected species over the past three months. (You can see the project here: https://github.com/LucyWeltner/birdwatching_data)

I created all my bar graphs with React and SVG elements. I passed down an array of data as a prop to each bar graph, and the graph class mapped each number to the height of a bar. This strategy worked well for me: I was able to create scalable graphs which changed dynamically as I added and removed data. However, midway through building the website, I learned about a Javascript library, D3, which could accomplish the same task potentially more efficiently. I set out to teach myself the basics of D3, and ended up finding whole new ways of creating bar graphs.

 Most of D3’s functions take in an array of numbers and output a group of svg elements with attributes related to the inputted numbers. Just like Javascript’s built-in map function takes each number in an array, performs an operation on that number, and puts the result into a different array, most of D3’s functions take each number in an array, perform an operation on that number, and assign the result to an attribute of a html element. For example, using D3, a programmer can take an array of 4 numbers, make 4 html elements, AND map each number to the width of the corresponding html element. In other words, you could turn the array [6, 9, 14, 2] into four svg rectangles with widths 6, 9, 14 and 2. 

Now that I’ve given an overview of how D3 operates, I’ll explain a few of D3’s most commonly used functions: 

The select() function: D3’s select function takes in a CSS selector and returns a html element. For example, consider the code let svg = d3.select(“#svg”). In this case, select returns the html element with id “svg” (which, as you might guess, is a svg). While select() always returns only one element, the selectAll function returns the set of all CSS elements that match the query.

The attr() function: This is where D3’s unique capabilities begin to shine. The attr function is called on an html element (or elements) to change an attribute of that element(s). In its most basic form, attr takes in two arguments: the attribute you want to change, and the new value of that attribute. For example, this line of code would color all rectangles light blue: 

```
d3.selectAll(“rect”).attr("fill", “lightblue")
```

However, attr() can be called on an array of data. In this the second argument of attr() will be an iterator function (like map or forEach) that takes each number in the array and assigns that number to the specified attribute. For example: 

```
d3.selectAll("rect").data([4, 30, 7, 18]).attr("width", number => number*5).attr(“fill”, “purple”)
```


First, the selectAll() returns all rectangles, then the data() function “joins” the rectangles with the array of data. We then call attr(), which takes each number in the array, multiplies it by 5, and assigns the result to the width of a rectangle. The end result is that the first four rectangles on the page now have heights 4, 30, 7 and 18. If there are more than 4 rectangles on the page, the remaining rectangles will not be altered. If there are only 3 rectangles on the page, they will have widths 4, 30 and 7; the last data point, 18, will not be represented on the DOM.

Assuming you already have five rectangles properly placed on the DOM, the result will look something like this: 

![bar graph](https://live.staticflickr.com/65535/50044570712_ffb3304091_b.jpg/assets/client-code.js)

I noticed that attr() iterates over both the rectangles and the data at the same time (ie, it assigns the first number in the array to the width of the first rectangle, the second number in the array to the second rectangle, etc). I suspect this is only possible because data() returns an object that associates the first rectangle with the first number in the array, the second rectangle with the second number in the array, etc. I envisioned data()’s return value as basically a fancy array of arrays that attr() iterated over: 

[[first rectangle, first number], [second rectangle, second number]....[nth rectangle, nth number]]

However, this is just a model; I couldn’t find any clear explanations of how D3 really works under the hood.

There are many D3 functions that work the same way. For example, text() can also be called after data(); like attr, simultaneously iterates over the html elements and the array of data, changing the text contained in each element to the corresponding number in the array.

Until now, we’ve only discussed selecting elements that already exist on the DOM, then changing their attributes. But what if we want to create elements that don’t necessarily exist? What if we have a data set containing hundreds of numbers, and can’t be bothered to create a rectangle for each number? Fortunately, D3 has that covered. D3’s “enter()” function returns an array containing the extra data that hasn’t been mapped to a svg element. To use our former example, if there were only three rectangles on the DOM and we inputted the array [4, 15, 6, 27, 3], the enter() function would return [27, 3]. We can then call the append() function, which creates an html element for each number in the array of data. 

Let’s say we have mapped our data to the width of a group of rectangles. We now have  a nice looking bar chart, and we want to add labels over the bars. We write this code:

```
d3.selectAll("#svg > text").data([4, 30, 7, 18, 4]).text(number => number + " things").attr("fill", “lightblue").enter().append("text").attr("y", (number, index) => 		15+index*25).text(number => number + " things").attr("fill", “lightblue")
```

It’s a bit complex, so let’s break it down and just look at the first line: 

```
d3.selectAll("#svg > text").data([4, 30, 7, 18, 4]).text(number => number + " things").attr("fill", “lightblue")
```

* d3.selectAll("#svg > text") selects all the text elements contained inside the svg box. 
* data([4, 30, 7, 18, 4]) joins the array [4, 15, 6, 27, 3] with the selected elements. 
* text(number => number + " things") simultaneously iterates over the text elements and the array of numbers, assigning each number (plus the word “things”) to the text of the corresponding svg element. 
* attr("fill", “lightblue") colors the text light blue by changing the value of the fill attribute of each element.


If there are 5 text elements to match the 5 numbers in the array, the result should look like this: 

![bar graph with labels](https://live.staticflickr.com/65535/50043750753_0328647390_c.jpg)

However, let’s say there are only three text elements on the dom, overlapping the first three rectangles. In this case, the fourth and fifth bars would not have labels. We must add the next line to solve the problem: 

```
enter().append("text").attr("y", (number, index) => 75+index*25).text(number => number + "things").attr("fill", “lightblue")
```


enter() will return an array containing the unused data: in this case, [27, 3]. The append() function creates two text boxes – one for each element in the array. Now, we must place the two new text boxes below the three that already exist. We can do this by setting the y attribute of each new element (the y attribute determines how far down the webpage the element will be placed). The placement of the element depends on the index of the array: we want the first element we create to be closest to the top of the page, the second element to be further down the page, and the third element to be even further down the page. The formula index*25 ensures the first element (associated with index 0) will be placed at the top, the second will be placed 25 pixels below the first, the third will be placed 25 below the second, etc.

Now, we simply need to add the text to each new element, just like we added text to the preexisting elements:

```
text(number => number + "things").attr("fill", “lightblue")
```

We’ve successfully created a bar graph with labels using D3! 

D3 was fun to learn about and use, but...was it really that much easier or more efficient than creating bar graphs with React? In short, no, I don’t think so. However, I have only scratched the surface of what D3 can do, and I suspect that with advanced D3 I can create much more complicated graphics than I could with React. I’ve also heard D3 allows users to create animations – something I definitely would not be able to do with React. I look forward to venturing further into the world of D3!

