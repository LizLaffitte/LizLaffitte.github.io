---
layout: post
title:      "Planning My First Rails App"
date:       2020-04-19 19:44:30 +0000
permalink:  planning_my_first_rails_app
---

If learning to create apps with Sinatra is akin to a child learning to walk, working with Rails is like learning to run. Suddenly all the pieces that took so much time and care are like second nature, taken care of automagically with generators, helper methods and other goodies built into the framework itself. But while that speed allows you to reach new levels of complexity, it also means that the trip-ups hurt a lot more.

For my first Rails project, my goal was to build an app that helps self-taught programmers keep track of their learning goals, progress and online courses all in one place. Before Flatiron, I was the only one keeping tabs on what I was learning and how far I’d come. I’d often come across promising online courses that I wasn’t ready to start, because of the lack of time or foundational knowledge. At one point I had a physical notebook, Pinterest board, Google doc and a bloated bookmark folder, all with links and lists of tutorials and courses I wanted to check out, one day. 

YellowBitRoad allows users to create learning roads, adding courses as checkpoints on their roads, complete with goal dates and links to the online courses. For example, Tim can create a Learn Python road with checkpoints like Formatting Strings in Python (from https://www.learnpython.org/) and Build a Mobile Application With the Kivy Python Framework (from https://realpython.com/). 

## Models and Associations
The first order of business, after deciding on an actual app idea, was to sketch out my models and how they would relate to one another.These decisions will shape your database structure, including necessary tables and foreign key placement.

Originally, I had settled on user, path and course models. I actually got pretty far into my project before deciding that path wasn’t the best model name. Seeing redirect_to paths_path a few times led me to change the model name to road. Word to the wise: words matter, especially when it comes to naming models. Keep in mind readability, reserved words and clarity. While “learning path” may conjure a clearer picture for those using the app, it was muddying up my code and confusing the programmer.

I wanted my users to be able to own roads, and add courses to those roads. Users should be able to create courses, but not edit or delete them, as that would remove them from other users’ roads. Enter the through model/join table. I needed a model that represented the pairing of a road with a course, that also accepted user-submittable data (per project requirements). Ultimately I settled on checkpoints, that allow users to associate a course with their road, as well as add a goal date to checkpoints.

For those of you playing at home:
**User **

  has_many :roads
  has_many :checkpoints
  has_many :courses, through:  :checkpoints
	
**Road**

  belongs_to :user
  has_many :checkpoints
  has_many :courses, through: :checkpoints

**Course**

  has_many :checkpoints
  has_many :users, through: :checkpoints
  has_many :roads, through: :checkpoints

**Checkpoint**
 
  belongs_to :user
  belongs_to :road
  belongs_to :course

## Database Structure and Migrations
Sketching out your models and their relationships to one another is an absolute necessity. This planning will determine your table names and what foreign keys you need ( any model that belongs_to another will have a foreign key column for its parent).

At this stage I also decided what other attributes my models would have (e.g. username, email for User; goal_date, name and description for Road) and ran the migration generators. If you peeked at my migration directory (please don’t, it’s embarrassing), you would realize that I ended up changing column names, a table name ( path > road) and added/dropped a few columns. For the love of Ruby, plan every piece of your project as much as you can before you run rails new. There’s nothing like realizing that several app-wide changes could’ve been avoided if you had bitten the bullet at the beginning - more on that later.

## Routes & Views 
I decided on which routes I wanted to nest (checkpoints under users), what forms I would need and what a user’s profile would look like. At that point I was itching to start the actual coding.

If I could rewind two weeks and change one thing about creating this project it would be to take a breath and flesh out my routes and my views. The only things that changed more than my database structure were my nested resources and my views.

If I had wireframmed my views in the beginning I would’ve been able to:
### Make better decisions about nested resources.
I started with all my road routes nested under users. This only created problems: I couldn’t nest checkpoints without creating a rat’s nest of URLs. 

Better nested resource planning would’ve led to better controller planning, when it came to handling those resources.

### Reduce time spent on CSS and HTML in views
If I had thought about what information I wanted to show users who owned a road, versus those who did not, I wouldn’t have had to rewrite my HTML, grouping owner-related information and re-writing the logic behind what information to display. When I decided to add sidebars to help separate that information, I had to switch from vanilla CSS to Flexbox.

### Written fewer migrations
Seriously, I have way too many migrations and they're not the result of my app growing or chaning. They're a result of not taking an extra hour to plan. Knowing what I wanted the users to see on the frontend would’ve made it easier to create a database structure at the beginning that didn’t change so drastically through the process.

What I forgot is that views are an important part of the puzzle and can offer as many challenges (Partials, Locals and Helpers, Oh My!) as all the juicy backend pieces. 

While I wouldn’t recommend starting your project by writing CSS style declarations or coding the HTML in your views, I would start your planning with the experience you want users to have while using your app. 

**What’s the purpose of your app? **

This is your 1,000 ft. view. Deciding the purpose behind your app, or the problem it will solve, will naturally lead you to the information and functionality you will need to make it happen.

**What data needs to be presented to the user, in what form, in which views? **

Answering this multi-level question will help you start thinking about the models needed and their attributes making decisions about models, their attributes and associations; routes and controllers.

**What’s your access policy? **

Who is allowed to read, update and delete different data? Where will you control the logic behind those decisions?


