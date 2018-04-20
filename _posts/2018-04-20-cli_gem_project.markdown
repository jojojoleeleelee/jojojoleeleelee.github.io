---
layout: post
title:      "CLI GEM PROJECT!! "
date:       2018-04-20 14:33:36 +0000
permalink:  cli_gem_project
---


**First part - *pinning down an idea***
- I debated among a lot of things I could do: Scraping recipes of the day? Quotes of the day? In the end, I settled with scraping info on classical composers. 
- So again, I had to narrow down options. To be specific, I had three:
I had the honor of tuning in a CLI project study group and I quickly realized fancy and grandiose is not the aim (also, to scrape websites that aren’t too javascript heavy). I wanted to start small but solid :) 
My initial bets were on wikipedia (which I imagined, might be a nono for serious music historians)
https://en.wikipedia.org/wiki/List_of_Romantic-era_composers
I liked how it’s as straightforward as it gets in terms of scrapability. 
But the concern were that there were… TOO many composers in this list - hence I decided that if I were to use this link, I'd have to chunk it up into categories of early - middle - end.

But there of course, were other better websites options. 
For example, just 50 of the most well-known composers in this website - 
https://www.discogs.com/lists/The-50-Greatest-Composers/1571
But hmm... scraping that javascript heavy album section looks like a pain in the rear. So nah. Pass.

Another option came across - https://www.ranker.com/crowdranked-list/my-favorite-classical-composers-of-all-time
But this link just didn't really sit well with me. There weren't all that much meaningful content, plus I found the ranking of these composers to be a bit arbitrary and debatable.

Finally I came across this one: http://www.classical.net/music/composer/masterindex.php
How about a menu of alphabetical letters as categories? Nice!
For example, if the user inputs “B”, they’d see a list of Beethoven,  Bach, etc… and they’d choose to learn more about the specific composer once they're in the alphabetized categories.


**Second part - *getting set up correctly***
As simple as this may seem, for a ruby newb like me, I actually had a considerable amount of trouble saving, setting up everything over on github, even running my program. Thanks to Dakota - GREAT coach!! - and his patient 1-1 session, I was able to pin down the basics of how to navigate through the terminal (he taught me essential commands like 'ls -lah' 'git status/add/push/pull/commit and so much more) and I was all set to start coding for reals. And it was great to be able to run my actual code, finally. Whew! Thanks again, Dakota!

**Last but not least - *the actual code***
This was actually immensely enjoyable. I found myself actually *wanting* to work late into the night to debug and write code. It's one thing to build something with parameters like the labs given in the Learn curriculum and then finally let loose and implement all that you've learned so far (Object Oriented Ruby, class methods, arrays, hash, all those awesome functions and method protocals GAH! SOO much fun. It really is the most enjoyable cerebral puzzle). I also had a few takeaways - never panic when you see an error. Read and scrutinize what the error message is saying. Really. 9/10 times, it's probably something silly and immensely fixable. I did come across some real tough ones, but a bit of google search and reviewing the lessons helped iron everything out. 
And most of the time, you just have to think strategically. For example, I found out that in my alphabetical list of composers, the website actually was missing a "U" category because the website couldn't think of any composers whose name started with a "U" (silly, I can think of Viktor Ullmann, why hadn't they thought of him?). So I had to modify my existing function so that everything still lined up after the user wrote any letter from the alphabet after "T". It was much fun.

To elaborate - I had the user input a letter from the alphabet. So, if they put in "B" they'd see a list of composers whose name began with a "B" (like Bach, Beethoven, Brahms). but in order for me to scrape the website correctly, I had to convert this letter into a number. Why a number? Because the alphabetized groups were in arrays. array[0] = "A" section composers, array[1] = "B" section composers, etc. So I buildt this method just for this purpose.
  def to_number(letter)
    number = letter.tr("A-Z", "1-9a-q").to_i(25)
    if number > 20
      number -= 1
    else
      number
    end
  end
	
	The number greater than 20 signified it was after "T" so that it would take into account for the missing "U" section.
	Pretty cool stuff.
	
	After I figured out the code for the scraping and the object class of composers, I put all the pieces together with the CLI class. And voi-la! Project completed! :)

