# Autodesk Forge Viewer – Getting Started Tutorial

* Introduction
  - [Audience](#Audience)
  - [What do you need for your project?](#WhatDoYouNeed)
  - [What are you going to achieve in this workshop?](#WhatAreYouGoingToAchieve)

* [Prerequisites](prerequisites.md#Prerequisites)
  - [Familiar with git?](prerequisites.md#FamiliarWithGit)
  - [Install Node.js](prerequisites.md#InstallNodeJs)
  - [Install a code editor](prerequisites.md#InstallCodeEditor)
  - [Get the sources](prerequisites.md#GetTheSources)

* [Chapter 1 – Get ready with the Model Derivative API](chapters/chapter-1.md#Chapter1)
  - [Create an App](chapters/chapter-1.md#CreateAnApp)
  - [Prepare a model](chapters/chapter-1.md#PrepareAModel)
  - [Create your web server](chapters/chapter-1.md#CreateYourWebServer)

* [Chapter 2 – (Optional) Extend your web server to support upload/translation](chapters/chapter-2.md#Chapter2)
  - [Choose your option](chapters/chapter-2.md#Options)
  - [Translating from the client](chapters/chapter-2a.md#Chapter2a)
  - [Translating from the server](chapters/chapter-2b.md#Chapter2b)

* [Chapter 3 – Customize the Viewer Behavior](chapters/chapter-3.md#Chapter3)
  - [Step 1 – Creating a basic extension](chapters/chapter-3.md#Step1)
  - [Step 2 – Reference the extension script](chapters/chapter-3.md#Step2)
  - [Step 3 – Load the extension in the viewer](chapters/chapter-3.md#Step3)
  - [Step 4 – Testing the extension](chapters/chapter-3.md#Step4)
  - [Step 5 – Adding a selection handler](chapters/chapter-3.md#Step5)
  - [Step 6 – Displaying a panel](chapters/chapter-3.md#Step6)
  - [Step 7 (Bonus step) – Moving the camera](chapters/chapter-3.md#Step7)
  - [Even more bonus steps](chapters/chapter-3.md#More)

* [Appendix A – More sample and demos](appendix/appendix-a.md)
* [Appendix B – Review of the node.js code](appendix/appendix-b.md)
* [Appendix C – Deploy on the web with Heroku](appendix/appendix-c.md)
* [Appendix D – Automatic deployment on the web with Heroku](appendix/appendix-d.md)
* [Appendix E – Troubleshooting](appendix/appendix-e.md)


<a name="Audience"></a>
## Audience

This documentation is designed for people familiar with [JavaScript](http://www.ecma-international.org/publications/standards/Ecma-262.htm) programming and object-oriented programming concepts.
You should also be familiar with the Autodesk Viewer and Model Derivative technology from a user's point of view. You can play with the technology [here](https://360.autodesk.com/viewer) – simply Drag 'n Drop a 2D/3D file,
and enjoy the result in your browser with no extension or plug-in installed on your computer or device.

This conceptual documentation is designed to let you quickly start exploring and developing applications with the Autodesk Viewer and Model Derivative API.


<a name="WhatDoYouNeed"></a>
## What do you need for your project?

The Autodesk Viewer and Model Derivative API web service consists of two APIs. The first API ([Model Derivative API](https://developer.autodesk.com/en/docs/model-derivative/v2/overview/)) is a REST API which enables users to represent and share their designs in different formats, as well as extract valuable metadata. The Second API ([Viewer](https://developer.autodesk.com/en/docs/viewer/v2/overview/)) – is a WebGL-based, JavaScript library for 3D and 2D model rendering. 3D and 2D model data may come from a wide array of applications, such as AutoCAD, Fusion 360, Revit, and many more.

Depending on your needs, you may prefer to write a server or a desktop application to consume the REST API. Your choice will be mainly based on how many files you need to translate,
and the frequency:

- If you need to translate only one (or very few) of your own models to be viewed by other people, then you may prefer to use a desktop application to do this, or one of our demo pages.

- If you need to translate multiple models in a batch process or allow other users to upload their own files, then a web server implementation will be needed.

Later in this workshop, you will need to decide which routes you want to go with depending on your needs.


<a name="WhatAreYouGoingToAchieve"></a>
## What are you going to achieve in this workshop?

When you finish the workshop you will be able to:

- Create a dynamic application that works in all modern browsers.
- View 2D/3D models in a browser or device without plug-in or additional software.
- Create and run Node.js application .
- Extend Autodesk WEBGL viewer to interact with the 2D/3D models.
- Identify resources for learning more about the Autodesk Viewer and Model Derivative API.

The tutorial guides you through the entire process of building a simple application, including writing and running viewer extensions. Experiments at the end of each step provide suggestions for you
to learn more about the Autodesk View & Data API and the application you are building.


=========================
[Start](prerequisites.md)
