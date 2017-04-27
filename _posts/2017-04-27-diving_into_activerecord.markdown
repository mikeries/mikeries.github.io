---
layout: post
title:  "Diving into ActiveRecord"
date:   2017-04-27 02:36:38 +0000
---


## Divers Logbook
The hardest part of my Sinatra CRUD app was picking a topic, but I eventually came up with the idea for an online Dive Logbook kind of app.  If you're a SCUBA diver, you know there are lots of little details that you like to track about what equipment you use, how much weight, how much air you used, etc.  For this project, I limited myself to just a few key parameters.

At the base level, we have a diver.  Divers are the owners of their logbooks, and as such the diver must log into the application to use it.  So the application permits a user to create an account and log in to access their data.

Once logged in, the diver is presented with a list of his or her dives, or may add a new one.  These dives are where the data are stored for reference later.  

One of the bits of information about a dive contains is its location.  Each location has its  information of its own such as a name, description, category, gps coordinates, depth, average water temperature, etc.  We don't want to duplicate this information every time the diver goes back to their favorite spot.  Furthermore, most dive sites are visited by many divers, so why not share the location data?

Thus,

* A Diver has many Dives, and has many Locations through Dive.
* A Dive belongs to a Diver, and belongs to a Location
* A Location has many Dives and many Divers through Dive.

## Implementation
This is a has-many to has-many relationship, which normally requires a [join table](https://en.wikipedia.org/wiki/Associative_entity).  In this case, the Dive class (or rather, the underlying :dives table) acts as the join table.

```ruby
class Dive < ActiveRecord::Base
  belongs_to :diver
  belongs_to :location
end
```

The Dive class represents the data for each dive that the diver made.  Therefore, the Dive is said to 'belong_to' the Diver.  It also 'belongs_to' a Location.

```ruby
class Location < ActiveRecord::Base
  has_many :dives
  has_many :divers, through: :dives
end
```

The Location can be shared by many Dives, which may include many Divers through those Dives.

```ruby
class Diver < ActiveRecord::Base
  has_many :dives
  has_many :locations, through: :dives
end
```

The Diver class corresponds to the user.  Of course each diver will have many dives, but also has many locations via his or her dives.

ActiveRecord reads these class definitions and the database schema and provides for us the necessary methods to perform many magic tricks.

```
[4] pry(main)> mike = Diver.new(name: "Mike")
=> #<Diver:0x000000025e2b88 id: nil, name: "Mike">
[5] pry(main)> gbr = Location.new(name: "Great Barrier Reef")
=> #<Location:0x000000029e1a40 id: nil, name: "Great Barrier Reef">
[6] pry(main)> mike.dives.build({datetime: "5/2/2017", location: gbr})
=> #<Dive:0x00000003afaac0
 id: nil,
 diver_id: nil,
 location_id: nil,
 datetime: "5/2/2017">
[7] pry(main)> mike.save
=> true
[8] pry(main)> gbr.divers
=> [#<Diver:0x000000038359e8 id: 7, name: "Mike">]
[9] pry(main)> mike.dives.first.location.name
=> "Great Barrier Reef"
```

Pretty neat, huh?  I didn't have to write any methods for those classes - they are exactly as you see above, but 
ActiveRecord added all the methods needed to connect the classes together and persist them to the database.  A careful reader will also notice that the id for mike was nil before saving, but was automatically set when the object was saved and the id was returned by the database.

## What I haven't told you
While that's how it is supposed to work, and is how it works now, it isn't what happened when I first built the app.  Here's what I got instead:

```
[1] pry(main)> gbr = Location.new(name: "Great Barrier Reef")
=> #<Location:0x00000003c1f8d8 id: nil, name: "Great Barrier Reef">
[2] pry(main)> mike = Diver.new(name: "Mike")
=> #<Diver:0x00000002c7cd90 id: nil, name: "Mike">
[3] pry(main)> mike.dives.build({datetime: "5/2/2017", location: gbr})
NameError: uninitialized constant Diver::Dife
from /home/michael/.rvm/gems/ruby-2.3.1/gems/activerecord-5.0.2/lib/active_record/inheritance.rb:152:in `compute_type'
[4] pry(main)>
```

What?  Uninitialized constant Diver::Dife?  That's odd -- maybe I have a typo somewhere.  But where?  I did a global search of the project for Dife and came up with nothing.  ActiveRecords must have gone crazy!

## Here's what happened
Take a look at the following line from the Diver class:

**has_many :dives** (pural *dives*)

And the corresponding line from the Dive class:

**belongs_to :dive** (singular *dive*)

In order to link our objects and tables correctly, ActiveRecord has to be able to pluralize and singularize words.  It does this through something called an [Inflector](http://api.rubyonrails.org/classes/ActiveSupport/Inflector.html#method-i-singularize).  Let's check it out in the console.

```
[4] pry(main)> ActiveSupport::Inflector.singularize("dives")
=> "dife"
```

Ah hah!  Now that we know what the problem is, how do we fix it?  Do we have to rename our tables?

Fortunately, ActiveRecords::Inflector provides a method to handle this problem, [#inflections](http://api.rubyonrails.org/classes/ActiveSupport/Inflector/Inflections.html).  This method allows us to specify how we want ActiveRecords to pluralize or singularize names.  Add the following to the environment.rb file:

```
ActiveSupport::Inflector.inflections do |inflect|
  inflect.irregular 'dive', 'dives'
end
```

and the application operates as intended.

## Conclusion
The message here is that ActiveRecords is like magic, but it isn't magic.  Like almost anything else, it is simply a very clever (and useful) abstraction.  Understanding what is going on underneath the surface will help you be a better, more productive coder.  (Did you get it? Beneath the surface? Diving?)

You can watch a demonstration of this app [here](https://youtu.be/hPuQPpQlBYg).







