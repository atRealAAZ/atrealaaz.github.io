---
title: "Part 2: Building your first frontend"
date: 2023-01-17
tags:
  - Focus

---

<h1>Introduction</h1>

Hi all, and welcome. Today we will be creating our first frontend. An application without a frontend is like talking to a person without a face. Try sending your message directly into the brain. Yeah, no fun.

<h1>Installing node.js and npm</h1>

We will be using ```node.js``` and ```npm```.

Follow this <a href="https://phoenixnap.com/kb/install-node-js-npm-on-windows">link</a> to install them.

Verify they are installed by running 

```
node -v
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_3/node_v.PNG" alt="">

```
npm -v
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_3/npm_v.PNG" alt="">

Ok nice, we can now start!
<h1>Creating a React application</h1>

Go into the folder where your application will be located. Here I will create the frontend by running the following command:

```
npx create-react-app frontend
```
This will create a React app in a folder called ```frontend```, including all necessary packages, etc. 
Move into it and start the application by typing the following:

```
cd frontend
npm start
```
You now have a running React application! Well done!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_3/react_first.PNG" alt="">

Now, in the next section we will be adding functionality to this.

<a href="http://localhost:4000/creating-an-overview/">Next Section: Creating an Overview</a>



