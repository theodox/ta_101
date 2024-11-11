# Shaders and materials

These questions are intended to assess the candidate’s familiarity with shaders and the architecture of modern graphics. In addition to familiarity with common techniques, it’s important to know if the candidate understands the performance and memory implications of their choices. The ability to make smart tradeoffs, rather than simply enabling all the high end features, is a key skill set in this area.

Note that PBR related questions ought to work well with candidates who are farther towards the “artist” end of the techart spectrum.

1. How would you describe physically based materials to a non-technical artist? What makes PBR different from an older shading model, like Blinn or Phong? What do PBR materials not do?
    - This question is not about the hardcore math needed to implement a BRDF – it’s intended to see if the candidate can communicate a less-technical introduction to PBR. Does the explanation seem to show skill at packaging up complex ideas into simple, concrete, artist-friendly concepts?
    -  From an artistic perspective, a PBR material is great at photorealism but can be used for other things as well. Does the candidate conflate “PBR” with “only realistic”? 
    - Technical details:
        -  A minimal answer is that a PBR shader ensures that the diffuse and specular values are properly related so that you can’t get both a high diffuse response and high specular at the same point in space. 
        -  An intermediate response recognizes the role of metallicity: a metallic reflection colors the specular highlights, a non-metallic one does not. An intermediate value like 50% metallic implies that at some more detailed resolution the surface includes both metallic and non-metallic responses.
        - Intermediate responses also reflect the gamut of real world reflectance, which is smaller than the mathematical 0-1 range. 
            -  Very dark materials (coal, tar) have an albedo of about linear 0.04. Very bright materials (snow) have an albedo of around linear 0.8
        -  Advanced responses may mention energy conservation (ie, the tradeoff between diffuse and specular response reflects the re-distribution of the incoming light energy – an old-fashioned Blinn-Phong shader with both albedo and specular responses turned up high would actually “reflect” more light that it was hit by.
        -  Does the candidate seem to understand the way roughness or “microfacets” control the tradeoff between diffuse and specular reflections?
        -  Does the candidate know that there are multiple, competing implementations of “Physically based” shaders?
        -  Does the candidate know where the microfacet model breaks down (extreme glancing angles, long view distances)?  

2. When would you recommend using a specular workflow for PBR materials? When would you recommend using metallic?
    - There’s no technically right or wrong answer here, since both workflows can produce similar results. The key thing to look for is the candidate’s understanding of the relationship between workflows, artists preferences, and ease of maintenance. More senior candidates will tend to understand the tradeoffs; more junior candidates will tend to frame this in terms of “better” and “worse” when those are really situational.
    -  Some things a candidate may observe:
        -  A metallic workflow can be easier on some artists, since it’s explicit about where something is supposed to display metallic (colored specular) behavior, and the “metalness” color lives alongside non-metallic albedo.
        -  In custom shaders a metallic workflow can allow you to save some texture memory since you only need a single metallic channel, rather than a 3-channel RGB input.
    - It’s generally easier to police the values in a metalness workflow, since you know if a given pixel is or is not supposed to behave like a metal.  
    - In theory, partial metalness values are not valid, but in practice they are almost inevitable in transition areas and lower mips. 
    -  A specular workflow can be easier for artists who want precise control more than physical correctness.
    -  A specular workflow can make it easier to approximate effects that don’t fit the classic hard-surface shading model.

3. When working with a PBR material pipeline, what can or should be done to make sure that the artists are creating properly set up PBR materials?
    - This touches on production experience and tooling, not just shader knowledge. Does the candidate have insights into user behavior? Into the tradeoffs involved in aggressively enforcing technical correctness? 
    - Technical details to watch for as evidence of sophistication:
        -  Appropriate range of albedo values (typically, linear 0.04 to 0.8, though some shaders normalize this)
    - Clear understanding of the difference between specular and metallic workflows. It’s easy to know if a metallic-workflow texel is supposed to be metallic or not, but what about specular workflows?
    - What about color linearization? Does tooling know/care about the color space of input textures?
    - What does the answer tell you about the candidate’s approach to user education? 
    - Do they expect to solve correctness problems exclusively through training? How well will that scale for a distributed production (or for our million plus user base)?
    - Do they expect to reject bad content using tools (ie, rejecting source control submissions with out of range values)? Who will be responsible for helping the users understand, fix, and hopefully not repeat their mistakes?
    - What’s the implied cost/benefit tradeoff between technical correctness, artistic freedom, and the need for a human being to make judgements? How flexible is the candidate’s approach? 

  4. How do you go about diagnosing the performance of a scene in your projects? What tools do you use to spot performance problems? What do you look for as likely causes of poor performance?
    -  This is a good diagnostic for technical experience with graphics performance. More junior candidates will probably focus on rules-of-thumb, such as “don’t use compex shaders” or “reduce polycounts.”  More experienced candidates will use diagnostic views (such as shader complexity or overdraw displays). Senior people will tend to reach for tools such as PIX, GPA, or RenderDoc.
    - If, as interviewer, you’re unfamiliar with their preferred tool: see how well they can explain it to you. Are they good at giving you a sense of how the tool works as well as what information it contains?
    - This question will tend to naturally segue into talking about optimization, so check the notes on the following optimization related questions too.
    - For answers relying on rules of thumb, how accurately does the candidate describe those rules?
    - Do they know the difference between the physical vertex count of a mesh and its runtime representation, where differences in normals, UVs, or vertex colors can effectively “duplicate” vertices?
    - How do they weigh the relative impact of draw call count, shader complexity, texture sizes, and polycount? In practice any of these could be a bottleneck, do they recognize that or are they convinced that only one of these things matters.
    - Do they have a mature approach to the difference between “good rule of thumb” and “scientific truth?”
    - For answers based on diagnostic views, how well do they understand the relative costs of shader complexity or ALU vs overdraw? 
        - On most modern hardware, overdraw is more impactful than shader complexity.
        - Do they recognize the fact that most ALU costs scale with screen size? 
        - Do they talk at all about the costs of blowing through the texture cache?
        - Do they know that texture sampling is generally much more expensive than pure math on modern hardware?
        - Do they know that some texture formats (3d formats, floating point, trilinear or aniso sampling) are more expensive than others?
    - For more advanced candidates who use graphical frame debuggers, look for examples of good forensic skills:
        - How do they go about finding the relevant draws when tracking down a problem?
        -  If they use PIX, are they familiar with the “Dr Pix” reporting tools for spotting things like texture bandwidth saturation?
        -  Are they familiar with using resource views to, for example, see how big the textures used in a given draw call are?
        -  Do they have enough experience in more than one debugger to recognize the strengths and weaknesses of each? Would they pick one to solve a particular problem? 
  - Overall – a pragmatic approach to profiling and debugging (based on data) is always better than a rote approach based on rules. Different projects have different needs, and the ability to accommodate those flexibly is a sign of technical maturity.
  
5. How would you go about optimizing a scene that was suffering from too much overdraw?
    - One obvious aspect of this is testing the candidate’s understanding of “overdraw.”
    - Do they know about the cost difference between alpha blended and alpha tested geometry?
    - Do they understand how hi-z or other forms of depth testing can improve performance on modern hardware?
    - Do they understand how depth sorting can influence performance in conjunction with depth testing? For example, drawing back to front works better for the visuals of alpha blending, but drawing front to back with 
    - All of these are potentially useful:
        - Switching from alpha blend to alpha test transparency
        - Aggressively trimming out transparent areas
        - Switching to opaque shaders in lower LODs
        - Tweaking depth sorting to increase up-front rejections
    - Do they think past just optimizing individual assets? For example, would they consider things like 
        - occlusion geometry to increase pixel rejection
        - Ray-marched volumetric effects to simulate large amounts of geometry at once
        - Tooling to automatically trim alpha-tested cards close to their alpha thresholds
    - All possible ideas have pros and cons – that’s why this space is so unsolved. However real or proposed alternatives the “standard” approach are a good place to assess both the depth of the candidate’s knowledge and their technical creativity – a good place to look for creativity and technical curiosity
    - Does the candidate approach this as “here’s what I would do as a solo technologist” or “here’s how I would train my artists”? How does the answer relate to how they'd function as a team member?

6. Tell me about a time when you helped a very junior artist – maybe somebody who had great visual skills but who made content that wasn’t very performant – become good at making shippable assets.
    - Ideally this can be a story question which will give you some insight into the candidate’s collaboration skills and their aptitude for team building as well as highlighting their knowledge of the graphics pipeline.
    - What tools does the candidate rely on for teaching?
  |     - Do they provide close 1:1 mentoring? (#InItTogether, #BuildingRelationships)
        - Do they create training materials?
        - Do they jump straight to talk about profiling and debugging?
        -  If the story includes roadblocks, how did the candidate work around those? Were they good at helping their artist colleague past the intimidating parts?
  -  Did their explanations indicate the ability to clearly and effectively summarize complex information?
  
7. When you want to assess the visual quality of a frame rendered in a video game, where and what do you look for in the image?
    - This is really about the technical delivery of the image (“graphics”) and not art content or style.
    - This is a pretty open-ended question, but a good proxy for both depth of graphics knowledge and for experience of production problems. To some degree “more is better” here.
    - Likely callouts can include (but aren’t limited to):
        - Aliasing
        - Filtering
        - Framerate and/or sync tears
        - Banding
        - View distance
        - Over-fogging or lack of atmospheric scattering 
        - Shadowing artifacts (bias, noise, resolution, cascade boundaries)
        - Balance of direct and indirect illumination
        - Valid PBR behavior (eg, no colored specular on non-metals)
        - Ghosting (from too much motion blur or poorly tuned TAA)
        - Tonemapping or lack thereof
        - HDR vs LDR behavior 
        - Bloom tuning
        - LOD pops
        - Framerate consistency during effects or animation.
        - The candidate will probably have a few of their own as well. Are these considerations realistic or are they a bit out of date (say, heavy focus on polycounts or particle counts)?

8. What do you think is the most interesting near future tech in graphics right now?
    - This is mostly an icebreaker question, since there is clearly no “correct” answer. The real intent is to identify candidates who are interested in the nuts-and-bolts of graphics tech (say, a new filtering algorithm or a better way to handle anisotropic hair rendering or something like virtual texturing that changes some of the traditional performance tradeoffs). 
   - Depending on the position the answer could be quite relevant or it could merely be a proxy for general level of graphics background. It might be a good idea to check with your developer colleagues to know if there’s a particular tech they think is highly relevant to the job being sourced.
   - The two most common filler answers are "AI" and "Raytracing".  These are both interesting technologies, but without some specifics from the candidate they aren't very useful. 
        - What _specifically_ do they think will be good about a new tech? What will it do differently in production? 
        - Is it going to be ubiquitous in things like mobile or web games? 
        - Where do you expect it to make things better and where do you think it won't have an impact?

9. Tell me roughly how you’d design a shader to do a procedural brick material that didn’t use any textures. 
    - Keep in mind (and tell the candidate) that this is **not** a whiteboard coding question – this is more like the conversation you’d want to have in the kickoff meeting for a new project. 
        - The goal is to understand the basic design parameters and the expected implementation – not to produce a working shader during the interview. There’s no “right” answer they need to get – the point of the exercise is to hear them think about the problem out loud.
        - Since we aren’t whiteboard coding this also is a way to see how well they explain abstract concepts verbally. A helpful prompt is “I’m the producer and I need you to explain to me what work will have to get done so I can schedule it.”
    - On the purely technical level, this question is going to need the candidate to show they get basic shader math. But it’s also about what questions they might ask: jumping right into math without asking any questions isn't good. 
        - Do they think about satisfying well known needs? Or will they jump in and try to satisfy all possible needs?
        - If they do ask, stipulate that this is a realistic material, intended to be used in a procedurally generated urban environment. Will need a number of different colored bricks with varying colors, brick aspect ratios, amounts of mortar, and regularity. Does not have to handle custom brick bonds or anything besides rectangular bricks – no herringbones, hex-tiles, etc. The no-texture requirement really means “no user supplied textures” – lookup textures or shared resources, like a detail bump map, are allowed.
        - Do they ask about performance budgets?
        - A no-texture solution is good for memory-constrained platforms – but not so good for one that is struggling on ALU or, possibly, on mobile battery life. 
        - In this case we’ll be running on midrange PC hardware, so this does not have to be a highly optimized solution but reasonable caution around shader complexity is required.
    - Do they ask about target hardware and platforms?
        - 4k resolution is extremely important for something like this – a brick pixel shader that looks good covering 10% of the screen could become very slow when filling a 4k screen unless it’s properly implemented.
        - In this case we are expecting to run on 4k (as above, using mid-level PC hardware) – this means that performance at full screen will become an issue for many techniques, especially those that try to naively use noise for variety. 
    - Do they ask about other constraints, eg, camera moves or animation?
        - The shader doesn’t need to animate but does have to handle the case of a camera getting quite close. The camera will be flying through a procedural environment and can’t stutter when the entire screen is filled with a fairly close up shot of the bricks
            - Since the camera will be flying through the scene, noise sizzle might be an issue.
    - Here are some follow up questions to help the candidate focus if they get stuck.
        - How are you going to tell the shader when you are in brick or in mortar? 
            - This is probably a variation of “multiply the UVs by some number of repeats in X and Y; use floor or ceil to know which “brick” you are in and use frac to get the uv space of a single ‘brick’
            - The parts of the brick which might want to be beveled can be identified using the same trick
            - Adding a secondary distortion to the UV value driving allow the above will allow for a bit of natural variation to make things less mechanical and perfect
        - How are you going to let the user offset the bricks to accommodate different styles of brick setting? 
    -       Likely, use floor or ceil on one of the coordinates to get the row; the modulo (row, 2) will be 0 for odd rows and 1 for evens; you could add some fraction of that modulo to offset the row. 
            - They’ll need controls to manage the height and aspect ratio of the bricks and the degree of offset on alternating rows.
    - An advanced version might allow you to isolate every Nth course for, say, a Victorian style ‘striped’ brick.
    - How would you differentiate between the bricks and the mortar?
        - If they can get the row-column part figured out, having 0-1 uvs inside each brick lets you use simple math to see if you’re close to the edge
        - Thresholding that value will give you “brick” or “mortar.” at the simplest level, output a different color, roughness and normal value based on that.
        - A nice touch would be to increase ambient occlusion inside the mortared regions.
        - Another nice touch is to slightly tweak the normal of the entire brick section to avoid overly uniform lighting.
    - How would you add randomness to the color of the bricks ? 
        - There are lots of valid answers here; noise being the easy one. However, can they describe how they would make each entire brick get a consistent random color? Typically that would involve generating a hash value from the row and column of the brick (see above) then using that hash to pick a color somehow. 
    - Do they note that using the row-column hash alone will mean that the Nth brick of every texture repeat will be the same? How would they try to make sure that brick [0,0] is not the same color on two different buildings? 
        - Or.. do they just add some per-instance UV offset to hide the repeat?
    - What issues are likely to be a problem with this shader in production?
        - All the easy answers to this question will include aliasing for thin lines – this is just a problem with this kind of math-based approach. 
        - Don’t bother to make the candidate explain a fix, but knowing that it’s a likely problem is a good differentiator between intermediate and advanced. 
        -  The likeliest “fix” you’ll get from somebody who has not had to do this before is a super-sampling approach (where you check the neighorhood of the pixel a few times to avoid aliasing). The is quite reasonable but has interesting perf implications (see below)
    - Another differentiator will be the way the cost scales on 4k hardware – since the solution is per-pixel, elaborate procedures will probably be expensive close up. Again, a full solution is out of scope, but _noting_ this is another marker of experience.
   - Skilled candidates might note that many noise types will “sizzle” during flythroughs. A mix of different frequencies and fixed resolutions is a good way to minimize this.
