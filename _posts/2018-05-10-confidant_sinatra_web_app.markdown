---
layout: post
title:      "Confidant, Sinatra Web App."
date:       2018-05-10 21:42:04 -0400
permalink:  confidant_sinatra_web_app
---


Ah, where do I begin! 

First sketches:
I wanted to build an interactive web app, something that output something different everytime. Something dynamic. Something fun and useful.

Basic blueprint:

Have a user put in their emotional state, write out a reason why, click submit.

The application processes the Emotion, in return uses that word to return a customized output of bible verses related to that emotion.

First the sketches: 

Class User:
* username
* email
* password

Class Emotion: 
* name of that emotion
* content: reason why 
* bible verses related to this particular event
* user_id 

User has many emotions
Emotion belongs to user.

The bible verse was my added touch - I wanted something fun and dynamic for the user, so I built a separate scraper class to scrape the bible verses using the instance of emotion that the user input.

The most challenging part of this project was to keep myself steady and not going too crazy. In my excitement, I envisioned something grand. Something visionary:
I wanted not only bible verses, but possibly synonyms of that word using Dictionary.com to scrape all synonymns and antonyms to save those emotions as an array and basically walk the user through a counseling session...

Then maybe add an easter egg by giving the user a few instances of high-profile figures who felt such emotion as well (I thought of scraping a quotes website to access inspirational quotes from books and other noteworthy sayings - Gettysburg Address? Yes! Winston Churchill? OH PLEASE!)

But I toned down my excitement, decided to have just bible verses. 

It was good review of what I was able to implement in my CLI Ruby gem project, and also a good way to practice nokogiri, object oriented programming in the ruby language, class methods vs. instance methods... modules and last but not least, get introduced to the RSPEC language. It was also fun to sharpen my CSS and HTML skills!

Some issues I ran into - 

should I add a timestamp? 

also, what about duplicate emotions? Say a user felt sad previously, should I update the previous one or create a new session with the same emotion?

```
  post '/emotions' do
    if !params[:name].empty? && !scrape_verses(params[:name]).empty?
      @emotion = Emotion.create(name: params[:name].downcase)
      @emotion.content = params[:content]
      @emotion.user_id = session[:user_id]
      @emotion.verse = scrape_verses(params[:name]).join(" *** ")
      @emotion.save
      flash[:message] = "I feel ya mate."
      redirect "/emotions/#{@emotion.slug}"
    else
      flash[:message] = "Sorry, can you write something... I can understand!"
      redirect '/emotions/new'
    end
  end

  post '/emotion/search' do
    @emotion = Emotion.find_by_slug(params[:slug])
    if !@emotion.nil?
```

More specifically, should it be

```
 @emotion = Emotion.create(name: params[:name].downcase)
 
 OR
 
 @emotion = Emotion.find_or_create_by(name: params[:name].downcase)

```

I decided to go with the former - Emotion.create. Each emotion is unique, though the word itself may be the same.

Also, I ran into shotgun server running in the background because I didn't ^C properly. 
Through this, I learned a valuable command - 

```
ps ax | grep shotgun

RETRIEVE THE PID

kill -9 <PID>
```
kill by itself doesn't work sometimes. kill -9 basically means hunt it down, burn it to the ground, leave no trace of it.


In all, it was bittersweet when I got all the functions, routes and views running. It was fun thinking about code, finding better ways to solve the issues.

I immensely enjoyed this process, perhaps while I am refractoring this, I can add more functions and perhaps even expand some of the classes - class Verse (maybe?) and associate users and emotions in a has_many: through: association :)

https://github.com/jojojoleeleelee/Confidant

It really is one thing to take notes, practice, hone coding skills through labs and assignments given by the curriculum (all exceptional ways to learn, no doubt!) and then finally let loose and utilize all the acquired knowledge and build something complete new from scratch! It is an exhilerating feeling to know that the possibilities are really limitless!
***
