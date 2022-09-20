---
title: Electrode Wishlist Assignment
sidebar: auto
permalink: /assignment/
toc: false
type: homepage
git-user: vn0silf

presentations:
  - name: Assignment Introduction
    url: /react-redux-fundamentals/pdf/RRFDay4.pdf
---
> A project to further reinforce the React and Redux concepts presented in our classroom labs.  Also gives practice implementing Walmart-specific components.

## Presentations
- ### [Redux - middleware]({{ site.baseurl }}/pdf/RRFDay4.pdf)

## Overview

For your take-home assignment, you'll be putting your own spin on the "Wish List" feature from www.walmart.com. Users will be able to search for products and then add those products to their own wishlist, which can be viewed from the comfort of a web browser.


#### Here's a screencap of what the finished assignment might look like:

![screenshot]({{ site.baseurl }}/images/electrode-wishlist-screenshot.gif)


#### Project Objectives:

 1. Reinforcement of Redux principles behind Actions, Reducers, and Stores
 2. Practice working with the Electrode platform

## Milestone 1: Get Started!
> Est. Time: .5 Hours

You'll need to submit your project to the instructor by creating a repository in [GeC github](https://gecgithub01.walmart.com). Start by forking the [Wishlist Assignment Repo](https://gecgithub01.walmart.com/developer-portal/rrf-wishlist). Click the Fork button, and you'll be taken to your fork's page in Git. Complete the assignment in _your own fork_ of the getting-started repository.

We've provided this as a starting point for your assignment. The project was scaffolded using the `wml-ignite` tool. The demo files have been removed to provide a clean workspace.

### Clone fork locally and run

After you have forked the Wishlist Assignment Repo, go ahead and clone that fork locally so you have a place to do the work.  

Then `cd rrf-wishlist`, and finally run `npm install` to grab all the dependencies needed for the project.  Once `npm install` finsishes, you should be able to do `clap dev` to actually run the server.

### Opening Pull Request

You'll eventually be submitting this assignment via a pull request. If you aren't familiar with pull requests, they are the preferred way to implement new code from a fork into an origin upstream repository. A pull request enables a developer and their peers to see the breadth of a proposed code change and review it by making comments and suggestions. Let's configure the pull request now so that as you push new code up to your fork, we can see it in live time.

First, you'll need to make a code change. Navigate to your fork (https://gecgithub01.walmart.com/USERNAME/rrf-wishlist) and through the GitHub website, click on your README.md file. Click on the `edit` pencil icon on the top right:

![edit readme]({{ site.baseurl }}/images/edit-file-github.png)

Replace line 1 (`# rrf-wishlist`) with `# Wish List Assignment`. Now scroll to the bottom and hit the big green `Commit Changes` button. Navigate to the [base repository](https://gecgithub01.walmart.com/developer-portal/rrf-wishlist) and click on the `Pull Requests` tab. Click the big `New pull request` button and select the `compare against forks` link underneath the `Compare changes` header. Keep the left-most `base fork` dropdown as the `developer-portal` repo, but update the right `head fork` dropdown to point to your fork. Click the `Create pull request` button.

Give your pull request a title with your own name, for instance, "Varoon's Wish List Assignment" and click the `Create pull request` button at the bottom. Pull request created! Now whenever you update the code in your fork, those changes will automatically be shown in this pull request. 

Now that we have a PR configured, let's look at some code.

Following Redux best practices means that we should separate out our reducers, actions, and React components into distinct folders. Under the `src/client` folder you'll find `reducers` and `actions` folders.  As you go through the assignment, you should be adding code to both of those folders. Typically you would have multiple files within those folders and use `index.jsx` to mange importing and exporting.

Let's now get familiar with the other important files in the `rrf-wishlist` repository.

### Redux Store File
Let's go ahead and open the file at `src/client/app.jsx` to see how our Redux store is being created. You will notice the line: `import { createStore, applyMiddleware } from 'redux';`.  This line does two things - first, it retrieves `createStore` from the Redux library, which allows us to create our Redux store.  

The second thing it does is retrieve `applyMiddleware`, which allows us to apply some middleware to our Redux store.  Remember that middleware is basically some third party software that comes in between the point at which an action is dispatched to the Redux store and the moment when that action reaches the reducer.  

The middleware being used is `redux-thunk` for asynchronous actions - we've added this to the starter repo.

The redux store is instrumented with the middleware when it is created:
```js
const store = createStore(rootReducer, initialState, applyMiddleware(thunk));
```

Note that in order to use the reducers that you define under the `reducers` folder you will have to replace the stub in the `reducers/index.jsx` file.  Your `reducers/index.jsx` should import all the reducers you define in separate files and use `combineReducers` to export the root reducer.


### Routes File
You should also take a look at the `src/client/routes.jsx` file.  This file is the key to mapping our components to actual urls. Electrode uses the React Router library to manage client-side routing.  Note that the path `/` maps to the `WishList` component defined at `src/client/components/wishlist.jsx`. You can modify the routes array to add multiple pages to your app if you wish. Read about using the `Link` component for navigating to different routes at the [official docs](https://reacttraining.com/react-router/web/api/Link).


## Milestone 1.5: Product Search Form
> Est. Time: 1-3 Hours

For this milestone you will create a product search form to allow the user to find products that can be added to a given wishlist.

This is actually very similar to what you already did as part of your prework for the Pangaea Fundamentals [Electrode Lab](http://code.walmart.com/pangaea-fundamentals/labs/electrode/), where you built out a product search tool that queries Walmart's database.  

You should add an input field where the user can enter text, and a button "Search for products" to grab the text from the input field and query Walmart's database. It's recommended that you create a new file for your component.

### Retrieve Product Data
But, how will we actually get that product data?  Well, if you remember from your pre-work - you created a [Hapi.js plugin](https://gecgithub01.walmart.com/developer-portal/pf-electrode/wiki/Add-a-Server-Plugin) to communicate with the Walmart Preso Search API .

So, you will have to create that Hapi.js plugin for your WishList app.

Searching for products will be your first _asynchronous_ Redux action, given the fact that you will have to wait for Walmart's API to respond with the product data from the search. You should read more about [action creators](http://redux.js.org/basics/Actions.html#action-creators) and [asynchronous actions](https://redux.js.org/advanced/async-actions) to understand this further.  
<!--
You should go through this [page](https://gecgithub01.walmart.com/developer-portal/pf-electrode/wiki/Wire-Up-the-User-Interface) to see the action creator that will return an asynchronous action - and retrieve the product data - specifically `_search`.  In accordance with Redux best practices, you should create this same action creator in a file inside your `actions` folder.  Further down in that same page you can see the corresponding reducer to handle that action.  You will also need to create a similar reducer in a file under your `reducers` folder.  
-->

Remember that the `redux-thunk` middleware library is already included as part of the `rrf-wishlist` repository.  You don't have to import any other libraries to get thunks to work correctly.

### Display Product Data
The search results (the actual products) should be displayed in a nice format.  You should re-use whatever components you like from the Project [New York Component Explorer](https://gecgithub01.walmart.com/FEDS/ny-core).  This project contains the latest components currently being used on the actual Walmart.com site.

A bonus here (not required) would be to search for products as the user types.


## Milestone 2: "Add" Button
> Est. Time: 1-3 Hours

Now you should have a working product search form which displays a list of Walmart products when the "Search for Products" button is clicked.  For each product displayed in the search results, we should also have an "Add" button as well which allows users to add a given item to the wishlist.  Your task for this milestone is to implement that "Add" button.

When an item is added to the wishlist it should be displayed alongside any other products that are part of the current wishlist (refer to the gif above).


## Milestone 2.5: "Remove" Button
> Est. Time: 1-3 Hours

Once users add an item to a wishlist, they should be able to remove it as well - again, refer to the gif above.

## Milestone 3: Add to Search Form
> Est. Time: 2-4 Hours

Now that you have the basic search functionality in place, let's add a few visual elements as well.  

- You should add a "show/hide" link like the one shown in the GIF above, where a user can click "Add New Products" to bring up the product search form or "Hide Product Search Form" to hide the form.
- The UI should update itself to reflect when it's fetching data (the GIF shows some "loading" text, a spinner would be more sophisticated).


## Milestone 4: Saving Wishlists
> Est. Time: 3-6 Hours

Now, we want to add the ability to save a given wishlist.  Then, if the user goes to `/lists` they should see all saved lists.  First off, you would need to add a simple button that says: `Save List`.  

Then, you would need to implement [redux persist](https://github.com/rt2zz/redux-persist) to add some persistence.

You'll also need to update your `routes.jsx` file. Read the [Electrode docs](https://electrode.gitbooks.io/electrode/content/chapter1/intermediate/react-routes/react-routes.html) and [this tutorial](https://css-tricks.com/learning-react-router/) for more information on React routes and the  react-router library.

 Note that you would have to assign each list a unique id as well.

## Milestone 5: Run the linter
> Est. Time: 1-2 Hours

Before submitting your code be sure to run the linter via `clap check` and clean up any errors that show up.

## Milestone 6: Edit README.md file
> Est. Time: 1 Hour

In addition to committing the source for your project, you should edit the `README.md` in the root of the repository and document your code. Be sure that it contains the following:

- An animated gif demo of your app, similar to the one above, that clearly demonstrates all implemented features. You can create this gif using a tool such as [licecap](http://www.cockos.com/licecap/) and commit it to the repository alongside the `README.md` file.

- In your `README.md`, you should document the "shape" of your application's state. Refer to [Redux Documentation on Reducers](http://redux.js.org/basics/Reducers.html) for examples of "state shape". Your code should be utilizing `combineReducers` to manage each top-level state object.

## Submitting

To submit your completed assignment, push your fork up to Github and make a pull request of your fork against the [developer-portal/rrf-wishlist repo](https://gecgithub01.walmart.com/developer-portal/rrf-wishlist).

---

## Tips
* If your app doesn't seem to be refreshing correctly, check http://dev.walmart.com:3000/__electrode_dev/reporter.
* New to ES6? [ES6 Features used in React](http://www.saltycrane.com/blog/2016/03/es6-features-used-react-development/) is a lifesaver.
  * [Object Spread Operator](http://redux.js.org/recipes/UsingObjectSpreadOperator.html) is also useful syntax.
* For form input fields, such as the Product Title or Search Box, remember these are [controlled components](https://facebook.github.io/react/docs/forms.html).
* If you get errors around `this` being undefined on callbacks, remember to call `bind` as [described here](https://facebook.github.io/react/docs/handling-events.html).
