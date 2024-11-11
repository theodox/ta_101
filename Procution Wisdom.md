# Production Wisdom
These questions help to gauge the candidate’s approach to content production: are they used to a highly structured process with a rigid pipeline, or are they coming from a more improvisational space where anything goes? Are they used to spotting and eliminating potential bottlenecks? Are they inclined to fix problems using technology or using rules and processes? 

In this space it’s important to account for differences in the scale and social setting of different projects: the best practices for a 300-person movie production team will be quite different from the rules that fit a 20 person indie game studio. This list doesn’t include informational questions like “how big was your team” or “what’s the biggest production you worked on” – if you can’t glean those from the candidate’s resume you’ll probably want to get a sense of them early to contextualize other answers.

Most of these questions don’t work very well for people who have never worked in production professionally.


1. Out of all the productions you’ve participated in, which went the most smoothly? What made it easy to get out the door? What aspects of that production do you try to remember when approaching other problems today?
    - The important thing is how well the production process went – not the ultimate success of the project. An 18-month crunch that scored an 85 metacritic but ended several marriages on the team is not “most smoothly” for purposes of this question – while a small team hitting deadlines and making good content for a game that disappeared into the lower tiers of Steam can still teach us a lot here.
    - We’re fishing for the candidate’s idea of what a smoothly functioning project looks like: is it a good team that collaborates well? Is it a solid pipeline that stays out of the way of creatives? Is it well managed technical risk, or tech bets that paid off well? What does the answer say about the candidate’s ideal environment?
    - This is not a great questions for juniors or students who won’t have a lot of grounds for comparison
    - How much did the candidate contribute to making things go smoothly? Do they take all the credit, or give props to colleagues?
    - Does the production actually sound like it went well to you? Is the candidate perhaps missing some best practices or relevant tech knowledge that could have made things go better?

2. What part of a typical production cycle do you find the most enjoyable? Why? What part of production do you like the least?
    - Does the candidate enjoy dreaming up new tools, processes, or visual targets, or do they prefer getting things set up cleanly and running a tight ship? 
        - A tech-focused person may focus on innovation or on bulletproofing.
        - A process-oriented person might focus on setting standards and best practices during pre-production
        - A craft-oriented person might prefer the last stages when things are mostly working and its possible to focus on perfecting the look
        - A service oriented person may enjoy the camaraderie and in-the-trenches spirit of the production crunch.
    - Do they enjoy the collaborative parts of the process (brainstorming in pre-production, helping artists during the ship cycle)?
    - Overall, what does the answer tell you about what motivates the candidate? 

3. Tell me about a production that really went off the rails, and how you think it could have been saved. What did you do to try to make it better? With the benefit of hindsight, what could you have done differently that would have gotten to a better outcome?
    - A good follow up to the question about the production cycle, with a similar range of options – does the candidate see success and failure from a technological perspective? Is the problem in team relationships? Should there have been a more rigid set of processes? Is it bias or unfair treatment?
    - The “how could you save this” is a chance to see if the candidate has something insightful to say beyond “we should do it better”. Do they see systemic issues that could be addressed in the tech, the culture, or in the way the tech and the culture interact (the latter is a good marker for insight).
    - While getting the details, look to see if the candidate engaged proactively to help fix things (good!) or if they sat on the sidelines and complained (bad).
    - Did they give the their colleagues any version of the feedback they're giving you now? How did it go? 
    - Did they approach the problem by trying to address team culture, production processes, technology, or personal issues? Is this part of a pattern in their other answers?  

4. In your experience, what kind of pipelines generate the most friction? What makes this particular kind of content so problematic? If you had the time and resources, what would you do to fix them?
    - This question benefits from targeting the candidate’s specialty field – ie, an environment person will talk about collaborating on a world art pipeline, not character workflows. There’s no “right answer,” this is intended to capture how well the candidate thinks about data flows in production. Some key things that indicate production savvy:
        - Does the candidate differentiate between different operations that are easy to iterate on (ex: tweaking a bitmap) and those where changes are costly (ex: changing a character’s skeletal topology)?
        - Does the candidate have smart things to say about when and where to prototype, graybox, or validate content? How would they mitigate the risks of bad upstream decisions? 
        - Are they overly optimistic about the ability to predict problems?
        - Are they good at spotting areas where work can be parallelized?
        - Does the candidate have a realistic understanding of the creative process? Does their point of view leave room for changes of direction and exploration?
        - Do they recognize the problems that come from resource contention, for example from having a dozen artists constantly waiting on one un-mergeable binary master file?
    - How do they accommodate the needs of diverse contributors to a project? 
    - If they have a proposed technical fix, is it feasible? Is it innovative?
    - If they have a proposed organizational or process fix, is it pragmatic and achievable?
    - Do their suggestions for improvement lean more towards user empathy or technical correctness?

5. PIPELINE DESIGN SCENARIO
    - This is an interactive exercise in which we ask the candidate to provide a high level design for a content pipeline. The interview team should work together to sketch out a scenario which is relevant to position in question -- it's important to do this together so everybody is providing answers which are consistent during the interactive part of the exercise; thorough prep is key to making it fair for the candidates.  
    - During the interview provide a basic sketch of requirements and resources and walk through the process interactively over about 20 minutes of real time.  The basic structure is in 5 parts
        - Initial spec with a problem and a target deliverable
        - Iterate with the candidate as they ask questions and flesh out an initial approach. Try to have answers for all the obvious followup questions pre-planned and ready to go. 
        - Add in a late change to the initial spec -– ideally, one that puts stress on their initial design or complicates the most obvious implementation path.
        - Iteratively adapt to the change, again with possible Q&A as they gather requirements for the shifting plan
        - If the exercise leads to a more or less complete design, finish with a short retro (2-3 minutes) on how they would approach another job for the same “client”
    - If the exercise stalls for some reason, don't drag it out. When the candidate feels as if they have "failed" the question it's likely to negatively impact the rest of the interview. Instead follow up by asking questions about how one generally approaches a scenario like this as a way of getting at the same information more directly:
        - "How do you indentify the stakeholders when setting up a pipeline?"
        - "How do you go about estimating the complexity of a project you've been asked to work on?"
        - "What do you do when requirements change unexpectedly?"
    - The point of the exercise is really to see how well the candidate copes with a limited information situation.  An innovative technical solution is a bonus, but the real value of the exercises is seeing how the candidate thinks through a problem with limited information. It's fine to tell the candidate up front that this is the point of the exercise, that actually helps to lower the emotional stakes and elicit better answers.
    - Ideally you should have the relevant situational information available ahead of time, but only provide it if the candidate asks for it.
    - It can be helpful to have one interviewer "role play" the customer while another interviewer acts as the "dungeon master" providing context and hints. Agree ahead of time who has to provide answers to completely unexpected questiosn so there's less chance of interviewers contradicting each other by accident.
    - When evaluating the answers the most important thing to look for is the candidate's questions:
        - Are they thorough enough to get to a good solution?
        - Do they reflect deep technical knowledge? If not, do they show good instincts for sketching out a solution?
    - This is a design exercise, not a coding test, so implementation details only need to be sketched out.
    - If the candidate proposes an approach that seems unworkable, push back gently. Do they respond well to being challenged on details? Do they adapt gracefully to making a misstep in front of others?




