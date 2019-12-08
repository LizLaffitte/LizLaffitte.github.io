---
layout: post
title:      "CLI Data Gem Portfolio Project"
date:       2019-12-08 15:24:17 +0000
permalink:  cli_data_gem_portfolio_project
---

The CLI gem project was the hardest assignment I've had to date. I've been teaching myself new programming languages and concepts for over four years, but I had never written a complete program from start to finish. 

The first step, picking an API to use for my program, was a daunting one. The endless amount of possibilities gave me decision paralysis; I was worried about making the wrong choice and getting stuck making an uninteresting or useless program. I toyed with the idea of an event or weather API, but wasn’t excited or inspired by either. I finally settled on the Harry Potter API, which provides information on the houses and characters from the beloved Harry Potter book and movie series. The only authorization it requires is an API key, passed in as a URL query parameter, and the API’s subject matter is interesting, fun and relatable to a good number of my peers. 

My program sorts the user into one of the four Hogwarts houses, and then allows them to pick whether they’d like to see a list of houses or students. Depending on the choice, users can them pick the house or student they’d like more information about.

Making the API calls and dealing with the data returned wasn’t a problem. I've experimented with API's before when attempting to create an email report creation application in Python. I know how to make basic GET and POST requests in other languages, and the Httparty gem made the transition to making calls in Ruby simple. Other than some inconsistencies in capitalization and spacing in the data, the Harry Potter API endpoints were straightforward and simple to use, and the documentation was specific on making calls and what data to expect in return. 

Surprisingly to me, the hardest part of the program wasn’t creating my house and character classes or having them interact, it was writing the flow of my CLI classes and dealing with different input types. I wanted my program to go a few levels deeper than the the code-along CLI projects we’d already completed as part of the course. I envisioned the program having sub menus – the house and student directories - in addition to the main menu/Great Hall. I started with a very complicated and hard to followed tangle of nested if/else statements. While I changed that mess into a while (and eventually an until) loop, I still struggled with seeing my error message when users entered correct input to go back a level in the menu, or to exit the program.

```
def houses_menu_options
        user_input = nil
        until user_input == "back"
            puts "You are currently in the House Directory. Where to now, wizard?"
            puts "Enter a house's number (1-4) to learn more about it,"
            puts "enter 'back' to go back,"
            puts "or enter 'exit' to leave Hogwarts."
            user_input = gets.chomp.downcase
            house_index = (user_input.to_i) - 1
            if user_input == "exit"
                @selection = "exit"
                break
            elsif house_index < House.all.length && house_index > -1
                house = House.all.sort_by(&:name)[house_index]
                house.house_info
            else
               error_message
            end
        end
    end

```

 I realized that the if/else statement I had inside the until loop was running before the until loop was reevaluating the value of my user input, therefore my else block would always run. I changed my else to and elsif and suddenly I was seeing (or not seeing) the output I expected.
 
Something that I learned in the process is that it’s always a good idea to let 2-3 other people try to break your program. I kept giving my program data it expected, so I assumed that all possible outcomes were accounted for. When my cohort lead used my project as an example during a POD meeting, I realized just how wrong I was. 

I’m very happy with my project. It shows good OO design, is relatively DRY, it’s fun and has the potential for expansion. I have plans to expand the type of characters users can learn more about (professors, ghosts, students from other schools), add a spells class type and maybe even add a search function, so users can find exactly the character they’d like. I’d also like to change the way characters are instantiated, making use of the API’s general Characters endpoint, instead of making an API call for each character created. The possibilities are almost endless.

