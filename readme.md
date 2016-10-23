# AJAX and APIs

## Learning Objectives
- Explain the difference between synchronous and asynchronous program execution
- Explain why synchronous program execution is not conducive to the front-end.
- Use jQuery `$.ajax()` method to make asynchronous GET requests for data.
- Use jQuery's 'promise-like' methods to handle AJAX responses asynchronously.
- Render new HTML content using data loaded from an Ajax request.
- Perform POST, PUT, and DELETE requests to an API to modify data.

## Framing

In the first couple of weeks we learned how to style a semantically structured HTML site with the ability to manipulate the DOM. In the past couple weeks or so, we've been learning a lot about server-side requests and responses. Today we'll be tying these concepts together. Currently we know how to create applications with full CRUD on models in our database, and that's great. When we do that CRUD, however, it requires a page reload of some kind. It would be really nice if had some functionality on the client side of our application but still communicate with the backend without a page refresh. If only we had a client side language that was non-blocking and asynchronous...

* **Whats the difference between synchronous and asynchronous program execution?**
* **What kind of things can we do with non-blocking asynchronous program execution?**

## Turn & Talk (10 minutes / 0:10)

> 5 minutes T&T. 5 minutes review.

[Open up Google Maps in your browser](https://www.google.com/maps). As you interact with the web app, consider the following questions...

<details>
  <summary>What is happening asynchronously?</summary>

  > Pretty much everything.

</details>

<details>
  <summary>How would Google Maps work if everything happened synchronously?</summary>

  > The page would need to re-load whenever the map is updated. Consider how http requests work within your rails application.

</details>

<details>
  <summary>Why might synchronous programming not be effective for the front end?</summary>

  > We don't want to sit around and wait for code to execute before we load the rest of our script. It would be really nice if we could just describe what we want to happen when the code finally does execute.
  >
  > [A demo.](http://codepen.io/amaseda/pen/gwqGgK)

</details>

### What Is An API?

API stands for "Application Program Interface" and technically applies to all of software design. The DOM and jQuery are actually examples of APIs. Since the explosion of information technology, however, the term now commonly refers to web URLs that can be accessed for raw data.

As we move into building single page applications, now is the perfect time to start understanding how to obtain data on the client side and then render it on the browser.

> **tl;dr** - an API is a service that provides raw data for public use.

### API Data Formats (5 minutes / 0:15)

#### JSON

**JSON** stands for "JavaScript Object Notation" and has become a universal standard for serializing native data structures for transmission. It is light-weight, easy to read and quick to parse.

```json
{
  "users": [
    {"name": "Bob", "id": 23},
    {"name": "Tim", "id": 72}
  ]
}
```
> JSON is a serialized format, meaning it is made up of strings. While it may look like an object, it needs to be parsed so we can interact with it as a true Javascript object.

#### XML

**XML** stands for "eXtensible Markup Language" and is the granddaddy of serialized data formats (itself based on HTML). XML is fat, ugly and cumbersome to parse. It remains a major format, however, due to its legacy usage across the web. You'll probably always favor using a JSON API, if available.

```
<users>
  <user id="23">
    <name><![CDATA[Bob]]></name>
  </user>
  <user id="72">
    <name><![CDATA[Tim]]></name>
  </user>
</users>
```

## Where Do We Find APIs?

APIs are published everywhere. Chances are good that most major content sources you follow online publish their data in some type of serialized format. Heck, [even Marvel publishes an API](http://developer.marvel.com/documentation/getting_started). Look around for a "Developers" section on major websites.

**That sounds hard. Can't you just give me a freebie?**

Try the [Programmable Web API Directory](http://www.programmableweb.com/apis/directory) or the [Public APIs Directory](http://www.publicapis.com/).

## What Is An API Key? (5 minutes / 0:20)

While the majority of APIs are free to use, many of them require an API "key" that identifies the developer requesting access. This is done to regulate usage and prevent abuse. Some APIs also rate-limit developers, meaning they have caps on the free data allowed during a given time period.

Let's try making a GET request to the [Giphy API](https://api.giphy.com/)...

* Fist with no key: [http://api.giphy.com/v1/gifs/search?q=funny+cat](http://api.giphy.com/v1/gifs/search?q=funny+cat)
* Then with a key: [http://api.giphy.com/v1/gifs/search?q=funny+cat&api_key=dc6zaTOxFJmzC](http://api.giphy.com/v1/gifs/search?q=funny+cat&api_key=dc6zaTOxFJmzC)

> **It is very important that you not push your API keys to a public Github repo.** This is especially true when working with [Amazon Web Services (AWS)](https://aws.amazon.com/). Here's an example of a [stolen key horror story](https://wptavern.com/ryan-hellyers-aws-nightmare-leaked-access-keys-result-in-a-6000-bill-overnight).

## The Weather Underground API

For the first part of this lesson we'll be using the [Weather Underground API](http://www.wunderground.com/weather/api/d/docs). Follow the link and sign up for a key.

Once you've done that, visit the below link but make sure to replace `your_key` with your API key...

`http://api.wunderground.com/api/your_key/conditions/q/CA/San_Francisco.json`

You should see a big JSON object. Lucky for us, we'll be able to navigate through it using Javascript.

## `$.ajax` (25 minutes / 0:45)

We can interact with APIs using a jQuery method called AJAX. With the help of AJAX ("Asynchronous Javascript and XML") we can send HTTP requests from the client asynchronously without having to reload the page.

Let's try that out ourselves. Let's start by cloning down [this repo](https://github.com/ga-wdi-exercises/weather_underground_ajax)...

```bash
$ git clone git@github.com:ga-wdi-exercises/weather_underground_ajax.git
```

In `script.js`, we will use AJAX to send a `GET` request to the Weather Underground API...

```js
$("h1").on("click", function(){
  var url = "https://api.wunderground.com/api/f28a93cae85945b6/geolookup/conditions/q/va/midlothian.json"
  $.ajax({
    url: url,
    type: "get",
    dataType: "json"
    // $.ajax takes an object as an argument with at least three key-value pairs...
    // (1) The URL endpoint for the JSON object.
    // (2) Type of HTTP request.
    // (3) Datatype. Usually JSON.
  }).done(function(){
    console.log("Ajax request success!")
  }).fail(function(){
    console.log("Ajax request fails!")
  }).always(function(){
    console.log("This always happens regardless of successful ajax request or not.")
  })
})
```

> AJAX is given to us through the jQuery library. Like all jQuery methods, [it is just running vanilla Javascript under the good](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest).

You'll notice there are 3 functions chained onto the AJAX call. These are known as **promises**. Promises are callbacks that may or may not happen. A promise represents the future result of an asynchronous operation.

* `.done()` - this code is run if the AJAX call is successful
* `.fail()` - this code is run if the AJAX call is not successful
* `.always()` - this code is always run, regardless of whether the AJAX call is successful or not

But how do we go about accessing that big JSON object we saw before? By passing in an argument to the anonymous function callback. The `$.ajax` call returns a response that you can then pass in as an argument to the promise.

```js
.done(function(response)(){
  console.log(response)
})
```

> `response` contains the information received from the AJAX call.

We can drill through this response just like any other JS object...

```js
.done(function(response)(){
  console.log(response.current_observation.temp_f)
})
```

> Note that we use traditional Javascript object notation to access the response.

## You Do: DOM Manipulation Using Response Data (10 minutes / 0:55)

Take our existing code for the the weather underground app. Instead of logging the temperature, the `.done()` promise should add a div to the page that contains the current wind speed in MPH.  

#### Bonus

1. Create an input text field for City and State in the HTML.  
2. Have the endpoint url change dynamically based on user input to generate a div about current weather in that area.  

## Break (10 minutes / 1:05)

## Intro: AJAX and CRUD (5 minutes / 1:10)

So we've used AJAX to do an asynchronous `GET` request to an API. More often than not, 3rd party API's are read-only. They probably don't want just anyone to be able to update the weather however they want. That is not to say that kind of functionality doesn't exist, we just don't have access to it.

It just so happens we've built a Tunr Rails API where we can do full CRUD with AJAX. Go ahead and fork and clone [this repo](https://github.com/ga-wdi-exercises/tunr_rails_ajax).

Once you've cloned the repo, `cd` into it and run the usual commands...

```bash
$ bundle install
$ rails db:create db:migrate db:seed
```

We can now use `$.ajax()` to make asynchronous HTTP requests to our Tunr API! Let's go ahead and create a new Artists controller action and corresponding view: `test_ajax`

## Set up an AJAX Test View (10 minutes / 1:20)

Let's update our routes in `config/routes.rb` for a new route to test all of our AJAX calls in...

```ruby
get '/test_ajax', to: 'artists#test_ajax'
```

In `app/controllers/artists_controller.rb`...

```ruby
def test_ajax
end
```
> This controller action will be triggered when somebody visits our new route

Create `app/views/artists/test_ajax.html.erb` and add the following...

```html
<div class="test_ajax_get">AJAX GET!</div>
<div class="test_ajax_post">AJAX POST!</div>
<div class="test_ajax_put">AJAX PUT!</div>
<div class="test_ajax_delete">AJAX DELETE!</div>
```

<!-- AM: Add some styling so that ^ these ^ look like buttons... -->

Let's add an event listener to the first link in `app/assets/javascripts/application.js`...

```javascript
$(".test_ajax_get").on("click", function(){
  alert("clicked!");
})
```

## AJAX GET (5 minutes / 1:25)

Great, everything's working. Let's try doing a `GET` request to our API like we did with the Weather Underground. In `app/assets/javascripts/application.js`...

```javascript
$(".test_ajax_get").on("click", function(){
  $.ajax({
    type: 'GET',
    dataType: 'json',
    url: "/artists"
  }).done(function(response) {
    console.log(response);
  }).fail(function(response){
    console.log("Ajax get request failed.");
  })
})
```

> If we access the response object, we can see all of the artists that were seeded in the database. Inside the done promise, we can interact with and display all the contents of the response.

## Setup for AJAX POST (10 minutes / 1:35)

Lets update our view to include some input fields and all of our existing artists in `app/views/artists/test_ajax.html.erb`...

```html
<div class="test_ajax_get">AJAX GET!</div>

<label>Name:</label>
<input class="name" type="text">
<label>Photo_url:</label>
<input class="photo_url" type="text">
<label>Nationality:</label>
<input class="nationality" type="text">

<div class="test_ajax_post">AJAX POST!!</div>
<div class="test_ajax_put">AJAX PUT!!</div>
<div class="test_ajax_delete">AJAX DELETE!!</div>

<h1>Artists</h1>
<ul class="artists">
  <% @artists.each do |artist| %>
    <li>
      <a href="/artists/<%= artist.id %>">
        <%= artist.name %>
      </a>
    </li>
  <% end %>
</ul>
```
> Now we're listing all the artists in this view. We've also included an HTML form we'll use soon to generate new artists.

## AJAX Post (10 minutes / 1:45)

Let's create an artist using AJAX. In `app/assets/javascripts/application.js`...

```javascript
$(".test_ajax_post").on("click", function(){
  $.ajax({
    type: 'POST',
    data: {
      artist: {
        name: "Limp Bizkit",
        nationality: "USA"
        photo_url: "http://nerdist.com/wp-content/uploads/2014/12/limp_bizkit-970x545.jpg",
      }
    },
    dataType: 'json',
    url: "/artists"
  }).done(function(response) {
    console.log(response);
  }).fail(function(response){
    console.log("AJAX POST failed");
  })
})
```

Every time we click on this button another artist is generated. We can now create things in our database from the client-side. But there's a problem here: we've hardcoded the attributes.

* **How might we be able to dynamically acquire data on the client side instead of hardcoding values?**

## Break (10 minutes / 1:55)

## You Do: Dynamically Create Artists (15 minutes / 2:10)

> 10 minutes exercise. 5 minutes review.

Use the form to dynamically generate artists from the client side.

#### Bonus

Once you create a new artist in the database, update the view so that it includes the new artist.

> Hint: look at the response.

<details>
  <summary><strong>Solution...</strong></summary>

  ```js
  ```

</details>

<!-- AM: Add solutions to exercises -->

## We Do: AJAX PUT (10 minutes / 2:20)

Let's update an existing artist by adding an AJAX call to the next event listener...

```javascript
$(".test_ajax_put").on("click", function(){
  $.ajax({
    type: 'PUT',
    data: {
      artist: {
        name: "Robert Goulet",
        nationality: "American",
        photo_url: "http://media.giphy.com/media/u5yMOKjUpASwU/giphy.gif"
      }
    },
    dataType: 'json',
    url: "/artists/6"
  }).done(function(response){
    console.log(response);
  }).fail(function(){
    console.log("Failed to update.");
  })
})
```

> NOTE: This is just to show you how put requests work. Normally we wouldn't hardcode the URL. But think about how we could modify the DOM in order to effectively use AJAX PUT requests.

## You Do: AJAX DELETE (10 minutes / 1:30)

Make an event listener that deletes a record in our database using AJAX in `app/assets/javascripts/application.js`

<details>
  <summary><strong>Solution...</strong></summary>

  ```js
  $(".test_ajax_delete").on("click", function(){
    $.ajax({
      type: 'DELETE',
      dataType: 'json',
      url: "/artists/9"
    }).done(function(response){
      console.log("DELETED");
      console.log(response);
    }).fail(function(){
      console.log("Failed to delete.");
    })
  })
  ```

</details>

#### Bonus

Each artist should have a button or link that, when clicked...

* Allows for inline editing of the artist.
* Submits an AJAX `PUT` request using the information in the input fields creating in the previous bonus prompt. Update the view with the new artist information.
* Have a button or link that, when clicked, submits an AJAX `DELETE` request to delete that artist from the database. Remove the delete artist from the view.

## Closing / Questions

Angular developers often need to perform CRUD functionality to/from an API. This is the stuff that's happening under the hood when we're leveraging Angular Resources, which you will learn about tomorrow. You could imagine that writing all the setup for CRUD functionality through javascript could get a bit unwieldy. That's why front end frameworks like Angular exist, they solve that problem.

## Sample Quiz Questions

* Write an AJAX GET request to a known end point.  
* How does a promise differ from a callback?  
* Write an AJAX POST to create an object in a rails application.  

------

## Screencasts
* [Part 1](https://youtu.be/K4zRqJm7sXU)
* [Part 2](https://youtu.be/WoA5LnPXWB8)
* [Part 3](https://youtu.be/3xUy5QmrBuc)

## Practice

* Do everything we've done in class today for Songs.
* Create an AJAX request in another app you've created (e.g., projects, Scribble). Be sure to make sure your controller actions respond to JSON.


## Cliff Notes

In order to do an AJAX `get` request to a 3rd party API...

```javascript
$.ajax({
  url: url,
  type: "get",
  dataType: "json"
  // $.ajax takes an object as an argument with at least three key-value pairs...
  // (1) The URL endpoint for the JSON object.
  // (2) Type of HTTP request.
  // (3) Datatype. Usually JSON.
}).done(function(response){
  console.log(response)
  // Here is where you place code for DOM manipulation or anything else you'd like to do with the response
}).fail(function(){
  console.log("Ajax request fails!")
}).always(function(){
  console.log("This always happens regardless of successful ajax request or not.")
})
```

In order to do an AJAX `get` request to your own rails API:

```javascript
$.ajax({
  type: 'GET',
  dataType: 'json',
  url: "/artists"
}).done(function(response) {
  console.log(response);
}).fail(function(response){
  console.log("Ajax get request failed.");
})
```

In order to do an AJAX `post` request to your own rails API:
```javascript
$.ajax({
  type: 'POST',
  data: {artist: {photo_url: "www.google.com", name: "bob", nationality: "bob"}},
  dataType: 'json',
  url: "/artists"
}).done(function(response) {
  console.log(response);
}).fail(function(response){
  console.log("Ajax get request failed");
})
```

In order to do an AJAX `put` request to your own rails API:

```javascript
$.ajax({
  type: 'PUT',
  data: {
    artist: {
      name: "Robert Goulet",
      nationality: "American",
      photo_url: "http://media.giphy.com/media/u5yMOKjUpASwU/giphy.gif"
    }
  },
  dataType: 'json',
  url: "/artists/6"
}).done(function(response){
  console.log(response);
}).fail(function(){
  console.log("Failed to update.");
})
```

In order to do an AJAX `delete` request to your own rails API:

```javascript
$.ajax({
  type: 'DELETE',
  dataType: 'json',
  url: "/artists/9"
}).done(function(response){
  console.log("DELETED");
  console.log(response);
}).fail(function(){
  console.log("Failed to delete.");
})
```
