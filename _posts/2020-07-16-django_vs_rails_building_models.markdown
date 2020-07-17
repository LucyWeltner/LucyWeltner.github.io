---
layout: post
title:      "Django vs Rails: Building Models"
date:       2020-07-17 02:33:03 +0000
permalink:  django_vs_rails_building_models
---


While I spend most of my time at Flatiron School learning and working with Ruby on Rails, I recently decided to build an application in Django. I already had a background in Python, and I wanted to see how easy it would be to interpret another web application framework using the basic principles I’d learned through working with Ruby on Rails. 

Fortunately, I found that the structure of Django almost perfectly mirrored the structure of Rails. Django has the equivalent of Ruby models, controllers and views, changes the database via migrations, and maps URLs to functions in the controller-equivalent. 

While I quickly grasped the big-picture similarities, I found myself struggling with implementation details. What does all of the syntax mean? Which properties and relationships did I need to set up myself, and which were set automatically? In this post, I want to explain how to set up models with has many/belongs to and has many through associations in Django, highlighting a few of the differences between Django and Rails.

Django syntax and Rails syntax each have a distinctive feel; Ruby syntax uses common language to disguise complicated processes, while Django commands often sound more complicated and technical. 

While I like the succinctness and readability of Ruby on Rail’s commands, some of Django’s commands more accurately describe what’s happening inside the database. For instance, let’s say I want to set up a “belongs to/has many” relationship between authors and books (a book belongs to an author, and an author has many books.) If I wanted to do that directly inside my database, I’d create a foreign key column in the “books” table, called author_id. Inside the author_id column, I’d simply store the ID of the author who wrote each book. 

Ruby on Rails does all that work automatically once we specify that books “belong to” an author and authors “have many” books. In Django, setting up a belongs to/has many relationship is just as simple, but the commands more closely correspond to actual changes in the database. Just as if we were changing the database directly, we add a new ForeignKey property to the books model, and name that property author_id:

```
author_id = models.ForeignKey
```

We must also specify that IDs in the ForeignKey column refer to Author objects:

```
author_id = models.ForeignKey(Author)
```

However, sometimes Django commands change the database in unclear, implicit ways. Suppose we want to set up a many to many relationship between, say, Uber drivers and passengers (drivers chauffeur many passengers, and passengers ride with many drivers). In the database, we create a join table, called Rides, to relate the two models.

In Ruby on Rails, we need to manually create the Rides model and specify its relationship to the Driver and Passenger models. However, Django creates that join table for us automatically. As soon as we specify that drivers have many passengers and passengers have many drivers, Django intuits the need for a third table to store the associations between the two models. Inside the driver model, we set up a many to many relationship with the Passenger model using a ManyToMany field:

```
Passengers = models.ManyToMany(Passenger)
```

And inside the Passenger model, we set up a many to many relationship with the Driver model the same way: 

```
Drivers = models.ManyToMany(Driver)
```

These two commands instruct Django to create a join table with driver_id and passenger_id columns. Just like in our Rides table, each row associates a specific driver with a specific passenger.

In many circumstances, it’s more efficient to set up a many-to-many relationship without having to directly deal with or worry about the join table. However, there’s a trade off: when Django does more of the work for us, we lose the ability to do the work in an unusual or nonstandard way.

For example, what if we want to store more information about each ride (for example, the length of the ride, or how much the ride cost)? Since we can’t easily access the join model Django creates, we can’t make changes. Fortunately, Django allows us to manually create a join model using the through command: 

Inside the Driver model, we write:

```
Passengers = models.ManyToMany(Passenger, through=Rides)
```

Inside the Passenger model, we write: 

```
Drivers = models.ManyToMany(Driver, through=Rides)
```

These commands instruct Django to look for a Rides model which connects passengers and drivers. We can now manually create a Rides table with all the properties we want. 

Django also has a few extra features that make has many through associations easier to work with. For instance, the “on-delete” property fixes a problem I often ran into while working with models in Rails. While creating my first Rails project, I had two models in a many-to-many relationship connected by a join table. Testing my app required re-importing, testing, then deleting several sets of data. However, while I remembered to delete the data in the two main models, I forgot to delete the data in the join table, resulting in hundreds of extra records associating objects that no longer exist. 

For example, let’s say our main models are “drivers” and “passengers.” Each instance of the join table, “rides,” associates a driver with a passenger. When a driver is deleted, all the rides associated with that driver should also be deleted. In Django, we can make this happen automatically whenever we delete a driver. First, we go inside our “Rides” model. For each ride, there is a foreign key column that contains the id of the driver. The on-delete property determines what to do when the driver that foreign key points to no longer exists. By setting on-delete to “CASCADE,” the delete command “cascades” down to the rides model and deletes all rides associated with the removed driver.

```
driver_id = models.ForeignKey(Driver, on_delete=models.CASCADE)
```

Now, whenever we delete a driver, we also delete all the rides associated with that driver.


