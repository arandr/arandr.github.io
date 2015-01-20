---
title: How I Learned To Stop Worrying And Debug Other People's Code
layout: default
---
I recently came across [this Reddit posting](http://www.reddit.com/r/cscareerquestions/comments/2ryckp/been_smart_my_whole_life_but_now_i_feel_like_im_a/) by a poor soul who just fell headfirst into enterprise code. 

>was always "smart" as a kid, excellent CS student, but get to enterprise development and I'm struggling so much. 
>Can't debug code I haven't worked with, can't use right design patterns, thinking I'm not cut out for being an SWE. How do you learn to debug large projects you've haven't worked with before?

Now I don't do legacy Java, so I might not have had it so bad, but here's all I learned feeling like that over and over again.

##Debug projects you haven't worked with before##

Assuming you can build and run the project, and you have a task (however small) to do in it. Some enterprise environments can in fact let you languish a long time without one or the other.

* Does the project have tests?

If it does, do they pass? If they do, reading test code tends to be more approachable than application code, likely because programmers don't write tests in a hurry at 8PM. If there are no tests and/or the tests are failing, making a test that runs can be a good first task if you're not quite up to debugging. For more information on writing tests on legacy code, see [Feathers's book](http://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052).

* Read the source

Reading code (even test code) can feel overwhelming if you don't have what you feel is the basic knowledge to grasp it, which is why most programmers hate reading other people's code. In that case, you should read the code anyway but not worry about understanding everything. Test is making a mock FactoryManagerSingleton and passing it to the AbstractAdapter? No problem, just read the function names. You'll go back to the manager singleton adapter stuff later, when you figure out that the test is asserting that two and two make four. Design patterns are called patterns for a reason -- they are ways of thinking that solve similar problems across differently named but structurally similar code bits. You need to expose your brain to several of these bits in different contexts, and it will do the rest itself. This also works for learning languages and mathematics.
Note, reading the source will not make you a legacy software guru overnight (or at all). It's merely a prerequisite for writing in it.

* Talk to other people

This covers both asking questions to more experienced developers and explaining your understanding to other newbies (or writing documentation). The original poster is, and this is a very common attitude, worried that asking questions is affecting how he (or she)is viewed by the team: "and who's going to think you're doing a good job when you constantly need help?"

The answer to that is everybody. Asking questions does affect how you are perceived... generally positively. There are a number of reasons to that, and variations in culture may mean some apply less, but at least one will be relevant:

1. When you ask questions, you are engaging with the rest of the team. In most work environments, it is expected that you will develop some sort of social position and relationship in the context of your group. Doing that while talking (and mostly listening) about work is much better boundary-wise than having beer with your coworkers, it shows interest on your part, and it tends to build positive feelings because people love being listened to. Not talking to your coworkers, only talking about non-work subjects, or imposing your opinions when you're new are all alternatives that are vastly more negative than questions as far as concern about your social perception goes.

2. It is mutually beneficial (in the most self-interested, purely rational way). By the time those developers who are mentoring you now got to grok the code, they are mostly being judged (by their peers and managers) on non-code-relevant things, such as do they mentor the new arrivals. Asking for help gives them a chance to shine.

3. It is faster. This seems obvious, but assuming the information that will enable you to solve the problem already exists and the problem is nontrivial (this is most business and software problems right there), getting at the information is quicker and usually more valuable than pounding out software by trial and error.

4. At the level you are likely to be as an entry-level programmer, a (let's say) 10% advantage in coding skill or speed, or in understanding of code is near worthless (it will be weeks before you make a commit in the best case, you don't have the option to contribute to the highest impact parts) but a 10% increase in communication ability and frequency can cross-pollinate the knowledge of the weird bits of code within your team.

5. Asking the right questions can identify problems before anyone would have seen them, and/or get you involved in architecture discussions when you wouldn't be otherwise.

* Use tools

Static analyzers, if you have access to them, can usually identify dead code, long functions, heavy branching and other places where live debugging could get tricky. By marking out those areas of the code base that are not called (therefore useless) or kitchen sink functions, you can get an idea of which code is more likely to matter.
Live debugging itself is also useful. In applications you'll ideally want to find the event handlers (or framework entry points if applicable) so that you can map out code paths. If your tooling or language includes a way for you to know when objects are modified (such as data breakpoints or Object.observe), try to add those on singletons and longer-lived objects. 

* Take notes

One of the problems with finding out what code does is organizations and teams often have their own jargon for how to refer to their business domain (or to part of their technology stack). Functions that add data to a collection, an object, a file or a database could be called add or append or push or edit or update or insert, or any of those terms except when they're called dragonize because in your business domain an object with a foo in it is called a dragon.
A way around that is take notes of the specific terms people (including nontechnical people) use when talking about features or actions that the code should do. The more unique the term, the more likely it is jargon and will yield decently specific results on a search through the codebase.

* Think it through

A lot of the design patterns may sound arcane when you read the names but come up quite naturally when asking yourself what you would do when faced with that specific problem. 
For example, the adapter tend to be the response to using e.g. two high-level frameworks or libraries depending on distinct lower-level libraries covering the same domain 
(a drawing library and a physics library using different geometry objects, boost and qt for some particularly bloated C++ projects). 

Various kinds of factories happen when you have a client on the other side of 
your interface that needs a Foo to use your library, and Foos come in several kinds, but the client doesn't care which type of Foo they get, only they need to be different for different contexts 
(e.g. a FooX and a FooWayland, to use a topical example). Now if _you_ were designing a system where your client is requesting Foos, and can't just do new Foo() because they don't know which Foo they want, but you do, 
how would you do it given a language with class-based inheritance?

Visitor is awfully overused, but in general SomethingSomethingVisitor means that the programmer wanted (or thought he might want someday) to do different types of processing on all the SomethingSomethings. 

##I was a smart kid but I can't use the right design patterns##

* Do code reviews

You will eventually get to understand the patterns in the existing code and why they are there, and from there on your own code will grow some of them, and you will end up using them naturally. Or you can ask someone in your team to review your code. You may have to actively tell them to ignore anything the compiler tells you anyway and to not nitpick your variable names to get there, but eventually, they will provide you feedback on the structure, even if not directly, even in the form of questions ("what happens when we're going to want to add a new database connection?" "does this interact with the file monitoring process?")

Ultimately, not knowing "the right patterns" is not an intelligence problem, it's a knowledge problem. You may protest that you accumulated knowledge for four years, but it is a different kind; computer science is chiefly focused on the study of problems. Most programming trouble you will have with other people's code will require you to spend some time studying systems, which is an entirely different mode of thinking, more synthetical than analytical, more based on studying large numbers of black boxes than building castles of clear ones. You can get knowledge in all the ways I outlined, but you can only affirm it through use. 
