---
title: My Org-Publish script
layout: posts
---


Majority of the notes that I take while learning online lessons is done on Emacs Org-Mode. I love Org-Mode due to its simplicity in design despite being an extremely powerful tool for notetaking and documenting. Within the world of Org-Mode, close to nothing is impossible. There is almost always a way. 

When I first started using Emacs Org-Mode, there was definitely a learning curve that I had to overcome. However the time and effort that I put into learning this tool completely worth it. I foresee myself using Org-Mode for so much more than just learning notes for Programming-related online lessons.

In this post, what will be covered is the Org publish script that I use to batch export/ publish my .org files into .html pages.

Why script? So I can perform this action within the Terminal comfortably before incorporating it in a bash script.

Oh, about the bash script, actual full workflow that I set up involves a bash script that does a few other things that may be covered in another post. However the following content will be solely about the org-publish script.

There is also a setup file that I host on my github. This setup file is referenced on the preamble of all my Org files. It is used for htmlizing and CSS styling.

A few things to start off..

- emacs scripts have `.el` file extension. The language is Lisp code that defines Emacs configuration settings or add additional features to the program.

- I run the emacs script file in Terminal like this:

```bash
emacs --batch -l ~/.emacs.d/org-publish-Learning.el > ~/Documents/Org-HTMLs/logs/$emacsLogFile 2>&1 
```

The `--batch` flag indicates emacs to run in CLI mode (not GUI). The `l` is to load the lisp file, followed by the lisp file. What follows is optional. Its function is to save status logs in a log folder.

Let's start with the actual lisp file:

First I initialize the script by loading packages and importing the necessary modules, including org modes, org export, publish and package.

```elisp
(package-initialize)
(require 'org)
(require 'ox)
(require 'ox-publish)
(require 'package)
```

The following part defines some customized settings for Org-Mode.

```elisp
(custom-set-variables
 '(org-export-preserve-breaks t)
 '(org-export-use-babel nil)
 '(org-export-with-section-numbers nil)
 '(org-export-with-sub-superscripts nil)
 '(org-src-fontify-natively t))
```

    - When export, keep the line breaks. On default, empty linebreaks are removed.
    - When export, don't re-execute the source block. Instead, keep the result block generated and last saved.
    - No number for headings
    - No subscript and superscript when exporting. In Org-Mode syntax, superscripts and subscripts are precede by `^` and `_` respectively. 
    - Code blocks to have Syntax highlighting. This is very important!


```elisp
(setq org-html-postamble "<p class=\"author\">Author: <a href=\"https://akhsim.github.io/\">%a</a> </p>
                          <p>%c</p>
                          <p>Created: %d</p>
                          <p class=\"date\">Updated: %C</p>")

```

This is my secret 'Back' button that goes back to the homepage of my website. I don't want to include this in my git hosted setup file for org mode as well as in the Emacs configuration because not all contents I publish into HTML need this.

```elisp
(defcustom org-hidden-links-additional-re "\\(<<\\)[[:print:]]+\\(>>\\)"
  "Regular expression that matches strings where the invisible-property of the sub-matches 1 and 2 is set to org-link."
  :type '(choice (const :tag "Off" nil) regexp)
  :group 'org-link)
(make-variable-buffer-local 'org-hidden-links-additional-re)
(defun org-activate-hidden-links-additional (limit)
  "Put invisible-property org-link on strings matching `org-hide-links-additional-re'."
  (if org-hidden-links-additional-re
      (re-search-forward org-hidden-links-additional-re limit t)
    (goto-char limit)
    nil))
(defun org-hidden-links-hook-function ()
  "Add rule for `org-activate-hidden-links-additional' to `org-font-lock-extra-keywords'
You can include this function in `org-font-lock-set-keywords-hook'."
  (add-to-list 'org-font-lock-extra-keywords
                              '(org-activate-hidden-links-additional
                                (1 '(face org-target invisible org-link))
                (2 '(face org-target invisible org-link)))))
(add-hook 'org-font-lock-set-keywords-hook #'org-hidden-links-hook-function)

```

This hides internal target `<<targer>>`. In Org-Mode, hide the emphasis markers. In exports, hide the whole target. These are basically internal anchors in my page.

```elisp
(defun replace-in-string (what with in)				  
  (replace-regexp-in-string (regexp-quote what) with in nil 'literal)) 
									  
(defun org-html--format-image (source attributes info)		  
  (progn								  
    (setq source (replace-in-string "%20" " " source))		  
    (format "<img src=\"data:image/%s;base64,%s\"%s />"		  
            (or (file-name-extension source) "")			  
            (base64-encode-string					  
             (with-temp-buffer					  
               (insert-file-contents-literally source)		
              (buffer-string)))					 
            (file-name-nondirectory source))				  
    ))
```

What this does is embed any image I have on the Org files in the HTML file. There is probably millions of better ways including uploading an image folder into my `static` directory on my git. 

```elisp
(setq org-publish-project-alist
      '(("Learning"
	 :base-directory "~/.staging/Org-HTMLs/Learning"
	 :base-extension "org"	
         :publishing-directory "~Documents/Org-HTMLs/Learning"
	 :recursive t
	 :publishing-function org-html-publish-to-html)))

(setq user-full-name "Mishka Nguyen")
```

This is quite important. It sets  attributes for the `org-publish-project` function which will be called at the end of this script.

```elisp
(load-theme 'monokai-pro t)
(org-mode)
(org-publish-project "Learning" t)
```

I like monokai, monokai pro and other variances. What I'm actually using is "molokai-pro" on my VSCode and Emacs. In this situation, the theme will only be loaded for the purpose of making my code blocks pretty. My stylesheet covers everything else.

Lastly, the `org-publish-project` function is called, to publish the "Learning" project with the attributes that have been set in the previous code blocks. The function call exports/ publishes all `.org` files in the base directory and put all HTML files into the Learning Folder. These HTML files are then published to git.
