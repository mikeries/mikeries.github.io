---
layout: post
title:  Learn-Co Companion - By Students, For Students
date:   2017-05-22 01:40:24 +0000
---


Last week I was hanging out in Slack, at a total loss for an idea for my Final Rails project.  I decided I'd have to go ahead and do one of the example domains -- a group task manager.  I figured this would be something useful that maybe could be built into a real project management app once I have more time.

And then I heard some students wish for a better way to track their progress through the curriculum, and I started to wonder if something like that could be made to fit the project requirements.  After some thought I decided it could, and so the Learn-Co Companion App was begun.

The application not only shows how many lessons you have completed, but also allows you to take notes and tag the each lesson so that you search and find stuff.  I know that's something I often do, trying to remember where we learned about the has_secure_password method, or what have you.

I had to be careful to restrict myself to a rather limited set of features, since I'd like to graduate before too long.  Besides, all the really cool stuff requires access to the Learn API (hint hint).

One of the challenges I faces was an unclear vision of exactly what it would be.  As a result, I ended up redesigning my database tables a few times in the beginning-- but I guess that's part of the learning process.  One of the features I ended up implementing that Avi talked briefly about was Single-Table Inheritance.

First, what is Single Table Inheritance?  Well, STI is a method of storing the data from multiple classes in a single table.  You would typically use this when you have classes that share a lot of attributes so that it makes sense to use inheritance.  For example, an Animal might be a base class, with attributes of name, size, color, weight, population, genre, species, etc.  And then you might have subclasses like Monkeys with some special behavior methods and only a few extra attributes. It's not a great analogy, but the idea is that you have a bunch of classes that are all basically the same set of data but might have different methods or relationships between them.

For the Learn-Co Companion, I scraped the Learn curriculum from the Learn webpage and got basically a JSON object hash with 4 types of objects.  The *Track* object, the *Topic* object, the *Unit* object, and the *Lesson* object.  Each of these objects had almost identical sets of data in them; timestamps, id, title, slug, completed flag, and similar.  What was different was that a *Track* has an array of *Topics* within it.  And each *Topic* has an array of *Units* within it.  Similarly, each *Unit* has an array of *Lessons* in it.  So clearly, these are nested inside one another, but otherwise share similar data.  A good candidate for STI.  In fact, looking at the ids in the data I scraped suggested that this is how Flatiron is storing the data, though it's not certain.

ActiveRecords makes it very easy to use STI.  I created a base class called *CurriculumElements* that has all the attributes in it, plus an additional column called 'type'.  When an ActiveRecord object is saved -- say for example, a *Topic* -- the attribute data are stored in the column_elements table, and the 'type' column would be set to 'Topic'.  When you want to retrieve a *Topic*, ActiveRecords knows to search that table for the data, but restrict its results to those records where type='Topic'.  Simple!

STI is very easy, but it can break down quickly if your subclasses start requiring attributes that aren't part of the base class.  In my case, as I was implementing the belongs_to relationships that I needed, I had to add additional columns to the table: lesson_id, unit_id, topic_id, track_id, and even student_id.  These were needed so that a lesson knew what unit and student it belonged to, a unit knew what topic it belonged to, and so on.  Even though some of these extra columns were only relevant to 1 of the 4 classes represented in the table, the column exists for all records, leading to a lot of NULL values in the database.

In the end, I still think STI was the right choice for this, but it is easy to see how it could get out of control, and why it has a bit of a bad reputation these days.

More reading:

[How (and When) to Use Single Table Inheritance in Rails](http://eewang.github.io/blog/2013/03/12/how-and-when-to-use-single-table-inheritance-in-rails/)

[Single-Table Inheritance (STI)](http://www.informit.com/articles/article.aspx?p=2220311&seqNum=4)

[Single Table Inheritance - Problems and solutions](http://blog.arkency.com/2013/07/sti/)

