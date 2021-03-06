## Simple Webmachine / Riak application for presenting slides

This is very much a work in progress and as such might not be useful for anybody
at all.

#### Resources:

* slide => individual slide authored in markdown?
* deck  => collection of slides with an implied ordering.
* library => collection of all decks
* author => author of a particular deck of slides

#### TODO
##### Integrate Riak - retrieve slide contents from riak backend.

* look at how to bundle a markdown parser in a library or application?

* integration tests for OTP applications? Need something!
* need way of packaging javascript
* start with modeling a basic slide and presenting it using backbone.js and
  html.
* backbone.js UI presenting a deck
* riak storage of slides, needs a single Riak node setup
* webmachine rest interface to the resources, slide and deck
* backbone.js UI presenting a library
* be nice to have a script to generate a new resource, similar to rails?

### BUGS: (Not that there are any)

* /slide/id works but want /slide to fail more gracefully.


### Layout
You should find in this directory:

    README.markdown : this file
    Makefile : simple make commands
    rebar : the Rebar build tool for Erlang applications
    rebar.config : configuration for Rebar
    apps : Erlang modules that make up this application
    /slide_machine_web : webmachine application
      /deps - the erlang library dependencies for the web application
      /ebin - compiled beam files for the erlang VM
      /src  - Erlang source code
      /priv - is where all the html and Javascript files are placed.
    /slide_machine_core : core logic to mediate between web app and riak db
      /deps - the erlang library dependencies
      /ebin - compiled beam files for the erlang VM
      /src  - Erlang source code

### Helpful Resources
 * [CloudEdit Backbone.js tutorial](http://www.jamesyu.org/2011/01/27/cloudedit-a-backbone-js-tutorial-by-example/)
 * [CloudEdit with Erlang and Webmachine P1](http://blog.erlware.org/2011/02/08/ecloudedit-erlang-webmachine-and-backbone-js/)
 * [CloudEdit with Erlang and Webmachine P2](http://blog.erlware.org/2011/02/12/ecloudedit-part-2-couchdb/)
 * [Webmachine, ErlyDTL and Riak by OJ](http://buffered.io/2010/10/13/webmachine-erlydtl-and-riak-part-3/)
 * [Erlang rebar tutorial: generating releases and upgrades](http://www.metabrew.com/article/erlang-rebar-tutorial-generating-releases-upgrades)
 * [Erlang/OTP releases: rebar, release_handler, .appup, etc](http://www.metabrew.com/article/erlangotp-releases-rebar-release_handler-appup-etc)
 * [Essential JavaScript Design Patterns](https://github.com/addyosmani/essential-js-design-patterns)
 * [Backbone Fundamentals](https://github.com/addyosmani/backbone-fundamentals)
 * [Chaplin: A Sample Application Architecture Using Backbone.js](https://github.com/moviepilot/chaplin)
 * [E2 Project](http://e2project.org/otp_primer.html)
 * [Backbone new aggregator](http://blog.topics.io/2012/03/15/how-build-news-aggregator-backbonejs-part-1/)

### Erlang Testing ###

 https://github.com/eproxus/meck

Basically the root url shows a preview collection of slide decks


    |------------------------------------------------------------
    |                                                           |
    |      |------|              |------|                       |
    |      | deck |              | deck |                       |
    |      |------|              |------|                       |
    |                                                           |
    |                                                           |
    |      |------|              |------|                       |
    |      | deck |              | deck |                       |
    |      |------|              |------|                       |
    |                                                           |
    |                                                           |
    |      |------|              |------|                       |
    |      | deck |              | deck |                       |
    |      |------|              |------|                       |
    |                                                           |
    |------------------------------------------------------------


Clicking on a deck brings you to Overview of Deck.
 show author, summary and TOC ie slide names.

    |------------------------------------------------------------
    |            |                                              |
    | Title      |          Current Slide                       |
    | 1.         |                                              |
    | 2.         |                                              |
    | 3.         |                                              |
    | etc        |                                              |
    |            |                                              |
    |            |                                              | ===> next slide
    |            |                                              |
    |            |                                              |
    |            |                                              |
    |            |                                              |
    |            |                                              |
    |            |                                              |
    |            |                                              |
    |            |           footer: Title                      |
    |------------------------------------------------------------

You can click on each slide on the side to go to that one.

There is a backwards and forwards buttons to navigate.

Deck => individual presentation, contains many slides
Slide => individual page in a presentation

Player => handles state of current presentation, ie current slide, current deck.

Have multiple views backed by the same model or collection? Provide a
DeckThumbnailView and a DeckView backed by the decks collection. One shows an
image, or author name, while the other shows just the title?

What does the tagName and ClassName within a view do again?

 NOTES:
Render all 3 views on the page when the main page is hit, hide any that we
don't need.

 Javascript Questions:

 - what is the pattern for JS object initialisation? How does it interact with JSON?

 Riak Questions:

- how  should the data modeling work?




Get the keys

curl -v http://127.0.0.1:8098/buckets/decks/keys?keys=true


Keep everything in a single bucket.

eg /riak/decks/

Within a Deck we have all the slides as a single document, for now!

 OTP Architecture Questions:
 - where do I start the core app from? does the web start it or do I need a
   supervisor for both web and core?
 - add rebar target to generate a release and start that up.
 - draw diagram of supervision tree for app.


### Supervision Tree

 slide_machine_core                  slide_machine_web              pool (riak)
       |                                    |                          |
       |                                    |                          |
 slide_machine_core_sup              slide_machine_web_sup         pooler_sup
       |                                    |                          |
       |                                    |                       -------------------
 slide_machine_core_server           slide_machine_web_server       |                 |
                                                                 pooler_pool_sup  pooler
