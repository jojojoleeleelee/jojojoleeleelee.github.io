---
layout: post
title:      "GASTRONOME:RAILS PROJECT"
date:       2018-05-25 21:23:46 +0000
permalink:  gastronome_rails_project
---


Before I begin, could I preface this blog post with this ... rather general statement.

**Rails is magical.**

It really is one magical beast.
Likewise, one must be a **wizard**  to know the ins and outs, the temperament and comprehensive list of spells to cast at particular situations. 

Well, general statements aside, let me walk you through how I built my project.

I always thought that it'd be great to have an actual application that manages my pantry. Something that kept track of the ingredients I currently have sitting on my cupboard, or possibly rotting in the back of my fridge.

So hence, the idea of this project.

Gastronome - a recipe manager while making sure the ingredients in your pantry are utilized before it goes bad. 


***THE MODELS***
User:
username
email
password
admin

Ingredient:
name
user_id (a carrot that belongs to Joe and a carrot that belongs to Anna is a different carrot)
quantity (1 bar of chocolate as opposed to 20 oz of chocolate)

Recipe:
title - e.g. chocolate pudding, deviled eggs
user_id - who made what and who's recipe does it belong to??
ingredients - e.g. 20 minutes or 2 days (except no one would actually make anything that took two days)
description - this would be the instruction to make the recipe
rating - given by any user

RecipeIngredient:
ingredient_id
recipe_id
=== basically the way to join the two tables together. Ingredients can have a lotta recipes, carrot cake, carrot soup, carrot pickle, etc. And Recipes can have many ingredients - chocolate pudding would have chocolate, milk, egg yolks, maybe a touch of salt, etc etc.

This is a snippet of my beginning thought process - 

> **
> This may be me being overtly ambitious, but honestly, who on earth has the time to fill in every single detailed instruction to their recipe? Nope. We live in a fast world, with impatient people. So SCRAPING TO THE RESCUE!
> 
> Websites I would like to scrape...
> 
> 1. kitchn - looks like a lot of React/Javascript. Could be more complex that I'd like.
> when I search quinoa carrot pepper I get zero results, though. Hmm.
> https://www.thekitchn.com/search?q=quinoa+carrot+pepper
> chocolate pudding is more promising:
> https://www.thekitchn.com/search?q=chocolate+pudding
> 
> OR a website that is pretty...?
> 2. Bon Appetit (what's up with %20 after each keyword search query?)
> when I search quinoa carrot pepper - this is what I get:
> https://www.bonappetit.com/search/quinoa%20carrot%20pepper
> 3. Food52
> In terms of sheer volume, this is the best option. I got 15,339 recipes matching "quinoa carrot rice"
> https://food52.com/recipes/search?q=quinoa+carrot+rice
> OKAY! I'm going with either Food52 or Bon Appetit. I'll decide later when I actually begin to scrape this.
> 
> A possible problem with scraping
> should I scrape the whole entire page of the recipe?
> For example, for FOOD52, there is a snippet of the recipe (optional)
> and then a serving (e.g. serves: 2-4)
> then a list of ingredients
> and lastly a numerically divided... and rather comprehensive list of directions.
> AND a photo too! (not always, but most of the time)
> After I search, I should just grab the first three or else my already slow app will become even more unbearably slower. For example, the quinoa carrot rice search page put out 22 recipes in its first page... I don't want the app to be retrieving and scraping the detailed recipe of each and every one of those recipes. Yes, just three will do.
> Let's try something with a typo... like instead of chocolate, our Mr. Joe is in a rush for his chocolate fix so he writes 'chocolat'. No worries, it still outputs 3,618 recipes
> https://food52.com/recipes/search?q=chocolat
> OR how about a REALLY BAD typo
> like coclate instead of chocolate?
> https://food52.com/recipes/search?q=cocloate
> no worries, still got three recipes.
> and what about super obscure ingredients... Like DURIAN
> https://food52.com/recipes/search?q=durian
> surprisingly works - got 10 recipes!
> OR...... something crazy
> like rabbit terrine pistachio
> https://food52.com/recipes/search?q=rabbit+terrine+pistachio
> I am getting a ton of irrelevent recipes like pistachio cake and summer squash couscous... and grilled aged cheddar cheese?? huh?
> but I guess it'll work. Better than a blank page (nil must be avoided at all costs, even though I have to make orange-pistachio biscotti instead of my desired rabbit terrine with pistachio).
> which makes me wonder... should I scrape google instead? Google would give me a legit rabbit terrine with pistachio recipe.
> https://www.google.com/search?ei=0eQFW878MJXQ9AOIlZ34Cw&q=rabbit+terrine+with+pistachio&oq=rabbit+terrine&gsl=psy-ab.1.0.35i39k1j0l7j0i22i30k1l2.786.3664.0.4584.17.12.5.0.0.0.148.1249.6j6.12.0....0...1.1.64.psy-ab..0.17.1304...0i131k1j0i20i264k1j0i20i263i264k1j0i20i263k1j0i10k1j0i22i10i30k1j33i160k1j0i13k1j0i13i30k1.0.yHAlvX3AyPA
> It's just hella long... and possibly error-prone.
> So yeah, Food52 it is!
> I think I may be spending an inordinate amount of effort adding this added function of scraping websites... but hey. It's cool.
> > *
> > 

Things were off to a good start, until I got to scraping. Oh man. Scraping the websites were not.... NOT EASY! It took some serious scraping wizardry. And then for the users to be able to choose certain recipes and such, it became immensely complex. In fact, I'm planning on refractoring some of my code in my recipes controller. 

Basically, I decided there was only one way to go. Make a whole new RecipeScraper class from scratch and plop it in the models.

```
require 'open-uri'
require 'nokogiri'

class RecipeScraper
  # attr_accessor :recipe_url, :pic, :title
  attr_reader :url, :data

  def initialize(url)
    @url = url
  end

  def data
    @data ||= Nokogiri::HTML(open(url))
  end

  def recipe_url
    @url = data.css('.collectable-tile').css('h3').xpath('//div/a/@href').map do |u|
      u.text.strip
    end
    final = @url.select {|x| x.start_with?("/recipes/")}
    final.pop(5)
    final.shift(4)
    final
  end

  def pic
    data.css('div.photo-block').css('img').map do |u|
     u['src']
    end
  end

  def title
    data.css('div.photo-block').css('a.photo').css('img').map do |u|
     u['alt']
    end
  end

  def all_info
    data.css('.recipe').each do |index|
      @recipe = {
      :title =>
      index.css('h1')[0].text.strip!,
      :pic_url =>
      index.css('img').map{|i|i['src']}.first,
      :ingred =>
      index.css('.clearfix').css('ul.recipe-list').css('li').map {|i|i.text.gsub(/\s+/, ' ')}.join.strip!,
      :description =>
      index.css('.clearfix').css('ol').map {|i|i.text.gsub(/\s+/, ' ').strip!}.join
    }
    end
    @recipe
  end
end

```

With this, I could call on this method in the middle of my controller action:

```
rec_scrape = RecipeScraper.new("https://www.food52.com/recipes/search?q=#{ingred_array.gsub(" ", "+")}")
      urls = rec_scrape.recipe_url
      pics = rec_scrape.pic
      titles = rec_scrape.title
      @collection = urls.zip(pics, titles).each {|b| b}
```

I had to bundle up the urls, pics and titles altogether to display it in the view and to do this #zip came in super handy. It took three of my arrays and combined it into one array. 
Let me elaborate.

```
@first_array = [1,2,3,4]
@second_array = ["chocolate" , "garlic", "beans", "rice"]
@third_array = ["jill", "polly", "dan", "mike"]

@first_array.zip(@second_array, @third_array).each {|b| b}

=

[[1, "chocolate", "jill" ], [2, "garlic", "polly"], [3, "beans", "dan"], [4, "rice", "mike"]]

```

It came in super handy. 


Overall, this was perhaps the best way to learn how to code. By building. I really loved the blend of problem solving along with the creative process. 

Time to go and actually cook myself a real meal :)
