---
layout: post
title:      "The Mysteries Of Ajax"
date:       2020-02-16 14:27:15 -0500
permalink:  the_mysteries_of_ajax
---



Last week, I encountered a mysterious problem while building a ruby on rails application. I was trying to build a library application with a page where users can search for books. My page used a form_with to send a GET request to the results page (“/results”). The user input should be stored in the in the GET request, inside the parameter query. The get request should trigger the results method in my controller, which searches for data matching the user’s query and renders the results in a view template. That’s what should have happened, but instead, pressing the “submit” button did not change the appearance of the page at all. I coded my form_with like this: 

```<%= form_with url: results_path, method: :get do |form| %>
	<%= form.label :query “Search For a Book:” %>
	<%= form.text_field :query %>
	<%= form.submit, “Search” %>
<% end %>
```

Fortunately, my professors had encountered many students with this issue before. The fix was simple: just make sure the form submits a “local” request by adding the attribute local: true, like this: 

```<%= form_with url: results_path, local: true, method: :get do |form| %>```

My professors explained that, by default, form_with sends Ajax requests, using javascript. By setting local to true, I made sure my forms sent normal https GET requests. While I now knew how to fix my forms, I still had many questions. What is Ajax? Why would sending a form via an Ajax request make my page render incorrectly?

1. How does Ajax work?

Basically, Ajax is a way for a website to get new data without having to reload a page. Let’s say you’ve created a form_with on the signup page. Users must fill out the form and press the submit button in order to sign up. At the moment the user hits “submit”, rails creates an object called a “Javascript XML request”. Rails then prevents the form from sending a normal POST request. Just like a regular POST request, the Javascript request object has a method variable set to post. Just like a regular POST request, the Javascript request object contains hidden data submitted by the user (we can call methods on a request object to retrieve the user-submitted data). In fact, since the Javascript request object stores all the same data as a normal post request, the server’s able to “translate” the Javascript object into a normal https POST request and transfer all the information from the Javascript object into the body of the https request. From that point on, the server doesn’t know or care that the https POST request originated as a Javascript object. The server receives what seems like a normal https post request; that request triggers a controller action; the controller accesses and manipulates the data in the http request (using the params hash); and, finally, the server sends back an https response which mostly likely redirects the user to a new page. The response contains all the html needed to render the page.

However, when that response gets back to the browser, everything changes. Instead of  re-directing the user to another page, the server replaces the html on the current page with html from the “/users” page. For example, let’s say the browser sent a Post request using a Javascript object. A moment later, the browser receives a https response: GET the page “/users.” Instead of redirecting to “/users,” the browser retrieves all the html that would be displayed at “/users” from the https response. The browser then puts all the html into a long string (escaping any characters that have meaning in javascript), and uses the “render” method to put the html on the page (the same way a user might display a partial). As a result, while the site appears to have loaded “/users” (probably displaying a message like “You’ve successfully signed up!”), no get request to “/users” has actually been sent, and the page never actually loaded. Wow!

2. Why would sending a form via an Ajax request make my page render incorrectly?

To answer my second question, I first decided to figure out where, exactly, the process of sending and receiving requests broke down. To figure this out, I looked at the process that enfolded in my terminal after I pressed "submit." Here's the info I gathered:

1. I saw that the http GET request was sent. 
2. Once I added a “puts” statement inside the controller action, I saw that the correct controller action was triggered, and the controller had access to the correct parameters.
3. I saw that an https response to load the correct page (“/results”) was triggered.
4. By inspecting the webpage after I press the "submit" button, I can see that the https response contained all the correct html (the html I want to be displayed on the “/results” page). However, that html doesn’t actually show up on the page.

Hmmm…while I still don’t know the source of the problem, I have pinpointed approximately where the error must occur: I know something goes wrong when the browser attempts to render the html from the https response onto the page. (Perhaps the browser does not know where to render the html?)
	I also have another clue: my form_withs that sent POST requests seemed to behave correctly. Only my form_with that sent a GET request failed. Why? 
	
The short answer is…I still don’t know! After doing a couple hours of research on the issue, I’ve found many people explaining how to fix the problem but no one explaining why the problem exists in the first place. That said, I still feel like my deep dive into Ajax was worth it. After developing a much better understanding of Ajax, I can now pinpoint exactly where my form failed, and come up with a couple hypotheses about why. If I encountered another, similar problem, I feel like with my new knowledge of Ajax I’d at least know where to start.
