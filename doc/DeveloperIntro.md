# MVBasic Extension New Developer Guide

![MV Logo](screenshots/mvlogo.png)

# Table of Contents
- [MVBasic Extension New Developer Guide](#mvbasic-extension-new-developer-guide)
- [Table of Contents](#table-of-contents)
- [0. Code of conduct](#0-code-of-conduct)
- [1. Introduction](#1-introduction)
- [2. Installing](#2-installing)
  - [2.1 Install the prerequisites](#21-install-the-prerequisites)
  - [2.2 Option 1 - Clone the repository directly](#22-option-1---clone-the-repository-directly)
  - [2.3 Start the extension and finish development setup](#23-start-the-extension-and-finish-development-setup)
- [3. Running in debug mode](#3-running-in-debug-mode)
- [4. Syntax definitions / theming](#4-syntax-definitions--theming)
  - [4.1 Resources](#41-resources)
  - [4.2 Changing/Adding Scopes](#42-changingadding-scopes)
  - [4.3 Overriding theme color choices](#43-overriding-theme-color-choices)
- [5. Formatting](#5-formatting)
- [6. Code validation/Linting](#6-code-validationlinting)
- [7. Building a custom version of the extension](#7-building-a-custom-version-of-the-extension)
- [8. Contributing back / creating a pull request](#8-contributing-back--creating-a-pull-request)
  - [8.1 Option 2 - Fork and Clone the MV Extensions Repo](#81-option-2---fork-and-clone-the-mv-extensions-repo)
    - [8.1.1 Fork the MV Extensions Repo](#811-fork-the-mv-extensions-repo)
    - [8.1.2 Clone the repository from a fork](#812-clone-the-repository-from-a-fork)
    - [8.1.3 Connecting to upstream](#813-connecting-to-upstream)
    - [8.1.4 Create a feature branch](#814-create-a-feature-branch)
    - [8.1.5 Creating a pull request](#815-creating-a-pull-request)

---
# 0. Code of conduct

Refer to the code of conduct at:

https://github.com/ianmcgowan/mvbasic/blob/add-code-of-conduct-1/CODE_OF_CONDUCT.md

# 1. Introduction

This guide is targeted at an MV Developer who is comfortable with MV Basic and happily using the MV Basic Extension, but would like
to tweak the extension or add new features (and hopefully contribute them back to the project, though that's not a requirement).

The guide will walk through the process of getting the source code for the extension from github, getting everything setup to run in
debug mode, running for the first time, various examples of making minor changes, and finally how to contribute your changes back
with a pull request.

Screenshots of this process will be done from Windows, but everything can be done from Mac/Linux just as easily.  The MV Gateway is
the only part that is not cross-platform, and it's not a hard requirement for using the extension to turbo-charge your MV code
editing.

[(top)](#table-of-contents)
# 2. Installing

## 2.1 Install the prerequisites

Before starting you should have the following installed and working:

* [VSCode](https://code.visualstudio.com/download)
* [Git](https://git-scm.com/downloads)
* [Nodejs](https://nodejs.org/en/download/)

Refer to the individual documentation for detailed instructions, but a basic install is fine.  When the installs are complete you
should be able to successfully run these commands from a shell (versions will be later of course):

```
src>code --version
1.40.2
f359dd69833dd8800b54d458f6d37ab7c78df520
x64

src>git --version
git version 2.14.3.windows.1

src>node --version
v10.16.3
```

## 2.2 Option 1 - Clone the repository directly

This is the simple approach, and less confusing when getting started.  We are going to directly "clone" the mvextensions/mvbasic
repo to our local machine so we have a copy of the source code to work with.

```
c:\>cd src

src>git clone https://github.com/mvextensions/mvbasic.git
```

This will create a new folder, mvbasic, with a complete copy of the repo from github.  This is a fully functional repository.
However, without contributor rights on the remote repo, local changes cannot be pushed back to github.  This is fine for starting
out.

## 2.3 Start the extension and finish development setup

Since we're right there at the prompt, start VSCode and tell it to open the directory we just cloned.

```
src>code mvbasic
```

After VSCode starts up, open a terminal (ctrl+` on windows, or view: toggle integrated terminal from the command palette), and type:

```
npm install
```

![npminstall](screenshots/devguide/npminstall.png)

The list of ten packages the extension directly depends on is specified in the top-level package.json.  Running "npm ls" from the
command prompt will show the tree of recursive package dependencies.

After churning for a while, downloading, extracting and installing hundrds of packages will complete:

![npminstall2](screenshots/devguide/npminstall2.png)

[(top)](#table-of-contents)

# 3. Running in debug mode

From inside the instance of VSCode, hit F5 or from the command palette choose ```Debug: Start Debugging```

This will start a new instance of VSCode running in debug mode, connected back to the controlling instance.  Any changes to the
extension will be made in the controlling instance, and tested in the debug instance.  After making a change, ctrl+shift+F5 or
Debug: Restart from the command palette to restart the debug instance and make it aware of the changes.

[(top)](#table-of-contents)
# 4. Syntax definitions / theming

## 4.1 Resources

An introduction to how syntax highlights work in VSCode: https://code.visualstudio.com/api/language-extensions/syntax-highlight-guide

A description of the various "scopes" typically used: https://macromates.com/manual/en/language_grammars#naming_conventions

Using "Developer: Inspect TM Scopes" to see how a particular token is interpreted.  This will show both the source as well as the scope for any language elements selected.

![Dev Inspect Scopes](screenshots/devguide/dev_inspect_scopes.gif)

## 4.2 Changing/Adding Scopes

Let's say we want a particular kind of comment to show up in a hard-to-ignore color.  Theme color choices are applied by matching
the scope rules from the language format selected.  Themes typically target broad scopes like "comment" or "comment.line", but we
can define a more narrow scope by editing mvon.tmLanguage.json, and moving REM to a new scope:

From:
```
{
  "match": "(?i)(^[\\*|!].*|^\\s+[\\*|!].*|^\\s*REM\\s.*|\\;[\\*|!].*|\\;\\s+[\\*|!].*)",
  "name": "comment.line.asterisk.mvon"
},
```

To:
```
{
  "match": "(?i)(^[\\*|!].*|^\\s+[\\*|!].*|\\;[\\*|!].*|\\;\\s+[\\*|!].*)",
  "name": "comment.line.asterisk.mvon"
},

{
  "match": "(?i)(^\\s*REM\\s.*)",
  "name": "comment.line.rem.mvon"
},
```

Which moves the regular expression matching REM at the start of a line into a new scope.

## 4.3 Overriding theme color choices

Now that we've added a new scope, we can target this in our workspace settings:

```
    "editor.tokenColorCustomizations": {
        "textMateRules": [
        {
            "scope":"comment.line.rem.mvon",
            "settings": {
                "foreground": "#FF007F"
            }
        }
    ]
    },
```

![Comment Theme Example](screenshots/devguide/comment_theming.png)

[(top)](#table-of-contents)
# 5. Formatting

Formatting is done in javascript code in client/src/extension.ts provideDocumentFormattingEdits.

[(top)](#table-of-contents)
# 6. Code validation/Linting

Code validation is done from javascript code in server/src/server.ts validateTextDocument.

[(top)](#table-of-contents)
# 7. Building a custom version of the extension

[(top)](#table-of-contents)
# 8. Contributing back / creating a pull request

The simple approach of directly cloning the mvextensions/mvbasic repo from github is good to quickly get your hands on the code and
see how the extension is put together.  To make changes and contribute them back to the project with a pull request is more
involved, and requires first forking the repo.

## 8.1 Option 2 - Fork and Clone the MV Extensions Repo

If you are making more than minor edits, it's a good idea to first fork the project repository on Github.  This is an optional step,
and not required unless you plan on submitting changes back to MV Extensions.

Forking will allow two things:

1) You will be able to push any commits made in your local cloned copy of the repo to your fork on github.
2) You will be able to create pull requests in github to allow the mvextensions/mvbasic repo to "pull" those commits into the
   official repo.

### 8.1.1 Fork the MV Extensions Repo

Forking basically means taking a personal copy, while maintaining links to the original project so any future changes can be synced.
At the same time, local changes you make can be pushed to your new fork of the project, since it is yours and yours alone.

To fork the repo from github, click on the fork button to the top right:

![fork](screenshots/devguide/fork.png)

After a few seconds of churning, you will be taken to your brand new repo, that is an exact copy of the MV Extensions repo:

![forkresult](screenshots/devguide/forkresults.png)

### 8.1.2 Clone the repository from a fork

After creating a fork, we clone the repo from that fork, rather than directly from mvextentions/mvbasic.

```
src>git clone https://github.com/ianmcgowan/mvbasic.git mvbasic_ian
```

This will create a new folder, mvbasic_ian, with a complete copy of the forked repo from github, and origin pointing to
ianmcgowan/mvbasic.

### 8.1.3 Connecting to upstream

The names for all these versions are arbitrary, but in the standard github workflow the repo on your PC is named local, the forked
copy you can push changes to is named origin, and the original mvextensions/mvbasic repo is named upstream.  Git is a decentralized
version control system, so none of these are "the" repo, they are all full copies with all the history up to the point they were
forked.  A picture from stackoverflow may be worth a thousand words:

![forks](screenshots/devguide/forkedrepo.png)

https://stackoverflow.com/questions/9257533/what-is-the-difference-between-origin-and-upstream-on-github/9257901#9257901

https://help.github.com/en/github/getting-started-with-github/fork-a-repo has instructions on how to add "upstream" as a target so
you can keep your local copy in sync with any changes made in the MV Extensions repo.

```
# Show the current remote repos we are connected to
git remote -v
# Add a new remote named "upstream"
git remote add upstream https://github.com/mvextensions/mvbasic.git
# Check the status of our current tree - simpler if we are in sync with origin/master
git status
# Fetch the latest changes from upstream
git fetch upstream
# Merge upstream to get back in sync with mvextensions/mvbasic
git merge upstream/master
# Push the changes to our forked copy (origin/master)
git push origin/master
```

### 8.1.4 Create a feature branch

To make things even more complicated, we can create a branch inside our local repo for whatever "feature" we plan on working on.
For example, I am working in a branch named "devdocs", and have another branch "rembugfix" for researching a bug related to comment
(remark) formatting.  This is very optional, and only comes into play when you are interested in contributing a change back to
mvextensions.  Having separate feature branches makes creating a manageable pull request easier.

### 8.1.5 Creating a pull request