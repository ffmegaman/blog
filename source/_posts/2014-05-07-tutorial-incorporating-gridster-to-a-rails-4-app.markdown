---
layout: post
title: "Tutorial: Incorporating Gridster to a Rails 4 app"
date: 2014-05-07 02:42:05 +0900
comments: true
categories: 
---
###Intro
Gridster, by Ducksboard, is a jQuery library for building drag and drop items in a grid layout. You can use it drag listed elements anywhere in the defined grid.

###Ensure proper configuration
Before we begin, first we need to ensure that jQuery will work properly in Rails 4. Rails 4 by default has the `gem 'turbolinks'` in the gemfile, which will enhance your app's loading speed by refreshing only the contents in the `<body>` of your html files, but it may screw up some jQuery scripts. To fix this, include `gem 'jquery-turbolinks'` which will allow your jQuery scripts to fire up properly. After adding the `gem 'jquery-rails'` to your gemfile, be sure to add `//= require jquery.turbolinks` just after `//= require jquery` to application.js and move `//= require turbolinks` to the very bottom of the list. Don't forget to perform bundle install. Now lets move on to the actual implementation.<!-- more -->

###Lets add Gridster to our app!
**First:** Go to http://gridster.net/#download and download jquery.gridster.min.js and jquery.gridster.min.css.

**Second:** Put jquery.gridster.min.js into the vendor/assets/javascripts and put the jquery.gridster.min.css file into the vendor/assets/stylesheets folder of the rails app.

**Third:** Go to application.js and add `//= require jquery.gridster.min.js`. Then go to application.css and add `*= require jquery.gridster.min.css`.

**Fourth:** In one of your stylesheets such as, custom_gridster.css.scss, add the follow code:

```css customer_gridster.scc.scss
.gridster-cell { 
	background-color: #2EFEF7;
	list-style-type: none;
}
```

**Fifth:** Create a new file in app/assets/javascripts. For example, custom_gridster.js.coffee. Add the following line to activate the gridster scripts:

```javascript custom_gridster.js.coffee
$(document).ready ->
  $(".gridster ul").gridster({
      widget_margins: [10, 10],
      widget_base_dimensions: [140, 140]
  }); 
```

**Sixth:** In your view, wherever you want the gridster layout to be, add the following:

```html
<div class="gridster">
    <ul>
        <li class="gridster-cell" data-row="1" data-col="1" data-sizex="1" data-sizey="1">Block1</li>
        <li class="gridster-cell" data-row="2" data-col="1" data-sizex="1" data-sizey="1">Block2</li>
        <li class="gridster-cell" data-row="3" data-col="1" data-sizex="1" data-sizey="1">Block3</li>
 
        <li class="gridster-cell" data-row="1" data-col="2" data-sizex="2" data-sizey="1">Block4</li>
        <li class="gridster-cell" data-row="2" data-col="2" data-sizex="2" data-sizey="2">Block5</li>
 
        <li class="gridster-cell" data-row="1" data-col="4" data-sizex="1" data-sizey="1">Block6</li>
        <li class="gridster-cell" data-row="2" data-col="4" data-sizex="2" data-sizey="1">Block7</li>
        <li class="gridster-cell" data-row="3" data-col="4" data-sizex="1" data-sizey="1">Block8</li>
 
        <li class="gridster-cell" data-row="1" data-col="5" data-sizex="1" data-sizey="1">Block9</li>
        <li class="gridster-cell" data-row="3" data-col="5" data-sizex="1" data-sizey="1">Block10</li>
 
        <li class="gridster-cell" data-row="1" data-col="6" data-sizex="1" data-sizey="1">Block11</li>
        <li class="gridster-cell" data-row="2" data-col="6" data-sizex="1" data-sizey="2">Block12</li>
    </ul>
</div>
```
`data-row` is the y position and `data-col` is the x position. `data-sizex` is the length of the block and `data-sizey` is the height of the block. By changing those values you can manually reposition or resize the blocks.


Restart your server and take a look at your new Gridster layout.
