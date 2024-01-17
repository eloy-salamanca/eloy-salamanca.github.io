---
layout: post
title: "Visual Studio Tips and Tricks"
description: "Tips and Tricks for Visual Studio users"
categories: [ PowerShell, Visual Studio ]
image: assets/img/2018/20.3.png
featured: false
author: eloy
comments: true 
---

[Visual Studio Code](https://code.visualstudio.com/){:target="_blank"} is a free and fast code editor for web and cloud applications: JSON, PowerShell snippets, etc. I’ve been using it for a while, and I must say I can’t code without it now!

Here, some insights very useful to me:


## Setting up environment

### Command Line Interface (CLI)

Visual Studio Code comes with a powerful command line interface, so you can open or diff files, install extensions, change display language, output diagnostics and more even from the outside

![Command Line Interface]({{site.baseurl}}/assets/img/2018/20.1.png)

### Using Integrated Terminal

Initially at the root of workspace, with this small **Integrated Terminal** you won't have to switch windows if you don't want to; I have everything I need to code and debug again and again very quickly from here:

![Integrated Terminal]({{site.baseurl}}/assets/img/2018/20.2.png)

### Recommended plugins

- **PowerShell**
- **Azure Resource Manager Tools** > Unlocaks IntelliSense and debugging support for Resource Manager templates.
- **Azure Account** > Simplifies VSCode Azure authentication and session management
- **Azure Resource Manager Snippets** > Constains a library of handy JSON code segments to make your ARM template development easier

{% include advertising/gl-adsense.html %}

## Appearance

### Make vscode transparent

- As you might have notice, my vscode env is transparent, to read docs, internet searches etc in the background without having to move any window, and this is thanks to extension: **GlassIt-VSC**

- Another cool alternative, if you are running into issues with the previous plugin > **Windows Opacity**. Look at **File** > **Preferences** > **Settings** and change to 220 as my recommended setting for this plugin.

![Make vscode transparent]({{site.baseurl}}/assets/img/2018/20.3.png)

### Changing default theme

There're tons of code themes to personalize yours; only had to go to [vscode marketplace](https://marketplace.visualstudio.com/search?target=VSCode&category=Themes&sortBy=Downloads){:target="_blank"} and have a look; for example one of the ones I do prefer most: **Cobalt2**

{% include advertising/gl-adsense.html %}

## Keyboard Shortcuts

I really love use of keyboard instead of mouse; for me, it's very nice not having to raise hands from it as much as possible, and Visual Studio is powerful for this.
Here some very common shortcuts a use a lot:

### Single Selections

* Select one letter at a time: `<SHIFT>` + `right` or `left` arrow key
* Select one word at a time: `<SHIFT>` + `<CTRL>` + arrow keys
* Select to end of line: `<SHIFT>` + `<END>` key

### Multiple Selection (multi-cursor)

* Add secondary cursor (thinner): `<ALT>` + `Click`
* Select multiple lines: Put the cursor where you want to start on, press `<SHIFT>` + `<ALT>` keys, then arrow keys to move on and select the snippet you need.
* Select all next occurrence of the current selection: `<CTRL>` + `D`

### Multiples Lines Edit

`<CTRL>`+`<SHIFT>`+`<ALT>`+`<DOWN>`: Column Select Down
`<CTRL>`+`<SHIFT>`+`<ALT>`+`<Up>`: Column Select Up
`<CTRL>`+`<SHIFT>`+`<ALT>`+`<Left>`: Column Select Left
`<CTRL>`+`<SHIFT>`+`<ALT>`+`<Right>`: Column Select Right
`<CTRL>`+`<SHIFT>`+`<ALT>`+`<PgDown>`: Column Select Page Down
`<CTRL>`+`<SHIFT>`+`<ALT>`+`<PgUp>`: Column Select Page Up

---

## Treating with large text files

Sometimes I had to tune different text files to get them ready to upload/modify large amount of data, mainly in Active Directory or SQL Server dbs, so dropping here some shortcuts:

### How to remove empty lines

1. Click Ctrl-H (quick replace)
2. Tick “Use Regular Expressions”
3. In Find specfiy ^$\n
4. In Replace box delete everything
5. Click “Replace All”

![Treating with large text files]({{site.baseurl}}/assets/img/2018/20.4.png)

{% include advertising/gl-adsense.html %}

## Conclusions

I know it's not very easy to move on from your favorite code editor to a new editor, but when it comes to **Visual Studio**, I'm very happy because I didn't miss anything on the way on and gain a lot of new features/facilities, so I recommend it everyone to keep on the track
