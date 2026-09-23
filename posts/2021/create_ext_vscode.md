---
title: "Create Extension for Visual Studio Code"
author: "Jim Carr"
date: "2021-11-12"
---
(This is a follow-up to a question asked during my recent Visual Studio Code talk)

Visual Studio Code already has an [extensive library of extensions](https://marketplace.visualstudio.com/VSCode). But, if you’d like to create your own, it’s not difficult to get started. These instructions are for Linux, but it should be easy to adapt them to another operating system.

## Prerequisites

If they aren’t already installed, you need to install the following:

* [Node.js](https://nodejs.org/en/)
* [Git](https://git-scm.com/)

Then, open a terminal, and install Yeoman and VS Code Generator:

```bash
sudo npm install -g yo generator-code
```

## Create a Project

Create a new project directory (e.g., “new_extension”) and cd into it:

```bash
mkdir new_extension
 
cd new_extension
```

Run Yeoman to generate a project:

```bash
yo code
```

I answered the project generator questions as follows:

* Project type: New Extension (TypeScript)
* What’s the name of your extension? HelloWorld
* What’s the identifier of your extension? helloworld
* What’s the description of your extension? (I left this blank)
* Initialize a git repository? Yes
* Bundle the source code with webpack? No
* Which package manager to use? npm

(Project is generated)

* Do you want to open the new folder with Visual Studio Code? Open with 'code'

## Running the Project

Inside the editor, press F5. This will compile and run the extension in a new Extension Development Host window. (A new instance of Visual Studio Code)

Once the new instance starts up, press F1 to display the command palette. Start typing “Hello World” in the command palette, and when you see the “Hello World” extension match, press [ENTER] to run it.

“Hello World from HelloWorld!” will display in a toast message.

Close 2nd instance of VS Code.

## Make Changes To Project

To make a simple change to the extension:

1. Open src/extension.ts.

2. Change the message text in the call to showInformationMessage to “Hello VS Code”:

```typescript
let disposable = vscode.commands.registerCommand('helloworld.helloWorld', () => {
	// The code you place here will be executed every time your command is executed
	// Display a message box to the user
	vscode.window.showInformationMessage('Hello VS Code');
});
```

3. Press [F5] to run the project, then run the “Hello World” extension in the new instance, again.

You should see the new “Hello VS Code” text in the toast message.

## Next Steps

This has been a simple walkthrough. For more information, please refer to [this article](https://code.visualstudio.com/api/get-started/your-first-extension), which is what I followed.
