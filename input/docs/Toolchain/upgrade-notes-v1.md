Title: Upgrading from minecraft-scripting-toolchain to minecraft-addon-toolchain v1
Published: 2019/01/29
Category: Toolchain
Author: Steven Blom

# Justification for change

For version 1.0 of the minecraft-scripting-toolchain, the build process was completely changed in order to better reflect
the capabilities of Minecraft's Addon System, as such it was worth making note of certain limitations that Minecraft and the Toolchain had.

* Minecraft can only load a single file
* The toolchain assumed that a Minecraft Addon was limited to a single resource pack and a behaviour pack. This is not the case.
* The toolchain seperated files in a way that made sense for the toolchain and assumed a single behaviour pack.
* The toolchain assumed that the user was running Windows.

# Benefits
The toolchain was extended and changed to fix these issues.

* The focus is now on multiple packs as part of a larger addon. 
* The name of the package has changed from `minecraft-scripting-toolchain` to `minecraft-addon-toolchain`
* The toolchain attempts to handle MacOS X, Linux and Android as additional platforms.
* The toolchain can now create .mcpack and .mcaddon files
* Source code is now contained together with the pack it will be distributed with, in a logical layout consistent with the pack.
* The toolchain now supports a rudimentary plugin architecture.
* Three plugins have been written, each with their own NPM package.

| NPM Package                            | Purpose                                                                                                                 |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| `minecraft-addon-toolchain-typescript` | Adds support for TypeScript                                                                                             |
| `minecraft-addon-toolchain-browserify` | Adds support for multiple files                                                                                         |
| `minecraft-addon-toolchain-terser`     | Obfuscates JavaScript in .mcpack and .mcaddon files                                                                     |
| `minecraft-addon-toolchain-webpack`    | An alternative to browserify for those more familiar with webpack, by [Destruc7i0n](https://twitter.com/TheDestruc7i0n) |

# Beginning the upgrade
A few directories changed names to better reflect the idea that an addon is made up of one or more packs. Because of this, it does not make sense to have the scripts as a top-level directory as it no longer reflects the pack it belongs to. Instead, scripts belong inside of their respective behaviour pack.

So to begin upgrading your project, rename the `./src` directory to `./packs`.

Next, inside of `./packs`, move the `./packs/script` directory to be underneath your behaviour pack.

you should have a directory structure that looks like this:
* packs
  * resources
    * `manifest.json`
    * `pack_icon.png`
  * behaviors
    * `manifest.json`
    * `pack_icon.png`
    * scripts
      * client
        * `client.js`
      * server
        * `server.js`

You may have additional files and directories as needed.

If you do not like the name for resources and behaviours, you may now rename them. They will be placed in the
correct directory when installing by examining the `manifest.json` file to determine if it is a behaviour or resource pack.

The next steps depend on whether you are using JavaScript or TypeScript.

# Updating a pure JavaScript add-on
To upgrade a JavaScript project from `minecraft-scripting-toolchain` to `minecraft-addon-toolchain`, version 1.0 perform the following steps.

Start by uninstalling the old toolchain and installing the new one.
It is highly recommended to also add the `minecraft-addon-toolchain-browserify` plugin to support splitting your project up into multiple files.

```powershell
npm remove minecraft-scripting-toolchain
npm install --save-dev minecraft-addon-toolchain
```

You will need to edit the `gulpfile.js` to reflect the new changes
```javascript
const MinecraftAddonBuilder = require("minecraft-addon-toolchain/v1");
const BrowserifySupport = require("minecraft-addon-toolchain-browserify");

const builder = new MinecraftAddonBuilder("<youraddonname>");
builder.addPlugin(new BrowserifySupport());

module.exports = builder.configureEverythingForMe();
```

# Upgrading a TypeScript add-on
To upgrade a TypeScript project from `minecraft-scripting-toolchain` to `minecraft-addon-toolchain`, version 1.0 perform the following steps.

Start by uninstalling the old toolchain, gulp-typescript and typescript and install the new one and the `minecraft-addon-toolchain-typescript` plugin.
It is highly recommended to also add the `minecraft-addon-toolchain-browserify` plugin to support splitting your project up into multiple files.

```powershell
npm remove minecraft-scripting-toolchain
npm install --save-dev minecraft-addon-toolchain
npm install --save-dev minecraft-addon-toolchain-typescript
npm install --save-dev minecraft-addon-toolchain-browserify
```

Now update the `gulpfile.js` to reflect the new changes.

Ensure that BrowserifySupport comes after TypeScript support, otherwise it may not work as intended.

```javascript
const MinecraftAddonBuilder = require("minecraft-addon-toolchain/v1");
const TypeScriptSupport = require("minecraft-addon-toolchain-typescript");
const BrowserifySupport = require("minecraft-addon-toolchain-browserify");

const builder = new MinecraftAddonBuilder("<youraddonname>");
builder.addPlugin(new TypeScriptSupport());
builder.addPlugin(new BrowserifySupport());

module.exports = builder.configureEverythingForMe();
```

# Plugins
If you need to extend the toolchain beyond what it provides by default (for this example, we'll use webpack. as mentioned above, there is now a dedicated webpack npm package you can bring in), you should implement a plugin.

Provided here is the description of a plugin
```TypeScript
interface IPlugin {
    //Use a property setter to verify version, etc.
    browser: MinecraftAddonBuilder;

    // Runs when the source code is being transformed and copied.
    sourceTasks?: ITask[];
    // Runs when behaviour packs are being installed into the Minecraft development_behavior_packs directory
    installBehaviorTasks?: ITask[];
    // Runs when behaviour packs are being installed into the Minecraft development_resource_packs directory
    installResourceTasks?: ITask[];
    // Runs when .mcpack files are being generated
    createMCPackTasks?: ITask[];
    // Runs when the .mcaddon file is being generated
    createMCAddOnTasks?: ITask[];
    // Allows you to modify and extend the gulp tasks before the main tasks that can be executed are constructed
    addDefaultTasks?: (gulpTasks: any) => void;
}

interface ITask {
    // a file match using the normal globbing format, for example, matching all files would be "**/*"
    condition: string | RegExp;
    // prevents a matched file from being written to the bundled directory, useful for redirecting files for intermediate processing.
    preventDefault?: boolean;
    // a factory method that returns a step to be executed in a gulp pipeline stream. 
    task: () => NodeJS.ReadWriteStream
}
```

One change between the older system and the new one is that there is no separate build step for .js files, (or whatever you changed the source file specification to be.)
Instead, all files in a pack are run through the same pipeline, and are transformed if they meet the condition of a task.

Here is an example of a v0 `minecraft-scripting-toolchain` extension.

```JavaScript
const compiler = require("webpack")
const webpack = require("webpack-stream")
const ModBuilder = require("minecraft-scripting-toolchain")

let builder = new ModBuilder("<youraddonname>");

builder.scriptTasks = [
  () => webpack({
    mode: 'production',
    entry: {
      client: './src/scripts/client/client.js',
      server: './src/scripts/server/server.js'
    },
    output: {
      filename: '[name]/[name].js'
    },
    module: {
      rules: [
        {
          test: /\.js$/,
          use: {
            loader: 'babel-loader',
            options: {
              presets: ['@babel/preset-env']
            }
          }
        }
      ]
    }
  })
]

module.exports = builder.configureEverythingForMe();
```

The equivalent as a plugin would be:

```JavaScript
const compiler = require("webpack")
const webpack = require("webpack-stream")
const MinecraftAddonBuilder = require("minecraft-addon-toolchain/v1");

const builder = new MinecraftAddonBuilder("<youraddonname>");
builder.addPlugin({
    sourceTasks: [
        {
            condition: "**/*.js",
            task: () => webpack({
                mode: 'production',
                entry: {
                    client: './src/scripts/client/client.js',
                    server: './src/scripts/server/server.js'
                },
                output: {
                    filename: '[name]/[name].js'
                },
                module: {
                rules: [
                    {
                        test: /\.js$/,
                        use: {
                            loader: 'babel-loader',
                            options: {
                            presets: ['@babel/preset-env']
                            }
                        }
                    }
                ]
                }
            })
        }
    ]
})

module.exports = builder.configureEverythingForMe();

```

# Final Changes
The default directory for outputs has changed, if you are using git to source control your projects, you should add `out/` to your `.gitignore` file in place of `built/`.