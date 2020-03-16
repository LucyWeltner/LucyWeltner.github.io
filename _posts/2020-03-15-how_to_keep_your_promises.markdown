---
layout: post
title:      "How to Keep Your Promises"
date:       2020-03-16 02:26:04 +0000
permalink:  how_to_keep_your_promises
---

Last week, I designed a one-page application in Javascript for helping people ID plants. The application fetched plant data from an API and rendered plant objects to the screen. When I started my project, I knew my code relied on Javascript “promise objects”. I knew that promise objects receive and manipulate data from other webpages. I knew that when I wrote statements like fetch(‘http://localhost/plants/13').then(response => console.log(response), I created promise objects. Other than that, I didn’t know much else about promises, the objects that allowed my website to do fundamental actions like get and edit data. I set off on a quest to learn more about promises, and ended up writing code to create promises from scratch. Now, I hope I can relate what I’ve learned clearly and concisely, so others, too, can understand the deep magic of promises. Like onions and ogres, promises have layers. Let's peel them back, one by one. 

Level 1: The browser
When you write fetch(‘http://localhost/plants/13').then(response => console.log(response), the browser makes a new Promise object. The Promise object takes in one argument, a function we’ll call the “handler”…

 Level 2: Within The Promise Object
The constructor sets attributes for the new promise (for example, the attribute “status” is set to “pending”, the “value” attribute is set to “undefined”, and the attribute “thenCallbacks” is set to an empty array…more on that later). Then, within the constructor, the handler function is called and passed two arguments, which are also functions (more on that later)…

Level 3: Within the Handler Function The handler function creates and sends a HTTP request to the URL provided in the fetch statement. For example, if you wrote “fetch(‘http://localhost/plants/13')", the function sends a HTTP request to the address http://localhost/plants/13. The function then receives the HTTP response from http://localhost/plants/13 and uses an if statement to see whether the response contains the data from http://localhost/plants/13. If the request was successful, the status of the HTTP response will equal 200, and we can go….

Level 4: Within the If Statement
Remember the two other functions passed into the handler function (the one we’re inside now). These two functions are called the “rejector” and the “resolver,” and they’re defined within the promise object. If the request successfully fetched the data, we pass all that data to the “resolver” function.

Level 5: Within the Resolver Function
First, the response object sets the promise’s status to “resolved” and the promise’s value to the data in the response (basically just a big string). (Remember, initially the promise’s status was “pending” and the promise’s value was “undefined”).
Now, something magical happens. Until now, we’ve focused only on fetching data. However, a promise doesn’t just fetch data from a URL; a promise also manipulates that data using functions passed into “then” functions. When you write fetch(“http://localhost/plants/13").then(data => console.log(data)) the promise object both fetches the data AND console logs the data it receives. The responder function does not just receive the data; it also passes the data into the functions provided in .then calls.
We’ve gone too far down. The next step requires us to zoom out and remember the asynchronous nature of Javascript. With Javascript, we can evaluate the .then statements chained onto our fetch request before the responder function gets called, before we even fetch the data.

Level 2: The Promise Object, Again 
When we write “fetch(“http://localhost/plants/13").then(data => console.log(data))” we are actually calling .then on the promise object. The promise object has a method, .then, that stores functions in a big array called “thenCallbacks” (remember, that’s the attribute we initially set to equal an empty array). If we write “fetch(“http://localhost/plants/13").then(data => data.json()).then(data => console.log(data))” the thenCallbacks will = [ data => data.json(), data => console.log(data)]. So, before the resolve gets called, before the data gets fetched, etc Javascript calls .then on the new promise and stores all the .then functions in a big array. If you choose to code your own promise class like I did, this “asynchronous” behavior is the most difficult to implement. I used a timeout function to make sure the handler function ran after .then was called on the promise, but this is not how real Javascript promises work. The asynchrony of Javascript promises is one of Javascript’s most deeply “magical” features.
	
Which brings us back to….

Level 5: Within the Resolver Function, Again
Since we’re still inside the promise object, we have access to thenCallbacks array inside the responder function. We can simply iterate through the array and call each function, passing in the data we fetched. Each time we call a function, we assign the return value to a variable, and pass that variable into the next function, then pass that function’s return value into the next function, etc. In this way, we ensure that each .then statement manipulates the data returned by the last .then statement.

In fact, the responder function is actually quite short: 
```resolver = (value) => {
		this.status = 'resolved'
		this.value = value
		this.thenCallbacks.forEach((func) => {
			this.value = func(this.value)
		})
		if (this.onFinally) {
			this.onFinally(this.value)
		}
	}```
	
Once we’ve performed all the functions passed into .then statements, the promise has been kept. Great job, Javascript!

I came away from my quest with a healthy respect for the code that lets software engineers quickly and easily get and manipulate data. What’s going on behind the scenes is more complicated than I would have initially thought, but it’s also (for the most part) comprehensible to someone with limited Javascript knowledge. I would recommend anyone learning Javascript venture into the fascinating world of promises.
