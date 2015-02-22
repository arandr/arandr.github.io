---
title: What Is "Production" Code?
layout: default
---

Inspired (loosely) by [this post](http://tomassetti.me/resurrect-a-c-codebase-and-create-a-proper-open-source-project-out-of-it/).

By way of summary, the author mentioned finding an unmaintained open-source project and giving it a home on GitHub, tests, documentation, and a decent build system.
As someone who builds a lot of prototypes (because it's my job description, not because finishing is too difficult) and is just now switching over to a greenfield project 
that is definitely more production-oriented, this made me think of the various ways "production code" can be defined.

##Production code has users##

That one is very common. Essentially, the idea that if it is shipped to external users, then it is live production code. There are some pitfalls with it; for example, 
it doesn't account for line-of-business apps, a (very profitable) segment of the industry involving code that will only ever have internal users, but that is sometimes the subject of more polish than some shipped code.
It's also debatable whether the code of a new startup's MVP (possibly a thousand users, almost certainly written under severe time restrictions with a test plan 
consisting of the developer clicking everywhere they could think of) counts as production. On one hand, it's shipped. On the other, my company does a bit of acquisitions 
and let me tell you it is unusual for a small company's only product to have some of the other points mentioned here, like regular automated tests and decent documentation.

##Production code has documentation##

That one is more common for libraries. A common way of expressing it is that production code is made to be read, and reading code more often than not requires documentation. 
Leaving aside "self-documenting code" proponents for a moment, this is by and large not the case for driver code and a lot of video game code, even though the former will be
used by legions of developers pulling their hair (looking at you video card drivers).

##Production code is maintained by a large team##

Largely not true. Even out of startups and line-of-business apps (and by that point you are reducing the tech industry to Microsoft, Google, and a few hundred other 
established software companies) it's not uncommon to have The Schglub Expert, the one guy who can write code in the Schglub System designed by they themself or a guy who since relocated 
to Timbuktu 5, 10 or 20 years ago. The expert is ridiculously well paid by their current company, which is justified because that particular product, or that particular product's differentiating 
factor, would die a slow death without them to dig into the code, pull out the traps, and build a tunnel to Mars. Schglub is most definitely production code (occasionally a couple millions dollars of yearly revenue worth of it). 

##Production code has a standard build system##

I wish.

##Then what does production code even *mean*?##
Well, that's the issue, isn't it? Since software is no longer shrinkwrapped and bound to every-two-years major releases, the line between released and not (let alone "finished" and not) 
can be pretty thin. In addition software is leaking its way into everything, and a lot of business use cases for it do not involve "shipping it" to "end users". 

Therefore let me include a wider definition that includes some, but not all, elements of the others.

##Production code is intended for other people##

They're your bosses, your users, your fellow developers, "the community". And the code exists for them. Whether this involves specific documentation or testing practices depends on the development 
process, team size, use case, etc., although it invariably involves some testing not performed by the developer (either performed by a script or manually by a QA team). 
It involves communication and collateral materials around the code and the product it makes up; for the specific case of projects on GitHub or other open source platforms, this is a detailed README
 with usage and installation instructions.
It involves a vision of the user and a deliberate effort to gain users within a target segment. This is conventional sales forces, landing pages, IRC channels, ads, promotional activities like
 hackathons and speaking at conferences, content marketing, making your company's other departments use it etc. 

If there is no communication and no active effort to gain users (and this is users not stakeholders; there is a great deal of communication and marketing work around research projects and prototypes, 
but it is usually meant to gain supporters and advocates not users and clients, and in this way more closely resembles the outreach practices for educational programs and political campaigns), 
then the code's goal is not to serve a functional purpose for someone else than its developer. In effect just as in other fields the sales make the product.

It's interesting that the original article example defines an open source project that actively wants users and contributors as a "proper open source project", as though the defining characteristic 
of an open source project versus code on the Internet is being made comfortable to use by more than the original authors (and incidentally there's no mention of adding in a specific license).  