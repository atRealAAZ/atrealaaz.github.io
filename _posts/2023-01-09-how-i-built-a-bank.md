---
title: "How I Built a Bank"
date: 2023-01-09
tags:
  - Focus
---

I don't know about you, but the way for me to learn is to do. Reading or hearing about something is nice, but until I've programmed something myself I can't be sure whether I have truly understood the concepts. If I can't create, I'm a cretin. Until I can reproduce, I am no master. Creating is the crucible on which conceptions and the illusions are shattered and you come face to face with the reality of your situation. Are you a nerd - or a newb? 

I did some online courses on creating web applications and for my work I'd done some backend. And then I realized - wait a minute, an application consists of a frontend and a backend. Could I create my own?

So I built a bank. 

How?

That's what I'll tell you in the coming blog post series. 

We have to answer the question first; what is a bank? Good question. A bank is according to the definition of a great online repository of wisdom, something that my good friend Mr. Socratic Bank will explain. Welcome Mr. Bank. What are you? "Well, I'm a financial institution that accepts deposits into an account. These deposits can then be transferred to other accounts, creating a network in which exchange of deposits is possible." All right, Mr. Bank. Thank you and Zeus speed. Watch out with what you drink though. 

So, for all intents and purposes a bank is thus a platform which has the core functionality of being able to transfer funds from one account to another. Wait a minute, this is something we can build!

<h1>Looks of a Bank</h1>

Before we get into it, however, I'll show you what it's going to look like. 

An account must be private, it must therefore be protected with user authentication:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/BankLoginNEW.PNG" alt="">

A new user must of course be able to register...

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/BankRegistrationNEW.PNG" alt="">

and be able to login where it shows an overview of its funds and transactions:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/EmptyOverview.PNG" alt="">

We should also be able to transfer some money, the core functionality of our bank:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/BankTransfer.PNG" alt="">

Now, once a transfer has been made, it should be visible in our overview, which should be updated:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/OverviewTransaction.PNG" alt="" class="full">

Damn, this actually looks pretty manageable.

Now I don't know about you, but I find this exhilariting. These massive behemoths of financial institutions with their trillions in currency and tens of thousands of employees, and we can just create one ourselves from behind our screens! The revolution will commence! 

Of course its not production-ready or even remotely secure, but conceptually it is there. 

And I built that myself. And soon, you can too! 

By flipping to the next page. Happy hacking!









