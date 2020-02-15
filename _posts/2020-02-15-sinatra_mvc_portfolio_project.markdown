---
layout: post
title:      "Sinatra MVC Portfolio Project"
date:       2020-02-15 20:49:07 +0000
permalink:  sinatra_mvc_portfolio_project
---


Starting on my second portfolio project for Flatiron, I felt much more prepared and confident than the first time around. My goal was to create a Sinatra web app (EventConnection) that allows users to add, edit, view and delete events from a community calendar. The idea originated from a WordPress website I help manage at work (TallyConnection), which accepts event submissions from the community to promote local businesses and organizations. 

My first Flatiron project, a Hogwarts CLI gem, took me a long time to build and refactor, because I tried to accomplish all my ideas at once. My strategy this time around was different: first achieve a minimum viable product, then add features, styling and error messages. I started by sketching out my models, associations, tables and routes.

**Models & ActiveRecord Associations**
I started with two models: Users and Events. Users can have many events and an event belongs to a user. Users must have an email address, username and password. Events must have a name, location and start datetime. Optionally, they can have an end date and time as well as a description.

**Tables & Active Record Migrations**
I created migrations to create both of my tables. The Events featured a user_id column as a foreign key, to create the ActiveRecord belongs_to association to a User. In creating the Events table, I ran into a choice. How was I going to treat the start and end datetimes? ActiveRecord supports datetime as a column type, but I was unfamiliar with it and the html forms would return the dates in the form of a string. Eventually I decided on storing the datetimes in my Events table as strings to keep the project moving forward.

**RESTful Routes & Views**
Sketching out the routes and views was the simplest part of the project. For my purposes, the routes in my Events controller followed RESTful route conventions exactly, and my User routes only have a few necessary deviations. Instead of a new users view, I have a signup view and route; there are two additional routes and corresponding views: logout and login; and I don't currently have a User edit route or view. Eventually I'd like to add the ability for users to change their email address and/or password. 

**Controller Actions**
Next, I focused on my controller actions; what HTTP verbs and routes belonged to each and what views they would render. At this point I also made decisions about whether users should be able to access routes or see specific rendered views based on whether they were logged in. Users who are not logged in can only access the login and sign up forms, and the homepage. Users who are logged in cannot access the login or signup forms, and can only access the edit routes for events that they own. 
**Validations**
Once the associations were in place, the controller actions were rendering the correct views as well as the correct User and Event attributes, I focused on validating user input. I started with the User model and used ActiveRecord validations. Users must have a unique username and email address; must enter an email address in the correct format; and must have a password between 6 and 20 characters. Events must have a name (at least 3 characters long; must have a start datetime; and must have a location.
```
class User < ActiveRecord::Base
    has_many :events
    has_secure_password
    validates :username, presence: true, uniqueness: true, length: { minimum: 3 }
    validates :email, presence: true, uniqueness: true, format: { with: URI::MailTo::EMAIL_REGEXP }
    validates :password, length: { in: 6..20 }
end
```
**Error Messages**
My next step was displaying error messages to the user. If someone tried to complete the signup form but failed (because they didn’t use a unique username, for example), they were redirected back to the signup form and left to guess what went wrong. In the post ‘/signup’ action, I used ActiveRecord’s  .valid?, .errors and .full_messages methods to store any error messages in the session hash as an array. Then, when the user is redirected to the get ‘signup’ action, that error array is then iterated over in the view, rendering each specific message to the user. The value of session[:current_errors] is then cleared, within the view, to keep previous error messages from being displayed on subsequent views.
```
#users_controller.rb
    post '/signup' do
        user = User.new(params)
        if user.save
            session[:user_id] = user.id
            redirect '/events'
        else 
            user.valid?
            session[:current_errors] = user.errors.full_messages
            redirect '/signup'
        end
    end
``` 
```
#layout.erb
<% if session[:current_errors] && session[:current_errors].length > 0 %>
        <div class="error-message">
        <% session[:current_errors].each do |error| %>
          <em><%= error %>.</em>
        <% end %>  
        <% session[:current_errors].clear %>
```

**CSS**
Styling the app was the fun part! I used vanilla CSS as well as flexbox to get the look that I wanted. I based the design on the TallyConnection and Eventbrite websites. I used COOLORS to create my color palette. The hardest part about the design was leaving it alone. While adding other features, I kept getting distracted by minor CSS improvements like the background color transitions on buttons.

**Categories**
Finally, I added a Category model to EventConnection. Categories have_and_belong_to_many Events, and Events have_and_belong_to_many Categories. Currently, users can only pick from a few existing category types to add to their events. In the future, I’d like users to be able to add their own categories and I’d like the event card’s background images to be determined by their primary category. (E.g. Sports events would have a soccer ball as an event card background image.)

To anyone looking to build a Sinatra app or start their Sinatra portfolio project, I would advise you to start with a minimum viable product and plan! What models will your app have? How will they be associated to one another? What journey will your user take from start to end when using your app? What should they be able to do? What should they not be able to do?

I hope to keep expanding this project as I learn more about Ruby, databases and their interactions.

