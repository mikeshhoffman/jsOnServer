# JavaScript on the server using Node.js and Express.js

Assignment 10 for HTML200

In this example code, a zip code is sent from HTML form via a jQuery Ajax call to an Express-on-Node.js app on my server (localhost:3000).
The server then calls a remote weather API and returns weather data as a JSON object which is then displayed as HTML in the browser.

This example uses jQuery's ajax support to send JSON-formatted data between local client browser and Node.js running on the server.

This *temperature-by-zip-code* example is from the book Express in Action by Evan Hahn.
My mirror copy:
<https://github.com/mikeshhoffman/jsOnServer/tree/master/temperature-by-zip-code>
Hahn's original github repo:
<https://github.com/EvanHahn/Express.js-in-Action-code/tree/master/Chapter_05/temperature-by-zip-code>


## General information

"Node.js" typically means Express running on top of Node.js.

Node.js (<https://nodejs.org/en/>)

Express.js (<https://expressjs.com>)

You can't host a Node.js site at GitHub Pages, because GitHub pages only serves out static files, such as .html, .css, and .js files, all to be rendered and run locally on the browser.

This example could be hosted on the Heroku cloud server, which is running a Node.js server, including npm capability on the server.
Heroku provides ways to remotely install npm modules, such as the equivalent of issuing "npm install express" on your local dev machine.


## Walkthrough of the example code

Below are selected lines from the app source code, that send data between client and server.

1.  An HTML form gathers the zip code from the user.

    _views/index.ejs_ -- assembled on the server into a complete HTML file that is sent to the client as an HTML form running on the client, which then submits a zip code to this app's _the.js_ running on the client.
    This file also uses the remote Google jQuery/ajax .js file.

```javascript
<form class="pure-form">
    <input type="number" name="zip" placeholder="12345" autofocus required>
...
<script src="//ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
<script src="/the.js"></script>
```

2.  A local JS file contains a jQuery `onload` handler, which selects the zip field from the submitted form, and prevents submitting the form, and trims the zip string.
    The onsubmit handler then calls the jQuery `$.ajax()` method, passing a JSON or JS object literal, which constructs a zip code url like "/#####", specifying to return data as a JSON object, in a GET call.
    So the endpoint REST method and url pattern resulting from the `$.ajax()` call is: 
    GET /#####

_public/the.js_ -- the client-side javascript file:

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

3.  The Express-on-Node-js server defines a URL route as pattern "/#####" that it listens for, for a GET method; it listens for an incoming request of type:
GET /#####
    The route (an Express-on-Node.js route) constructs a function which takes the regex variable (\d{5}) representing the zip code and puts that variable into the first parameter of the constructed function, as `req.params[0]`.
    This way, the server has the zip code that was entered into the HTML form.
    The server calls another service, zippity-do-dah, to convert the zip code to a longitude/latitude.

  The server then calls the forecast.io service endpoint, as `weather.forecast()`, passing the longitude and latitude (for the zipcode), and a callback function.
  The weather endpoint service puts the weather data for that location into the data parameter of that callback function.

  The weather endpoint service is Darksky.net (which appears to have taken over forecast.io). <https://darksky.net/dev/docs>

  The callback function in the server route GET /##### then selects only the `data.currently.temperature` member, as the value of the temperature member in the route's result's `res.json` object. 
  This ends the _app.js_ processing and returns the `res` result object to _the.js_ running on the client.

_app.js_ -- the main app javascript file residing and running on the server:

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
the.js on the client, in the same form submit handler, uses the "request" variable defined above, to display the temperature.

public/the.js (again) -- the client-side javascript file:

```javascript
$(function() {
    ...
    request.done(function(data) {
      var temperature = data.temperature;
      $h1.text("It is " + temperature + "º in " + zipCode + ".");
    });
```
