---
layout: post
title:      "Context Clues: React's Context API"
date:       2020-03-31 18:04:17 -0400
permalink:  context_clues_reacts_context_api
---


In my opinion, context is one of the oddest-looking and least intuitive features of React. To understand React context, I first tried to understand the types of situations where context is useful. Why would anyone want to use such a complicated-looking interface in the first place?

To illustrate, let's look at an example. Say you’re building a Facebook-like website, and you want to implement a privacy setting. When the user turns the private setting on, that user will no longer show up in searches, and their contact info will only be visible to friends. Parts of that user’s history will be hidden from the public, but other parts will still be visible. Changing the privacy setting requires changing many different components – the search results page, the user’s profile, the user’s history. However, other parts of the website – for example, the user’s newsfeed – would not change at all. 

Where should we store the information about whether the privacy setting is on or off? Since the privacy setting effects many different components, it probably should be stored in the main App component, like this:

```
Class App extends React.Component {
	state: {
		private: true
	}
}
```


The privacy setting will influence lots of small parts of the website. Ideally, all smaller components would be able to access the value of “private”. In other words, we’d like the value of “private” to act like a global variable. However, React makes this difficult, because App can only pass values to it’s immediate children. 

For instance, imagine your app is set up like this. A change in the privacy settings effects both the search results summary (for example, a private user's location could be hidden, or the user could be removed from search results all together) and the contact info (an email or phone number could be hidden).

![Component Map](https://flic.kr/p/2iKH5yn)

App can easily pass the value of private as a prop to any of its three child components:

```
Class App extends React.Component {
	state: {
		private: true
	}

	render() {
		<fragment>
			<NewsFeedContainer />
			<SearchContainer private={this.state.private} />
			<ProfileContainer private={this.state.private}/>
		</fragment>
	}
}
```

However, the App component cannot pass the value of “private” to any of its grandchildren (Result, About, Recent Posts and Friends).  

Props can only be passed down one level: from a parent component to its children. In this case, the Profile container can pass the value of “private” it received from App to its children, like this:

```
Class ProfileContainer extends React.Component {

	render() {
		<fragment>
			<ProfileHeader />
			<About private={this.props.private} />
			<RecentPosts />
		</fragment>
	}
}
```

The About component will then need to pass the prop down to Contact Info, where the value of private will (finally) effect the DOM.

Just to summarize: 

* App: stores value of “private”, passes it to ProfileContainer
* ProfileContainer: passes value of Private to About
* About: passes value of Private to ContactInfo
* ContactInfo: uses the value of Private (finally)

We passed the Private prop four levels down just to change one of small part of the website affected by the privacy setting. Worse, the ProfileContainer and About components aren’t actually using the value of private – they’re just “middlemen” that pass the value down.

And this is just a simplified example – in a complex website, props might have to be passed seven or eight levels deep! Yikes!

Context to the rescue!

Context is a way to make local variables into global variables. Using context, values initiated in a parent component can be accessed in all of that component’s children, grandchildren, great-grandchildren, etc.

We create the context object using the React.createContext method: 

Const contextObj = React.createContext()

Often, we create the context object in a separate file, then import that file into our parent component (in this case, App): 

In Context.js:

```
const contextObj = React.createContext()
export default contextObj
```
	
A context object contains two keys, Consumer and Provider; each key points to a class component. The consumer and provider components are linked; the consumer component(s) can access data stored in the provider component (more on that later). We render the components in JSX the same way we’d render any other component – by wrapping the components in < />: 

A rendered provider component: <contextObj.Provider />
A rendered consumer component: <contextObj.Consumer />

Back in the App render function, we wrap all App’s children in a Provider component. Any values we want to make global variables, we must store in the value attribute of the Provider component:

```
Import contextObj from “./context.js”
Class App extends React.Component {
	state: {
		private: true
	}

	render() {
		# create a provider component with a value attribute. Assign the value of private to the “value” attribute:
		<contextObj.Provider value = {this.state.private}>
			<NewsFeedContainer />
			<HistoryContainer private={this.state.private} />
			<ProfileContainer private={this.state.private}/>
		</contextObj.Provider>
	}
}
```

But how do we access the value of private from, say, the Contact Info component, a great-grandchild of App? First, we import the Context Object.

The Consumer component is a blank class component, without a render function. We must tell the consumer component what to render. First, we define a function that returns the JSX we want to render. Then, we wrap that function in a consumer component. Think of this function like a render function – it gets called when the component mounts, and tells the component what JXS to display.

Here’s where the magic comes in: the context object automatically passes the Provider’s value attribute to the function! Since the consumer component has access to the value of “private”, we can simply tell the consumer component to render different JSX depending on whether private is set to true or false.

```
Import contextObj from “./context.js”

Class ContactInfo extends React.Component {

	render() {
		# create a consumer component with a function as a child. The function gets passed whatever is stored in the provider’s “value” attribute. Within the render, we display different components depending on whether or not “private” is set to true:

		<contextObj.Consumer>
			{
				(value) => value ? (<p>This user’s contact info is private.</p>) : 						(<p>{user.email}</p>)
			} 
		<contextObj.Consumer/>
	}
}
```

Hopefully, this example explains 1) how to use context and 2) why someone would want to go to the trouble of using context in the first place.

While I understand how to implement context in React, I still have a few questions:

1. How do the provider and consumer components “talk” to each other? Does the provider’s value attribute get passed to each consumer, and if so, how? 


2. I’ve seen several articles describe the child function of a consumer as the consumer’s “render” function. This makes sense, as the function gets called when the consumer component renders (and returns JSX that renders to the DOM). However, while the child function plays the role of a render function, the consumer component does not seem to have a render attribute like most other components. Re-writing the child function inside the “render” attribute, like this: 

```
<contextObj.Consumer render = () => ((value) => value ? (<p>This user’s contact info is private.</p>) : (<p>{user.email}</p>) />
```

causes an error. Why can’t you tell the consumer component what to render via the render attribute? Why do you need to tell the consumer component what to render by writing a function inside it?

3. (Answered question) When the provider’s value changes, does everything wrapped in the provider re-render? 

	I managed to find an answer to this question: everything inside the provider component does not re-render when the provider’s value changes. Only the pieces of the app rendered by Consumer components (in other words, the pieces that can access the provider value) re-render when the provider’s value changes. Handy!

			

