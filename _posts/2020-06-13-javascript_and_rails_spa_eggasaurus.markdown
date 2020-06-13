---
layout: post
title:      "Javascript & Rails SPA: Eggasaurus"
date:       2020-06-13 17:52:48 +0000
permalink:  javascript_and_rails_spa_eggasaurus
---


For my fourth Flatiron project, I was tasked with building a single page application with a HTML/CSS/JavaScript frontend and Rails API backend.

I wanted to use this opportunity to build a game that was simple enough for my three-year-old to play. The resulting game, Eggasaurus, came from a combination of my love of browser games and my son’s fascination with dinosaurs. Users can create dinosaur pets and interact with them by feeding, playing and sending the dinos to take a nap.

##Application Planning & Vertical Build
I started app planning with model diagramming and user stories.

###Models:
User 
* has many dinos, has many species through dinos)
* username, email, password

Dino
* belongs to a user, belongs to a specie
* name, happiness, tiredness, hunger, user_id, specie_id

Specie 
* has many dinos, has many users through dinos
* name, sprite_url

###User Stories:
* A user is able to sign up, login and log out
* A user is able to view dinosaurs, add new dinosaurs, and delete dinosaurs.
* A user is able to edit dinosaurs by playing/interacting with them
* A user is able to choose a species when creating a dinosaur

Building YellowbitRoad taught me how to concentrate on creating a MVP, building Eggasaurus taught me the benefits of building a MVP vertically.

Previously, I would’ve created all of my migrations, moved on the models, then routes, controller actions, etc. Instead, I concentrated first on the Dino Read action. I created the Dino migration and model, added seed data for one Dino instance, created the index route and controller action, made a fetch call and added that returned data to the DOM. 

Building vertically allowed me to get features up and running faster, and allowed me to concentrate on one hurdle at a time. Bonus: I didn’t end up with an endless list of migrations. 

To keep myself on track and focused on the vertical build, I kept my notes open and added new feature ideas as they occurred to me. If I noticed a previous feature was newly broken, I added it to the list of things to come back to after I finished the CRUD action I was working on, instead of switching gears right away.

##Making Use of Git Branches 
Eggasaurus was the first application where I took full advantage of Git branches. Instead of camping out on master, I created a new branch for each feature or CRUD action as I built. This allowed me to rein in my usually wandering eye, and stay focused on the feature at hand. 

##Pit of Despair
My biggest pain point, and greatest learning experience, came from the last place I expected: event listeners. I’ve worked with JavaScript and the DOM before and had no issue with the idea or implementation of event listeners. I’ve used them extensively to track goals on client websites.

As I added new features to the application, a previously working event listener was DOA. The listener was supposed to create new dinosaurs and render them to the screen when users clicked the submit button on the new dino form. The event listener would only work the first time users clicked the button. Later clicks did nothing, no console.log messages, no debugger hits: nothing.

After much rubber ducking, frantic googling, head-pounding and a few walks around the block, I realized the culprit was .innerHTML. From MDN’s .innerHTML resource page:
	 Setting the value of innerHTML removes all of the element's descendants and replaces them with nodes constructed by parsing the HTML given in the string htmlString.

Because I was using .innerHTML to render the new dinosaur to the page, I was destroying the previous child nodes and therefore rendering any event listeners on those childnodes useless. 

To fix the issue, I moved from using innerHTML to appendChild wherever I could, and re-added event listeners anywhere I was replacing childnodes. 

##Favorite Features
As it stands, my two favorite parts of Eggasaurus are the auto-adjusting mood levels and pop-up message feature. 

When a user creates a dinosaur, all three mood levels (hunger, tiredness, happiness) are set to 100 and the meters displayed are full. I used setInterval to continually decrease a dino’s mood levels and display this decrease to users . Every second, decreaseMoods() is called on the dino currently in play. 

decreaseMoods() checks to see if the each mood level (happiness, hunger and tiredness - see note about mood level names at the end of this post) is above 0; if true, it calls the appropriate function to lower those levels (etc. hungry(); bored();) and adjust the width of mood meter div.

```
#index.js
function moodTimer(dinoId){
    newMoodAdjust = window.setInterval(() => {Dino.findDino(dinoId).decreaseMoods()}, 1000)
}
```


```
#dinos.js
class Dino {

    decreaseMoods(){
        if(this.hunger > 0){
            this.hungry()
        }
        if(this.happiness > 0){
            this.bored()
        }
        if(this.tiredness > 0){
            this.tired()
        }
    }
}
```

The pop-up message feature uses and event listener and a CSS animation to make messages fly up from the bottom of the sidebar and then disappear when users save or delete a dino. It is an homage to one of my favorite idle browser games: Cookie Clicker.

##New Features Coming
Eggasaurus is far from done. I want to implement some type of scoring system, add more pixel art to allow animation of the dinosaurs and add new dinosaur species, among about 100 other features. First on the list is re-naming the dinosaur mood levels to match their values: tiredness will become energy and hunger will become fullness. Keep an eye out for Eggasaurus as it grows. 

