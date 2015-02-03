---
layout: post
title:  "What Untappd Users Drink"
date:   2015-02-02
tags:   data beer
---

If you enjoy drinking beer, then you should be using [Untappd](https://untappd.com/).  There are a couple of [articles](http://www.brewsnews.com.au/2015/01/the-anti-social-social-beer-app/) [online](http://hoosierbeergeek.blogspot.com/2013/11/is-untappd-killing-craft-beer-culture.html) that discourage its use because of the badge system and the "gamification" of social applications, but ignore them.  As long as you use it the way it was meant to be used (to check in when you drink a beer or to view what beers your friends have recently checked in), then it is the perfect app for beer lovers.

I've been using Untappd for almost a year now.  Sadly, I only have 72 checkins on Untappd.  There are a few reasons for this:

1. I forget to checkin.
2. I do not have as much money to spend on beer as I would like.
3. My phone for the majority of this past year was a [Droid Razr](http://en.wikipedia.org/wiki/Droid_Razr) which was low on battery more often than not.
4. I usually avoid checking in non-craft beers (I think I checked in Patriot Ale once, <3 Spanky and Darla's).

Anyway, it's a great app and I'll be using for as long as it exists.

When I was designing [my web portfolio](http://jonost.me/) for the 2nd time, I decided that I wanted to include a list of my most recent Untappd checkins, along with a list of the most recent books I had read.  I was still pretty new to both backend and frontend development (I still am, but let's ignore that fact) and I knew most web applications offer public APIs for developers.  Lucky for me, Untappd and [Goodreads](https://www.goodreads.com/) (a less desirable version of Untappd for books) both have public APIs.

# The Ramblings Portion of the Post

First, let me talk about Goodreads to get it out of the way.  I don't actually use Goodreads's API to get my most recent books, I use their custom widget that is available by going to ["https://www.goodreads.com/user/edit"](https://www.goodreads.com/user/edit) and clicking the widgets tab if you're logged in.  I do this for two reasons.  One, their API serves [XML](http://en.wikipedia.org/wiki/XML) instead of [JSON](http://en.wikipedia.org/wiki/JSON) for most of their endpoints.  Two, their API doesn't allow [cross-origin resource sharing](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing).  

Sidenote:  I use [Github Pages](https://pages.github.com/) to host my portfolio, because it's free and easy to update, which means I have to make all of my API requests from the client.  It's not a big deal, but in the future I want to set up a small [Flask](http://flask.pocoo.org/) app on [Heroku](https://www.heroku.com/) that I will handle API requests for me.

So, Goodreads custom widgets are configurable on their site and Goodreads supplies script tag with a unique url that, I assume, uses their API to generate a javascript file that contains the HTML necessary to create the widget.  It's for someone with a Tumblr blog, or something similar, to copy and paste.  It's great for what it is, but the available styles for the widget didn't match my portfolio's design so I had to hack it a little.  First, I added a simple bit of CSS (`.gr_custom_container_1422601795 {display: none;}`) to hide the generated widget.  Then I wrote a little javascript to get what I wanted from the hidden widget:

{% highlight javascript %}
var getBooks = function (callback) {
    var wid_id = "1422601795";
    var waitForBooks = function (callback) { 
        if (!($('.gr_custom_container_' + wid_id).length)) {
            setTimeout(waitForBooks, 15);
        } else {
            callback();
        }
    };

    waitForBooks(function () {
        var books = [];
        var book_container = $('.gr_custom_container_' + wid_id).first();
        book_container.children('.gr_custom_each_container_' + wid_id).each( function () {
            book = {
                title: $(this).children('.gr_custom_title_' + wid_id).children('a').html(),
                author: $(this).children('.gr_custom_author_' + wid_id).children('a').html(),
                url: $(this).children('.gr_custom_title_' + wid_id).children('a').attr('href').split('?')[0],
                img: $(this).children('.gr_custom_book_container_' + wid_id).children('a').children('img').attr('src')
            };
            books.push(book);
        });
        callback(books);
    });
}
{% endhighlight %}

Goodreads creates a new custom widget id every time a new widget is created, so the only thing to change if you use this script is to change `wid_id` to whatever Goodreads supplies.  `waitForBooks` is necessary, because the Goodreads script runs asynchronously and it allows the rest of my site to load while `getBooks` waits for the widget to appear in the DOM.

# Let's Talk Beer

