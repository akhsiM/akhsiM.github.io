---
layout: posts
title: Publishing Markdown Notes
---

# Pre-face 

On my blog I have two tabs 'Random' and 'Learning' which are quite different. 

The posts in 'Learning' are written in Org-Mode and have a separate publishing workflow using emacs and a bash script. What the hell is Org-mode and why am I using it? Org-Mode is a really powerful document editing and formatting mode within Emacs. Using Emacs I can execute code from within Org-mode itself, which is really handy for notes that are written by me whilst learning programming. 

The posts in 'Random' are simply written in Markdown format which is pretty much a more modern brother of Org-mode. It has less functionality, sure, but with less functionality also comes ease of use. Markdown is what I use a lot these days for work & personal when I don't need the superpowers that come with Org-mode.

# What I am doing

I have recently decided that I will be writing more Markdown notes and publishing them more onto this site more regularly. These notes can be non-programming related and most often do not need fancy features that are offered by Emacs Org-mode. 

The articles need to have images in them. No one likes a wall of text.

What I am aiming to do is establish a workflow that enables me to easily publish Markdown notes written on my local computer to this blog site easily without having to moves files around.

The end goal is for this workflow to be set up:

- A md post is created on local machine.
- The md post is passed through a module.
- The md post is published to this site.

A tricky thing is to move the images in the Markdown post to the proper folder on my Jekyll directory.

This is the folder that I am going to use for storing images that are displayed in Markdown posts' content:

`static/img/`

# How I am doing it

I intended this to be a quick solution so the setup will be simple. There are two scripts:

- A bash script that will run the Python script and perform the Git pull&push
- A Python script that will handle the Markdown file

The steps are detail in the following flowchart:

![](/static/img/Publishing Md Notes/mdpub.png)

The source files for this can be found here: https://github.com/akhsiM/mdpub

If anyone would like to re-use these scripts, they would need to update the first three lines of the `mdpub.sh` file to suit their own page. And that's pretty much it, I'm 90% sure.

```bash
gitUrl='ssh://git@github.com/akhsiM/akhsiM.github.io.git'
gitpostdir='/Random/_posts/'
gitimgdir='/static/img/'
```
