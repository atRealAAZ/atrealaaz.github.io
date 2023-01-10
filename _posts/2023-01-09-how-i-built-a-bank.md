---
title: "How I Built a Bank"
date: 2023-01-09
tags:
  - Focus
---

I don't know about you, but the way for me to learn is to do. Reading or hearing about something is nice, but until I've programmed something myself I can't be sure whether I have truly understood the concepts. Until I can reproduce something, I haven't mastered something. Building is the crucible on which conceptions are shattered and you come face to face with the reality of your situation. Are you a nerd - or a newb? 

So I built a bank. 

How?

That's what I'll tell you in the coming blog post series. 

We have to answer the question first; what is a bank? Good question. A bank is according to the definition of Wikipedia a financial institution that accepts deposits into an account. These deposits can then be transferred to other accounts. For all intents and purposes the core functionality is thus the ability to transfer funds of one account to another. But what, this is something we can build!

Before we get into it, however, I'll show you what it's going to look like. 

An account must be private, it must therefore be protected with user authentication:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/BankLogin.png" alt="">

A new user must of course be able to register:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/BankRegistration.png" alt="">

Great! If we now create a user called New User, we get to the following overview, which is empty of course:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/EmptyOverview.png" alt="">

In this overview we can also select to get a particular financial product:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/Products.png" alt="">

We can also transfer some money, the core functionality of our bank:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/BankTransfer.png" alt="">

Now, once that is done, we can return to our overview, where we will see the sent transaction with its information, plus the account information that is updated:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/BuildingABank/OverviewTransaction.png" alt="" class="full">

Now I don't know about you, but I find this exhilariting. These huge financial institutions, and I built one myself. Of course its not production-ready or even remotely secure, but the core concept is there. 
And the fact that I can build one myself. And soon, you can too! 

The course starts here. Happy hacking!







