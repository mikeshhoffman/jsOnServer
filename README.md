# JavaScript on the server using Node.js and Express.js

Assignment 10 for HTML200.

In this example, a zip code is sent from HTML form via a jQuery Ajax call to an Express-on-Node.js app on my server.  The server then calls a remote weather API and returns weather data as a JSON object which is then displayed as HTML in the browser.

This example uses jQuery's ajax support to send JSON-formatted data between local client browser and Node.js running on the server.

As an example, the *temperature-by-zip-code* example from the book Express in Action by Evan Hahn is provided here at 
<https://github.com/mikeshhoffman/jsOnServer/tree/master/temperature-by-zip-code>

mirrored from the repo which is at 
<https://github.com/EvanHahn/Express.js-in-Action-code/tree/master/Chapter_05/temperature-by-zip-code>


## General information

"Node.js" typically means Express running on top of Node.js.

Node.js (<https://nodejs.org/en/>)

Express.js (<https://expressjs.com>)

You can't host a Node.js site at GitHub Pages, because GitHub pages only serves out static files, such as .html, .css, and .js files, all to be rendered and run locally on the browser.

Heroku cloud server is running a Node.js server, including npm capability on the server.
Heroku provides ways to remotely install npm modules, such as the equivalent of issuing "npm install express" on your local dev machine (localhost).

<https://pages.github.com/>

JSON is hierarchical/nested name-value pair objects.

Search web for "JavaScript on the server":
<https://www.google.com/search?q=javascript+on+the+server>


## Walkthrough of the example code

Below are selected lines from the app source code, that send data between client and server.

1. An HTML form gathers the zip code from the user.

views/index.ejs -- assembled on the server into a complete HTML file that is sent to the client as an HTML form running on the client, which then submits a zip code to this app's "the.js" running on the client.  This file also uses the remote Google jQuery/ajax .js file.

```javascript
<form class="pure-form">
    <input type="number" name="zip" placeholder="12345" autofocus required>
...
<script src="//ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
<script src="/the.js"></script>
```

2. 
A local JS file contains a jQuery onload handler, which selects the zip field from the submitted form, and prevents submitting the form, and trims the zip string.
The onsubmit handler then calls the jQuery $.ajax() method, passing a JSON or JS object literal, which constructs a zip code url like "/#####", specifing to return data as a JSON object, in a GET call.
So the endpoint REST method and url pattern resulting from the $.ajax() call is: 
GET /#####

public/the.js -- the client-side javascript file:

```javascript
$(function() {
  ...
  var $zip = $("input[name='zip']");
  $("form").on("submit", function(event) {
    event.preventDefault();
    var zipCode = $.trim($zip.val());
    ...
    var request = $.ajax({
      url: "/" + zipCode,
      dataType: "json"
    });
```

3. The Express-on-Node-js server defines a URL route as pattern "/#####" that it listens for, for a GET method; it listens for an incoming request of type:
GET /#####

The Express-on-Node.js route constructs a function which takes the regex variable (\d{5}) (the zip code) and puts it into the first param of the constructed function, as req.params[0].
This way, the server has the zip code that was entered into the HTML form.
The server calls another service (the zippity-do-dah zipcode-to-longitude/latitude service) to convert the zip code to a longitude/latitude (not shown, because less important than getting weather data).

The server then calls the forecast.io service endpoint, as weather.forecast(), passing the longitude and latitude (for the zipcode), and a callback function.
The weather endpoint service puts the weather data for that location into the data parameter of that callback function.  todo: put documentation from service here.  The server route GET /##### then ... or the callback function in that route, then selects only the data.currently.temperature member, as the value of the temperature member in the route's result's res.json object. 
This ends the app.js and returns the res result object to "the.js" running on the client.

app.js -- the main app javascript file residing and running on the server:

```javascript
var ForecastIo = require("forecastio");
var weather = new ForecastIo("my secret api key here");

app.get(/^\/(\d{5})$/, function(req, res, next) {
  var zipcode = req.params[0]; // from form submit
  ...
  weather.forecast(latitude, longitude, function(err, data) {
    ...
    res.json({
      zipcode: zipcode,
      temperature: data.currently.temperature
    });
  });
});
```

4. 

public/the.js (again) -- the client-side javascript file:

```javascript
$(function() {
    ...
    request.done(function(data) {
      var temperature = data.temperature;
      $h1.text("It is " + temperature + "º in " + zipCode + ".");
    });
```


## Heading 2

This is a test sentence.


### Heading 3

This is a test sentence.

*  Item
*  Item
*  Item

