---
layout: post
title:      "Round and Round with Circular Imports"
date:       2019-12-07 19:07:13 +0000
permalink:  round_and_round_with_circular_imports
---


Since I started seriously programming three months ago, I’ve run into hundreds of problems with my code. I’ve come to see errors as completely normal, usually fixable with de-bugging techniques and not a cause for concern. However, there are, occasionally, problems I encountered that were less easy to understand and fix, problems that revealed fundamental misconceptions in my model of how code should work. In this blog post, I want to discuss a fundamental conceptual issue I encountered while designing a CLI application for accessing new music. For the sake of this post, all you need to know is that my program contained two classes defined in two separate files: the Artist class, and the Song class. I called methods from the Artist class inside the Song class and vice versa; as a result, each class needed to access the class methods and variables defined in the other. I thought I knew how to make this happen with a few well placed require relative statements.

I went into the file where I’d defined the song class – song.rb – and required the artist class. I went into the file where I’d defined the artist class – artist.rb – and required the song class. Since I wanted to test the methods in the artist class in isolation first, I called the method Artist.find_or_create inside the file artist.rb. Immediately, the program broke, throwing the error “uninitialized constant Song.” Even though I’d required the file song.rb, the Artist class still could not access the Song class. In fact, the program did not even recognize Song as a class, and instead assumed that Song referred to an undefined constant.

After some research and experimentation with require_relative, I realized I did not fully understand the meaning of the statement require_relative. Until now, I had assumed the require_relative statement allowed the code in one file to “see” the methods, class variables and class constants defined inside another file. What I did not realize was that require_relative statements actually run the code inside another file. For example, I called the method display_all_songs (which, you guessed it, prints all songs) inside the Song class and then required the Song class in the Artist class. When I ran the Artist class, the Artist class ran the code in the Song class and printed out a list of all the songs. Typing “require_relative ‘./song.rb’” in my Artist class was equivalent to copy-pasting all of the code from song.rb into artist.rb.

However, my newfound understanding of require_relative did not solve my original problem: when I required song.rb in artist.rb, the Artist class still could not access the methods defined inside the Song class (and vice versa – I could not access Artist class methods inside the Song class, either). In fact, the problem made even less sense now: if the “require_relative ./song.rb” statement at the top of artist.rb is equivalent to copy-pasting the contents of song.rb into artist.rb, why wouldn’t artist.rb be able to access all the class variables, methods etc defined in song.rb? 

I struggled to figure the problem out on my own – I had no idea what to google, and after trying and failing to locate the bug with print statements, I was stumped. I looked for help from a programmer friend, who was able to explain the issue in a way that made sense. Here’s a step by step explanation of what happened when I ran artist.rb:

Step 1: The require_relative statement “require_relative ./song.rb” tells the program to go to the song class and start running all the code in song.rb

Step 2: The program evaluates the first line of code in the song class: “require_relative ./artist.rb.” This require statement tells the program to return to the Artist class and run all the code in Artist.rb.

Step 3: The program knows it’s already read and evaluated the first line of Artist.rb (“require_relative ‘.song/rb’) so it skips that line and begins evaluating the rest of the code in the Artist class. 

(Initially, I thought the program would evaluate all the code in the Artist class again. If this were true, the program would start again at the first line – require_relative “./song.rb” – and be redirected back to the Song class, where it runs into the statement require_relative “./artist.rb,” creating an infinite loop.)

Step 4: The program raises an error when the Artist class tries to call a method in the Song class. Remember, the program has not read or evaluated any of the code in the song.rb past the first line (require_relative “./artist.rb”). Therefore, the program does not know about any of the methods, class variables, or class constants contained in the Song class. 

(Since the computer hasn’t read past the first line of song.rb, it doesn’t even know that Song is a class. As a result, when a method inside the Artist class calls a method inside the Song class using the format Song.method_name, the program notices that Song is capitalized and assumes Song must be a constant inside the Artist class. As a result the program throws the error “uninitialized constant Song.”)

Uh oh.

This issue is common enough to have a name: a “circular import.” So, how do you fix a circular import? As I learned from my helpful friend, there’s a fairly simple solution. Instead of testing Artist class methods inside of artist.rb and Song class methods inside of song.rb, I created a new test file, test.rb. I then required both song.rb and artist.rb inside test – the equivalent of copy pasting all the code from artist.rb and song.rb into test.rb. Now, since the Artist and Song classes are both defined in the same file, each class can access all the methods, variables, and constants contained in the other class. After writing the two require statements, I called the individual methods of the Artist and Song classes I wanted to experiment with inside of test.rb.

My misadventures with circular imports taught me to always question my initial, intuitive understanding of a computer science concept. After looking at hundreds of files that included require_relative statements, I thought I instinctively understood how require_relative worked. In reality, I’d constructed a story about how require_relative statements work that made sense, but was incorrect. Since I never critically examined or tested my ideas about require_relative, I ran into fundamental issues with my programs. I hope that other programmers who may have the same misconceptions about require_relative find this post useful.

