---
layout: post
title:      "Recursion Inception"
date:       2020-07-09 22:12:59 +0000
permalink:  recursion_inception
---


I haven’t seen Inception since my senior year of high school, but the concept stuck with me. The movie centers around a group of information thieves, professionals who infiltrate dreams to look for secret information. When a dream gets too dangerous, the characters enter a dream within that dream. In order to wake up, the adventurers must first exit the innermost dream, then progress backwards through all the previous dreams. Little did I know as a high school senior that Inception provides the perfect metaphor for recursive functions. 

While I learned the basics of recursion in college math classes, I have not needed to use or deeply think about recursion for years…until last week during a coding interview. Predictably, when posed with a coding challenge that could easily be solved via recursion, I flailed around aimlessly. My inability to solve the challenge motivated me to dive deep into recursion. I challenged myself to not only solving the question posed in the interview, but also walk through my solution, explaining what happens at each step. Too often, I think of recursion as a kind of magic; and while recursion is mind bending and requires imagining lots of behind-the-scenes work, it is possible and, in my experience, useful to trace exactly what occurs when you run a recursive function. 

The challenge: Given a maze represented by an array of arrays, find whether the maze is solvable (ie whether there is a path from the starting point to the end.) “#” represents a wall, “S” represents the starting point, and “E” represents the end of the maze. Write a function that takes in the indices of the starting point (expressed as an array of two numbers, for example [4, 2] if S is placed at maze[4][2]) and the maze, and outputs a boolean (true if the maze is solvable, false if not).

```
//Sample maze: 

[["S              ####"],
 ["####  ##  ####"],
 [“####  ##  ####”],
 [“#        #######”],   
 [“####  E######”]]

//My solution: 

function solveMaze(currentLocation, maze) {
	let x = currentLocation[0]
	let y = currentLocation[1]
	if (maze[x][y] === "E") {
		return true
	}
	if (maze[x][y] === "#") {
		return false
	}
	if (maze[x][y] === " ") {
		let resultOfRight = solveMaze(maze[x + 1][y], maze)
		let resultOfDown = solveMaze(maze[x][y + 1], maze)
		let resultOfLeft = solveMaze(maze[x - 1][y], maze)
		let resultOfUp = solveMaze(maze[x][y - 1], maze)
		if (resultOfRight || resultOfDown || resultOfLeft || resultOfUp) {
			maze[x][y] = "X"
			return true
		}
		else {
			return false
		}
	}
}
```

Step-By-Step Explanation:

1. As long as the current location doesn’t overlap a wall or the ending spot, go into third “if” statement on line 9.
2.  The function gets called again, with the index one to the right of our current location passed in as the new current location. You can think of this step as entering a function within a function, just as the thieves in Inception enter a dream within a dream.
3.  If that, new current location does not overlap the ending spot or a wall, we again enter the third “if” statement, and call the function again. If you’re keeping track, this is the third time we’ve called the function – we’re now calling a function inside a function call inside our first function call. To use our Inception metaphor, we’ve now entered a dream within a dream within a dream.
4.   As long as the spot directly to the right of our current position is empty (not a wall or the end of the maze), the function will keep calling itself, and we will progress deeper and deeper into “dream space”. For example, if there are five blank spaces in a row to the right of the starting place the function will be called five times, and we’ll be in a function within a function within a function within a function within a function. 
5.   Once our current location overlaps a wall, the function returns false. Once the innermost function returns a value, we return to the previous function call. (To extend our Inception metaphor, we “wake up” from the innermost dream.) In the previous function call, recall that we were on this line: 
   ```
	let resultOfRight = solveMaze(maze[x + 1][y], maze)
```

	solveMaze(maze[x + 1][y], maze) just returned false, so:

```
	let resultOfRight = false
```

	and we proceed to the next line: 
	
```
	let resultOfDown = solveMaze(maze[x][y + 1], maze)
```

6.   The next line calls the function AGAIN, but sets the new current location to the space directly under our current location. We’ve “explored” as far as possible to the right, hit a wall, and now we turn to the spot directly below us. While we’ve “woken up” from the innermost dream, we’re still deep in dream space, and we’re going deeper…
7.   Just as we called the function each time we moved one spot to the right, now we call the function each time we move one spot down. 
8.   Once we reach a dead end, the innermost function returns false and we re-enter the previous function call. ResultOfUp = false, so we proceed to the next line, let resultOfLeft = solveMaze(maze[x - 1][y], maze), which “explores” the space directly left of the current space.
9.   If resultOfRight, resultOfDown, resultOfLeft and resultOfUp all return false, we’ve hit a dead end – no matter which direction we turn, we hit a wall. In this case, the function returns “false” (line 28) and we exit the current function call. We are now in the previous function call, where currentPosition = someplace earlier in the maze, before we explored down, left or up. We must now re-explore down, left and up to see if there’s another path we missed. In our sample maze
```
[["S              ####"],
 ["####  ##  ####"],
 [“####  ##  ####”],
 [“#        #######”],   
 [“####  E######”]]
```
we've gone all the way to the right and down, hit a wall, and are now backtracking. At each point, we access whether we can move right, up, left or down. If the answer’s yes, we create new function calls, progressing further into “dream space”; if no, we exit the innermost function and re-enter earlier function calls. When we re-enter outer function calls, we also literally progress backwards through the maze, since the outer function calls have currentValue set to an earlier place in the maze.

10.   OK, what happens when we finally find the correct path? Suppose we go left and hit the “E.” In this case, we’ll hit our first if statement and return true, exiting the innermost function. In the next function up, resultOfLeft = solveMaze(maze[x - 1][y], maze). Since solveMaze(maze[x - 1][y], maze) completed and returned true, resultOfLeft = true. We now hit this if statement: 

```
if (resultOfRight || resultOfDown || resultOfLeft || resultOfUp) {
   maze[x][y] = "X" 		
   return true	 
}
```
Since resultOfLeft = true, we return true and exit the inner function. The return statement of the inner function = resultOfLeft in the previous function:

```
	let resultOfLeft = solveMaze(maze[x - 1][y], maze)
```
	solveMaze(maze[x - 1][y], maze) returns true so 
```
	let resultOfLeft = true
```

Think of the thieves in Inception, backtracking through dreams until they reach the first, original dream. Think of the boolean “True” as the information the thieves need to carry out of each dream. Eventually, we will wake up after exiting each dream in order, ferrying along the information. I hope you've enjoyed our daring heist into the innermost depths of a recursive function. 


