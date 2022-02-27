---
title      : Build blog with hexo and org-mode
excerpt    : Build blog with hexo and org-mode, use org-publish to generate md files.
date       : <2022-02-27 Sun>
categories : util
tags       : [blog, org-mode, hexo]
---


# Introduction

In this artical, we'll demonstrate how to use [Hexo](https://hexo.io/) to build the blog with [icarus](https://ppoffice.github.io/hexo-theme-icarus/) theme.

To be more specific, we'll share how to use [org-capture template](https://orgmode.org/manual/Capture-templates.html) to generate blog structure, and then use [org-publish](https://orgmode.org/manual/Publishing.html) to generate Github Flavored Markdown.


# Initialization


## Installation

First, let's create the initial blog structure.

```bash
npm install hexo-cli -g
hexo init KimiSpace
cd KimiSpace
npm install
hexo server
```

Next, install icarus theme.

```bash
npm install hexo-theme-icarus
hexo config theme icarus
```

After install the theme, the website portal cannot be displayed normally. This [issue](https://github.com/ppoffice/hexo-theme-icarus/issues/855) is because of missing dependencies.

```bash
npm install --save bulma-stylus@0.8.0 hexo-renderer-inferno@^0.1.3
```

Finally, we can configure the Plugins & Widgets, and customize our personal information.
