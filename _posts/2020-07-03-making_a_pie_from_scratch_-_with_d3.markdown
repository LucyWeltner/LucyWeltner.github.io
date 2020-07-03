---
layout: post
title:      "Making a Pie From Scratch - With D3"
date:       2020-07-03 21:23:02 +0000
permalink:  making_a_pie_from_scratch_-_with_d3
---


How to create a pie chart in d3() from scratch. d3 provides two commands to make it easier to create pie charts: d3.pie() and d3.arc(). First, let’s explore d3.pie().

Essentially, the d3.pie() function takes in an array of data, and outputs instructions for how, exactly, to turn that data into a pie chart: how many pie wedges to make, how big each pie wedge should be, and how to arrange the pie wedges next to each other in a circle. More specifically, the d3.pie() function maps each data point to an object; each object contains information about the wedge of the pie chart representing that data point. For example, the startAngle property specifies how far to “turn” around the pie chart’s center before we draw the wedge, and endAngle specifies how far we’ve turned once we’re done drawing that wedge. 

As I tried to wrap my head around the inner workings of d3.pie(), I began to wonder: is a complex function like d3.pie() really necessary to create something as simple as a pie chart? As a math student and teacher, shouldn’t I be able to figure out how to make a pie chart without needing to use a complicated-seeming function? Moreover, if my middle school students all started using a computer program to create pie charts, I would expect them to at least understand how the program calculates angles, and ideally be able to walk through all the steps the program takes. Shouldn’t I hold myself to as high a standard? 

I set out to create my own version of d3.pie(). Like the original function, I decided my function would take in an array of data points and output an array of objects with properties like startAngle, endAngle, data, and index. I started with what I already knew about creating pie charts: how to calculate the angle of each wedge. First, I would sum all the data points:

```
        let rawdata2 = [4, 50, 20, 8]
        let sum = rawdata2.reduce((total, number) => total + number)

```

In this case, the sum is 82, so the circle should be divided into 82nds. The first 4/82nds of the circle make up the first wedge, the next 50/82nds make up the second wedge, and so on. Since a full circle (ie, a 360 degree turn) is composed of 2π radians, the first wedge should encompass 4/82ths of those 2π radians: 4/82 * 2π =  about .306 radians. Therefore, the first wedge should have an angle of .306 radians. I wrote a function to map each data point to the angle of its wedge in the pie chart:

```
rawdata2.map(dataPt => ((dataPt/sum)*(Math.PI*2))
```

But how to incorporate the information about the size of each wedge into the d3.pie() object? The object contains properties like startAngle and endAngle, but no sizeOfAngle property. How could I use the map function and the size of the angle to find the startAngle and the endAngle?

Normally, map functions deal with each data point individually, and require no information about the rest of the array. However, setting startAngle and endAngle requires knowing about the other wedges in the pie chart (ie, to know where wedge #3 starts, I need to know where wedge #2 ends). I initialized the variable totalDegrees, to keep a running total of how far around the circle the pie chart extends. Each time I ran the function, I added the size of the current angle to the total. I then used the total to calculate the starting and ending angle for each wedge.

```
        let totalDegrees = 0
        let data2 = rawdata2.map((number, index) => {
				let currentAngleSize = (number/sum)*2*Math.PI 
				totalDegrees += currentAngleSize
				return {data: number, index: index, endAngle: totalDegrees, startAngle: totalDegrees - currentAngleSize}
        })

```

For example, let’s say we’re looking at the 3rd data point. The 3rd data point’s wedge has an angle of 2/82 x 2π = 1.6 radians. (So 1.6 = currentAngleSize.) 

Let’s say the first two wedges combined have an angle of 0.4 radians (so, at the beginning of the function totalDegrees = 0.4). 

On line 4, we add the current angle size to totalDegrees, so the new value of totalDegrees is 2. 
In other words, if we start at 0 radians, after drawing all three wedges we will have turned 2 radians around the circle. So, the ending angle of the 3rd wedge = 2 radians.

The starting angle is how far we’ve turned around the circle before adding the third wedge. We can calculate this pretty easily by taking totalDegrees and subtracting the current angle size. (In this case, the starting angle’s 2 - 1.6 =  0.4).

Now, we simply put all the properties into an object. In addition to the starting and ending angles, we include the value of the data point in the data property, the index of the data point in the index property, and a label for each wedge in the value property (think of this as similar to the value attribute for html input fields). The resulting map function returns an array of objects with all the same properties as d3.pie().

Yay! 

However, we are not yet done. We need a function that will use the object we’ve created to actually draw a curve. Fortunately, SVG includes a “path” element. 

If you’ve ever tried to draw a picture using the “pen” tool provided by a word processor, you probably already have a mental image of a path element. Recall how, after you click and drag with the “pen” to make lines and curves, the word processor places a little box around your drawing. You can think of that little box as the “path” element, and the drawing inside the box as the “d” attribute of the path element. 

How can a single attribute – ”d” – specify something as complex as a drawing? In order to capture the complexity of a shape, the “d” attribute contains a series of directions, written in shorthand, that specify exactly how to draw the shape by moving between different points on the canvas. The “d” attribute represents the svg container as a graph. In the top left hand corner, the x and y coordinates are both zero; as you move down, y grows, and as you move right, x grows. 

Using this coordinate system and a series of commands, you can make drawings. For instance, “M 30 25 L 40 55” means “first, put down your pen at the point (30, 25) (30 pixels down and 25 pixels left), then make a line to the point (40, 55).”

 Fortunately, the shorthand language – also called the “SVG Mini-Language” – also has a command for drawing a circular arc. To specify which arc we want to draw, we input the point where we want the computer to begin drawing the arc, the radius of the circle, and (sound familiar?) the startAngle and endAngle. 

Therefore, all we need is a function that will take in the d3.pie() objects we’ve created and output each as a series of commands in SVG Mini-Language. Fortunately, d3 provides a function especially for this purpose. 

The function d3.arc() turns each object created by d3.pie() into a description of an arc in “SVG mini language.” 

Each pie() object specifies the starting point, start angle and end angle of a wedge, but does not specify the radius of the circle. Since the “d” attribute requires the radius of the circle, we tell the arc() function to specify a radius of 150 degrees for each object using this code: 

```
d3.arc().innerRadius(0).outerRadius(150)
```

We now need to a) join each piece of data created by d3.pie() to a path object, b) use the info in each d3.pie() object to create a description of an arc using d3.arc(), and c) assign that description to the “d” element of each path. Here’s the code I used to accomplish this: 

```
d3.selectAll(“path”).data(data2).attr(“d”, d3.arc().innerRadius(0).outerRadius(150))
```

Let’s break it down a little: 
* d3.selectAll(“path”).data(data2) joins each data object to a path object.
* d3.arc().innerRadius(0).outerRadius(150) creates a description of an arc in SVG shorthand from each piece of data
* attr(“d”, d3.arc().innerRadius(0).outerRadius(150)) assigns each piece of SVG shorthand to the “d” attribute of a path.

Now, hopefully, you’ve successfully created a pie chart in d3! You can see for the full code I used to create my own bar and pie charts in D3 here: https://github.com/LucyWeltner/ExperimentingWithD3

 (it’s a bit more complex than the stripped-back example code in this blog post). I’m still working on properly placing labels inside the pie chart – perhaps look forward to a description of how to do that in a future post.





