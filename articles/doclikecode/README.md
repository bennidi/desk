After reading [this discussion](http://discuss.fogcreek.com/joelonsoftware/default.asp?cmd=show&ixPost=35336) on documentation, I decided to organize my own thoughts on code comments and other types of documentation. I do feel it is a topic that does receive far too few attention. Working with legacy code without having a decent amount of good documentation is really painful and lack of documentation generally slows down the development progress. Still, documentation is not treated as a first class citizen of a software system.

 

# What is documentation?

Documentation appears to have many aspects and may take many forms – and it changes…and it moves. But I think in its essense, it is always intended to provide valuable information on something that requires additional explanation because it is not entirely self explanatory. In other words, every thing that is not completely obvious in its function and way of operation but intended to be used by a certain audience requires some form of documentation. The form of documentation varies wildly and may be anything from a piece of paper with poorly written instructions to a full-fledged owners manual with fancy video tutorials dubbed by Chuck Norris. In this article I'll refer to them as "documentation artefact".

So, the purpose of documentation is to describe (a) what a thing can do and (b) how it is intended to be used and that this description has a target audience. In the realm of software development, however, there are more things that require documentation and not all the documentation deals with concrete code artefacts and their functions and the specific needs of the target audience will also vary. As a developer, for example, I am also interested in how and why a thing works which is generally of no importance for an end-user. So, my software engineers perspective defines:

> Documentation consists of one or more documentation artefacts, an artefact being every thing/asset that offers explanation about a given piece of software as to

- **why** it exists
- **what** it's capabilities are
- **how** it is intended to be used
- its design principles together with it's motivation and implications (context)
- how it actually is used as part of a bigger system (context)

A documentation artefact materializes in a certain form like

- API documentation generated from source (javadoc or the like)
- UML diagrams or more informal drawings of system structures or interactions
- Wiki pages, Google Docs or other formats of web based software for information management

The guy who happens to know all about the system because he's been around from the beginning is a common but unfortunate form of documentation because documentation.

# Documentation is like code

An interesting observation is that, like code, documentation tends to have different levels of granularity. This is implicitly so because documentation deals with code and its higher level concepts, so it naturally tends to reflect this structure. In a well documented system you will find artefacts that describe

- system metaphors
- architecture patterns (pipes & filters, transaction strategies)
- system/subsystem communication schemes
- API and service contracts
- component design
- source code comments for classes, functions, algorithms etc.

So there is documentation for the different levels and parts of the entire software system and they take different perspectives on the system – as would any developer when he talks about the request processing pattern in general compared to talking about the implementation of a specific request filter.

Besides this obvious analogy in structure, it seems that documentation shares even more characteristics with code artefacts: Scope or say, responsibility which refers to what a particular documentation artefact is supposed to explain and which is very similar to what is considered the **responsibility** of a function/module/class. Different documentation artefacts will have different **stabilities**, meaning that they are more or less likely to change. Both, documentation and code can suffer from **fragmentation/scattering** and **duplication** and its severely harmful to both of them. A major problem with documentation is that it can become stale, thus diverging from the code/system it actually deals with. Something very similar exists in code: One code artefact is updated and receives new capabilities but clients do not update.
 

# Benefits of documentation

As stated before, I think that documentation is a neglected and underestimated part of any software system because it does add significant value….over time. One of the main benefits I see is that **good documentation is like an investment** - it will safe you a lot of time in the future. It . As per the day of writing it, it is just a cost factor as it consumes resources. But when the first new developer can start doing work on his own immediately because he can read the documentation to understand the important parts of the system, the first portion of time and money has been saved.

As a developer I have learned that **code is written once but read many, many times**. Each time that I have to read code that is not documented I have to mentally reengineer its meaning and design ideas. Each time the project looses time and money. Also, each **mental reengineering bears the potential for misunderstandings and programming errors**. This is why I usually add comments while I read because the boy scout rule says so and I believe in it.

One cost factor of software systems is driven by code degradation which slowly introduces more and more technical debt. Providing guidelines and metaphors will **increase the likelyhood that new code fits well into the system**, hence it counteracts degradation. It will also ensure that existing infrastructure like basic libraries and frameworks will be used as intended an as such the potential for bugs is reduced.

Having clear documentation will also help a lot when you have to make changes to the codebase on a larger scale. If you understand how the code/system is designed and why that so, then you can safely reason about changes and their implications. In contrast, if you don't know how it works, you are unlikely to change it – and more likely to introduce bugs.

 

# Why is documentation neglected so much?

There are definitely many reasons why documentation is neglected. Here is my proposition:

1. Documentation appears to be generally not considered part of the solution to a specific programming problem. Assuming that (most) developers are interested in designing solutions for complex problems this means that as soon as the program is written and the solution is found, they want to move on to the next problem
2. He who should or could write documentation is usually not part of the group interested in reading it. In other words, the people who benefit from documentation are not the ones who write it and the ones who have to write it don't get any benefit out of it
3. Good documentation is actually very hard to write and by our intuition (and probably experience) we know that poor documentation is more harmful than benficial
4. Often when code is written it appears obvious what the code does, hence, it doesn't need documentation. Many developers also seem to think that it is possible to write entirely self-documenting code, which, so says I, is simply not possible –> documentation is not only about code but ideas and all the abstract things around it
5. Time pressure to meet deadlines will make documentation appear superfluous, not adding any business value
6. The management (and thus the process) is often focussed on short term benefits, whereas documentation shows its value only on mid or long term scale
7. Documentation becomes stale easily and is not of much value when stale. This generally hints to the lack of good tooling to support durable, high-quality documentation
 
I also think that there are two primary actors involved in documentation:

[Developer]--(3,4)------------(2)-----(1)------------------(5,6)--[Management/Process]

# How to improve?

First and most importantly I would say that developers should **take more pride in writing good documentation**. They need to understand that this in itself is a complex task and that good documentation is a vital part of any elegant solution to a programming problem.

IT managers should understand that they gain nothing if they make their developers constantly neglect documentation in favor of just the next important feature. It's only a matter of time until the slowdown of undocumented system sets in and when it shows it's already quite late to remedy. In that particular aspect **lack of documentation is a like technical debt** [at least much of its fundamental notion applies].

I would suggest to make writing documentation a part of the daily development routine. Documentation should be included into workload estimates and undergo reviews like any other code artefact. Personally, when I have to write more complex algorithms or object collaboration schemes I start off by creating the empty methods and fill them with comments that represent the "high level" algorithm. Then I incrementally translate them into code. I think this is an established programming technique (called constant refinement) and one of my professors tried to convince us of its value [ and rightfully so ].

Selecting the right tools to store and maintain available documentation is also vital for its acceptance. Outdated documentation is not of much help – not to say, it can be very harmful – and will not be taken seriously by the majority of people. So if a great deal of your documentation tends to be even slightly old, chances are high that many people will dismiss reading it at all.

 

# Quotes about documentation


> "I've repeatedly seen the claim that code can be written in such a way as to make comments unnecessary.  But in almost 30 years, I've never seen such code."
          —- Hardware Guy —-

> "Methods names don't provide context and they don't tell you why. These are the most important things when looking at code."
          —- Unknown —-

> "You don't comment primarily on what is in the code. You comment on what isn't in the code (the why, the business case it's trying to address, the reason for doing it this way and not another, any pitfalls to watch out for when refactoring, and so on)"
          —- Stephen Jones —-

> "You could use meaningful names instead but do you really want controls called
PrintReportToShowHowManyApplicantsFromCountrySpecifiedinCountryOfCurrentResidenceDialogBox
ArrangedInReverseChronoligicalOrderofApplicationDateButton"
          —- Stephen Jones —-

> "[...]inherting a system this well documented, especially if the domain knowledge is complex, is awesome!  It makes life sooo much easier."
          —- Canuck —-

> "Documentation is the single most important change I've made to my coding style in the last year"
          —- Zach Holman —- 

          
# Resources

[1](https://plus.google.com/106052512842507340767/posts/heH3pT2pczt) Fifteen Thoughts and Tips on Writing Software Documentation

[2](http://www.bluemangolearning.com/software-documentation/) 5 Keys to Creating Docs that Rock

[3](http://stackoverflow.com/questions/209015/what-is-self-documenting-code-and-can-it-replace-well-documented-code) Discussion on stackoverflow.com about self-documenting code

[4](http://tom.preston-werner.com/2010/08/23/readme-driven-development.html) Readme Driven Development
