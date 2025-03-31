---
title: Zimbra Knowledge Base
author: Sales Engineering 
abstract: This repository contains a collection of administrative, support, and best practice documention to help partners find the right information.
---

## Table of Contents
- [Documentation In Spanish](es-docs/)
- [Documentation In Japanese](ja-docs/)
- [ActiveSync Topics](async/)
- [Zimbra Security](sec/)
- [Posting Schedule](https://github.com)
- [Notes From The Field](nftf/)

## Getting started
This is the set of instructions for Zimbra team members to download, edit and publish documents to the ZDOCs repository.

1. Start by making sure you have downloaded and installed the [Github client](https://github.com/apps/desktop) on the your PC or Mac.

2. Open a CLI, CD into or create a folder for this repo and copy/paste the following `git clone https://github.com/zdocs/KB.git`
3. Using [VSCode](https://code.visualstudio.com/Download), [Asciidoctor](https://asciidoctor.org/) or your favorite IDE for editing MarkDown or HTML open the repository you just downloaded and start editing or creating files. 
4. Add the file in what ever format you like to the corresponding directory. eg. security posts are placed in the folder `/sec`
5. Update index.html by adding the link and title. You can follow along on how the other links are added or create your own section for other topics or langauges. 
6. When done saving your work perform the following CLI commands from within the local repo: 
    a. `git add -a` to add all edited files associated with the repo. If you wish to ignore any files just add the name to the .ignore file. 
    b. `git commit -m "TYPE REASON FOR UPDATE"` write one sentance that describes the change.
    c. `git push` Check the site to ensure the updates took. eg. `git commit -m "updating typo in readme.md"`
