# How to build a basic Node.js project
<a href="https://nodejs.org/en/"><img src="./imgs/nodejs.jpg" style="width: 100%; height: 160px"></img></a>

Let’s learn how to get started with Node.js by creating a simply Node.js ```file.In``` this example, we will be setting up our computer to work as a server!

## Table Of Content
1. [Install Node.js and NPM](#install)
2. [Create a file](#create)

<hr>
<!-- Install section -->
<section id='install'>

## Install Node.js and NPM

First, you need to Go to the site <a href="https://nodejs.org/en/">Node.js site</a> and download the files.

Follow the installation prompts and restart your machine for best results.

- Then, test that it’s working by printing the version using the following command:
```console
 node -v
```
- You should also test npm by printing the version using the following command:
```console
npm -v
```
</section>
<!-- Install section ends -->

<!-- Create section  -->
<section id='create'>

## Create a file

> All code need for this exercise can be found in ```code``` folder.

Once you have installed Node.js properly, create a Node.js file. In this example, we have named it named ```first.js```. We then add the following code and save the file on your computer like so: C:\Users\Your Name\first.js

### Commands to start node application
- npm init (can be used to set up a new or existing npm package)
- npm i ```<packageName>``` (install dependicce of choice)
- node ```<fileName>``` (starts node serve)

```ruby
# Modules package required (npm i http)
var http = require('http');

http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/html'});
  res.end('Hello World!');
}).listen(8080);

```

> This code is essentially telling the computer to print “Hello World!” when accessed on port 8080.


### Command line interface

Node.js files must be initiated in your computer’s ```Command Line Interface``` program. Navigate to the folder that contains the file ```first.js```.
```
C:\Users\Your Name>_
```

### Initiate your file

This file needs to then be initiated by Node.js. Do this by starting your command line interface, writing ```node first.js```, and clicking enter:
```
C:\Users\Your Name>node myfirst.js
```

> Awesome! Now your computer is set up as a server, so when you accesses the computer on port 8080, the ```Hello World!``` message will print.

To see this in real time, open your browser, and type: [http://localhost:8080](http://localhost:8080)

</section>
<!-- Create section ends -->

<a href="https://github.com/ShaneKolkoto">Click here to learn how to build a CRUD server </a>

<h3 align="left">Connect with me:</h3>
<a href="https://codesandbox.com/https://codepen.io/shanekolkoto" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/codesandbox.svg" alt="https://codepen.io/shanekolkoto" height="30" width="40" /></a>
<a href="https://linkedin.com/in/shane-morne-kolkoto" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="shane-morne-kolkoto" height="30" width="40" /></a>
<a href="https://discord.gg/Shane Kolkoto (web-dev)#7552" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/discord.svg" alt="Shane Kolkoto (web-dev)#7552" height="30" width="40" /></a>
<a href="https://github.com/ShaneKolkoto" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/github.svg" alt="ShaneKolkoto" height="30" width="40" /></a>
</p>

<h3 align="left">Support:</h3>
<a href="https://www.buymeacoffee.com/shanekolko" target="blank"><img align="center" src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="https://www.buymeacoffee.com/shanekolko" height="30" /></a>
