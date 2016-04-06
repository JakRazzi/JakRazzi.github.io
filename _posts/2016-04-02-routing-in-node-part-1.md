---
layout: post
title: "Routing in Node, Part 1"
date: 2016-04-02
categories: feature
tags: featured JavaScript
image: /assets/article-images/2016-02-26-preparing-for-hack-reactor/terminal-sublime-blurred.jpg
image2: /assets/article-images/2016-02-26-preparing-for-hack-reactor/terminal-sublime-blurred-mobile.jpg
image-align: right
image2-align: 82%
---

### Routing in Node.js
Routing tends to be a confusing topic when starting to dive into setting up a Node server. Hopefully, this post can clear some of that confusion up. Routing is actually very easy.

In this first part, I will go over how to set up routing in plain Node. In the second part, I will explain how to do this with the Express module (which makes things much easier). 

### What is Routing Anyway?
Routing is just telling your server where to look, or what to do for certain requests. When someone goes to www.example.com, they are going to the root of the server (or, '/' as in www.example.com/). We need to make sure that when they go there, they will get back the index.html file, along with any other dependencies that index.html needs (such as css or javascript files).

### Setting up a Route for the root -- '/'
First, let's set up a simple server in node. Be sure to install required dependencies with npm and save them to your package.json.

~~~javascript
var http = require('http'),
    fs = require('fs');

var app = http.createServer(requestHandler);

function requestHandler(req, res) {};

server.listen(3333, function(err) {
  if (err) return console.error(err);
  console.log('Server listening on: localhost:3333');
});
~~~

So now we have a server running, but it won't do anything for us yet. we need to set up our requestHandler to handle all of the requests we will have coming in. For this example, let's pretend we have a file structure like so:

~~~javascript
project-directory/
  public/
    index.html
    styles/
      style.css
    scripts/
      app.js

  server.js
~~~

To serve all static GET requests for the root, '/', we can do something like this:

~~~javascript
var headers = {
  // Declare your default headers here
};
var sendResponse(data, statusCode) {
  statusCode = statusCode || 200;
  data = data || 'OK';

  res.writeHead(statusCode, headers);
  res.end(data)
};
var actions = {
  GET: function(req, res, path) {
  fs.readFile(path, function(err, data) {
    if (err) {
      console.error(err);
      sendResponse('NOT FOUND', 404)
    } else {
      sendResponse(data);
    }
  })
  }
};

function requestHandler(req, res) {
  var staticReqPath = __dirname + '/public' + 
    (req.url === '/' ? '/index.html' : req.url);

  if (!actions[req.method]) {
    return sendResponse('NotFound', 404);
  }
  actions[req.method](req, res, staticReqPath);
  };
};
~~~

That's a good little chunk of code, so let's break it down. First, we're declaring some basic default headers. Then we're creating a simple helper function that will write the response headers and send it with our data in the body.

We're also declaring a helper object with property names of the types of request methods that we're willing to serve. In this case, we're just serving GET requests. The GET method will use the fs module to read the file at the path given to it. If the file doesn't exist, it will send back a 404 "NOT FOUND". If the file does exist, it will send back a 200 status code with the contents of the file in the body.

Finally, we set up our requestHandler function that we left blank earlier. var staticReqPath is just the variable that's going to hold the full path of the file we want to access. We are concating __dirname (which just equals the full path of the current file) with our static files directory, '/public', and then if they are just trying to access the root, '/', then we will send them to the index.html. Otherwise, we will send them to whatever file they are requesting.

If the request method they are asking for is not one that we are willing to serve, then we will send them back a 404. otherwise, we will call the method defined in our actions object with the request, response, and path arguments.

### Now We're All Set Up to Route Static File Requests

I'll go through a quick rundown of what happens when someone goes to localhost:3333

going to localhost:3333 is a GET request for the root, '/', so the path we will serve is /.../.../project-directory/public/index.html (where "/.../.../" is just the full path to the project directory).

Then, our html file is going to need its css file. So it will send a GET request to localhost:3333/styles/style.css, so this time, the path will be /.../.../project-directory/public/styles/style.css

Lastly, the html is going to need its javascript file, so it will send a GET request to localhost:3333/scripts/app.js, and our requestHandler will route it to /.../.../project-directory/public/scripts/app.js

### Other Routing Options

We don't always want to route to static files, so we can also declare individual paths that may or may not match actual directory paths.

~~~javascript
var paths = {
  '/not-an-actual-directory': function(req, res) {
    sendResponse();
  }

function requestHandler(req, res) {
  var staticReqPath = __dirname + '/public' + 
    (req.url === '/' ? '/index.html' : req.url);

  // New non-static routing
  if (paths[req.url]) {
    return paths[req.url](req, res);
  }

  // Static Routing
  if (!actions[req.method]) {
    return sendResponse('NotFound', 404);
  }
  actions[req.method](req, res, staticReqPath);
  };
};
~~~

Here, we can create an object that will hold methods for all the paths we are willing to accept non-static requests to. If we try to go to localhost:3333/not-an-actual-directory, then our 'if' statement will intercept the request, and we will get back a 200 'OK' response.

### Conclusion

So that's the basics of how routing works with a plain old Node server. All of this can actually be done in just a couple lines of code by using Node's Express module, so I'll write up a 'part 2' that talks about how express makes life easier. Thanks for reading!
