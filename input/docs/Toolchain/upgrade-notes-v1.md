
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
* Source code is now contained together with the pack it will be distributed with, in a logical layout consistent with the pack.
* The toolchain now supports a rudimentary plugin architecture.
* Three plugins have been written, each with their own NPM package.

| NPM Package                  | Purpose                                             |
| ---------------------------- | --------------------------------------------------- |
| `minecraft-addon-typescript` | Adds support for TypeScript                         |
| `minecraft-addon-browserify` | Adds support for multiple files                     |
| `minecraft-addon-terser`     | Obfuscates JavaScript in .mcpack and .mcaddon files |

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

# Final Changes
The default directory for outputs has changed, if you are using git to source control your projects, you should add `out/` to your `.gitignore` file in place of `built/`.