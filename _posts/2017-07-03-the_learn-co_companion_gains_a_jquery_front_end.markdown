---
layout: post
title:  "The Learn-Co Companion gains a JQuery front end"
date:   2017-07-03 22:29:20 +0000
---


Well, I've finally finished my Rails-JQuery project.  The key to completing this was to read and understand the requirements and make things only as complicated as necessary.

At the start, I picked a couple of key pages to convert to AJAX, and I started the process without any real difficultly.  After a few days, I had my show page running, and I could next/previous between them with no issues.  But then my vacation intervened -- A really awesome trip to London -- but I expected to be able to complete my project within a couple days after returning.

When I got back, I got ready to start working on another page to modify, but my overconfidence led me to thinking I'd implement client-side page navigation.  I imagined a router.js file that would initialize, update the page, and add listeners.  There would be a parent view.js that would define the interface for the views, and individual pages would create page-specific children of view.js.  Data would be neatly seperated into javascript model objects.  It was going to be really slick!

I began by looking to see if there was already a straightforward blog or tutorial.  I found a few:

- https://tutorialzine.com/2015/02/single-page-app-without-a-framework
- https://jack.ofspades.com/developing-small-javascript-components-without-frameworks/
- https://www.codeproject.com/Articles/1046893/A-Simple-Single-Page-Application-Framework

Collectively, these were great resources that highlighted some useful techniques for using JS and AJAX to build a website.  But none was really what I was looking for -- they didn't provide enough separation of concerns to justify the added complexity, or they didn't scale to the more complicated pages I wanted to build.

## Frameworks vs. Libraries

I also found the following blog post that I found really illuminating:  [Do you really want an SPA Framework?](https://mmikowski.github.io/no-frameworks/)  It's a great post that I recommend you read, but here are a few of the key points:
- frameworks are constantly evolving and dictate the structure of your program.  Consequently, it can be much more difficult to keep your app 'up to date'
- frameworks are generally 'all-in-one' solutions that are complex and require more time to learn and master than simple libraries.
- because of the complexity and size, frameworks tend to slow down your learning and testing cycle times.

On the other hand, you can generally mix-and-match libraries to get whatever features you need, update (or replace) them individually, and they are more portable than the latest and greatest framework.

The argument being made here is that you're generally better off creating your own custom 'framework' using libraries.  Exactly what I was planning to do.

## But, building a custom framework is hard.
After a couple more days searching, reading, and thinking, I decided that doing this 'right' isn't straightforward, but I had fallen into the mental trap that this was necessary.  I got frustrated, and a couple days later attended a study group for the Jquery-Rails project planning.  It was in this study group that I was reminded that the project requirements did not include anything nearly as complicated as my vision had become.

I reviewed the project requirements again, and found that I had already met about half of them.  I vowed to complete the remaining ones as simply as I could and I was done in a couple of days.






