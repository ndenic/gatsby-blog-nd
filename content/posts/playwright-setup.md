---
title: Set up your test automation project with Playwright using Typescript
date: 2022-09-03
tags:
  - Playwright
  - Automated E2E testing
description: Setup your project for E2E testing using Playwright
---
![Cover img](/media/playwright-setup/cover-pw.jpg)
**Playwright is a powerful framework, which provides cross-browser automation through a single API, but this post is not entirely about it, but about how to set up a project in a good way. I will use Playwright for this purpose**

What you will not find in this post is an initial level explanation of the playwright framework. I think there is already a lot about it on the internet. This is pure practice, something that will serve you well if you are the first to break the ice in terms of setting up a project.

You started working on a new project (or an old one) and a request came or you decided to use Playwright yourself. It's ok to start writing tests just after you install playwright, but in fact, this kind of start will affect your later more complex tests, where your code will be difficult to maintain and even more difficult to add new tests. I will try to make this part easier for you and to help you set good roots at the very beginning.

What you will learn from this post is:
- Project setup starts with a good folder structure

- Which design pattern is good for that purpose

- A step-by-step explanation of what you need so that you can just write the tests afterwards without a headache

## Warm-up
If you haven't written a single basic test or at least spent a few hours in this framework, I suggest you first visit their official site (https://playwright.dev/) and look around a bit and write some tests. It's not difficult, and believe me if you deal with test automation at work, you will like this.
I have been using this framework for a year or so. I can tell you that the transition from Selenium/Java to Playwright/TypeScript was not the best.
I'm not saying it was difficult, but I wasn't the happiest at that moment.

But we won't talk about that, now I efficiently use that tool to finish all the work with quality and speed. So in this post, we are working with Playwright using Typescript. These are some general things and can be applied with different tools and it's not related only to the playwright.

## Blasting off
We will start with setting up the project. Of course, we need nodejs (https://nodejs.org/en/)
We will create an empty directory, for example, pw-test-automation and after that, we will open an IDE, some code editor. I use VSCode for this purpose, so I will show an example using that code editor
![VSCode](/media/playwright-setup/01.png)

Then we will need to open terminal and do
`npm init`

After that, we will add the dependencies that are basic for our work, which is only Playwright. You can add everything else as you wish, test runners or any other dependencies.
So next things are
`npm i -D @playwright/test`  
`npm i -D playwright` 

After we have successfully installed the dependencies, we will see the versions of the playwright in the package.json file.

## Design pattern 
Ok, let's move on to something more serious. We will use what some call the **Business-Layer Page-Object Pattern**, where the tests will have a clear structure of what is happening there. So we don't catch the selectors in tests, we don't go through the lists, but we call the functions that contain it and which are named enough that someone who doesn't have that much knowledge in that area can understand what's happening there.
Don't worry, it will become clearer to you soon if it hasn't already.
This is my way of implementing this design pattern and it works well for me.

Let's create inside pw-test-automation folder (root folder)
services folder, then inside we will create two more - **pages** and **steps**
We need one more folder inside the root folder and that's
**tests**. Where we will create our test files

![Business-Layer POM](/media/playwright-setup/02.png)

## Config files
We will quickly create a tsconfig.json file (in root folder), to use better the TS features and avoid some JS syntax errors which will appear without a config file.

![TS config file](/media/playwright-setup/03.png)

Those are just some basic compilerOptions , but of course you can expand it.

Next, we'll create a playwright config file so we can configure our tests to run the way we want. And of course we have the possibility to expand if the project requires it. 
(https://playwright.dev/docs/test-configuration)

![playwright config](/media/playwright-setup/04.png)

Here we have some simple things configured.
As u can see for key **baseURL** we just added URL, but in real projects we won't have one environment, this means that we will have to change the URL as soon as we want to run our tests on another environment. 
Now let's change that so that we can change the environment based on the entered input parameters.

![Environments](/media/playwright-setup/05.png)

I have created one more JSON file which is for environments and it contains two keys. One key is prod, other is dev. But that's all up to you and your project. It depends which environments you got and how are they called.

Okay we added process environment variable ENV, so how to specify that and when? 
Next step , we should open package.json and change scripts -> test
`"scripts": {
    "test": "ENV=$npm_config_ENV playwright test"
  },`

Now we just set that if in the future we wanted to run a test based on some environment, we would type, for example, `npm run test --ENV=prod` or dev in our case. At the moment we don't have a single test, so we have nothing to run, but no rush.

When setting up a project, you have to do it thoroughly and think many steps ahead. Now let's do it again as for the environment, but this time we need to run the tests by tags as well. For example run all regression tests

![Run tests by tags](/media/playwright-setup/06.png)
And then update also test script in package JSON file with latest changes 
`  "scripts": {
    "test": "ENV=$npm_config_ENV TAGS=$npm_config_TAGS playwright test"
  },`

Ok, I think you get the point. A lot of things can be configured this way. I'll give you a hint for further options, what if we want to run by browser type?

I didn't spend time explaining to you what each key in the config file means individually because I think there is no better explanation than the documentation itself.

## Selectors
Let's go to the next section.
We will need to store selectors, somewhere. My advice is to have separate file for that purpose. There are many options -> create only one **selectors.json** file or have folder selectors which contains JSON files for each Page. I prefer first option , because we have enough freedom to create nested JSON object and store selectors in similar way as to create N amount of files.

![Selectors](/media/playwright-setup/07.png)

## Services
Now we have the selectors, we have the config file, we have the basic structure according to the Business-Layer Page-Object Pattern and we are starting to create, for example, the HomePage.

![home.page.ts](/media/playwright-setup/08.png)
The point is that here we use the selectors that we saved in the json file later in the functions and return them as a Locator object that we can work with.

Let's create steps for HomePage

![Home page steps](/media/playwright-setup/09.png)
Now you can already understand that we will only call those steps in the test files. This is just an example and in reality we will have a lot of steps/functions here.

## Test data
And now you will definitely need **Test data**, so for example you pass some data to the function with which you want to test
For this purpose, my suggestion is to create a **test-data** folder in the root folder and create as many JSON (or any other type) files with test data as you need.

![Almost complete structure](/media/playwright-setup/10.png)

As you can see, the project setup seems almost complete. Everything is clearly separated into units.
But of course CI/CD integration is also needed here, which is not difficult at all, but it takes this story to the other side. I'll leave that up to you, and I'll be there to help you if you need it, of course don't hesitate to ask a question.

It's not over yet, hold on :) 

The last thing in that magic circle of POM model is the Test file. So we created a folder in the start for that purpose -> tests.

In that folder we will create, for example, the 
home-page-tests.spec.ts file

![Home page test file](/media/playwright-setup/11.png)

In the test file, in this case, we have beforeEach, but don't pay attention to that now, because it could also be beforeAll, and in that case, it would be desirable to have afterEach or afterAll as well.
We initialize the page in the before method so that we can pass it to the constructor of the steps class.

We do not use the HomePage functions in tests, unless there is an overwhelming need for it and in some cases it is unavoidable. We already used only Steps functions that do all the work. For example, we have inputTextWithTestData() and after that we add a function for assertion in steps and name it appropriately.

**What do we get with this kind of structure ?**
_Easier maintenance of tests_ (more precisely steps)
_Code redundancy is avoided_ (we don't have to repeat ourselves, we can use the same step in several test cases if there is a need for it)

Do you need more?

At the end of the day, someone who doesn't understand the code that much will understand what the test is about. You get an almost Gherkin-like syntax, if you look at the "before" method as GIVEN and the rest of the action WHEN insertInputTextWithTestData , THEN assertion

So let me repeat the most **important**

_Service folder contains Page and Steps
Page functions are functions that return Locators or Promises<> that we solve later. We use those functions in the Steps class.
The Steps class literally contains the steps that we will do in the test files to execute a test case (add a book to the cart, open the cart, confirm that the book is in the cart).
We don't want it all to be in one function, but several smaller ones, so that they can be used again if we need to perform the same actions again and also so that the name of the function itself is short and clear.
In the test file, we initialize page and steps, write test() and call functions from the steps class._ And let the game begin

## Summary
![Final](/media/playwright-setup/12.png)
This is how it might look in the end. I added the pipeline YML file, .jenkinsfile and the helper class
Now you can start writing your e2e/UI tests or eventually expand that framework by your project needs.

And yeah, finally, this would be an example of how we will run the tests
`npm run test --ENV=dev --TAGS=regression`
but we will probably have more parameters to manage what and how we will run in our tests.

Thanks for reading, I hope this will help you if you find yourself in the situation of setting up a project from scratch and of course shoot your questions if you have any.

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qfy1noybqyg2t4yd2fji.gif)