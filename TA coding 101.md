TA coding 101


Intro [](#intro)
=====

This document is intended as a reference for our shared coding work. It's an attempt to balance the need for consistency and standardization with individual styles and ownership.  

This is not exactly a traditional 'coding standards' document: things like where to put your commas are really a problem for machines, not people. See [Coding Standards](CodingStandards) below for details, but keep in mind that formatting is the least interesting part of the problem set.  

It's also not about rules like _use this language feature_, or _use this paradigm_. There are some rules of thumb here, but this doc is not a code cookbook.  It is intended to help us collectively think about how our work and our code fits together. 


Know where we're at
===================

The first and most important principle for our code is to know what _kind_ of code it's supposed to be.  

Not every line of code demands the same level of scrutiny.  A simple user facing tool isn't the same thing as a piece of library code that will be run thousands of times.  Runtime Unreal code is not the same thing as an artist tool.  

Broadly speaking we write four different kinds of code, and the thought process behind each one is a bit different.  Knowing what kind of code we are writing, and where it fits into the hierarchy,  is important for helping us know how to structure our work.

Starting from the most deep and deliberate and moving up towards user facing code which needs to be responsive to user feedback, the four big types of code are:

### Runtime code

This is code that runs in a game, or in another environment where milliseconds actually count.

This kind of code needs to be carefully vetted for memory usage and performance.  Ideally it also needs to be evolved in cooperation with the engineering team.  This is high-stakes code that should be planned and tested accordingly.

When working on runtime code, we need to be sure to:

* Get code reviews from inside and outside the team
* Stay in touch with engineering to make sure that our code complements theirs
* Use profiling data to make sure you're optimizing the right things
* Make sure we understand the memory costs of our work

### Frameworks

This is code that's intended to solve a general problem in a repeatable way.  It could be a library for reading and writing a particular file type, or a system for adding metadata to an import pipeline.  Out-of-house examples would be something like the `xmltree` module or `django`, which will be specialized by other users to solve concrete problems.

Framework code is more general and high level than other kinds of code library.  A framework generally has an opinion about how a particular problem should be handled:  it will influence the style of code that users write.  Therefore, frameworks have to have strong, clear, and consistent APIs so that users can get value from them instead of fighting against them.

Famework code needs to:

* Be well reviewed to get buy-in
* Be carefully designed. This kind of code gets reused in many places and needs to be solid.
* Have high test coverage. Because framework code is heavily reused, we need tests to ensure that it doesn't cause bugs as it evolves.
* Do a small number of things extremely well.
* Provide a clear, well documented interface.
* Explain its own assumptions and paradigms

Framework code is going to be deep down in the foundational layers of code that users see. Because it's not close to users it can't make assumptions on their behalves. Framework code should resist the temptation to make guesses about what users want: just fail loudly and let code closer to the user decide what to do next.

### Libraries

This is the largest category of TA code -- the place where most of the problem / solution work is actually done.  Library code is re-usable code that's specific to particular problem set:  dealing with UV sets in Maya, or providing debug info in Unreal.  Like framework code, libraries are basically invisible to end users: the customers for libraries are fellow coders.

Library code serves two primary purposes.  

First and foremost, it _encourages the re-use of high quality code_ -- as Python says _there should be one, and preferably only one, way to do it_. 

Second, it's intended to cut down _repetitive nuisance code_ -- a good library makes sure that more code is tested and bulletproofed and that one-off, unique code really is unique.  

Library code is how we accumulate knowledge about our problem spaces. It formalizes best practices and cuts down on wheel-reinvention.

Because of these roles, library code needs to:

#### Be clear, well organized, and discoverable  
It's no good making libraries if nobody else knows they are there.

#### Be well tested and reliable  
This is code that will be heavily reused and it needs to be as deterministic as possible.

#### Stick to one problem domain
A library for processing geometry should not expose a string formatting function.
#### Avoid UI concerns
Library code is code for other code to use.

#### Fail  
Library code does not know about context. Decisions about what to do if something goes wrong are left to user-facing tools.

Library code resembles framework code in one way: it's still far away from the user, so it should not try to make assumptions on the user's behalf. Unlike framework code, though, library code does not want to impose a programming style or paradigm: the api of a library is basically just a set of well named and well documented functions.

Good libraries are easy to spot: they make it easy to do the right thing and hard to do the wrong thing.  [This talk](https://youtu.be/5tg1ONG18H8) is nominally about C++, but it's a good discussion of the ways api design and UX design are similar in any language.  Libraries should be designed for simplicity, consistency and lack of surprises.

### Tools.

Tools are the parts of our work that are visible to our users -- this is where the menus, buttons, dialogs and scripts go.  Tool coode marshals the functionality from our frameworks and libraries into a user-friendly package that's easy to maintain and support.  

Tool code needs to:

* Make good use of available frameworks and libraries.  Don't reinvent the wheel -- make sure that you've looked for existing solutions.
* Work independently of its own GUI. We may want to batch the tools operation, or change the way the operations are bundled together -- tying functionality to particular UI layouts makes tools harder to design and maintain.
* Make good decisions for users.  Unlike library and framework code, tools include a lot of user-facing code.  Tools should have good defaults and safe, unsurprising behavior.
* Deal with bad data.  Tools usually have a user present to ask for help, so it's tool's job to deal with the unexpected.
* NOT import other tool code. A tool is the root of a dependency tree -- if tools import each other, the structure becomes impossible to maintain. If you want a function from another tool, that's a good reason to suspect that that function needs to migrate upstream to a library.

### TLDR:
* Frameworks are standalone code, with careful design and heavy testing
* Libraries are code for code reuse.  They are building blocks to make the process of delivering tools faster and less tedious.  Libaries use frameworks and sometimes other libraries
* Tools import a variety of libraries and/or frameworks to accomplish an actual job for our end users. 
    + Tools don't import other tools
    + UI always lives at the level of tools.
    + If code in a tool turns out to be generally useful, push it up into a new or existing library.  And then test it.


Design standards [](#design_standards)
================

Again, this is not about _coding_ standards.  Design standards are about how we approach problems, not what the solution looks like written down.

These sections are not really 'rules': they form a logical sequence.  TA code is a bit different from other programming problems -- even by software standards our world is chaotic and prone to change.  These guidelines are intended to help us build more stability into an environment that is prone to frequent, unplanned changes.  

### Talk it out.

The first thing to do when starting a new project is _don't start writing._  Start by _talking_ to colleagues and customers to make sure you understand what you're being asked to do.  

In that conversation, sketch out a general idea of how it ought to work.  For a big project that's probably an actual meeting, but for most work this could be a five minute conversation with the [understudy](#understudies) on the code.

The goal of talking first is to make sure that (a) the job needs doing at all, (b) it hasn't already been solved by one of our colleagues and (c) our approach to the problem isn't going to cause problems down the road.

### Start small.

It's tempting to try solving all future problems when writing code, particularly library or framework code.  

Don't. 

We need to _think_ about future problems and try to solve today's issues without prejudicing future enhancements. But we don't want to actually write code that we don't need right now.  

The hard truth of TA life is that the future we are envisioning may not ever arrive, or will arrive in an almost unrecognizable form.  So, don't do work today to solve a problem that may never come to pass.

An idea for how to solve a future problem is a great note to put into a comment or @todo.  But don't write future code until it serves a present need.  Long-lived library and especially framework code need to be more future-friendly.  However, even there it's a good idea to write as little code as is practical today.

### Garden your code

Code is alive. We want to guide its growth and trim it back when it gets overgrown.  An ongoing diet of small upgrades and improvements is better than letting code rot for years and then doing dramatic, drastic surgery on it. 
Refactoring is just an ongoing part of maintenance --  not a scary dramatic intervention.

For this reaon, we want to be comfortable with refactoring as a regular part of our work.  We want to do as much testing as possible so that maintenance can be done with maximum safety and minimal disruption.   

We also want to retire code that's not serving a purpose any longer.  It's all backed up, if we need it again some day we can find it again.  But leaving unused code around provides a temptation to borrow from it -- thereby to keep alive no just the one function that was borrowed, but all of its dependencies alive.

### Test if you can

TA work makes it hard to completely embrace [test-driven development]().  Because a lot of TA work happens inside complex environments that we don't control from end to end, it's hard to hit 100% test coverage without a lot of work.

However, tests are still an extremely valuable tool.  Tests are particularly important if we want to make refactoring a regular, ongoing part of our work: refactoring is a far less scary prospect when you have tests which demonstrate that moving a file or renaming a method has not created surprising sideffects.

Another very important benefit of tests is that testing encourages good design.  Testable code is generally going to be less complicated, less overly coupled, and less prone to wierd side effect bugs.  [This talk](https://youtu.be/DJtef410XaM) is Python specific but it lays out the case for why testing makes for better code structure overall very well -- and the idea that he's actually propunding originated in Java. 

Library and framework code should be tested.  Tools code -- with it's UI and scene dependencies is harder to test, but even there it's a good idea to design is in ways where testing is possible.

### Good enough.

This goes hand in hand with getting comfortable with refactoring as a regular event. Working through a problem is usually messy, and produces messy code. Every new tool is  full of odd, one-off bits of code.  Getting a good understanding of the problem usually requires experimentation and that's OK.

However, once a tool moves into production it should be refactored into as clean and simple a form as possible. Once you see how things fit together you are in far better place to pick the right abstractions for the final version.  It's also a good time to see what parts of the tool are really general purpose code and can be pushed upstream into reusable library code.  And, when the problem involves some wierd, idiosyncratic code make sure to comment the reasons.  

What you write should get better over time -- just because it's not springing fully formed from your.

###  Less is more

A wise man once said ["We hate code. We want to ship as little of it as possible"](https://www.youtube.com/watch?v=o9pEzgHorH0).  Not everything needs to be a class or a metaprogramming paradigm.  We ought to start with the simplest solution first, and add complexity when its called for.  Since we want to be comfortable with refactoring anyway there's no need to write a complex system until the simple system is failing to work.  

In keeping with that:

* Don't write a class where static functions will do.
* When classes are necessary, [prefer composition over inheritance](https://robots.thoughtbot.com/reusable-oo-composition-vs-inheritance) anyway. Deep hierarchies are a sign of over-complication.
* Use [pure functions](https://hackernoon.com/functional-programming-concepts-pure-functions-cafa2983f757) wherever you can.  If calling a function _changes_ things (in a class, in the scene) it's a potential bug. Pure functions are easier to test and can be swapped around.
* Abstract from a concrete, working example, instead of designing a perfect abstraction.

There are situations where a higher-level approach adds more simplicity over the long haul.  These are cases for writing frameworks.  However these situations are _rare_.




### Don't optimize on faith.  

Write a simple, working example and then profile it.  Don't expend effort on speculative optimizations until (a) you know the algorithm and problems solving approach actually work and (b) you need more perf.  

Optimized code is a good thing -- when it's not a bad thing.  If the basic design is sound, it's usually possible to improve with optimization tricks.  But if the basic design is not sound, trying to speed it up with lots of micro-optimization is a lot of work and not.   Save the gimmicky stuff for the last stage of stuff that's working, reliable and has already been checked against a profiler.

Coding Standards[](#code_standards)
================

This is the part of the doc that approximates a traditional coding standard.

### General principles

Code formatting is not an interesting problem.  All standards rub somebody the wrong way.  But even a standard that you personally dislike is probably fine: it's job is too make your work more readable to _other_ people.  

So: suck it up and use the conventions of the environment you're in.  

### Write readable code.

9/10th of the life of a line of code is in maintenance and bug-fixing.  Take the time to write clear, descriptive names for everything -- classes, functions, and variables. 

Add comments, particularly if there are edge cases or special reasons you've made a decision. 

So:  **It's not done till it's properly named and commented.**  Comments for other coders and docs for users are _as important_ as code.  When reviewing each other's work we should always call out opportunities for better codes and comments.

For precise advice what good names and comments look like, follow the guidelines in sections one of [The Art of Readable Code](https://www.amazon.com/Art-Readable-Code-Practical-Techniques/dp/0596802293).  We can adapt those rules for language flavor -- is, use Unreal-style casing in C++ and underscored names in python -- but the principles are the same everywhere.  **Required reading.**


### Write idiomatically. 

Don't write Python in C++ or C# in Python.  Most of us like one language more than others, which is fine.  But code is a shared asset, and making your code readable and maintainable for other people means going with the flow of the language you are in.

[This video](https://www.youtube.com/watch?v=wf-BqAjZb8M) is specifically about Python but it's a good illustration of the difference between writing code that's formally correct and code thats idiomatic.

[This one](https://youtu.be/xnqTKD8uD64) covers modern C++ with a good, reasonable approach to managing complexity.

### Formatting

#### C++
Follow the [Unreal coding standards](https://docs.unrealengine.com/en-us/Programming/Development/CodingStandard). 

#### Python

Follow [Pep-8](https://www.python.org/dev/peps/pep-0008/?).  We allow up to 120 characters wide (instead of the 80 characters in default pep-8) to avoid wasting time noodling on line breaks.  In Sublime Text use the [Anaconda](http://damnwidget.github.io/anaconda/) plugin for linting; in PyCharm use the default linter.  Use 4-space indentation.


Working together [](#understudies)
================
As the department grows we need to do a better job of making sure we spread vital knowledge around. 

In order to do that, new features, systems or tools should always involve at least two people:  an **implementer** and an **understudy**.  The implementer does the bulk of the work, but the understudy is an important part of the process.  The goal here is to strike a good balance between individual autonomy in design and implementation on one side and encouraging collective ownership of code and tools on the other.  It's well short of [pair programming](https://www.agilealliance.org/glossary/pairing/#q=~(filters~(postType~(~'page~'post~'aa_book~'aa_event_session~'aa_experience_report~'aa_glossary~'aa_research_paper~'aa_video)~tags~(~'pair*20programming))~searchTerm~'~sort~false~sortDirection~'asc~page~1)) but it's more structured than our current approach.

The relationship looks like this:

**Design review**  When the feature is first sketched out, the implementer will have to do some homework on how to approach the problem.  This could involve anything from reading a talking to customers to digging up research papers to reading code in the Unreal Engine.  When the implementer has a general idea of how to proceed, s/he should run it by the understudy to make sure that it makes sense.  The understudy should explicitly sign off on the general direction.  This could be as simple as 
    
> "Hey X -- I think I'm going to tackle this by creating a map of CharacterAssets to ClothingAssets and storing that as an Unreal Datable.  Users can open that in the editor and add their annotiations that way" 

> "Sounds good, maybe you should talk to Marky about how he handled that for the facilities system."

or it could involve an actual whiteboarding session.  The point is _not_ to enforce team design on everything - it's to make sure that there are more eyes on the problem. Discussion can help head off potential problems or simply provide context for future code reviews. 

Understudies can, and should, remind implementers if there is existing functionality that can be reused.  Less is more!

**Code review**  As the feature or system progresses, understudies should review major checkins.  We'll need to work out the right cadence, it's too much to expect that every checkin will be reviewed by another person.  But significant waypoints in the evolution of the feature should be reviewed. The goal is that the finished code is at least somewhat familiar to at least two people at all times.  An important part of the understudy's review process will be pointing out places where the code needs comments or is hard to follow, and to encourage attention to our general design standards. 

**Wrap up** Nothing is done unless it's readably commented (for other coders) and documented (for end users).  The final documentation pass should fall on the understudy, with input and review from the implementer. This is important for making sure that the system makes sense to at least two people.

In many cases -- particularly for more complex jobs -- the understudy will also be working on the same code.  In cases like that the two roles should basically be reversed as needed:  A understudies B's work and vice-versa.  

