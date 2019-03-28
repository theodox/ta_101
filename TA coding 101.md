TA coding 101

<a id="intro"></a>
Intro
=====

This document is intended as a reference for our shared coding work. It's an attempt to balance the need for consistency and standardization with individual styles and ownership.  

This is not exactly a traditional 'coding standards' document: things like where to put your commas are really a problem for machines, not people. See [Coding Standards](#coding-standards) below for details, but keep in mind that formatting is the least interesting part of the problem set.  

It's also not about rules like _use this language feature_, or _use this paradigm_. There are some rules of thumb here, but this doc is not a code cookbook.  It is intended to help us collectively think about how our work and our code fits together. 

<a id="know-where"></a>
Know Where We're At
===================

The first and most important principle for our code is to know what _kind_ of code it's supposed to be.  

Not every line of code demands the same level of scrutiny.  A simple user facing tool isn't the same thing as a piece of library code that will be run thousands of times.  Runtime Unreal code is not the same thing as an artist tool.  

Broadly speaking we write four different kinds of code, and the thought process behind each one is a bit different.  Knowing what kind of code we are writing, and where it fits into the hierarchy,  is important for helping us know how to structure our work.

Starting from the most deep and deliberate and moving up towards user facing code which needs to be responsive to user feedback, the four big types of code are:

### Runtime Code

This is code that runs in a game, or in another environment where milliseconds actually count.

This kind of code needs to be carefully vetted for memory usage and performance.  Ideally it also needs to be evolved in cooperation with the engineering team.  This is high-stakes code that should be planned and tested accordingly.

When working on runtime code, we need to be sure to:

* **Get code reviews** from inside and outside the team
* **Stay in touch with engineering** to make sure that our code complements theirs
* **Use profiling data** to make sure you're optimizing the right things
* Make sure we **understand the memory costs** of our work

<a id="frameworks"></a>
### Frameworks

This is code that's intended to solve a general problem in a repeatable way.  It could be a library for reading and writing a particular file type, or a system for adding metadata to an import pipeline.  Out-of-house examples would be something like the `xmltree` module or `django`, which will be specialized by other users to solve concrete problems.

Framework code is more general and high level than other kinds of code library.  A framework generally has an opinion about how a particular problem should be handled:  it will influence the style of code that users write.  Therefore, frameworks have to have strong, clear, and consistent APIs so that users can get value from them instead of fighting against them.

Famework code needs to:

* **Be well reviewed** to get buy-in
* **Be carefully designed**. This kind of code gets reused in many places and needs to be solid.
* **Have high test coverage**. Because framework code is heavily reused, we need tests to ensure that it doesn't cause bugs as it evolves.
* **Do a small number of things extremely well**.
* **Provide a clear, well documented interface**.
* **Explain its own assumptions** and paradigms

Framework code is going to be deep down in the foundational layers of code that users see. Because it's not close to users it can't make assumptions on their behalves. Framework code should resist the temptation to make guesses about what users want: just fail loudly and let code closer to the user decide what to do next.

<a id="libraries"></a>
### Libraries

This is the largest category of TA code -- the place where most of the problem / solution work is actually done.  Library code is re-usable code that's specific to particular problem set:  dealing with UV sets in Maya, or providing debug info in Unreal.  Like framework code, libraries are basically invisible to end users: the customers for libraries are fellow coders.

Library code serves two primary purposes.  

First and foremost, it _encourages the re-use of high quality code_ -- as Python says _there should be one, and preferably only one, way to do it_. 

Second, it's intended to cut down _repetitive nuisance code_ -- a good library makes sure that more code is tested and bulletproofed and that one-off, unique code really is unique.  

Library code is how we accumulate knowledge about our problem spaces. It formalizes best practices and cuts down on wheel-reinvention.

Because of these roles, library code needs to:

- **Be clear, well organized, and discoverable** It's no good making libraries if nobody else knows they are there.
- **Be well tested and reliable.** This is code that will be heavily reused and it needs to be as deterministic as possible.
- **Stick to one problem domain.** A library for processing geometry should not expose a string formatting function.
- **Avoid UI concerns.**
Library code is code for other code to use.
- **Fail.**  Library code does not know about context. Decisions about what to do if something goes wrong are left to user-facing tools.

Library code resembles framework code in one way: it's still far away from the user, so it should not try to make assumptions on the user's behalf. Unlike framework code, though, library code does not want to impose a programming style or paradigm: the api of a library is basically just a set of well named and well documented functions.

Good libraries are easy to spot: they make it easy to do the right thing and hard to do the wrong thing.  [This talk](https://youtu.be/5tg1ONG18H8) is nominally about C++, but it's a good discussion of the ways api design and UX design are similar in any language.  Libraries should be designed for simplicity, consistency and lack of surprises.

<a id="tools"></a>
### Tools

Tools are the parts of our work that are visible to our users -- this is where the menus, buttons, dialogs and scripts go.  Tool code marshals the functionality from our frameworks and libraries into a user-friendly package that's easy to maintain and support.  

Tool code needs to:

* **Make good use of available frameworks and libraries**.  Don't reinvent the wheel -- make sure that you've looked for existing solutions.
* **Work independently of its own GUI**. We may want to batch the tools operation, or change the way the operations are bundled together -- tying functionality to particular UI layouts makes tools harder to design and maintain.
* **Make good decisions for users**.  Unlike library and framework code, tools include a lot of user-facing code.  Tools should have good defaults and safe, unsurprising behavior.
* **Deal with bad data**.  Tools usually have a user present to ask for help, so it's tool's job to deal with the unexpected.
* **NOT import other tool code**. A tool is the root of a dependency tree -- if tools import each other, the structure becomes impossible to maintain. If you want a function from another tool,  that that function probably needs to migrate upstream to a library.

### TLDR:
* **Frameworks** are standalone code, with careful design and heavy testing
* **Libraries** are code for code reuse.  They are building blocks to make the process of delivering tools faster and less tedious.  Libaries use frameworks and sometimes other libraries
* **Tools** import a variety of libraries and/or frameworks to accomplish an actual job for our end users. 
    + Tools don't import other tools
    + UI always lives at the level of tools.
    + If code in a tool turns out to be generally useful, push it up into a new or existing library.  And then test it.

<a id="design-standards"></a>
Design Standards
================

Again, this is not about _coding_ standards.  Design standards are about how we approach problems, not what the solution looks like written down.

These sections are not really 'rules': they form a logical sequence.  TA code is a bit different from other programming problems -- even by software standards our world is chaotic and prone to change.  These guidelines are intended to help us build more stability into an environment that is prone to frequent, unplanned changes.  

### Talk It Out

The first thing to do when starting a new project is _don't start writing._  Start by _talking_ to colleagues and customers to make sure you understand what you're being asked to do.  

In that conversation, sketch out a general idea of how it ought to work.  For a big project that's probably an actual meeting, but for most work this could be a five minute conversation with the [understudy](#working-together) on the code.

The goal of talking first is to make sure that (a) the job needs doing at all, (b) it hasn't already been solved by one of our colleagues and (c) our approach to the problem isn't going to cause problems down the road.

### Start Small

It's tempting to try solving all future problems when writing code, particularly library or framework code.  

Don't. 

We need to _think_ about future problems and try to solve today's issues without prejudicing future enhancements. But we don't want to actually write code that we don't need right now.  

The hard truth of TA life is that the future we are envisioning may not ever arrive, or will arrive in an almost unrecognizable form.  So, **don't do work today to solve a problem that may never come to pass**.

An idea for how to solve a future problem is a great note to put into a comment or @todo.  But don't write future code until it serves a present need.  Long-lived library and especially framework code need to be more future-friendly.  However, even there it's a good idea to write as little code as is practical today.

### Garden Your Code

Code is alive. We want to guide its growth and trim it back when it gets overgrown.  An ongoing diet of small upgrades and improvements is better than letting code rot for years and then doing dramatic, drastic surgery on it. 
Refactoring is just an ongoing part of maintenance --  not a scary dramatic intervention.

For this reaon, we want to be comfortable with refactoring as a regular part of our work.  We want to do as much testing as possible so that maintenance can be done with maximum safety and minimal disruption.   

We also want to retire code that's not serving a purpose any longer.  It's all backed up, if we need it again some day we can find it again.  But leaving unused code around provides a temptation to borrow from it -- thereby to keep alive not just the one function that was borrowed, but all of its dependencies alive.

### Test If You Can

TA work makes it hard to completely embrace [test-driven development](https://news.codecademy.com/test-driven-development/).  Because a lot of TA work happens inside complex environments that we don't control from end to end, it's hard to hit 100% test coverage without a lot of work.

However, tests are still an extremely valuable tool.  Tests are particularly important if we want to make refactoring a regular, ongoing part of our work: refactoring is a far less scary prospect when you have tests which demonstrate that moving a file or renaming method, or even fixing a bug has not created surprising sideffects.

Another very important benefit of tests is that testing encourages good design.  Testable code is generally going to be less complicated, less overly coupled, and less prone to wierd side effect bugs.  [This talk](https://youtu.be/DJtef410XaM) is Python specific but it lays out the case for why testing makes for better code structure overall very well -- and the idea that he's actually propounding originated in Java. 

Library and framework code should be tested.  Tools code -- with it's UI and scene dependencies is harder to test, but even there it's a good idea to design it in ways where testing is possible.

### Good Enough

This goes hand in hand with getting comfortable with refactoring as a regular event. Working through a problem is usually messy, and produces messy code. Every new tool is full of odd, one-off bits of code.  Getting a good understanding of the problem usually requires experimentation and that's OK.

However, once a tool moves into production it should be refactored into as clean and simple a form as possible. Once you see how things fit together you are in far better place to pick the right abstractions for the final version.  It's also a good time to see what parts of the tool are really general purpose code and can be pushed upstream into reusable library code.  And, when the problem involves some wierd, idiosyncratic code make sure to comment the reasons.  

What you write should get better over time.  It's very rare that you'll understand the whole problem set the first time. Good design will make it possible to add or extend the initial feature set in response to feedback and to the problems that only show up over time.  Again, being comfortable with refactoring makes it easier to adapt to new problems.  Every kind of coder struggles with the balance between 'just get it done' and the designing the perfectly general system that can be extended to all eventualities. The safest approach is to strike a middle ground: start small and improve, taking time to go back and rationalize the basic structure as you understand it better.

###  Less Is More

Starting small has another benefit as well. A wise man once said ["We hate code. We want to ship as little of it as possible"](https://www.youtube.com/watch?v=o9pEzgHorH0).  Not everything needs to be a class or a metaprogramming paradigm.  We ought to start with the simplest solution first, and add complexity when its called for.  Since we want to be comfortable with refactoring anyway there's no need to write a complex system until the simple system is failing to work.  

In keeping with that:

* Don't write a class where static functions will do.
* When classes are necessary, [prefer composition over inheritance](https://robots.thoughtbot.com/reusable-oo-composition-vs-inheritance) anyway. Deep hierarchies are a sign of over-complication.
* Use [pure functions](https://hackernoon.com/functional-programming-concepts-pure-functions-cafa2983f757) wherever you can.  If calling a function _changes_ things (in a class, in the scene) it's a potential bug. Pure functions are easier to test and can be swapped around.
* Abstract from a concrete, working example, instead of designing a perfect abstraction.

There are situations where a higher-level approach adds more simplicity over the long haul.  These are cases for writing frameworks.  However these situations are _rare_.


### Don't Optimize On Faith  

Performance occupies a strange place in TA coding.  On the one hand, it's often a secondary consideration: the difference between a one-a-day button push that takes one second and one that takes two seconds is not really that important.  On the other hand, responsiveness is a critical part of how users will see your tools.  If the tool _feels_ slow it will be unpopular, even if it's useful.  

So, it's not usually a good idea to write everything from the ground up as a super-optimized piece of code torture that wrings every last millisecond out of the hardware.  That takes a lot of precious time you could be spending on helping your users in other ways.  On the other hand it will often be the case that what you write will be better and get more use from your customers if it feels snappier.

Trying to balance these conflicting needs isn't always easy, but when you do see a place where performance does matter, take the time to optimize it correctly.  Write a simple, working example and then profile it.  Don't expend effort on speculative optimizations until (a) you know the algorithm and problems solving approach actually work and (b) you need more perf.  

Optimized code is a good thing -- when it's not a bad thing.  If the basic design is sound, it's usually possible to improve with optimization tricks.  But if the basic design is not sound, trying to speed it up with lots of micro-optimization is a lot of work that may or may not really pay off.  **Save the gimmicky stuff** for the last stage of stuff that's working, reliable and has already been checked against a profiler.

<a id="coding-standards"></a>
Coding Standards 
================

This is the part of the doc that approximates a traditional coding standard.

### General Principles

Code formatting is not an interesting problem.  All standards rub somebody the wrong way.  But even a standard that you personally dislike is probably fine: it's job is too make your work more readable to _other_ people.  

So: **suck it up and use the conventions of the environment you're in**. 

### Write Readable Code

9/10th of the life of a line of code is in maintenance and bug-fixing.  Take the time to write clear, descriptive names for everything -- classes, functions, and variables. 

Add comments, particularly if there are edge cases or special reasons you've made a decision.  Good function and variable names go a long way -- but if you have to do something that looks wierd because it's accomodating a quirk in Maya or Unreal, leave a comment so the next person in knows not to 'fix' it.

So:  **It's not done till it's properly named and commented.**  Comments for other coders and docs for users are _as important_ as code.  When reviewing each other's work we should always call out opportunities for better codes and comments.

For precise advice what good names and comments look like, follow the guidelines in sections one of [The Art of Readable Code](https://www.amazon.com/Art-Readable-Code-Practical-Techniques/dp/0596802293).  We can adapt those rules for language flavor -- that is, use Unreal-style casing in C++ and underscored names in python -- but the principles are the same everywhere.  **Required reading.**

### Write Idiomatically 

Don't write Python in C++ or C# in Python.  Most of us like one language more than others, which is fine.  But code is a shared asset, and making your code readable and maintainable for other people means going with the flow of the language you are in.

[This video](https://www.youtube.com/watch?v=wf-BqAjZb8M) is specifically about Python but it's a good illustration of the difference between writing code that's formally correct and code thats idiomatic.

[This one](https://www.youtube.com/watch?v=hEx5DNLWGgA) covers modern C++ with a good, reasonable approach to managing complexity, though it's not quite as punchy.

[This one](https://www.youtube.com/watch?v=anrOzOapJ2E) illustrates a lot of good ways to make code more 'pythonic'.

### Formatting

#### C++
Follow the [Unreal coding standards](https://docs.unrealengine.com/en-us/Programming/Development/CodingStandard).  Use the [Clang Format](https://marketplace.visualstudio.com/items?itemName=LLVMExtensions.ClangFormat) plugin in visual studio and or the equivalent [plugin](https://packagecontrol.io/packages/Clang%20Format) for Sublime.

> @TODO: get the canonical clang format file as an appendix

#### Python

Follow [Pep-8](https://www.python.org/dev/peps/pep-0008/?).  We allow up to 120 characters wide (instead of the 80 characters in default pep-8) to avoid wasting time noodling on line breaks.  In Sublime Text use the [Anaconda](http://damnwidget.github.io/anaconda/) plugin for linting; in PyCharm use the default linter.  Use 4-space indentation.

<a id="working-together"></a>
Working Together
================
As the department grows we need to do a better job of making sure we spread vital knowledge around. 

In order to do that, new features, systems or tools should always involve at least two people:  an **implementer** and an **understudy**.  The implementer does the bulk of the work, but the understudy is an important part of the process.  The goal here is to strike a good balance between individual autonomy in design and implementation on one side and encouraging collective ownership of code and tools on the other.  It's well short of [pair programming](https://medium.freecodecamp.org/the-benefits-and-pitfalls-of-pair-programming-in-the-workplace-e68c3ed3c81f) but it's more structured than our current approach.

The relationship looks like this:

#### Design Review  
When the feature is first sketched out, the implementer will have to do some homework on how to approach the problem.  This could involve anything from reading a talking to customers to digging up research papers to reading code in the Unreal Engine.  When the implementer has a general idea of how to proceed, s/he should run it by the understudy to make sure that it makes sense.  The understudy should explicitly sign off on the general direction.  This could be as simple as 
    
> "Hey X -- I think I'm going to tackle this by creating a map of CharacterAssets to ClothingAssets and storing that as an Unreal DataTable.  Users can open that in the editor and add their annotations that way" 

> "Sounds good, maybe you should talk to Marky about how he handled that for the facilities system."

or it could involve an actual whiteboarding session.  The point is _not_ to enforce team design on everything - it's to make sure that there are more eyes on the problem. Discussion can help head off potential problems or simply provide context for future code reviews. 

Understudies can, and should, remind implementers if there is existing functionality that can be reused.  Less is more!

#### Code Review
As the feature or system progresses, understudies should review major checkins.  We'll need to work out the right cadence, it's too much to expect that every checkin will be reviewed by another person.  But significant waypoints in the evolution of the feature should be reviewed. There are several goals here:

- **Ensure that finished code is at least somewhat familiar to at least two people at all times** An important part of the understudy's review process will be pointing out places where the code needs comments or is hard to follow, and to encourage attention to our general design standards. 
- **Share the good stuff**  We all have a lot to learn from each other; a neat line of code that's obvious to you may not be so clear to the next person in line, so getting and participating in reviews is important for helping to nudge us in the direction of mutually readable code.
- **Spot opportunities for reuse**  More eyes on the problem is a very good way to increase the chances that you have not reinvented the wheel -- if colleague X wrote a library to do something and you've just invented a slightly different way to do the same thing, X should at least point out the existing stuff so we get redundancy by choice, not by accident.

An important part of getting high quality reviews is to set the review up with clear expectations.  In your checkin or review comments make sure to explain what the code is supposed to do and why.  Lay out the basic assumptions and general structure.  Don't be afraid to ask for advice or point out possible issues: "Does anybody know a better way to do this?" is professionalism, not weakness!

Above all remember that reviews are not like high school tests; nobody wants to flunk you. We want to get good visibility into each other's code and also to be reassured that what we're doing actually makes sense to other people.  There will be disagreements about style and strategy; that's just inevitable.  But it's good to _know_ when what you're doing looks odd to others, even if you think the oddity is worth the cost.  [This article](https://www.developer.com/tech/article.php/3579756/Effective-Code-Reviews-Without-the-Pain.htm) and [this one](https://medium.freecodecamp.org/unlearning-toxic-behaviors-in-a-code-review-culture-b7c295452a3c) both have some good concrete tips on how to participate effectively on both ends of a review.

**TLDR** A small investment in reviews makes us all better coders. It's not a bureaucratic hassle -- its a free course in how to be a better TA.  Not convinced? [Check out the stats!](https://blog.codinghorror.com/code-reviews-just-do-it/)


#### Wrap Up
Nothing is done unless it's readably commented (for other coders) and documented (for end users).  The final documentation pass should fall on the understudy, with input and review from the implementer. This is important for making sure that the system makes sense to at least two people.

In many cases -- particularly for more complex jobs -- the understudy will also be working on the same code.  In cases like that the two roles should basically be reversed as needed:  A understudies B's work and vice-versa.  



Appendix 1: Style notes
-----------

This section is going to be a bit subjective, and is not intended to be enforced as 'rules'.  It's a set of style considerations to consider when you are designing new code structures.  


Python Style
=================

First and foremost, think about **how Python wants to be written**.  Simplicity and readability are key values in python code.  There's a reason a lot of core Python developers use the word 'beautiful' a lot when talking about programming.  Making good use of common Python idioms will make your code easier to read and maintain for everybody on the team.

Unfortunately, since most of of our python is written inside of Maya we are often writing agains an API  that is neither very simple nor very readable. Everybody who works with Maya has had to write countless variations on
 
    cmds.getAttr(object_name + '.property')

or 

    cmds.editDisplayLayerMembers( 'displayLayer1', query=True)

Neither of these line is a big deal -- but both impose an incremental tax on your thought process, particularly when you're reading a long, complex piece code where the Maya mechanics are main point. When you first learn Maya the challenge is simply getting things working and that kind of very explicit step-by-step code is part of the learning process. When you're more senior, you will start seeing opportunities to clean up and clarify the chattiness and clutter of the Maya API.  

It is a good idea to resist the temptation to write and endless series of wrappers for convenience.  A good simplification is one that expresses your intentions better and offers more tools for working with the problem -- not just one that saves a few characters!   See the discussion of [frameworks](#frameworks) above.

[This talk](https://www.youtube.com/watch?v=wf-BqAjZb8M) by Raymond Hettinger is a great example of how you want to approach code simplficiation -- it's an example of a Python core developer explaining how to 'Pythonify' an api that was written for another language mindset.  It's a natural analogy for how we relate to Maya.

## The toolbox

<a id='modules'></a>
### Modules

Modules are a vital tool for keeping python code organized.  Keeping related code together in a well-named module allows for a good mix of descriptive names wihout redundancy.  Good modules reduce the need for extremely long function names, and allow for more flexibility in layout

    import texture_tools
    texture_tools.get_uvs_fom_selected()

and 

    import uvs
    uvs.from_selected()

Don't write a class just to create a holder for methods  -- make a nested python module instead. 

Python modules are also the right way to handle shared state which in other languages requires a singleton.  For example, you might want to have project related code for managing data about your working environment.  You would not want different parts of the same application to disagree about that kind of data.  In Python delegate that code to a module instead of trying to write a singleton class:  Modules are globally accessible, only run their own code once, and they are 'singletons' by default.  

In general, "singleton" style shared state is something to avoid whenever possible in any case.  Too much shared state makes it too easy for bugs to crop up in untraceable ways.  However when you do need to share information, use the module mechanism as the easy, Pythonic way to maintain shared data.

<a id="basics"></a>
### Lists, Tuples and Dictionaries -- know the basics

Collections are the heart of Python programming -- they're super useful and reduce a ton of the boring boilerplate common in other languages.

It's important to get good use out of them, which means a couple of basic things:

#### Use idiomatic looping

Use  

    for item in my_list:
       do_thing(item)

for most loops and 

    for index, value in enumerate(my_list):
       do_thing(index, item)

if you need the indices.  Don't use `for i in range(len(my_list)))`

For dictionaries, prefer 

    for item in my_dict

for most things, and
   
    for item in my_dict.keys()

if you might be adding or removing entries in the loop.

#### Use 'in' for content checks:

Use 
   
    if "x" in my_list

and

    if "x" in my_dict

and

    if "x" in my_tuple


#### Learn slicing

One of the best tools for writing compact, readable Python is [slice notation](https://medium.com/@adamshort/python-slicing-72f76bb36e31).  It's very useful for a wide variety of tasks -- anything from reversing a list to subsetting it to taking every Nth item can be done concisely with slices.

An extra incentive to learn slicing is [`itertools.islice`](https://docs.python.org/2/library/itertools.html#itertools.islice) which offers the same functionality but as an iterator -- it's an elegant way to express an idea like "take every third item from this list, starting at number 10000 and counting down"  without the expense of creating a new in-mempory copy of the list.  See [Use Generators](#generators), below.

#### List comprehension are great -- if comprehensible

Most of the time `my_list = [x for x in range(100) if x %2 ==0]`  is better than a for loop.  However if you're finding it hard to fit a comprehension onto a single like it's probably too complex and ought to revert to being a loop.


#### Prefer tuples to lists if you can

Tuples are slightly faster than lists, and they have less overhead if you don't expect their contents to change.  Whenever you're simply answering a question like "what are the objects in this Maya scene using this shader" -- it's better to return a tuple instead of a list, since it's the correct answer now and user's can't accidentally change it.  There's no way to, say, try to change a tuple while loopong over it.

Tuples are also faster to create as literals.  List initializers are fast:

    data = ['a', 'b', 'c']

but tuple literals are faster (note, btw that it's the _commas_, not the parens, which define a tuple) 

    tuple = 'a', 'b', 'c'

So tuples are a natural fit for constants lookups or other data you don't expect to change at runtime.

Tuples can also be used as keys in a dictionary, where lists cannot.  So you could for example represent something like a chessboard as dictionary using tuples as keys:

    Chessboard = {(0,0): 'Rook', (0,1), 'Pawn', ... }

This is much more efficient than having an actual nested 2-d array to represent the board.

#### Prefer namedtuples to tuples for structured data

[`collections.namedtuple`](https://pymotw.com/2/collections/namedtuple.html)offers an excellent alternative to dictionaries and custom classes for structured data.  It's far better to write

    if person.age > 21

than 

     if person[7] > 21

 namedtuples make for more readable code with very little work.  They are also cheaper than dictionaries and -- being immutable, like tuples -- they are less prone to accidental mutations.

#### Dictionary idioms

Dictionaries are one of Python's best features -- an excellent way to handle information flexibly. Using them idiomatically is a key to good Python style:

* Don't forget about dictionary comprehensions:  {k: v  for k, v in zip(keys, values)}
* Check for inclusion with `if value in my_dict`.
* Loop over the keys and vales together with `for key, value in my_dict.items()` (or `.iteritems()` to save memory).
* Use `my_dict.get(key, fallback_value)` rather than checking to see if a key already exists.  Use `my_dict.setdefault(key, value)` to do the same thing _and_ to ensure that the key is present in future.
* Remove dictionary keys and values with `del my_dict[key]`, but get-and-remove in one operation with `my_dict.pop(key)`  using `del` makes it clear that you intend to remove a key
* Use [`collections.defaultdict`](https://docs.python.org/2/library/collections.html#collections.defaultdict) if you have to do a lot of checking to see if a dictionary already has what you need instead of hand-writing an if-check.  

Dictionaries make a very useful alternative to long string of `if - elif - else` comparisons, particularly if you're routing to different code based on some value.  For example if you were tring to choose between  handler functions based on a selector string:

     options = {
        'a': a_handler,
        'b': b_handler,
        'c': c_handler
     }
     func = options.get(selector, fallback_handler)
     func()

is more compact and easier to extend than

    if selector == 'a':
        a_handler()
    elif selector == 'b':
        b_handler()
    elif selector == 'c':
        c_handler()
    else:
        fallback_handler()

<a id="generators"></a>
### Use Generators

One of the most important things you can do to improve the speed of your Python is use generators and iterators instead of creating large in memory lists.

These two pieces of code produce the same result:
    
    test = 0
    for n in range( int(40e6)):
        test += n

and 

    test = 0
    for n in xrange (int (40e6)):
        test += n

however the second one is **40% faster** because it does not create a complete list in memory of 40,000,000 integers -- it just processes the numbers one at a time.

Whenever possible, use the [`yield`](https://jeffknupp.com/blog/2013/04/07/improve-your-python-yield-and-generators-explained/) keyword to have your functions generate results that can be iterated over, rather than lists which have to be created in memory.  If the caller needs the entire list, it's easy to turn a generator into a list or a tuple.  But if the caller does not need the whole thing in one go -- if they just want to process items one at a time -- widespread use of generators makes for faster, more memory efficient code.  

It's also easy to [chain generators together to create fast pipeline-style code](https://brett.is/writing/about/generator-pipelines-in-python/) which does filtering or transformations in small, composable pieces.  The helps with both reusability and performance.

<a id="tryblocks"></a>
### Try-Except-Finally

`try` and `except` are basic parts of Python.  Their lesser-known sibling `finally` however is a very powerful tool for writing cleaner code.  The basic structure is :

    try:
        do_something()
    except:
        handle_problems()
    finally:
        clean_up_after_yourself()

In a `try/except/finally` block the code in the `finally` section is guaranteed to execute whether or not the `try` block raised an exception.  This is important because it allows you to handle cleanup tasks knowing that they will execute regardless of whether or not the code above them succeeded or failed.  Consider something like this:

    try:
       output = open('filename', 'wt')
       output.write( 99 / x)
       output.close()
    except IOError:
        print "could not open file"

This looks OK -- the code correctly handles a case where, say, 'filename' is write-locked.  However if the variable `x` is zero, the code will fail with an uncaught `ZeroDivisionError` and the file handle will be left open, which will leave an undeletable file on disk until the python interpreter is forcibly closed.  A `finally` block will make sure that the cleanup happens no matter what:

    try:
        output = open('filename', 'wt')
        output.write( 99 / x)
    except IOError:
        print "could not open file"
    finally:
        output.close()

In this version, `output` will be properly closed no matter what else happens.  In Maya programming this is often a vital tool for making sure that the scene is restored to a legitimate state if one of your tool operations goes awry.

<a id="contexts"></a>
### Context managers

A variant on the same theme is the context manager: the Python construct that start with `with`.  A context manager is an excellent way package up work that has a defined beginning, middle and end in a readable, but also safe way.

For example, Maya wants you to do this to create and work in a new namespace:

     cmds.namespace(set = ":")  
     #  you have to go back to the root namespace, or you'll create a child
     cmds.namespace(add = 'new_ns')
     cmds.namespace(set = 'new_ns')
     
     # ... do work here ...
     cmds.namespace(set = ":")  

     # return to root, or other work will be done in your namespace 

This is all very simple, and maya vets don't think it's a big deal.  However, for four lines of boilerplate it has four significant weaknesses:

1. If you forget to reset the namespace at the top, the rest of the code will quietly produce data in the wrong place
2. If you mistype the namespace in the second or third lines, the code will fail. Because the typo is in a string, you won't know it until runtime.
3. If you forget the reset the namespace at the end of the block, you'll be changing the behavior of all the code that runs after this.
4. If the work code raises an exception, the namespace won't be reset and all future code will also fail.

Luckily Python has a great native mechanism for dealing with this kind of setup->work->cleanup paradigm: the `with` statement.  It's not a lot of work to create a context manager that handles the boilerplate above:

```
import maya.cmds as cmds
class Namespace(str):
    def __init__(self, name):
        self._name = name
        self.last_namespace = None
        self.namespace = None

    def __enter__(self):
        self.last_namespace = ":" + cmds.namespaceInfo(cur=True, fn=True)
        
        if self._name.startswith(":"):
            should_add = not cmds.namespace(exists = self._name)
        else:        
            should_add = self._name not in (cmds.namespaceInfo(lon=True, sn=True) or [])
            
        if should_add:
            cmds.namespace(add = self._name)
        cmds.namespace(set = self._name)
        self.namespace =  ":" + cmds.namespaceInfo(cur=True, fn = True)
        return self

    def __exit__(self, *exception):
        cmds.namespace(set = self.last_namespace)
        return False  
        
    def __str__(self):
        return self.namespace or 'invalid-namespace' 

    def __repr__(self):
        return 'Namespace("' + self.namespace  + '")' or 'Namespace(" + self.name + ")'
```


That's a quite a few more lines to write -- but once it's written all other namespace jobs can handled much more cleanly:

    with Namespace('my_namespace'):
        # do work in 'my_namespace',
        # and return to whatever namespace you were in
        # when done

Formally you could do the same work in `try-except-finally` structure and be guaranteed to get the same results.  From a readability standpoint, however, the `Namespace` context manager is a big win -- it takes a series of steps that are all related, all necessary, and all pretty common in Maya programming and renders them both safe and easily readable at the same time.

You may note that `with` blocks have a generic similarity to `try-except-finally` constructs.  Generally the former are best for things you have to do a lot, like namespaces and the latter are the fallback for one-offs.


<a id="closures"></a>
### Closures

Python [closures](https://www.programiz.com/python-programming/closure) are a very important tool for sharing data without the need for elaborate class structures.  The rules have some interesting subtleties, but basically they boil down to this:

1. When you create a new scope -- a function or a class -- it automatically inherits the names defined in its current scope. If you declare a variable first and then declare a function right afterwards, that function gets access to that variable:
```
    test = "hello world"
    def do_test():
        print test
    # hello world
    print test   # here we're in the outer scope again
    # hello world 
```
2. Names inherited via a closure can be _read_ (as in the above example), or _mutated_:
```
    test = {'hello': 'world'}
    def do_test():
        test['hello'] = 'sailor'
        print test
    # hello sailor
    print test  # back to the outer scope -- the change sticks
    # {'hello' : 'sailor'}
```
3. However, inherited names _cannot_ be re-assigned.  So, the obvious extension of the first example will not work as expected:
```
    test = "hello world"
    def do_test():
        test = 'hello sailor'
        print test
    # hello sailor
    print test # back to the outer scope: no change!
    # hello world
```

This key difference here that assignment (that is, the `=` operator) does not work its way back up the outer reference chain; instead it creates a new variable that masks the inherited version inside the local scope only. 

The inheritance rule can be a bit intimidating, but closures are a very powerful tool for simplifying Python code particularly in a maya context.  Probably the best example is creating GUI, which often requires different widgets to know about each other.  Here's an example of a simple GUI that uses closures to let the different widgets communicate without the need for an elaborate class structure:

```
    def closure_window():
        window = cmds.window(title ='closure example')
        layout = cmds.columnLayout(adj=True)
        rowLayout = cmds.rowLayout(nc=3)
        increment = cmds.button(label = 'plus')
        decrement = cmds.button(label = 'minus')
        counter = cmds.intField()

        def get_counter():
            return cmds.intField(counter, q=True, v=True)

        def set_counter (val):
            cmds.intField(counter, e=True, v=val)

        def add(_):
            set_counter(get_counter() + 1)

        def sub(_):
            set_counter(get_counter() - 1)

        cmds.button(increment, e=True, c=add)
        cmds.button(decrement, e=True, c=sub)

        cmds.showWindow(window)
        return window

```

Here, the closure allows the increment and decrement buttons to know which field to affect even after the function has fired and the window has been created.  You could achieve the same effect with a class, using instance fields and instance methods to connect the different widgets together. In complicated cases that's still a good way to go. However closures provide many of the same benefits with much less overhead and should be part of your design process.  

Closures are particularly attractive because they are space efficient -- however you do need to avoid going too deep, or your readers may not be able to figure out where your variable names are coming from. If you are inheriting a variable several screens away from where it originates, at least leave a comment indicating where the name comes from. The `ALLCAPS` convention for module-level constants is also a good way to remind readers when they may be seeing a closure variable.



### Decorators

Python decorators are a powerful tool for making simpler code. A decorator is basically just a way of wrapping a ordinary function to add some additional functionality.  For a toy example:

```
    def rightslash(original_function):
        def right_slashed(*args):
            result = original_function(*)
            return result.replace('\\', '/')

        return right_slashed

    @rightslash
    def working_directory():
        return os.getcwd()

    @rightslash
    def parent_directory():
        return os.path.dirname(__file__)

```

This would allow you to take a bunch of file management functions and ensure they all returned right-slashed pathnames instead of left-slashed ones on windows.  

You could of course do the same thing by doing the replace operation in every one of the functions, but the decorator version has two key advantages. First, it *spells out its own intentions clearly* -- it promises the reader that a function will (in this example) return a certain kind of data. Second, it's *easily extensible*.  Say you discovered that you did not want to right-slash paths if they were UNC-style paths like `\\tajiri\team\steve` -- going back and adding the same logic to a dozen different file handling functions would be tedious and an invitation to bugs, while fixing a single decorator function would be far quicker, easier and safer. 

Decorators are a very powerful tool for enforcing consistency across functions which are otherwise independent of each other.  For example, a Maya library that dealt with geometry could use a decorator to make sure that it's functions transparently found the shapes associated with transform arguments in a robust and reliable way instead of forcing dozens of functions to use similar but not-quite-identical ways to find the geometry.  Or, a file exporter could use decorators to make it easy to provide common logging and error handling for the many individual operations that make up an export. Whenever you're faced with the problem of coordinating similar behavior across a large range of functions (or even of classes -- classes can be decorated too) decorators are an excellent tool, offering the standardization that other languages get from class hierarchies without the accompanying complexity.

The one thing to remember about decorators is that they shouldn't interfere with readability:  a decorator that automatically right-slashes file names won't surprise readers, but one which causes a function that looks like it returns string to return numbers instead will generate a lot of confusion.

## Afterword: The proper uses of magic

Python has a high magic quotient; you can change a lot about the behavior of basic Python entities, allowing you to customize many aspects of how your code looks and acts.  In the right circumstances this is a very powerful tool: you can write code that more clearly and concisely expresses its intent if you know how to use advanced techniques.  However, this is a power that should be used _sparingly_: it's very easy for a system to become so sophisticated that  it's incomprehensible to any one except the author -- or, sometimes, _including_ the author once a few months have passed.

There's no hard and fast rule to knowing when the time for magic has come, but a good rule of thumb to start with is actually aesthetic.  Consider the case of a class that acts as a vector for vector math:

```
class Vector (object):
    def __init__(self, x, y, z):
        self.x = x
        self.y = y
        self.z = z

    def add_in_place(self, othervector):
        self.x += othervector.x
        self.y += othervector.y
        self.z += othervector.z

    @classmethod
    def add (cls, vector1, vector2):
        return Vector (vector1.x + vector2.x, vector1.y + vector2.y, vector1.z + vector2.z)

    # imagine similar methods for subtraction, multiplication and so on...

v1 = Vector (1, 2, 3)
v1.add_in_place(Vector(4,5,6))
# Vector(5,7,9)

v2 = Vector.add(v1, Vector (4, 2, 0))
# Vector(9,9,9)

```
        
This works pretty well -- but it violates a basic expectation for vector math, namely, that you can use standard mathematical notations (with all the other things they imply, like order of operations, commutation, and so on). Here a small investment in 'magic' makes sense:

```
class Vector (object):
    def __init__(self, x, y, z):
        self.x = x
        self.y = y
        self.z = z

    # inplace addition
    def __iadd__(self, othervector):
        self.x += othervector.x
        self.y += othervector.y
        self.z += othervector.z

    # addition
    def __add__ (self,  vector2):
        return Vector (self.x + vector2.x, self.y + vector2.y, self.z + vector2.z)

    # there are similar magic methods for subraction, multiplication etc.

v1 = Vector (1, 2, 3)
v1 += Vector(4, 5, 6)
v2 = v1 + Vector(4, 2, 0) 
```

The functional part of the code is no different but this class operates in an appropriate problem space.  The win in readability is strong, but the underlying relationships are still easy to parse.  That's some white magic.

On the other hand there are times when the magic is too deep and you risk tangling with Things Better Left Alone. Python [metaclasses](https://jakevdp.github.io/blog/2012/12/01/a-primer-on-python-metaclasses/) earned this quote for a reason:
> “Metaclasses are deeper magic than 99% of users should ever worry about. If you wonder whether you need them, you don’t (the people who actually need them know with certainty that they need them, and don’t need an explanation about why).” — Tim Peters

There really are legit applications for metaclasses, but they are rare (and often, the temptation to find uses coincides a bit too much with learning about metaclasses for the first time).  If you're considering a metaclass -- or other highly involved techniques like runtime [monkey-patching](https://www.youtube.com/watch?v=ZpJxwpyJpq4) -- be sure you're actually solving the problem and not embracing cleverness for its own sake.
