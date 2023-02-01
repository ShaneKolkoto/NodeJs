# How to build CRUD System with Nodejs
<a href="https://nodejs.org/en/"><img src="https://github.com/ShaneKolkoto/NodeJs/blob/main/imgs/nodejs.jpg" style="width: 100%; height: 160px"></img></a>

Node.js is eating the world. Many of the largest companies are building more and more of their websites and API services with Node.js, and there’s no sign of a slowdown. I’ve been working with Node.js since 2012 and have been excited to see the community and tooling grow and evolve — there’s no better time to get started with Node.js development than right now.

This tutorial will take you step-by-step through building a fully-functional Node.js website. Along the way, you’ll learn about Express.js, the most popular web framework, user authentication with OpenID Connect, locking down routes to enforce login restrictions, and performing CRUD operations with a database (creating, reading, updating, and deleting data).

## Table of content
1. [About Express.js](#about)
2. [Create Your Express.js App](#create)
3. [Initialize Authentication](#initialize)
4. [Install Dependencies](#install)
5. [Define Database Models with Sequelize](#define)
6. [Initialize Your Express.js App](#initialize_app)
    - [Initialize Node.js Middlewares](#middleware)
    - [Initialize Node.js Routes](#routes)
    - [Initialize Error Handlers](#errors)
7. [Get User Data](#get_user)
8. [Create Express.js Views](#views)
9. [Create Styles](#styles)
10. [Create Routes](#create_routes)
    - [Create User Routes](#user_routes)
    - [Create Blog Routes](#blog_routes)
11. [Test Your New CRUD App!](#test)
12. [Do More With Node!](#do_more)

<!-- About Section -->
<section id="about">

## About Express.js
[Express.js](https://expressjs.com/) is the most popular web framework in the Node.js ecosystem. It’s incredibly simple and minimalistic. Furthermore, thousands of developer libraries work with Express, making developing with it fun and flexible.

Whether you’re trying to build a website or an API, Express.js provides tons of features and an excellent developer experience.

Through this tutorial, you’ll be building a simple blog. The blog you build will have a homepage that lists the most recent posts, a login page where users can authenticate, a dashboard page where users can create and edit posts, and logout functionality.

The blog will be built using Express.js, the user interface will be built using Pug, and the authentication component will be handled by Okta. Sequelize.js will handle the blog post storage and database management.

</section>
<!-- About Section Ends -->

<!-- Create Section -->
<section id="create">

## Create Your Express.js App

Before we begin, make sure you have a recent version of Node.js installed. If you don’t already have Node.js installed, [please visit](https://nodejs.org/en/download/package-manager/) this page and install it for your operating system before continuing.

To get your project started quickly you can leverage ```express-generator```. This is an officially maintained program that allows you to scaffold an Express.js website with minimal effort.

> To install express-generator run:
```
npm install -g express-generator
```
> Next, you need to initialize your project. To do this, use the newly installed express-generator program to bootstrap your application:
```
express --view pug okta-express-basic-crud-app-example
cd okta-express-basic-crud-app-example
npm install
npm start
```
> The above command will initialize a new project called okta-express-basic-crud-app-example, move you into the new project folder, install all project dependencies, and start up a web server.

```Once you’ve finished running the commands above, point your favorite browser to http://localhost:3000, and you should see your application running:```
</section>
<!-- Create Section Ends -->

<!-- Initialize Section -->
<section id="initialize">

## Initialize Authentication

Dealing with user authentication in web apps can be a massive pain for every developer. This is where Okta shines: it helps you secure your web applications with minimal effort.

Before you begin, you’ll need a free Okta developer account. Install the [Okta CLI](https://cli.okta.com/) and run ```okta register``` to sign up for a new account. If you already have an account, run ```okta login```. Then, run``` okta apps create```. Select the default app name, or change it as you see fit. Choose ```Web``` and press ```Enter```.

> Select Other. Then, change the Redirect URI to http://localhost:3000/authorization-code/callback and use http://localhost:3000/ for the Logout Redirect URI.

<details>
<summary>What does the Okta CLI do?
</summary>
The Okta CLI will create an OIDC Web App in your Okta Org. It will add the redirect URIs you specified and grant access to the Everyone group. You will see output like the following when it’s finished:
</details>

Finally, create a new API token. This will allow your app to talk to Okta to retrieve user information, among other things. You can find the steps to [create an API token here](https://developer.okta.com/docs/guides/create-an-api-token/main/). Copy down this token value as you will need it soon.

</section>
<!-- Initialize Section Ends -->


<!-- Install Section -->
<section id="install">

## Install Dependencies
> The first thing you need to do in order to initialize your Express.js app is install all of the required dependencies.

```
npm install express@4
npm install @okta/oidc-middleware@4
npm install @okta/okta-sdk-nodejs@6
npm install sqlite3@5
npm install sequelize@6
npm install async@3
npm install slugify@1
npm install express-session@1
```
</section>

<section id="define">

## Define Database Models with Sequelize
> The first thing I like to do when starting a new project is define what data my application needs to store, so I can model out exactly what data I’m handling.

- Create a new file named ./models.js and copy the following code inside it.

```ruby
const Sequelize = require("sequelize");

const db = new Sequelize({
  dialect: "sqlite",
  storage: "./database.sqlite"
});

const Post = db.define("post", {
  title: { type: Sequelize.STRING },
  body: { type: Sequelize.TEXT },
  authorId: { type: Sequelize.STRING },
  slug: { type: Sequelize.STRING }
});

db.sync();

module.exports = { Post };
```

> This code initializes a new SQLite database that will be used to store the blog data. It also defines a model called Post which stores blog posts in the database. Each post has a title, a body, an author ID, and a slug field.

- The ```title``` field will hold the title of a post. For example, “A Great Article.”
- The ```body``` field will hold the body of the article as HTML. For example, ```<p>My first post!</p>```.
- The ```authorId``` field will store the author’s unique ID. This is a common pattern in relational databases: store just the identifier of a linked resource so you can look up the author’s most up-to-date information later.
- The ```slug``` field will store the URL-friendly version of the post’s title. For example, “a-great-article”.

<strong>NOTE</strong>: If you’ve never used SQLite before, it’s amazing. It’s a database that stores your data in a single file. It’s great for building applications that don’t require a large amount of concurrency, like this simple blog.

> The call to ```db.sync()```; at the bottom of the file will automatically create the database and all the necessary tables once this JavaScript code runs.
</section>

<section id='initialize_app'>

## Initialize Your Express.js App
The next thing I like to do after defining my database models is to initialize my application code. This typically involves:
- Configuring application settings
- Installing middlewares that provide the functionality to the application
- Handling errors, etc.

> Open the ./app.js file and replace its contents with the following code.

```ruby
const createError = require("http-errors");
const express = require("express");
const logger = require("morgan");
const path = require("path");
const okta = require("@okta/okta-sdk-nodejs");
const session = require("express-session");
const { ExpressOIDC } = require('@okta/oidc-middleware');

const auth = require("./auth");
const blogRouter = require("./routes/blog");
const usersRouter = require("./routes/users");

const app = express();

app.set("views", path.join(__dirname, "views"));
app.set("view engine", "pug");

#  Middleware
app.use(logger("dev"));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(express.static(path.join(__dirname, "public")));

const oidc = new ExpressOIDC({
  appBaseUrl: "http://localhost:3000",
  issuer: "{OKTA_OAUTH2_ISSUER}",
  client_id: "{OKTA_OAUTH2_CLIENT_ID}",
  client_secret: "{OKTA_OAUTH2_CLIENT_SECRET}",
  scope: "openid profile",
  routes: {
    login: {
      path: "/users/login"
    },
    loginCallback: {
      afterCallback: "/dashboard"
    }
  }
});

app.use(session({
  secret: "{aLongRandomString}",
  resave: true,
  saveUninitialized: false
}));

app.use(oidc.router);

app.use((req, res, next) => {
  if (!req.userContext) {
    return next();
  }

  auth.client.getUser(req.userContext.userinfo.sub)
    .then(user => {
      req.user = user;
      res.locals.user = user;
      next();
    });
});

#  Routes
app.use("/", blogRouter);
app.use("/users", usersRouter);

#  Error handlers
app.use(function(req, res, next) {
  next(createError(404));
});

app.use(function(err, req, res, next) {
  res.locals.message = err.message;
  res.locals.error = req.app.get("env") === "development" ? err : {};

  res.status(err.status || 500);
  res.render("error");
});

module.exports = app;
```

> Be sure to replace the placeholder variables with your actual Okta information.

- Replace ```{OKTA_OAUTH2_ISSUER}``` with your Org’s OAuth2 Issuer URL.
- Replace ```{OKTA_OAUTH2_CLIENT_ID}``` with the Client ID of your application.
- Replace ```{OKTA_OAUTH2_CLIENT_SECRET}``` with the Client secret of your application.
- Replace ```{aLongRandomString}``` with a long random string (just mash your fingers on the keyboard for a second).

<strong>NOTE</strong>: We will create the auth.js file later on in the tutorial.

Let’s take a look at what this code does.


</section>
<!-- Views Section -->
<section id="views">
</section>
<!-- Views Section Ends -->



<!-- Styles Section -->
<section id="styles">
</section>
<!-- Styles Section Ends -->

<!-- Create_routes Section -->
<section id="create_routes">
</section>
<!-- Cbout Section Ends -->

<!-- Test Section -->
<section id="test">
</section>
<!-- Test Section Ends -->

<!-- Do_more Section -->
<section id="do_more">
</section>
<!-- Do_more Section Ends -->

<h3 align="left">Connect with me:</h3>
<a href="https://codesandbox.com/https://codepen.io/shanekolkoto" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/codesandbox.svg" alt="https://codepen.io/shanekolkoto" height="30" width="40" /></a>
<a href="https://linkedin.com/in/shane-morne-kolkoto" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="shane-morne-kolkoto" height="30" width="40" /></a>
<a href="https://discord.gg/Shane Kolkoto (web-dev)#7552" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/discord.svg" alt="Shane Kolkoto (web-dev)#7552" height="30" width="40" /></a>
<a href="https://github.com/ShaneKolkoto" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/github.svg" alt="ShaneKolkoto" height="30" width="40" /></a>
</p>

<h3 align="left">Support:</h3>
<a href="https://www.buymeacoffee.com/shanekolko" target="blank"><img align="center" src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="https://www.buymeacoffee.com/shanekolko" height="30" /></a>
