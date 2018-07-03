TA coding 101


Intro
=====

This document is intended as a reference for our shared coding work. It's an attempt to balance the need for consistency and standardization with individual styles and ownership.  This is not primarilay 'coding standards' document: things like where to put your commas are really a problem for machines, not people. See [Coding Standards]() below for details, but keep in mind that formatting is the least interesting part of the problem set.


Know where you're at
===================

The first and most important principle for our code is to know what _kind_ of code it's supposed to be.  A simple user facing tool isn't the same thing as a piece of library code that will be run thousands of times.  Runtime Unreal code is not the same thing as an artist tool.  Not every line of code demands the same level of scrutiny.

Broadly speaking there are four kinds of code we write, starting from the most deep and deliberate and moving up towards user facing code which needs to be responsive to user feedback.

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
5. Split out content that changes frequently from content that changes rarely.  Usually this means keeping 'code' and 'data' separate -- but it also means isolatiing complex algorithms from structures.
6. Don't use classes as namespaces, that's what modules are for
7. Want a singleton? Use a module.
8. Write idiomatically.  Don't write Python in C++ or C# in Python.
9. Don't optimize on faith.  Write a simple, working example and then profile it if you're worried about performance.  Don't write a complex, tweaked out version until (a) you know the algorithm and problems solving approach actually work and (b) you need more perf.


Working together
================
As the department grows we need to do a better job of making sure we spread vital knowledge around. 

In order to do that, new features, systems or tools should always involve at least two people:  an **implementer** and an **understudy**.  The implementer does the bulk of the work, but the understudy is an important part of the process.  The goal here is to strike a good balance between individual autonomy in design and implementation on one side and encouraging collective ownership of code and tools on the other.  It's well short of [pair programming](https://www.agilealliance.org/glossary/pairing/#q=~(filters~(postType~(~'page~'post~'aa_book~'aa_event_session~'aa_experience_report~'aa_glossary~'aa_research_paper~'aa_video)~tags~(~'pair*20programming))~searchTerm~'~sort~false~sortDirection~'asc~page~1)) but it's more structured than our current approach.

The relationship  looks like this:

**Design review**  When the feature is first sketched out, the implementer will have to do some homework on how to approach the problem.  This could involve anything from reading a talking to customers to digging up research papers to reading code in the Unreal Engine.  When the implementer has a general idea of how to proceed, s/he should run it by the understudy to make sure that it makes sense.  The understudy should explicitly sign off on the general direction.  This could be as simple as 
    
> "Hey X -- I think I'm going to tackle this by creating a map of CharacterAssets to ClothingAssets and storing that as an Unreal Datable.  Users can open that in the editor and add their annotiations that way" 
> "Sounds good, maybe you should talk to Marky about how he handled that for the facilities system."

or it could involve an actual whiteboarding session.  The point is not to team design things - it's to make sure that there are more eyes on the problem. Discussion can help head off potential problems or simply provide context for future code reviews. Understudies can, and should, remind implementers if there is existing functionality that can be reused.  Less is more!

**Code review**  As the feature or system progresses, understudies should review major checkins.  We'll need to work out the right cadence, it's too much to expect that every checkin will be reviewed by another person.  But significant waypoints in the evolution of the feature should be reviewed. The goal is that the finished code is at least somewhat familiar to at least two people at all times.  An important part of the understudy's review process will be pointing out places where the code needs comments or is hard to follow, and to encourage attention to our general design standards. 

**Wrap up** Nothing is done unless it's readably commented (for other coders) and documented (for end users).  The final documentation pass should fall on the understudy, with input and review from the implementer. This is important for making sure that the system makes sense to at least two people.

In many cases -- particularly for more complex jobs -- the understudy will also be working on the same code.  In cases like that the two roles should basically be reversed as needed:  A understudies B's work and vice-versa.  

