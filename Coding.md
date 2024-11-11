# Coding

These questions are intended to identify the candidate’s familiarity with programming concepts and best practices. 
When interviewing code-oriented candidates remember to look for aptitude, willingness to learn, and particularly evidence of a self-help mindset, rather than simply checking to see if they have already worked with a particular technology or technique. For the most part these questions are not language specific, but it's a good idea to follow up with clarifying questions that might show depth of knowledge about language particulars.

In many of these questions, it will be important for the candidate to ask follow up questions to get a proper view of the problem. It’s fine to tell the candidate in general terms that part of the exercise is seeing what questions they ask. Avoid volunteering extra detail unless the interviewee is getting stuck – the questions they ask provide a good insight into their approach to problem solving.

When asking about their own experiences, ask for additional implementation details on any tooling or optimization work. Make sure they understand how they implemented any tool and why they made specific performance decisions. 

1. Would you describe yourself as an “engineer”, a “programmer”, or a “scripter”? Why?
  - This is useful for calibration, but also an interesting way to see how they understand the use of code in production. 
    - Are they focused on using code to achieve results? 
    - On writing solid, maintainable code? 
    - Or on high-performance, carefully optimized code?
  - If they are self-taught, it may be useful in followup questions to be careful with jargon
  - Sometimes this will also reveal a candidate who is gunning for a higher paying engineer title in the future
  
2. Are you curently thinking about learning any new programming language? If so, why are you interested in that language? What are you doing to learn the language?
    -  Interviewer should already know (from resume and/or prescreen) what languages the candidate knows. 
    - The choice of a subsequent language is a chance to see what problem spaces they are interested in: 
        - Do they want to learn C++ for higher performance or to get deeper into their engine? 
        - Are they interested in Rust because of its reputation for reliability? 
        - Do they want to learn Python for its flexibility?
        - For something more esoteric, what's the motivation? Curiosity? Hobbyist interest? Some specific project need?
    - Alternatively, do they just see code as a tool and they learn whatever their job requires? For example are they picking up C++ to be able to write tools for Unreal, rather than because they are interested in C++
    - The choice of learning method can be revealing - is this somebody who picks up new skills on their own, or are they taking a class? Do they seem to be a regular lifelong learner?

3. Say I have a list of a hundred thousand items and I need to retrieve items from the list quickly. Is there a data structure you’d reach for to store the items? What else would you want to know before picking one? 
    - The easy answer is a dictionary-like structure with linear search time.
    - An even better response is to ask if the access pattern is predictable: a dictionary is ideal for purely random access, but if you know the order in which things are going to be retrieved then a plain array might be a better choice. 
    - Do they ask questions about the potential ordering of the data, and if so, does their answer reference a sorting method which would be a good candidate for fast retrieval?
    - If the candidate is thinking about accessibility, rather than speed, they might think SQL. If so, do they know how to optimize a SQL index for fastest retrieval?
    - Do they realize that merely finding one item in 100,000 is not really a terrible performance cost even if it’s not handled optimally?  Are they comfortable saying "for a one-time lookup, it's not worth optimizing too much?"

4. We have a large collection of 3-d meshes, some which might or might not be duplicates. We don’t need to code a detailed solution – but, roughly, what approach would you use to find the duplicates? Is there anything you need to know before trying something?
    - A good part of the value of this question comes from the user’s follow-up questions. Here are some of the constraints they can find out by asking. It’s generally better if they ask rather than volunteering information.
        - For purposes of this question we only care about geometry duplicates – no need to consider UVs, vert colors, or materials. This actually doesn’t affect the solution but it simplifies it.
        - We only care about local vertex positions, not world space positions: we might find many copies of a mesh moved and rotated all over the place.
        - There’s no guarantee that there won’t be many copies of a given mesh.
        -  A mesh is a “duplicate” if it has the same local space vertices, regardless of vertex index order.
    - If the user is a DCC expert, they may note that ‘duplicates’ could also be instances, which can be detected by analyzing the scene graph. This is an acceptable part of the solution, but won’t catch all possible cases.
    - This is not a real-time algorithm, but we’d like it to be performant. We don’t need a hyperfast exotic solution, but brute force will be unpleasant for users.
    -  “Large collection” is thousands to tens-of-thousands of meshes and millions of vertices.
    - The key thing here is to avoid an N^2 solution which compares every mesh to every other. Any good solution will avoid looping over all the combinations.
    - A reasonable “low tech” solution would be something like binning meshes by vert count – if two meshes don’t have the same vert count they can’t be identical, so you can ignore all the comparisons outside your “bin”
    - A higher-tech solution would be to generate a hash value based on the topology of the mesh; that will spot identical values in linear time.
    - You could combine a vert-count prepass and hashing for a minor speedup – does the candidate consider the cost/benefit of moving past a “reasonable” solution?
    - If they were using the scene graph in a dcc, did they combine it with other backup methods of detecting duplicates?

5. What’s the biggest speedup or performance improvement you’ve ever added to an existing tool? How big was the gain? Why did your fix work well? What lessons did you learn from making this fix?
    - This question is really only relevant to fairly experienced coders.
    - What is their unique contribution to the problem? Was this a purely algorithmic optimization? A reduction in unnecessary work? Was it simply tinkering until things work?
    - Senior candidates will have a solid understanding of how to spot and fix bottlenecks
    - Junior candidates may simply get lucky. With a junior, see if they have related experiences that are leading them to have a more systematic approach.
    - It's also quite likely that the biggest speedup is not actually algorithmic: for example, parallelizing a routine with a lot of disk or network IO to avoid blocking. Are they familiar with how to work around blocking reads and writes?

6. If you have written scripts for multiple DCC environments, which one is your favorite, and why? Which is your least favorite and why?
    - This question is better for candidates who are more on the “scripter” end of programming. It’s intended to get at their sense for the architecture and integrity of different DCCs. However its’ only really informative if the candidate knows at least two different environments well.  Here are some things they may call out:
    1. Max:
        -  MaxScript often intertwines UI state and functionality – this is a Bad Thing and should be something to criticize
        - On the plus side, MaxScript’s DotNet bridge opens up more general purpose programming via C#. Have they experimented with that?
        - Despite general bugginess of implementation, MaxScript's Smalltalk-derived architecture is actually pretty elegant in its own way, do they appreciate that aspect of the language?
    2. Maya
        - Maya used to do a good job of decoupling front end UI and back end APIs. This has been getting less reliable lately.
        - Most experienced Maya users will prefer Python to MEL
        - More experienced Python developers will usually have strong opinions about PyMel; does the candidate have good insights about the tradeoffs around programmer convenience, start up time, and performance that come with Pymel?
        - Have they used the thin wrapper around Maya’s C++ api? This is effectively writing C++ in Python, an awkward but powerful tool – do they have interesting things to say about this hybrid kind of programming?
    3. Blender
        - Blender has a very extensive Python integration, do they have strong feelings about how easy it is to extend Blender without going to C++?
        - Because Blender’s API is a lot more “pythonic” than Maya’s it can be an interesting point of comparison. Does the candidate have insights into the pros and cons of the two approaches? Are there performance or maintenance implications they can see?
    4. Zbrush
        - ZScript is a very unusual language. Does the candidate have strong feelings about the language design and its pros/cons? Are their opinions well supported?
        - Has the candidate tried creating a DLL to extend Zbrush’s limited scripting capabilities? This is awkward to do, but can be a good sign of willingness to push technical boundaries.
    5. Photoshop
        - Photoshop scripting is notoriously inadequate. Does the candidate have strong feelings about it? Can they describe the limitations clearly?
        -  What have they tried to get around the limitations of Photoshop’s native scripting? Have they, for example, tried to make a COM wrapper tool that talks to Photoshop directly?

7. Tell me something about your debugging process. When you get a bug report, how do you isolate the problem and start fixing it? What about when the problem is non-specific, like an operation that works but just seems “slow”?
    - Does the candidate use a debugger, or do they fall back on beginner tricks like print debugging?
    - Does the candidate talk about the use of asserts to track expected conditions? If so, do they consider conditionally compiling the asserts out in runtime code? 
    - Do they talk about structuring code to validate all the invariants early / early-outing when there is missing information? This is not, strictly speaking, debugging but it’s a good indicator for experience and code cleanliness.
    - When dealing with non-specific issues like “slow” code, do they use a profiler (good!) or do they attempt to optimize by instinct (less good)?

8. How do you structure your code for maintenance and growth? What's do you think is the most effective way to keep code healthy for the long haul?  What steps can a team take to improve overall quality?
    - This is intended to get at the candidate's level of optimism about "perfect" solutions (typically, a sign of immaturity) and their willingness to commit to continuous small upgrades to keep a code base healty. 
    - Do they use automated tests to make it easier to refactor safely?
    - Do they commit to good documenation practices?
    - Do they use CI to do regular builds and catch potential problems before they reach users?
    - Do they use static analysis tools or linters to improve consistency?
    - Do they advocate for code reviews?

9. When do you default to using object-oriented programming and when do you use other techniques?
    - Does the candidate use OO for problems where data and methods work together, or do they use it as a modeling technique (the typical "car -> truck" hierarchy)?
    - Do they use static methods for simplicity and statelessness?
    - Do they seem to be familiar with data-oriented design principles (particularly in performance oriented situations)?
    - It's important not to get too hung up on whether you agree with their preferences: the key indicator of general facility is general awareness of the general connection between different techniques and different use cases.  How many tools does this person have in their toolbox, and how appropriate is that to the level of the role?



