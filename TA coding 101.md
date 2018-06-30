TA coding 101


Intro
=====

This document is intended as a reference for our shared coding work. It's an attempt to balance the need for consistency and standardization with individual styles and ownership.  This is not primarilay 'coding standards' document: things like where to put your commas are really a problem for machines, not people. See [Coding Standards]() below for details, but keep in mind that formatting is the least interesting part of the problem set.


Know where you're at
===================

The first and most important principle for our code is to know what _kind_ of code it's supposed to be.  A simple user facing tool isn't the same thing as a piece of library code that will be run thousands of times.  Runtime Unreal code is not the same thing as an artist tool.  Not every line of code demands the same level of scrutiny.

Broadly speaking there are four kinds of code we write:

### Runtime code

This is code that runs in the game.  This kind of code needs to be carefully vetted for memory usage and performance.  Ideally it also needs to be evolved in cooperation with the engineering team.  This is high-stakes code that should be planned and evolved accordingly.

When working on runtime code, we need to be sure to:

* Get code reviews from inside and outside the team
* Stay in touch with engineering to make sure that our code complements theirs
* Use telemetry markup to help with profiling
* Make sure we understand the memory costs of our work

### framework code

This is code that's intended to solve a general problem in a repeatable way.  For example, it could be a library for reading and writing a particular file type, or a system for adding metadata to an import pipeline.  Out-of-house examples would be something like the `xmltree` module or `django`, which will be specialized by other users to solve concrete problems Framework code is more general and high level than other kinds of code library: if your code is going to impose a particular approach to a broad problem it's likely framework code.  It's as close to 'pure' programming as we get.

framework code needs to:

* Be well reviewed to get buy-in
* Be carefully designed. This kind of code gets reused in many places and needs to solid.
* Have high test coverage. Because framework code is heavily reused, we need tests to ensure that it doesn't cause bugs as it evolves.
* Do a small number of things extremely well.
* Not try to dictate end-user experiences.
* Provide a clear, well documented interface.
* Explain its own assumptions and paradigms


### library code

This is the largest category of code -- the place where most of the problem / solution work is actually done.  Library code is re-usable code that's specific to different problem set. Library code serves two primary purposes.  First and foremost, it encourages the re-use of high quality code -- as Python says _there should be one, and preferably only one, way to do it_.  Second, it's intended to cut down repetitive nuisance code -- a good library makes sure that more code is tested and bulletproofed and that one-off, unique code really is unique.  Library code is how we accumulate knowledge about our problem spaces.

Library code needs to:

* Be clear, well organized, and discoverable.  It's no good making libraries if nobody else knows they are there.
* Be well tested and reliable.  This is code that will be heavily reused and it needs to be as deterministic as possible.
* Stick to one problem domain.  A library for processing geometry should not expose a string formatting function.
* Avoid UI concerns.  Library code is code for other code to use.
* Be willing to fail.  Library code does not know about context. Decisions about what to do if something goes wrong are left to user-facing tools.

### Tool code.

Tools are the tip of the iceberg -- the parts of our work that are visible to our users. This is where the menus, buttons, dialogs and scripts go.  The main job tool coode is to coordinate functionality from the avaiable libraries into a user-friendly package that's easy to maintain and support.

Tool code needs to:

* Make good use of available frameworks and libraries.  Don't reinvent the wheel -- make sure that you've looked for existing solutions.
* Make good UI decisions.  Unlike library and framework code, tools include a lot of user-facing code.
* Deal with bad data.  Tools usually have a user present to ask for help, so it's tool's job to deal with the unexpected.
* NOT import other tool code. A tool is the root of a dependency tree -- if tools import each other, the structure becomes impossible to maintain.


To sum up:
* Frameworks are standalone code, with careful design and heavy testing
* Libraries are code for code reuse.  They are designed to be used by tools.  Libaries use frameworks and sometimes other libraries
* Tools use a variety of libraries and/or frameworks.  Tools don't import other tools.
* UI always lives at the level of tools.
* If code in a tool turns out to be generally useful, push it up into a new or existing library.  And then test it.


Design standards
================

Again, this is not about _coding_ standards.  Design standards are 'how do we approach a problem'

1. 105% rule.  It's tempting to try solving all future problems when you write code, particularly library or framework code.  Don't. _Think_ about future problems and try to design your implementation in a way that will not prejudice future enhancements -- but don't write code that you don't have a need for in the immediate future.  You may change your mind, and the more code you write today, the less willing you'll be to do so.
2. Fix what you can. Code is like a garden. You want to guide its growth and trim it back when it gets overgrown.  An ongoing diet of small upgrades and improvements is better than letting code rot for years and then doing dramatic, drastic surgery on it. Learn refactoring tools and use testing to make sure that you can do ongoing maintenance with maximum safety and minimal disruption.
3. Solutions, then abstractions, then shipping code.  Working through a problem is usually messy, and produces messy code. That's ok as a prototype: if you write a new tool it might be full of odd, one-off jobs.  However once a tool moves into production it should be refactored into as clean and simple a form as possible. Once you see how things fit together you are in far better place to pick the right abstractions for the final version.  At that point   
2. ["We hate code. We want to ship as little of it as possible"](https://www.youtube.com/watch?v=o9pEzgHorH0).
4. Write for the reader. 9/10th of the life of a line of code is in maintenance and bug-fixing.  Take the time to write clear, descriptive names for everything. Add comments, particularly if there are edge cases or special reasons you've made a decision. Required reading: [The Art of Readable Code](https://www.amazon.com/Art-Readable-Code-Practical-Techniques/dp/0596802293)

2. 
