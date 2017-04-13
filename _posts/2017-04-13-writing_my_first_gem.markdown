---
layout: post
title:  "Writing my first GEM"
date:   2017-04-13 18:43:19 +0000
---

I've just finished writing my first gem -- the CLI Gem that is one of the final projects in the Object Oriented Ruby module of the curriculum.  Though I find it hard to say it's 'finished' because every time I look at it I think of new features and code improvements that could be made.  But the community has cautioned me not to fall too deeply into that rabbit hole, so I plan to submit it today and I can always come back and spit-polish it later.

## Todays-Baseball
When I started the project, I had planned to scrape data for today's MLB games -- schedule, scores, roster, etc.  I went to [mlb.com](http://mlb.com) and inspected (ctrl-shift-I) the page and saw that there was a wealth of information and lots of elements with id's and classes that should make it straightforward to scrape.  Great, let's get to work!

I watched Avi's video [walkthrough on making a CLI Gem](https://www.youtube.com/watch?v=_lDExWIhYKI) and found it to contain valuable information on getting started and practical workflows.  I scaffolded my gem, coded my CLI, added my data model, and finally got to where I would scrape the real data.

I added nokogiri and open-uri to my dependences and created a Scraper.scrape_games method:

```ruby
class TodaysBaseball::Scraper
  def self.scrape_games
    doc = Nokogiri::HTML(open('https://www.mlb.com'))
    doc.css('ul.mlb-scores__list li')
		binding.pry
  end
end
```

and then in my console:
```
2.3.1 :002 > TodaysBaseball::Scraper.scrape_games  
```

and finally in the pry:
```
[1] pry(TodaysBaseball::Scraper)> doc.css('ul.mlb-scores__list li')                                                                                                                                                          
=> [] 
```

Wait a minute, nothing was returned?  I went back to mlb.com, double-checked my selector, and tried several variations.  All gave me the same empty array.  What was going on?

As I pondered the question, with the Developer Tools open to the mlb.com webpage, I saw the DOM change before my eyes and it occurred to me that this is a dynamic webpage and much of the content is loaded by javascript after the page loads.  So I right-clicked on the webpage and chose 'View source' to get an idea of what the static portion of the page looks like.  Ah hah!  None of the selectors I had been trying to scrape were anwhere to be found.

The learning event here is that Nokogiri can only scrape data loaded by the server: (from https://www.distilled.net/resources/web-scraping-with-ruby-and-nokogiri-for-beginners/)

>There are some limitations to scraping with Nokogiri.  The biggest limitation is that you’ll only be able to scrape page content that is loaded server side.  If content is loaded on a page using Ajax you will not be able to scrape it with Nokogiri alone. 

There are [ways around this](https://morph.io/documentation/scraping_javascript_sites), of course, but this seemed to be taking me too outside the assignment scope.

**Pro-Tip: Before starting your CLI Gem project, check that the data you want to scrape are available in the 'view-source' output from the page.**

## Beer-Advocate
What to do?  Well, I decided to start over with a new project.  This time, I chose to scrape a [Beer Advocate](https://www.beeradvocate.com/lists/top/) page containing a list of the top 250 beers as ranked by their members.  It's an interesting site if you like beer and are intersted in discovering some great new beers.

So I 'view source'ed the page and took a look.  All the data I wanted were there, but the author had used almost no classes or id's to identify the elements.  I threw together a quick Scraper class in the console and tried a few things.  Scraping this site would be messy, but not impossible.

So I started again, scaffolding the gem, creating the CLI and testing with fake data, then building the data model to return fake data, and finally built the Scraper class.  As expected it's got some messy code, but it works:

```ruby
  def self.scrape_beers
    doc = Nokogiri::HTML(open(ROOT_URL+'/lists/top/'))
    rows = doc.css('#ba-content table tr')

    results = (3..12).collect {|index| parse_row(rows[index-1])}
  end

  def self.parse_row(row)
    {
      name: row.children[1].children[0].children[0].children[0].text,
      brewery: row.children[1].children[1].children[0].text,
      style: row.children[1].children[1].children[2].text,
      abv: row.children[1].children[1].children[3].text,
      rank: row.children[0].text,
      url: row.children[1].children[0].attribute("href").value,
      rating: row.children[2].children[0].text
    }
  end
	```
	
## Implementation Notes
Since this assignment is supposed to be less about scraping and more about the process of designing and building a gem, I wanted to comment on some of the design decisions that I made.

The app is primarily comprised of 5 files:

* bin/beer-advocate - the executable.  It loads the enviroment and calls CLI.start.
* lib/beer-advocate.rb -- contains the require statements necessary for the environment
* lib/beer-advocate/cli.rb -- defines the CLI class, which is responsible for handling all user input and output.  The class does not store any model data -> it relies on the Beer class to handle that.
* lib/beer-advocate/beer.rb -- defines the Beer class.  The class methods here handle instaniating, storing, and retrieving instances of itself.  The instance methods are getters and setters for the instance properties.
* lib/beer-advocate/scraper.rb -- defines the Scraper class.  The Scraper.scrape_beers method scrapes the web page for the top 10 beers and requires an array of hashes containing the necessary properies to instantiate new beers.  The Scraper.  The Scraper.scrape_description method is called by instances of the Beer class when the beer's description is requested.  This is done because it requires scraping a new page and isn't needed unless the user requests beer details.

In review other student's gems, I found several different approaches to doing the scraping.  In some cases, the scraping code was inside the data model (Beer class, in my case).  In others, a separate Scraper class was doing the scraping, but it would call the Beer class to instantiate new beers as it did the scraping.  Rather than either of these approaches, I chose my structure because

1. It keeps all the details of the HTML code, css selectors, etc, outside of the Beer class.  I think this separates concerns better, in case I want to use a different scraper (for a different website, for example) or if the website changes.
2. I chose to have the Scraper return an array of object hashes rather than call the Beer class directly.  The reason is that I didn't want the Scraper to be linked so closely to the Beer class.  For example, what if I want to sub-class the Beer class (i.e. class Lagers < Beer) and use the Scraper to collect the data?  This won't work if the Scraper class always instantiates objects in the Beer class.

Even as I write this blog, I think of improvements.  What if I sub-class the Scraper and pass instances to the beer class for it to use?  Then I could write a custom Scraper for different web sites and the Beer class wouldn't care where the data are coming from, as long as it gets an array of property hashes... but as I said, you gotta draw the line somewhere, and Learn-co is telling me my project is overdue.

[https://github.com/mikeries/beer-advocate](https://github.com/mikeries/beer-advocate)

