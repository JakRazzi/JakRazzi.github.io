---
layout: post
title: "Read and Write Data in Node"
date: 2016-03-23
categories: feature
tags: featured JavaScript
image: /assets/article-images/2016-02-26-preparing-for-hack-reactor/terminal-sublime-blurred.jpg
image2: /assets/article-images/2016-02-26-preparing-for-hack-reactor/terminal-sublime-blurred-mobile.jpg
image-align: right
image2-align: 82%
---

### Being able to read and write files is a useful and necessary tool with servers. 
In Node.js servers, we can do this with the built-in 'File System' module. You can require this in your javascript files with:  

~~~javascript
var fs = require('fs');
~~~  
  
Once fs is required by Node, we can take advantage of everything it has to offer us (which happens to be quite a lot).  Node has comprehensive [documentation](https://nodejs.org/api/fs.html) on their API doc page.

The list is so exhaustive that it would be beyond the scope of this little blog post to write about all of them, so consider this a little primer.

To write files on our server, we can use fs.writeFile(). This is the asynchronous option for writing files, which is important. If the data being sent to our server is exceptionally large, then writing synchronously would hold our server up for the entire download time as well as the write time. 

Asynchronously, we can recieve packets of information as they come in, and our server is still able to handle other requests. Then, once all the data is in, we can write the file and continue with our work.

fs.writeFile takes a file location argument, a data argument, an [optional] options argument, as well as a callback. The callback will be executed upon completion of receiving the data, and provides any error information. Here's an example:

~~~javascript
fs.writeFile('~/Documents/file.txt', 'Hello World!', function(error) {
  if (error) {
    console.log(error);
  } else {
    console.log('Writing file complete!');
  }
});
~~~  
  
fs.readFile is similar, but instead returns the contents of a given file. It takes a file path argument, an [optional] options argument, and a callback (again, which will be called upon completion of reading the file and will provide any error information). Here's an example of that:

~~~javascript
fs.readFile('~/Documents/file.txt', function(error) {
  if (error) {
    console.log(error);
  }
});

// returns 'Hello World!'
~~~  
  
So that's the basics of reading and writing files in Node.js!