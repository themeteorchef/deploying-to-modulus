### What is “deploying?”
When we talk about deploying an application, what we really mean is making that application accessible to others outside of our own computer or our local network. When we “deploy” something, we send it to one of a few `targets`: different environments where our application will run. Depending on the type of application we’re building and what we need to test, we generally deploy to one of the following environments:

#### Development
While not necessarily an environment that we _deploy_ to, our development environment is sort of like the mothership. This is where the bulk of our work occurs. This is your local computer (or your teammate’s computer) where you run the application in isolation. For example, this is where we can throw random bits and bobs of data into the database without worrying.

#### Staging
A staging environment is another name for an environment that closely mimics our final production environment, but isn’t accessible by users or customers. We use this environment to “stage” our application for testing purposes. Why? Well, as much as we’d like our application behave in production exactly as it does on our local machine, unfortunately, that’s rarely the case. 

Because our _live_ application is interacting with so many different parts (e.g. our database might live on a service independent of our hosting service), we have to test and account for this. A staging environment, then, allows us to do this without breaking things for real-world users or paying customers.

#### Production
Another environment—the one we think of most when we say “deployment”—is the _production_ environment. This is the environment that users and customers have access to. In other words, the production environment is sacred ground. As best as we can, we need to ensure that the code we’re _deploying_ to this environment is stable and working; hence our use of the staging server!

### What is Modulus?
Modulus is a PaaS (Platform-as-a-Service) company that offers hosting for Node.js, PHP, Java, Static Sites, and most importantly: Meteor applications (technically a Meteor application built as a Node.js application). It’s a favorite of Meteor developers because they offer an easy workflow (similar to Meteor’s `meteor deploy`) that allows for fast deployment of applications. Additionally, they also handle the more difficult aspects of _configuring_ servers, meaning our focus as developers is kept on writing the software, not managing the systems that _host_ our software.

<div class="note">
  <h3>Did Modulus sponsor this post?</h3>
  <p>Nope! Modulus just happens to be a favorite of mine and is the service used to power all of the Meteor applications powering The Meteor Chef. If anything, this is a fanboy post. No paid shilling here :)</p>
</div> 

### Alternatives to Modulus
Before we dig into getting set up on Modulus, you may be wondering…”are there alternatives to Modulus?” Of course! Here’s a quick look at some of the more popular options for hosting a Meteor application (and how they differ) with Modulus:

#### Digital Ocean
Digital Ocean is a popular choice for hosting Meteor applications due to its value. The service is fairly inexpensive and offers a “blank slate” server using one of six operating systems: Ubuntu, Fedora, Debian, CentOS, CoreOS, or FreeBSD. The difference between Digital Ocean—“D.O.” (DEE-OH) as you may hear in nerd-to-nerd conversations—and something like Modulus is that installation and configuration of all the software needed to run your application is left up to you. For example, you will need to install Node.js, MongoDB, and all other tools needed to get up and running. 

Though inexpensive, D.O. is best reserved for developers who have some experience with system administration, or, aren’t afraid to get their hands dirty.

#### Amazon EC2
Another option for hosting our Meteor applications is Amazon EC2. This service is similar to Digital Ocean in that it allows you to spin up virtual “instances” (copies) of your application. Again, configuration and management of these instances is up to you so working with EC2 requires a fair bit of system administration knowledge to get up and running.

<div class="note success">
  <h3>A loophole!</h3>
  <p>If you’re interested in saving a bit of cash using something like Digital Ocean or Amazon EC2—and don’t mind getting your hands a <em>little bit dirty</em>—you can make use of the wonderful <a href="https://github.com/arunoda/meteor-up">Meteor Up</a> tool built by Arunoda Susiripala. Up gives you an easy-to-configure file that automatically installs and configures the software you need to run a Meteor application on Debian/Ubuntu and Open Solaris backed servers.</p>
</div>

#### Heroku
Possibly the closest option to Modulus at the moment, Heroku, allows you to easily deploy your application and add support for different things like databases, sending email, and error logging through something they call "add-ons." To make the initial configuration of Heroku a bit easier, you can make use of the [Heroku Buildpack for Meteor](https://github.com/jordansissel/heroku-buildpack-meteor) by Jordan Sissel. 

#### What's the best?
Oh, silly you, can you guess the answer? It's up to you! All of these services essentially get us the same end using different means. My advice: experiment. Put aside a few days (or even weeks) to evaluate the different options and figure out what is most comfortable for you and/or your team.

In order to get started with deploying to Modulus we need to set up an account on their site. Let’s hop over their now and get set up!

### Setting up an account on Modulus

The easy part! Well...this is actually all pretty easy (a good thing). Pretty much what you'd expect. Hop over to the Modulus site and hit their [signup page](). 

![Signing up on Modulus](http://cl.ly/image/2E071E0V1I2m/Image%202015-08-11%20at%202.21.38%20PM.png)

One of the nice things—and something that's made myself and clients very happy—is that you get a credit for signing up so your servers will run for about one month 100% free. This is really handy if you're on the fence and don't want to pay any money just yet and want to get a hands-on feel for the service.

Once this is in place, we're going to pause on the dashboard for a bit and get the Modulus CLI installed.

### Installing the Modulus CLI
The CLI—command line interface—for Modulus is really handy. This is what we can use to quickly and easily deploy our application to the service and perform some administrative and maintenance tasks. To install it, we need to make sure we have [Node.js/NPM](https://nodejs.org/) installed first. Once that is in place, we can hop into our terminal and get on with it:

<p class="block-header">Terminal</p>

```bash
npm install -g modulus
```
This will get Modulus installed on our machine and give us access to the CLI via the `modulus` command. Just to make sure we've got everything setup correctly, go ahead and run `modulus login` to login to the account we set up earlier.

![Logging into Modulus](http://cl.ly/image/142H0E3a2q2q/modulus-login.gif)

If all is well we should get the `Signed in as user <username>` message! Now that we've got this squared away, we need to set up a server and a database on Modulus. This is just as easy, but we need to pay attention so we have the right information for setting up our app.

### Setting up a server and a database
If we hop back over to the Modulus dashboard, we can create a server and a database pretty quickly. From your dashboard, click on the "Create a New Project" button to spin up a server:

![Modulus dashboard](http://cl.ly/image/3R0o2f283b1y/Image%202015-08-11%20at%203.12.43%20PM.png)

Okay, this is where we need to pay attention. First, we need to give our server a name. This can be anything you'd like, but make sure to jot it down. This is case sensitive, so make sure to use something easy that you will remember. 

![Creating a new project on Modulus](http://cl.ly/image/0D21331f2L0O/Image%202015-08-11%20at%203.13.39%20PM.png)

Next, we need to pick which type of project we're going to set up. Where's Meteor? Didn't they advertise that on the home page? They did, but—sneaky, sneaky—technically a Meteor application is just a Node.js application. As we'll learn in a bit, to get our app working on Modulus we need to convert it from a Meteor app into a plain Node.js app so that it works. Don't worry, Modulus has automated this for us. For now, let's just select the `NODE.JS` option.

After this, we get into some more specific details about our server, specifically, how much memory we want allocated to it. How much do we need? It depends on the application. Memory is where your server stores data that it's going to either _write_ or _read_ from the database. Think of it like a waiting room for your CPU (central processing unit). The amount of memory you have is the _size_ of your waiting room. 

If your application has a lot of traffic—translating to user interactions resulting in read/write operations on your database—your memory will fill up as things "wait" to either go into the database or come out of the database. Just like a waiting room, data—or people—are only there _temporarily_. When the computer turns off (or the data is no longer required), it empties out. 

Contrast this with a hard drive which is meant as a more permanent storage: staying in the hospital for a few weeks because you "just had to see if I could jump my bike off the roof."

So, what does this translate to? We need to select memory size based on the relative amount of usage our application will have. If our application is only for external use or deployed to a small set of users, we can probably get away with a lower amount of memory like `192MB` or `396MB`. This is also a good size for our staging server which will only ever have a handful of people on it at once (trivial for memory). 

In terms of production application that's seeing regular usage, the `512MB` and up options are probably a better fit. Keep in mind, though, don't just max out your memory for the hell of it. Start with whatever you think the lowest possible amount you can use is and then monitor usage. One of your responsibilities as a Sworn Knight of Production is conserving resources as much as possible; yes, even you Silicon Valley cowpokes with your bottomless bank accounts. Every penny counts!

Okay, enough preaching. Last step...what is this dropdown? This is where we can configure the _location_ of our server. Wait...what? This deserves an explanation. When we say "server" in the Wild Wild Cloud West, what we _really_ mean is a _virtual_ server. As cool as it would be for a team of folks to board a golf cart and briskly install a rack mounted server into a cabinet every time we add a new server to our account...that's not quite what's happening here. Instead, we're actually spinning up a "virtual" server which is like running a computer within a computer but only using software. Huh? 

Think about how we start our servers locally. With Meteor, when we call `meteor` in our terminal (from within a Meteor project), our app is automatically booted on `localhost:3000`. That port, `3000` can be thought of as an "instance" or copy of our application. Locally, we only need one copy running at any given time because it's just us using it. When it comes to deploying our app into production, though, we potentially need to have several instances or "copies" running because a single copy of our application can only handle so much traffic. So...how does _that_ work?

<figure>
  <img src="https://slack-files.com/files-tmb/T02913KSV-F04FYTJ3F-e80b16b5b6/howmodulusworks-graphs-01-04_1024.png" alt="Diagram of how Modulus works.">
  <figcaption>Diagram of how Modulus handles incoming requests. <a href="https://modulus.desk.com/customer/portal/articles/1957328-how-modulus-works?b_id=9670">Source</a>.</figcaption>
</figure>

Load balancers! Hooray! A load balancer is basically a routing mechanism that takes user requests from a given domain (e.g. http://app.com) and [redirects each request to an _instance_ of our application](). Can you guess how that works? Ports! For each instance of our server we create, it's assigned to a port number. The load balancer, then, is responsible for routing traffic to a given port (running copy of our application). This quite literally spreads traffic out over a number of servers, effectively _balancing_ the load of requests hitting the application. How neat is that?! 

Okay...that all makes sense but weren't we talking about this dropdown and locations or something? Right. Long story short, this dropdown allows us to setup _where_ our virtual machines are spawned. The Matrix aside, the names here actually have significance. Each of the names in this list represent a **provider** and a **region** where that provider offers servers. When we say _provider_, we mean the company or service that's going to host our application. 

Wait, I thought _Modulus_ is going to host our application? In theory, yes. But, really what Modulus offers is more of a well-thought out, user friendly version of deploying our application to other services like Amazon EC2.

<div class="note">
  <h3>How Modulus Works</h3>
  <p>Remember how we mentioned that as an alternative early on and suggested only using those services if you understand the more complicated parts?  Modulus understands those parts and have created a service that removes the thinking for us when deploying to those services. Woof. It's a bit Inception-y, but it means making stuff like hosting a cloud application a lot easier for your average developer (like us). Don't feel silly. <em>I have no freaking clue either</em>, but it works.</p>
</div>

Okay! So after we push the magic "Create Project" button, we should have a shiny new Modulus server set up. Let's take a peek at setting up a database on Modulus so we can complete the circle of services needed to deploy our application.

#### Setting up a database on Modulus

Setting up a database is pretty easy. First we need to hop over to the "Databases" tab in our dashboard. Once we're there, we need to click the big ol' "Create Database" button in the top right. From there, we'll get a little modal action like this: 

<figure>
  <img src="http://cl.ly/image/2L0i0X2U3D2q/Image%202015-08-11%20at%2011.56.16%20PM.png" alt="Create Database modal on Modulus">
  <figcaption>Create database modal on Modulus.</figcaption>
</figure>

Same rules apply, albeit a little less complicated here. The label, username, and password behave how you'd expect. Keep the username and password _top secret_. If someone gets this, they get access to your database. For the region, this is an identical concept to creating our server. Pick the region that's closest to you/where your users will primarily be. If you're in New York, NY, for example, any of the "east" options will fit the bill. If you're international and don't see anything close to you, set this to the next closest thing (it should work just as well, too).

![Example connection strings on Modulus](http://cl.ly/image/1D1J1a1x1V3N/Image%202015-08-11%20at%2011.58.09%20PM.png)

Once we're up and running, we should be presented with some information about our database. Under that `Mongo URI` subheading we'll find our _connection string_. This is the MongoDB friendly URL we can use to _connect_ to our database. Let's see how we can make use of this along with some other details when configuring our server.

### Configuring a server
Okay, so, even though Modulus gets us down to the least possible amount of configuration necessary for spinning up a cloud-based server for our Meteor app (it's really quite impressive), we _do_ have to do a little bit of config. Don't hate. It's just as easy as everything else we've seen. A few form fields, a double check, and off to Mars we go.

#### Environment Variables
First up, we need to configure a handful of environment variables. Environment variables are like the normal variables you might use in your application, except that they're specific to the _server environment_ our application is in and are what the server uses to get our server ready for prime time when it boots. To set these—again, from our dashboard—we need to click on the project we set up earlier and then click on the "Administration" tab.

![Example of Environment Variables on Modulus](http://cl.ly/image/3M2w0B2C3A0A/Image%202015-08-12%20at%2012.21.45%20AM.png)

Once we're here, we need to scroll down a ways until we see a table labeled "Environment Variables." Here, we need to add a handful of settings. By default we'll only have one setting, `NODE_ENV` which is assigned a value of `production`. We can leave this as-is, but based on the name and it's value we can guess what this does. Remember when we talked about deploying to an environment earlier? This setting gives our server a way to identify as a specific environment. 

This particular value, `NODE_ENV` is mostly helpful for accessing in your application code when you need to limit certain functionality to a given environment (e.g. I only want to post tweets from the application in the `production` environment). Make sense? Let's look at the rest of our values and then get it all set up.

<table>
  <tr>
    <th>NODE_ENV</th>
    <td>production</td>
  </tr>
  <tr>
    <th>PORT</th>
    <td>The default port number our application will run on. For Modulus, this will be <code>8080</code>.</td>
  </tr>
  <tr>
    <th>ROOT_URL</th>
    <td>Our app's URL: either our Modulus domain up top or our custom domain like <code>http://bestappever.com</code>.</td>
  </tr>
  <tr>
    <th>MONGO_URL</th>
    <td><em>The connection string we got from Modulus earlier</em>, with our actual username and password replacing the <code>&lt;user&gt;</code> and <code>&lt;pass&gt;</code> placeholders after the <code>mongodb://</code> part.</td>
  </tr>
  <tr>
    <th>METEOR_SETTINGS</th>
    <td><code>{ "private": {}, "public": {} }</code></td>
  </tr>
</table>

Most of this should make sense. After our `NODE_ENV` setting, we pass a `PORT` number. This is the port number our application will need to run on in order for Modulus to properly route requests to it using its load balancer. We set this here so our application can reference it when it starts up. 

Next, we have our `ROOT_URL`. This is something that's specific to Meteor and used by the platform for generating absolute URLs in our application via the [`Meteor.absoluteUrl()`](http://docs.meteor.com/#/full/meteor_absoluteurl) method. Generally we won't use this in our own application, but a lot of the core packages _do_ make use of it, so we need to make sure it's set; otherwise we'll get an error when we try to boot our app.

This next one, `MONGO_URL` should make sense. Remember that connection string we got earlier after we set up our database? This is where it goes! The only thing to note is that we need to replace the `<user>` and `<pass>` placeholders at the beginning of the string with our actual username and password for the database. 

<div class="note danger">
  <h3>Protect your connection strings <i class="fa fa-frown-o"></i></h3>
  <p>A connection string with a username and password in it is like pure gold for anyone that wants to get access to your database. When it comes to handling these values, make sure to store them in a secure place and only let the people who absolutely need access to them to see them. <strong>This is not a joke; if this gets out, you run the risk of exposing all of your users/customers data, so <em>be careful</em>.</strong></p>
</div>

Ok! Last up is `METEOR_SETTINGS` and this one is optional depending on how you've built your app. If you've [used a settings.json file](http://themeteorchef.com/snippets/making-use-of-settings-json) in your application, this value will need to be set equal to the _contents_ of your `settings.json` file. This can be copy/pasted into the field, or, set via the Modulus CLI (we'll learn how to automate this in a bit).
 
Once we click "Save" beneath the list of environment variables, we've got our app configured! This should be all we need to do in order to successfully deploy our application. Real quick, we should make a nod to SSL and get an understanding for how it's set up on Modulus.

#### SSL
In order to keep our applications a little more secure, we can add [SSL]() to our application so that any data moving between the browser and our server is kept private with encryption. When you load up an app and see the URL turn green (or display a lock) and change the protocol from `http` to `https`, this signifies that an application is using SSL and that your connection to that application is secure.

Depending on the type of application we're running, we may need to add support for SSL. Any application that transmits any for of secure data for users (e.g. credit cards)—whether to a third-part service or otherwise—should use SSL. To "use" SSL, we need to acquire an SSL _certificate_ from a provider. We won't cover obtaining this here, but a quick search for SSL providers will turn up a lot of different options. My personal recommendation is [Namecheap](http://namecheap.com) as their basic certificates are relatively inexpensive and the turnaround time for getting a certificate is pretty quick (a few minutes to a few hours).

For now, let's focus on setting up an SSL certificate on Modulus. From the "Administration" tab of your project's dashboard, locate the Custom SSL block.

![Custom SSL block on Modulus](http://cl.ly/image/3m2A2m2w3K1M/Image%202015-08-12%20at%209.25.26%20AM.png)

When we click that red plus `+` symbol on the right, we'll be presented with a modal for inputting our "Private Key" and our "Certificate." The "Private Key" field is where you'll place the RSA Private Key you receive when you [generate a CSR](https://github.com/DavidWittman/csrgenerator.com). A CSR is what a certificate provider will require in order to _generate_ your certificate. In that second field, "Certificate" we'll paste in the SSL certificate we receive from our provider. Note: this isn't a "file" like you might expect it to be. Instead, this is just plain text with some specific formatting that the browser can recognize as an SSL certificate. Here's an example of what you might have:

<p class="block-header">Web Server Certificate</p>

```text
-----BEGIN CERTIFICATE-----
Areallylongstringofibberishthatisactuallyyoursslcertificate...
-----END CERTIFICATE-----
```

You might also have an "Intermediate CA" (some SSL providers will include this, some might not) which is similar to our Web Server Certificate:

<p class="block-header">Intermediate CA</p>

```text
-----BEGIN CERTIFICATE-----
Areallylongstringofibberishthatisactuallyyoursslcertificate...
-----END CERTIFICATE-----
```
Combined, these two form an SSL "chain" confirming the identity of your server, allowing connections to be served over SSL. Oof. Back on Modulus, if we have just a Web Server Certificate, we can paste this into our "Certificate" field. If we have _both_ a Web Server Certificate _and_ an Intermediate CA, we want to paste _both_ into the "Certificate" field; the Web Server Certificate first followed by the Intermediate CA. Something like this:

```text
-----BEGIN CERTIFICATE-----
This is our Web Server certificate.
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
This is our Intermedia CA certificate.
-----END CERTIFICATE-----
```
In case you're wondering, _yes_, you need to include the `-----BEGIN CERTIFICATE-----` and `-----END CERTIFICATE-----` lines in both certificates. **Paste these exactly as you receive them from your SSL provider**.

Once we have this in place, we should be all set on Modulus for SSL. 

<div class="note">
  <h3>The Force SSL Package</h3>
  <p>If we're using SSL in our app, we can ensure that our users are connecting via SSL by adding the <a href="https://atmospherejs.com/meteor/force-ssl">Force SSL</a> package by MDG. Once installed, this will make sure that any connections to your application are served over SSL by default (e.g. if a user attempted to connect to <code>http://app.com</code> they would be automatically redirected to <code>https://app.com</code>).</p>
</div>

### Deploying to Modulus
Okay! Finally! We've got our app configured, so now it's time to deploy. Back in our application directory in the terminal, all we have to run is:

<p class="block-header">Terminal</p>

```bash
modulus deploy
```
Nice, right? When we do this, we'll be prompted by Modulus to select the server we'd like to deploy to. If we only have one, we'll be given a simple prompt to enter `yes` like this:

![](http://cl.ly/image/090f3V2g2g3u/Image%202015-08-12%20at%209.53.57%20AM.png)

Once we enter `yes`, Modulus will get to work. You may notice something interesting. As soon as we do this, Modulus will display a message like `Meteor project detected...` and then display some information about determining a Meteor version and building your Meteor application. What's this? 

Remember earlier when we mentioned that Modulus technically only hosts Node.js apps and not Meteor apps as-is? This is where we make the connection. Here, Modulus is using a tool they've built called [Demeteorizer](https://github.com/onmodulus/demeteorizer) which converts our Meteor app into a vanilla Node.js app. Cool, eh? This functionality is built into the Modulus CLI by default, but we can install Demeteorizer on its own to convert our app for hosting on other PaaS providers, too!

![Example app running on Modulus](http://cl.ly/image/3T1p281I2Q01/Image%202015-08-12%20at%2010.11.30%20AM.png)

After a few seconds, our app will be deployed! If everything worked as expected, we should be able to point our browser to the URL for our application (e.g. `http://demo-femo-50073.onmodulus.net`) and see our application up and running. Hell yeah! 

Congratulations: you just deployed an application into production. Not as scary as you though, huh? Granted, this is just one piece of going into production, but it's a big one. From here, we can focus on improving the performance and security of our application trusting that all of that work has a place to live!

Before we part ways, we should chat about automating this process a bit.

#### Automating deployments
This is entirely optional but something that's really smart to consider when putting an application into production. When it comes to running an application for users and customers, we need to keep in mind that _things will change_. Hopefully that just means that we'll deploy new features or upgrade our app, but sometimes that can mean having to fix bugs and other problems that crop up in the day-to-day of running an application.

In light of this, it's smart to have a well-tuned workflow that we can rely on when it comes to deploying our application. Even though something like Modulus makes all of this fairly simple, there's still a small margin of error that crops up when we start to do things like host multiple servers on Modulus (e.g. staging vs. production) and [making use of a settings.json file](http://themeteorchef.com/snippets/making-use-of-settings-json). Automating all of this means thinking about all of the steps involved and baking them into a simple, easy-to-remember process that performs all of the steps _for us_.

To do this, we're going to rely on something we actually get from NPM (Node package manager): "scripts." [A tip](http://blog.differential.com/use-package-json-in-your-meteor-app-for-fun-profit/) picked up from [Gerard Sychay](https://twitter.com/hellogerard) over at [Differential](http://differential.io), we can use NPM scripts to automate our workflow a bit by creating short-hand commands that combine a longer string of shell commands. Confused? Let's take a look.

<p class="block-header">package.json</p>

```javascript
{
  "name": "application-name",
  "version": "1.0.0",
  "description": "Application description.",
  "scripts": {
    "start": "meteor --settings settings-development.json",
    "staging": "modulus env set METEOR_SETTINGS \"$(cat settings-development.json)\" -p 'Demo Femo - Staging' && modulus deploy -f -p 'Demo Femo - Staging'",
    "production": "modulus env set METEOR_SETTINGS \"$(cat settings-production.json)\" -p 'Demo Femo' && modulus deploy -f -p 'Demo Femo'"
  }
}
```
To use NPM scripts, we have to create a file called `package.json` in the root of our application. Here, we add a few items to describe our application (not 100% necessary but nice to have) and then we add an object called `"scripts"`. Inside, we store a handful of key/value pairs that represent commands we can call from the terminal when we're inside of our project directory. Notice that each command corresponds to a longer string of commands. Here, we've set up a few things to help us account for using multiple settings files and making sure that we deploy to the correct _environment_, either `staging` or `production`. 

Above, we only setup a single server so this may be a bit confusing. Recall back to the beginning of this recipe where we discussed having different environments. This is where something like this comes into play. Here, we're creating commands that point to one of those environments. For example, let's look at the value of our "production" script:

```javascript
"modulus env set METEOR_SETTINGS \"$(cat settings-production.json)\" -p 'Demo Femo' && modulus deploy -f -p 'Demo Femo'"
```
This one string is actually responsible for _two_ things:

1. Taking the contents of our `settings-production.json` file and setting it equal to the `METEOR_SETTINGS` environment variable that we looked at earlier (notice, Modulus gives us a handy command for doing this from the command line) for the specified project name.
2. Calls to `modulus deploy`, passing the name of the project we want to deploy to.

See what this does? When we call our `production` script, we'll automatically get our `METEOR_SETTINGS` value set to the current contents of the settings file we pass in `\"$(cat settings-production.json)\"` and then deploy our application. Not only does this save us a few keystrokes, but it protects us from doing things like using the wrong settings file or deploying to the wrong server. Further, this means that now we can call a single command to deploy our latest work into the specified environment. Okay! Sold! But how do we call it?

<p class="block-header">Terminal</p>

```bash
npm run production
```
When we call this, each of the commands in the string above will be called in order for us. Let's take a look at how it looks in action.

![NPM scripts running in production](http://cl.ly/image/0j2h0l162u3A/using-npm-scripts.gif)

So cool! Notice that this behaves just like us typing out each command ourselves. Again, the benefit being that we make sure the _right_ commands get called in the _right_ order each and every time. 

### Wrap Up & Summary
In this recipe we learned about taking a Meteor app into production using the Modulus PaaS. We learned about setting up a server on Modulus, setting up a database, and configuring our server to get our deployment up and running. We also learned how to deploy our project from the command line and automate the process of deployment using NPM scripts!