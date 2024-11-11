# UI, UX and design

These questions are primarily for TAs who will be focused on creating or improving tools. They help gauge how candidate takes user needs into account, how they handle user feedback, and their commitment to creating workflows that empower artists.

1. What are the most important parts of user experience in tools development? What do you think art tools frequently get wrong?
  - This is mostly an opener to get at the candidate’s values in UX development, good icebreaker.
  - Are they self-aware about the difference between UX (user experience) and UI (user interface)? More senior candidates will generally be able to make the distinction clear, more junior ones often think that the graphic design of the UI is the only thing that matters.
  - What values does the choice of “most important” represent? Is the attitude more user-centric or is it tech-centric? What value do they place on existing culture and workflows?
  - Does the candidate focus on user convenience, on long term maintenance costs, or on technical correctness? Generally more junior answers will focus on tactical efficiency, more senior ones will reflect more thought about the long term costs of ownership and support.

2. Describe your process when designing art tools and workflows. When you're asked to build a tool to do something for some artists, how do you start the process? Once you've got a tool working, how do you get feedback on it?
    - Do they show familiarity with design principles or structured approaches to designing products?
    - Do they start by collecting user requirements or problem statements (better), or do they tend to jump right to a potential solution (usually a more junior answer). 
    - Does the candidate have a structured approach to usability (= senior) or are they merely reacting to problems and making tools on the fly (= more junior)?
    - Do they make a distinction between tools (code and interfaces) and workflows (how work is structured, where it lives, and the constraints the shape the use case)?
    - How do they balance respect for user wishes with the ability to push back against unreasonable or unrealistic requests?
    - Do they plan to start small and grow tools, or do they gravitate towards larger, more complete and more complex solutions? Do they sound like someone who will deliver a test version soon, or someone who will work on a more polished version for months before rolling something out?
    - Do they have a mechanism for comparing the likely impact of a change against the development effort?
    - Does their approach to feedback indicate more of a service mentality ("give the users what they want"), a tech-first point of view ("this is the correct solution")?
    
3. Tell me about a time when you had to support beginners and experienced users with the same tooling. How did you make this work? What kinds of design choices help support a broad range of users at different levels of expertise?
    - Does the candidate design UI and workflow with particular user personas in mind, or do they think in "one size fits all" terms?
    - Does the candidate understand how to progressively disclose information as users gain experience?
    - Does the candidate have good insights about how to provide help through design, or do they fall back on handholding or training?
    - What insights does the candidate share about the different needs of beginner and advanced users? Is the progression simply one of skill building, or are there different needs (automatability, say, or chaining between processes) which need to be attended to differently?
    
4. What’s the best workflow you ever helped to create? Why do you think it’s the most effective?
    - What values are shown in their choice of “best”? Are they proudest of solving a technical problem? Of satisfying a user’s needs? Of making a pretty, slick piece of software?
    - Did they catch "workflow" rather than "tool"?  The strongest answers are about the match between a production problem and a way of working, not about a nice trick.
    - A good workflow does not have to be elaborate: cutting out steps by redefining outcomes can be just as good as desiging a particular tool.
    - When they talk about effectiveness, what are they priding themselves on: saving time? enhancing creativity? solving a hard tech issue?

5. Tell me about a time when your plans for a tool or workflow were not popular with your artists. How did you resolve the disagreement? What do you think you and the artists learned from the clash?
    - This question is great for getting at the candidate's perspective on user behavior. Are they respectful or patronizing? Are they flexible in accommodating desires they disagree with? How good are their listening skills? 
    - Does the candidate distinguish between the popularity of a tool (say, a minor improvement that removes an irritating but trivial hiccup) and its importance (a workflow change that saves a lot of time, increases quality, or prevents problems on a large scale).
    - Did the candidate seem to internalize lessons from this disagreement? Has it made them more likely to get user buy-in in the future? Are they grateful for the feedback now?
    - Did the candidate show good communications skills in getting users to adjust their behavior?
    - From your own perspective, did it seem like the user's resistance was justified? If so, does it seem like the candidate profit from the experience?
    
6. From a user experience perspective, what’s the best art tool you’ve worked with? If you could import some concepts from that tool into a future project, what would they be?
    - Overall this a good chance to see if candidates have a broader perspective that goes beyond just personal preferences. 
        - Are they able to separate out good design from good technical capacities? Houdini, for example, is extremely powerful but a bit of a mess on the design front. Do they focus on information architecture (menu design, consistent design language) or merely on features?
        - How nuanced is their view of their favorite tool? Do they have the distance to see the pros and cons, or are they approaching it as a fan?
    - What outcomes does the candidate prioritize: Technical power (ex: Houdini)? Approachability (Max, Blender)? Ergonomics for pro users (ZBrush)? Extensibility (Maya)?
    - A good follow up is "And what's the biggest mistake in your favorite tool?" Is the thinking in that answer very tactical ("I hate the ____ button") or strategic ("The real problem with ____ is that it's hard to incorporate into a pipeline" or "I love ____ but it will never be a frontline tool until they fix ____")?
    
7. Where do you go to get inspiration for UI/UX designs? What is it you learn there that you think is most valuable?
    - No right answer, of course, but it's instructive to see if the candidate is heavily focused on web UI, on particular DCC paradigms, or on more strucured design approaches.
    - If they cite a particular DCC, is the same as their favorite tool? Or does their discussion indicate a broader interest in UI patterns and advances broadly?
    - If they cite a given design community, what values in that community do they think are the most relevant?
    - What does their choice of inspiration say about how central design is to their view of their work? 

8. When building tools, how do you think about the relationship between the front end that the user sees and the functional code which does the actual job?
    - This is a good quick check for architectural experience, conversely, not great for junior candidates.  Some markers of experience:
        - Do they see the value of keeping the backing code and the UI separate?  
        - Do they talk about separation of concerns?
        - Do they talk about testability of pieces?
        - Do they talk about supporting automation or refactoring? 
    - Followup question. Have you ever had a time where you thought your problem was in the tool code but you were really just dealing with a buggy UI? How did you figure out and resolve that?
        - this can be a good one for showing first hand how they think about front-end/back-end relationships