---
title      : Build blog with hexo and org-mode
excerpt    : Build blog with hexo and org-mode, use org-publish to generate md files.
date       : 2022-02-27
categories : util
tags       : [blog, org-mode, hexo]
---


# Introduction

In this article, we'll demonstrate how to use [Hexo](https://hexo.io/) to build the blog with [icarus](https://ppoffice.github.io/hexo-theme-icarus/) theme.

To be more specific, we'll share how to use [org-capture template](https://orgmode.org/manual/Capture-templates.html) to generate blog structure, and then use [org-publish](https://orgmode.org/manual/Publishing.html) to generate Github Flavored Markdown.


# Hexo Installation

First, let's create the initial blog structure.

```shell
npm install hexo-cli -g
hexo init KimiSpace
cd KimiSpace
npm install
hexo server
```

Next, install icarus theme.

```shell
npm install hexo-theme-icarus
hexo config theme icarus
```

After install the theme, the website portal cannot be displayed normally. This [issue](https://github.com/ppoffice/hexo-theme-icarus/issues/855) is because of missing dependencies.

```shell
npm install --save bulma-stylus@0.8.0 hexo-renderer-inferno@^0.1.3
```

Finally, we can configure the Plugins & Widgets, and customize our personal information.


# Org-mode configuration


## Org-capture

Use org-capture to generate article.

```lisp
(defvar blog-directory (expand-file-name "~/Documents/KimiSpace/") "Blog path.")
(defvar blog-drafts-dir (concat blog-directory "_drafts/") "Blog drafts path.")
(defvar blog-post-dir (concat blog-directory "source/_posts/") "Blog post path.")

(defun create-blog-post ()
  "Create an org file."
  (interactive)
  (let ((name (read-string "Article name: ")))
    (expand-file-name (format "%s.org" name) blog-drafts-dir)))

(add-to-list 'org-capture-templates
             `("b" "Blog" plain (file create-blog-post)
               ,(concat "#+OPTIONS: toc:nil n:t\n"
                        "#+begin_export html\n"
                        "---\n"
                        "title      : %^{Title}\n"
                        "excerpt    : %^{Excerpt}\n"
                        "categories : %^{Categories}\n"
                        "tags       : [%^{tags}]\n"
                        "---\n"
                        "#+end_export\n"
                        "\n"
                        "%?\n")))
```


## Org-publish

Use org-publish to generate markdown file.

```lisp
(setq org-publish-project-alist
      '(("blog-org"
         :base-directory "~/Documents/KimiSpace/_drafts/"
         :base-extension "org"
         :publishing-directory "~/Documents/KimiSpace/source/_posts/"
         :recursive t
         :publishing-function org-gfm-export-to-markdown
         :with-toc nil
         :headline-levels 4
         :auto-preampble nil
         :auto-sitemap nil
         :html-extension "md"
         :body-only t
         )
        ("blog" :components ("blog-org" ))))
```

1.  I cannot use `blog-drafts-dir` and `blog-post-dir` here, so replace with actual paths.
2.  Org-publish should work properly, but got an error ``Process `org-export-process' exited abnormally``, so I use self-defined function and use global keymap to publish.
    
    ```lisp
    (defun k/hexo-ox-gfm (&optional async subtreep visible-only)
      (interactive)
      (let ((outfile (org-export-output-file-name ".md" subtreep blog-post-dir)))
        (org-export-to-file 'gfm outfile async subtreep visible-only)))
    ```
