---
layout: post
title: "JS: Passing by Reference or Value"
date: 2016-03-05
categories: 
tags: JavaScript
image: /assets/article-images/2016-02-26-preparing-for-hack-reactor/terminal-sublime-blurred.jpg
image2: /assets/article-images/2016-02-26-preparing-for-hack-reactor/terminal-sublime-blurred-mobile.jpg
image-align: right
image2-align: 82%
---

### When passing around variables in JavaScript, it's important to remember how each type of variable gets passed. 

Numbers, strings, booleans, 'null', and 'undefined' are all considered immutable. Once you create them, the value itself cannot be changed. 

On the other hand, all objects (including arrays and functions) are considered to be mutable. You can change the properties of these at any time, from anywhere, without needing to make a copy first.

Let's look at a few examples:

## Passing values

When we say,

    var foo = 39;
    var bar = 'hello world'

we are passing in the immutable number 39 to foo, and the immutable string 'hello world' to bar. I can reassign foo and bar to new values at any time, but I can't ever change the original values.

So what about this?

    var foo = 39;
    var foo = foo + 60;  // foo now equals 99

didn't we just change the 39 into a 99? Well, no. Not exactly.

What we actually did was take the original value 39, and used it plus 60 to create a *new* value, 99. We then reassigned foo to that new value.

This may seem like I'm nitpicking, but it becomes important once I get into passing references. Here's that same example, done in a different way, just to make sure we're all on the same page.

    var foo = v1;
    var foo = foo + v2;
    console.log(foo);  // =>  v3

In this example, v1 equals some value (possibly a number, like above), and v2 and v3 equal totally separate values.

So what does this mean when we start passing values by their variable names? Here are a few examples of what happens.

A.

    var foo = 33;     //  (v1)
    var bar = foo;    //  (v1)
    foo = 63;         //  (v2)
    console.log(bar)  // =>  33  (v1)

B.

    var foo = 'hello';      //  (v1)
    var bar = 'world';      //  (v2)
    bar = foo + ' ' + bar;  //  (v1) + (v3) + (v2)
    console.log(bar);       // =>  'hello world'  (v4)
    
I make such a fuss about this because objects work in a similar way, except that you can edit their properties from anywhere they are referenced.

## Passing References

If I create an object and fill it with a few properties, then assign that object to a variable, then I have just created a "reference link" (that's not actual terminology, just my way of explaining things). Any time we create a new variable and assign to it any existing or new objects, we are creating new reference links.

    var foo = {a: 'hello', b:'world'};
    
Here, we have created a new object, given it two keys (which we've assigned two immutable values), and then passed that object *by reference* to the variable foo.

If we were to substitute referenced objects with 'r' and values with 'v', it might look like this:

    var foo = r1;  //  which contains r1.a: v1, and r1.b: v2
    
If we then make a new link, and overwrite the original link, what happens?

    var foo = {a: '21', b:'hello', c:'world'};
    // r1 contains r1.a: v1, r1.b: v2, and r1.c: v3
    
    var bar = foo;  //  bar now links to r1
    
    var foo.c = 'ciao';  //  here we *edit* r1.c to equal v4
    
    var foo = null;  // here we are *unlinking* foo from r1
    
    console.log(bar);  // =>  {a: '21', b: 'hello', c: 'ciao'}
    // r1 contains r1.a: v1, r1.b: v2, and r1.c: v4

I hope this is all making sense. Even though we set foo to null, we didn't overwrite the r1 object. We simply unlinked foo from r1. Our bar variable still had a link to the r1 object, so it still exists.

We were also able to change the object that bar pointed to from outside of bar because foo also had a link to that object.

Remember, arrays are just another form of object in JavaScript, so this applies to them as well.

    var foo = [1, 2, 3];
    // r1 contains r1.0: v1, r1.1: v2, and r1.2: v3
    
    var bar = foo;  //  bar now links to r1
    
    bar.push('hello')
    // here we create a new index, r1.3, and assign v4
    
    bar = 93;  //  We unlink r1 and assign v5 to bar
    
    console.log(foo);  // =>  [1, 2, 3, 'hello']

## Practice

Here is a tricky problem that will text your knowledge of passing by value or reference. Try to figure it out on your own, a sketchpad might help if you need it. The answer will be at the bottom.

A.  

    var foo = {
      a: 33, 
      b: [1, 2, 3],
      c: 'Hi'
    };
    
    var bar = foo.b;
    
    bar[2] = {
      d: 'good',
      e: 'luck'
      };
    
    var fie = bar[2];
    
    var objMangler = function (obj) {
      delete obj.b;
    };
    
    bar = undefined;
    
    objMangler(foo);
    
    console.log(foo);  //  ???
    console.log(bar);  //  ???
    console.log(fie);  //  ???
    

### ANSWER: 

    console.log(foo);  // =>  {a: 33, c: 'Hi'}
    console.log(bar);  // =>  undefined
    console.log(fie);  // =>  {d: 'good', e:'luck'}


#### Explanation:

foo is initialized to a referenced object (r1) with properties. Its 'b' property references an array (r2).

bar is linked to the referenced array (r2) at foo.b

The immutable value at index 2 inside bar (the referenced array, r2) is replaced with another reference to an object (r3).

fie is linked to the referenced object (r3) at index 2 inside bar (the referenced array, r2).

The reference from bar to the array (r2) is now replaced by the immutable value 'undefined'.

objMangler is run with foo as an input, and foo's reference to the array (r2) is deleted. Since nothing else is linking to the array (r2), it gets garbage collected by JavaScript.

*BUT*, the object (r3) that was **referenced by** the array (r2), still has a reference to it, so it *doesn't* get garbage collected along with the array (r2).

*This is where most people get tripped up.* A referenced object will exist for as long as something references it. *Even if its containing object is deleted*.

So foo equals {a: 33, c:'Hi'} because it always referenced the object (r1), and because objMangler deleted its 'b' property.

bar equals undefined because we replaced its reference to the array (r2) with the value 'undefined'.

Finally, fie equals {d: 'good', e:'luck'} because it maintained its reference, despite the fact that its containing array (r2) was deleted.


## Closing

I really hope this all made sense. If you have any further questions, then please leave a comment. I'd be happy to help!
