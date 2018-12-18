Description: Installing the tools necessary to start developing Bedrock Scripts
Published: 2018/12/16
Category: Beginning
Order: 1
Author: Steven Blom

---

About
===

You're about to embark on a journey of extending Minecraft to add functionality and content that the original creators had not imagined that the game would be used for.

On the 24th of October, the scripting documentation was released to the public for review, and the scripting API made available to a small focus group to test it out (versions 1.8.0.50 and 1.8.0.51), then on the 5th of December, the API was given to the public beta community.

It is now possible for anybody to participate in the beta and write your own scripts for minecraft, and this tutorial series is here to help get you started.

Where to go for help?
===
We are currently setting up a Discord server which will provide the recommended source for help in the future. In the short term, you may ask me questions on twitter ([AtomicBlom](http://twitter.com/AtomicBlom)), PM me for help in discord (AtomicBlom#3671), or open an issue on [GitHub](https://github.com/minecraft-addon-tools/minecraft-addon-tools.github.io/issues). I will answer as I have time.

Pre-requisites
===
In order to develop a script addon, you will need a number of things. At [minecraft-addon-tools](http://github.com/minecraft-addon-tools/), we provide additional tools to make it easier to make it easier and faster to make your addon.

The bare minimum you will need are the Mojang recommended requirements:

| Software    | Minimum                                                                                                        | Recommended                                                                                                                                                                                                     |
|-------------|----------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Code Editor | [Visual Studio Code](https://code.visualstudio.com/) or any plain-text editor                                  | [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/) with the following components installed: 'JavaScript diagnostics', 'JavaScript and TypeScript language support', 'Just-In-Time debugger' |
| Debugger    | N/A                                                                                                            | Visual Studio Community 2017                                                                                                                                                                                    |
| Minecraft   | [Minecraft on your Windows 10 device](https://www.microsoft.com/en-us/p/minecraft-for-windows-10/9nblggh2jhxj) | [Minecraft on your Windows 10 device](https://www.microsoft.com/en-us/p/minecraft-for-windows-10/9nblggh2jhxj)                                                                                                  |
| Other       | Vanilla Behavior Pack available from https://aka.ms/MinecraftBetaBehaviors                                     | Vanilla Behavior Pack available from https://aka.ms/MinecraftBetaBehaviors                                                                                                                                      |
| Storage     | 1.0 GB of free space for text editor, game, and scripts                                                        | 3.0 GB of free space for Visual Studio, game, and scripts                                                                                                                                                       |

To use the additional tools we provide, you will need some additional dependencies

| Software | Minimum | Recommended                  |
|----------|---------|------------------------------|
| Node JS  | 10.14.2 | The most recent 10.x release |

It's highly recommended that when you install Node JS, you leave the option "Add to PATH" selected.

As of the time of writing, the scripting API is in public Beta. That means that you will need to follow the Mojang guide to get signed up for the public beta. 

[https://minecraft.net/en-us/article/how-get-minecraft-betas](https://minecraft.net/en-us/article/how-get-minecraft-betas)


Creating your first Addon
===
Now that you have all the tools installed, it's time to create your first addon.

To get started quickly, we'll be using [generator-minecraft-addon](https://github.com/minecraft-addon-tools/generator-minecraft-addon), which is a generator for [yeoman](http://yeoman.io), which itself is a tool made for quickly putting together a project.

Start by installing both of these tools globally.
```powershell
# install yeoman
npm install --global yo
# install the minecraft addon generator
npm install --global generator-minecraft-addon
```

With these installed, we'll need a directory on your computer to develop your code in, so create a directory and enter it, for the sake of this tutorial, we'll be using `C:\Dev\Minecraft`

```powershell
mkdir \Dev\Minecraft
cd \Dev\Minecraft
```

Now we can create our addon. `generator-minecraft-addon` will ask you a number of questions about your addon and how you wish to develop it, this information will be used to both structure the addon directories and create the various files that Minecraft will use to identify the addon.

```powershell
yo minecraft-addon
```
| Prompt                                                                | Description                                                                                                   | Answer                                      |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|---------------------------------------------|
| `What will be the name of your addon?`                                | This will be the name displayed in Minecraft when players select your add-on                                  | `Getting Started`                           |
| `What will your addon do or provide?`                                 | This will be the description displayed in Minecraft when the player selects your add-on                       | `Demonstrate a very basic Minecraft Add-on` |
| `What namespace will you use?`                                        | The namespace helps to separate your add-on's functionality from other addons so they do not collide          | `gettingstarted`                            |
| `What kind of modules will make up the addon? (Behaviors, Resources)` | This warrants a full explanation, see directly below                                                          | `Behaviors`                                 |
| `Will you be adding scripts?`                                         | It's possible to create an addon that does not use scripts, however we will be using scripts for this series. | `Yes`                                       |
| `What language do you want to script in?`                             | This warrants a full explanation, see directly below                                                          | `JavaScript`                                |

An example of the output should look like this:
![Example output](/assets/Tutorials/GettingStarted_ExampleGeneratorOutput.png)

Behaviors and Resources
---
Minecraft addons are made up of two main concepts:
* **Behavior packs** which modify the way the game behaves.
* **Resource packs** which modify the how the game looks.

Both types of pack can modify existing (vanilla) content, or add new content in addition to the existing content.

For the sake of this first tutorial, we do not need to change the looks, we simply want to test out and make sure that you are configured to run a script.

*The use of the term `behavior` follows US spelling, in order to save on confusion it will always be referred to in the same spelling as in the official documentation in order to avoid something breaking because of the spelling being wrong*

Scripting Languages
---
When setting up your addon, you will have been presented with at least two options, JavaScript and TypeScript.

JavaScript is the add-on scripting language. It is a very widely used language, most commonly used in your web browser. It has been around in various forms since May 1995, with many new revisions and additions made to the language in the years since. At the time of writing, Minecraft supports the features that were introduced in EcmaScript 5.1 (EcmaScript being the specification for JavaScript versions).

Despite it's ubiquity, it does have a reputation in some circles for being very easy to write code that is buggy or doesn't work at all. To help with that, 
[minecraft-addon-tools](http://github.com/minecraft-addon-tools/) provides definitions for a language called TypeScript. The best description I've read about can be found on Stack Overflow ([What is TypeScript and why would I use it in place of JavaScript](https://stackoverflow.com/questions/12694530/what-is-typescript-and-why-would-i-use-it-in-place-of-javascript/35048303#35048303)): `TypeScript is modern JavaScript + types. It's about catching bugs early and making you a more efficient developer, while at the same time leveraging the JavaScript community.`

TypeScript is converted into JavaScript, and although it helps to write less buggy code, it does add an additional layer of complexity that I would like to avoid for these tutorials.

You can expect that resources from Mojang and other places around the community will be written in JavaScript.

A quick tour of your new add-on
===
Now that your addon has been created, Open your IDE in the newly created directory `C:\Dev\Minecraft\GettingStarted`

The project has been layed out according to the expected layout of the [minecraft-scripting-toolchain](https://github.com/minecraft-addon-tools/minecraft-scripting-toolchain) package that we provide.

The toolchain provides the following features:
* Adds support for languages other than JavaScript
* Creates a directory in the `.mcaddon` format.
* Installs the mod into Minecraft
* Watches for changes to files and automatically updates.

We'll use the toolchain in a moment, but first let's look at each file and directory in the project and what it's for.

| Path                         | Purpose                                                                                                                                                                                                                      |
|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| node_modules/                | This is where `minecraft-scripting-toolchain` and it's dependencies live.                                                                                                                                                    |
| src/                         | The files that make up your addon will be somewhere under this directory.                                                                                                                                                    |
| src/behaviors/               | This is where the files that make up the behavior pack will live. This does not include scripts, they are currently stored separately                                                                                        |
| src/behaviors/manifest.json  | This file is used by Minecraft to identify the behavior pack for your add-on, It provides the name and description for users to see                                                                                          |
| src/behaviors/pack_icon.png  | This icon is used to identify your addon. You should change this as soon as possible                                                                                                                                         |
| src/scripts/client/          | This is where scripts that need to run on your computer will live.                                                                                                                                                           |
| src/scripts/client/client.js | This is the example client side script. It has some basic code, but does not do anything.                                                                                                                                    |
| src/scripts/server/          | Most of the game logic happens on the server, which could be your computer, a friend's computer or a dedicated server, and this is where those server-side scripts will live.                                                |
| src/scripts/server/server.js | This is the example server side script. It has some basic code, but does not do anything.                                                                                                                                    |
| gulpfile.js                  | This file is part of the `minecraft-scripting-toolchain`, which is built on top of a system called `gulp`. It is highly configurable, but the version that is provided by `generator-minecraft-addon` starts off very basic. |
| package-lock.js              | This is used by `npm`, part of Node JS to keep track of `minecraft-scripting-toolchain`'s version and dependencies.                                                                                                          |
| package.js                   | This is used by `npm`, part of Node JS to define what version of `minecraft-scripting-toolchain` and the commands you can run that make development of your addon easier.                                                    |

If we had created the addon with resources, there would have been three additional entries.

| Path                        | Purpose                                                                                                                             |
|-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| src/resources/              | This is where the files that make up the resource pack will live.                                                                   |
| src/resources/manifest.json | This file is used by Minecraft to identify the resource pack for your add-on, It provides the name and description for users to see |
| src/resources/pack_icon.png | This is the same icon that is used in your behavior, and should also be changed as soon as possible                                 |

Now that we've explored the files, let's try installing it into minecraft and we'll make sure that everything is running okay.

Running the add-on
===
Start by making sure you have a command prompt open, and in the directory of your mod. (If you've been following this tutorial exactly, it will be in `C:\Dev\Minecraft\GettingStarted`)

The simplest way to get your mod into minecraft is to run
```powershell
npm run installmod
```

The output should look similar to the following:

![installmod output](/assets/Tutorials/GettingStarted_InstallModOutput.png)

With that complete, we can now load Minecraft and test it out, here's the steps:
1. Launch Minecraft
2. Select `Play`
3. Under `Worlds`, Select `Create New`
4. Select `Create new World`
5. On the Left-hand side of the screen, under `Add-Ons`, select `Behavior Packs`
   * You should see "Getting started" as an option. 
6. Click on "Getting Started"
7. Select the `+` button to add it to your world.
8. You will be prompted to disable achievements which you must accept.
9. On the Left-hand side of the screen, under `Edit Settings`, select `Game`
10. Scroll down and turn on `Use Experimental Gameplay`
11. confirm you wish to change this setting.
12. Give your world a name if you wish
13. Press the `Create` button to enter the world.


Because we are running with features that are not available to the general public, we need to enable experimental mode.

When the scripting API is released to the public, this will not be necessary.

If all goes well, you will be presented with a warning to enable scripts. This is an indication that Minecraft has found the scripts and is about to load them.

![Scripts found](/assets/Tutorials/GettingStarted_ScriptsFound.png)

Hit the `Enter World` button and... you'll be in the world, but nothing exciting will have happened.

Modifying the client script to test out scripting
=
Introduction to the client.js script
-
Ok, we have an indication that scripts are running, but how can we prove it to ourselves?

First, in your IDE, open client.js, the current code should look like this:
```javascript
var clientSystem = client.registerSystem(0, 0);

// Setup which events to listen for
clientSystem.initialize = function () {
    // set up your listenToEvents and register client-side components here.
}

// per-tick updates
clientSystem.update = function() {
    // Any logic that needs to happen every tick on the client.
}
```

There's not a lot going on in this file. Everything starting with `//` is a comment and simply serves to describe what is going on.

The first line registers a new system with the Minecraft Client. `client` is a global variable provided by Minecraft and only has a couple of functions we can call.

`registerSystem` asks Minecraft to provide a system with the specified API version `0, 0`. This provides backwards compatibility so that your addon is more likely to run on newer versions of Minecraft without any changes.

The object it returns, the system, is tied to the Minecraft client itself and provides a number of functions you can call on it to interact with Minecraft. It also provides a number of extension points that Minecraft will automatically call if you define. Two of them are pre-defined for you: `initialize`, and `update`. 

`initialize` is called only once when Minecraft has finished loading everything it needs to before it can start running script code.

`update` is called every game tick. A game tick is one cycle of the game logic, which happens twenty times per second.

Automatically updating the code
-
Let's run the `minecraft-scripting-toolchain` in watch mode make it easier to update our code in Minecraft so we don't have to run `npm run installmod` every time we want to test something.

In your console terminal, run

```powershell
npm run watch
```

It should follow the same steps as `npm run installmod`, however it doesn't exit. It hangs on 'watchFiles' and doesn't exit.

The toolchain is now watching for changes you make, and will automatically build the add-on, and install it into Minecraft every time you save a file. It will then resume watching for more changes.

![watch output](/assets/Tutorials/GettingStarted_Watch.png)

You can now leave your command line in the background and it will update as necessary.

Hello World
-
For this tutorial, let's greet ourselves when we enter the game. We can do this by broadcasting an event to Minecraft, and this will be covered in much greater detail in the second tutorial, but for now, let's update the `initialize` function and call into minecraft with the `minecraft:display_chat_event` event and a message to get it to display a message in the chat log when the world is loaded.

```javascript
// Setup which events to listen for
clientSystem.initialize = function () {
    // set up your listenToEvents and register client-side components here.
    clientSystem.broadcastEvent("minecraft:display_chat_event", "Hello World!");
}
```

When you save the file, `npm run watch` should have detected the change, built the addon and installed it into Minecraft. Unfortunately Minecraft does not have a way to reload scripts, you will need to exit out of the world (not the game, just the world) and enter the world again.

Once you've done this though, you should be able to open the chat log (`t` by default), and it should have a message for you:

![watch output](/assets/Tutorials/GettingStarted_DisplayChatEventResult.png)

Where to Next?
=

Congratulations! If you've made it this far, you have completed the basics of getting up and running to know the tools that will help you build your Add-ons.

Next we'll take a short look at events and the client/server communications.