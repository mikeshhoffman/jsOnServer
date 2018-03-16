# JavaScript on the server using Node.js and Express.js

Assignment 10 for HTML200.

"Node.js" typically means Express running on top of Node.js.

Node.js (<https://nodejs.org/en/>)

Express.js (<https://expressjs.com>)

You can't host a Node.js site at GitHub Pages, because GitHub pages only serves out static files, such as .html, .css, and .js files, all to be rendered and run locally on the browser.

Heroku cloud server is running a Node.js server, including npm capability on the server.
Heroku provides ways to remotely install npm modules, such as the equivalent of issuing "npm install express" on your local dev machine (localhost).

<https://pages.github.com/>

As an example, the *temperature-by-zip-code* example from the book Express in Action by Evan Hahn is provided here at 
<https://github.com/mikeshhoffman/jsOnServer/tree/master/temperature-by-zip-code>

mirrored from the repo which is at 
<https://github.com/EvanHahn/Express.js-in-Action-code/tree/master/Chapter_05/temperature-by-zip-code>


This example uses jQuery's ajax support to send JSON-formatted data between local client browser and Node.js running on the server.

The crucial link is:



JSON is hierarchical/nested name-value pair objects.

Search web for "JavaScript on the server":
<https://www.google.com/search?q=javascript+on+the+server>


views/index.ejs --

```javascript
<script src="//ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
```

public/the.js --

```javascript
$(function() {
    var request = $.ajax({
      url: "/" + zipCode,
      dataType: "json"
    });
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

