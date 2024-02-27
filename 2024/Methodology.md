# Primer: Research Methodology 

If you want really want to get my attention as a researcher, arrange for me to hear the same thing three times in three different contexts from three different people. As long as my memory is sharp, I will get very excited by this "confluence" or "serendipity," and start looking for deeper connections. This is how I became interested in "methodology."

I was forced to think what "methodology" meant when I was handed a course called "security evaluation methodologies" as a first year assistant professor. The course outline had a bunch of terms that were not Google-able, the previous instructor's slides were a mismash of security topics, and it took me teaching it several times to work out what exactly should be and should not be in scope for a course with that name. 

Next I applied for a grant that was sent to an external reviewer who wrote: "Overall, the provided 'methodology' sections boil down to 'these people will apply knowledge of blockchain to these problem and solutions will result,' which, at best, is unsatisfactory for a research proposal’s required methodology discussion." While I did get the grant despite this (and several other positive reviews), I had to begrudgingly admit that the reviewer was right. I was not really sure of my methodology. And to the extent that I followed one, I did not do a good job of articulating it. 

Lastly, in a legal context, researchers are occasionally asked to server as an expert witnesses in a court case. A decision (in the United States) established the "Daubert standard" for what constitutes admissible testimony and the methodology used by the expert to determine their testimony is an important consideration.

---

Before I try to outline a useful and academically-backed methodology you can steal for your grant proposals, I want to first address the elephant in the room. Is it actually important to follow a methodology? Or is it just some BS that we all write down, and then once we get the grant, we freestyle our approach to research the way we always have? At this point, I should write that I am going to convince you by the end of the article that methodology is important and worth doing in real life, not just on paper. However I am going to settle for the middle-ground: maybe there is a degree of BS in methodology, but it can be helpful to think about.

My initial reaction to the idea of a research methodology was skepticism. Sure you can ask Radiohead or Metro Boomin' to describe their methodology for writing a great song like Everything in its Right Place or No More. You might learn that Johnny Greenwood wanted to use a Kaoss Pad to mess with Thom Yorke's vocals, or that Metro wanted to sample The 24-Carat Black on a song. These are interesting tidbits but not useful as a methodology. If I followed the same methodology and tried to write a song, it would be too generous to call my output a "song." It seemed to me that great research comes from creativity and insights and breakthroughs; things you cannot write a methodology for.

In the end, I still think I am right about that. But I also think methodology is important. How are these two competing ideas reconciled? I realized I was thinking about methodology wrong. In fact, I was thinking about it backward. The big realignment I had to make was this: methodology does not help you to do something great. You are still on your own there. Instead, methodology is there to stop you from doing something terrible. It is not a way to get from point A to point B, instead it is a set of guardrails that keep you from drifting too far off course.

Quickly recall the grandest method of them all: the "scientific method." I do not do classical science but I know that it is not a promise that you will be the one to make a great scientific breakthrough. Instead, it tries to stop you from committing simple errors that could invalidate your results or make them irreproducible. When you start thinking about methodology as "what not to do," it becomes easier to articulate.

I remember a smart person somewhere, sometime saying something along the lines of this (in my recollection, it was Steven Levitt, co-author of Freakonomics, but I cannot find the direct quote). "Coming up with ideas is easy. I can come up with 100 ideas a day. The skill is going through them and figuring out why you should throw the idea out. My actual skill is throwing ideas out. I am very good at throwing ideas out. And then, after a while, if I can't find a reason to throw it out, maybe I have something great. If I get 3 great ideas a year, I am happy."

---

For me, I want a methodology to help me throw out bad ideas before I sink too much time into exploring them. What constitutes a "bad idea" and a "good methodology" will vary a lot depending on exactly what kind of research you do. Within computer science, you might already have a well-trodden "good methodology" adapted from another scientific discipline if you do, say, usability or measurements or empirical experiments. However in my case, I tend to design new things (algorithms, primitives, protocols, and systems). That seems a little closer to "writing a song."

A further complication is sometimes we write proposals for "research programs" rather than "research project." A project is likely to be fairly well conceived, even before doing it, and an outline of the steps you will follow usually passes as a methodology. But for a research program, you have to write a lower-level methodology for how you go about research in general, not solve a specific problem. So a good methodology should help stop you from doing bad research and should be a philosophy about how to research in general (rather than how to complete a specific project).

As a PhD student, I took a course on requirements engineering (RE) with a professor (Dan Berry, Waterloo) who turned out to be one of my favourite professors ever. I didn't know what RE was, the course just fit my schedule. I walked out of the course thinking (and hopefully this is right) that RE tries to stop you from one specific bad idea in software engineering. The bad idea is that you start coding up your idea right away and figure it out as you code. This bad idea leads to other bad outcomes: you might spend a long time patching your poorly designed software, you might realize you got in too deep to add key features (like privacy), or you might realize that your software does not actually solve the problems that people want solved.

The solution is a methodology. It goes in four stages: elicitation, negotiation, specification, implementation. Elicitation is figuring out what people want from a potential solution, either by thinking hard about it, learning from what people have written about it, or (best yet) actually talking to people who might benefit from (or be harmed by) it. The list of "what people want" are called requirements, hence the name. Turning what people write or say in an open-ended way (say they recount an experience they had) into requirements is not precisely covered by RE. However a field called qualitative analysis can fill the gap. I have had great luck in assigning tags to the themes in the things I read (using software like NVIVO), and then looking theme by theme for an underlying requirement (thematic analysis). Requirements should be descriptive ("a stolen password should not be enough to impersonate a user at log-in") instead of proscriptive ("use two-factor authentication").

Once you have gathered requirements, you might have conflicting requirements where different people want different things. Sometimes I model this formally with a table of who wants what (see stakeholder conflict table in [X]). Deciding on which requirements to prioritize, assuming you cannot do them all, is the output of the negotiation step. Next you design "on paper" your solution at some level detail. Next you iterate through the first three steps---elicitation, negotiation, specification---adding more detail to your specification, ensuring you are capturing all the requirements, trying to solve trade-offs between conflicting requirements. You continue 

 to turn the themes into requirements. 



, and a basic technique is to identify themes in what they right and say (thematic analysis) 

 Requirements should be descriptive ()



It will not lead to good idea necessarily, just try to stop you from 



I have never seen someone use technique A to solve problem B so I'll do it and figure out 



 propose something that The good idea of RE is that you front-load as much of the design as possible before you write a line of code.







, y But what if you design new algorithms or new cryptographic protocols or propose new secure systems?

Formal methods, proofs, 







 is obviously not very useful . I could follow the same 



: (1) you probably already use a methodology, you just don't have a name for it. 





 your methodology in real life? 

---

