---
layout: post
title:      "Gastronome and its JS Upgrade"
date:       2018-08-24 14:36:46 +0000
permalink:  gastronome_and_its_js_upgrade
---


So, the premise of the Rails - Javascript Project is pretty straightforward. 

1. Add a index page that renders links and new pages using Active Model Serialization and JSON.
2. Have a navigation button that can go between previous and next Ingredients in the pantry.
3. Add comments to the Recipe page using javascript prototypes. This would fulfill the requirement of adding JSON responses into Javascript Model Objects. Done with AJAX POST request.
4. Sign up page/Log in page is rendered through a AJAX GET request.

Some issues I faced was the navigation button for the ingredients show page.

For one, how would I show the next as opposed to the previous ingredient?

That is when I came up with a Ingredient model specific method for determining whether it is not last nor first:
```
class Ingredient < ActiveRecord::Base
  belongs_to :user
  has_many :recipe_ingredients
  has_many :recipes, through: :recipe_ingredients
  validates :name, presence: true

  def self.fav_ingredient
    Ingredient.group(:name).order('count_id DESC').limit(1).count(:id)
  end

  def self.top_three_favs
    Ingredient.group(:name).order('count_id DESC').limit(3).count(:id)
  end


  def is_not_last
    Ingredient.exists?(self.id + 1)
  end

  def is_not_first
    Ingredient.exists?(self.id - 1)
  end
end

```

Pretty simple enough.
In my Active Model Serializers, so the IngredientSerializer, I made sure to add this method.
```
class IngredientSerializer < ActiveModel::Serializer
  attributes :id, :name, :created_at, :is_not_first, :is_not_last
end

```

This would then be transferred through JSON and plugged into the Ingredient prototype.

```
function loadIngredient(button, next) {
  let ingredientId = 0;
  if (next) {
    ingredientId = parseInt(button.dataset.ingredientId) + 1
  } else {
    ingredientId = parseInt(button.dataset.ingredientId) - 1
  }


  let posting = $.get(`/ingredients/${ingredientId}.json`)
  posting.done(function(ingred) {
	
    let id = ingred["id"]
    let name = ingred["name"]
    let created_at = ingred["created_at"]
    let notFirst = ingred["is_not_first"]
    let notLast = ingred["is_not_last"]

    ingredient = new Ingredient(id, name, created_at, notFirst, notLast)
    ingredient.display();
    addIngredientNavEvent();
  })
}
```

And then created and displayed on the page -

```
class Ingredient {

  constructor(id, name, created_at, notFirst, notLast) {
  // how to determine not first or not last?? notFirst notLast
  this.id = id;
  this.name = name;
  this.created_at = created_at;
  this.notFirst = notFirst;
  this.notLast = notLast;
  }

  display() {
    let ingredient = ""
    let date = (this.created_at).slice(0, 10)
    let newName = (this.name).toUpperCase()

    ingredient += `<h1><em> ${newName} </em></h1>`
    // debugger;
    if (this.quantity !== undefined) {
    ingredient += `<h2>${this.quantity}</h2>`
    }

    ingredient += `<h2>Date added: ${date} </h2><br>`
    ingredient += `<br><br><h2><small>Back to</small><sup><a href="/recipes/index"> PANTRY</a></sup></h2><br><br>`

    if (this.notFirst) {
      ingredient += `<button id='previous-ingredient' data-ingredient-id="${this.id}" style='float:right;'>PREVIOUS</button>`
    }
    if (this.notLast) {
      ingredient += `<button id='next-ingredient' data-ingredient-id="${this.id}" style='float:left;'>NEXT</button>`
    }
    $("div#ingredient")[0].innerHTML = ingredient
  }
}
```

And finally time to build the actual button!

```

function addIngredientNavEvent() {
  $("button#next-ingredient").click(function(e) {
    // debugger
    console.log("Load Next Ingredient")
    loadIngredient(this, true)
  })
  $("button#previous-ingredient").click(function(e){
    console.log("Load Previous Ingredient")
    loadIngredient(this, false)
  })
}
```

And order matters! To make sure these lines of code are read, the $ - preventDefault Jquery

```
$(function() {
  addIngredientNavEvent()
})
```

And finally, add the <div> with id of "ingredient" for the page to render the new JSON results.

A few things I had to keep in mind was everytime the button of previous and next was clicked, I needed a click listener attached to the freshly rendered button elements. Hence the function for the new click events in the newly rendered buttons.

While building these new added javascript functions, I couldn't help but realize how sometimes developing a webpage is really one little detail at a time. That detail might cause a few bugs and hair pulls, but in the end, it's promising how versatile and endless the possibilities in user experience is! :)




