# Org Mode in Emacs

Emacs Org Mode (using Babel) is a package for the Emacs text editor that connects with Jupyter kernels, such as stata_kernel, 
to display results interactively inside the text editor.  Emacs also allows full console access to jupyter kernels 
for an interactive Stata experience. Like jupyter notebook or lab, it allows for mixing of text narrative, latex math, 
with code and Stata results.  Emacs Org Mode allows for fine grained control of document output for producing pdf manuscripts.

## Installation Instructions 
  1. Install and load emacs-jupyter.el
  2. Ensure that you have activated the python environment where stata_kernel is available
  3. Install [`emacs-jupyter`](https://github.com/nnicandro/emacs-jupyter)
  4. Add the following lines to your init.el: 
     ```lisp
        (when (functionp 'module-load)
        (use-package jupyter)
          (with-eval-after-load 'org
             (org-babel-do-load-languages
	          'org-babel-load-languages
	          '((jupyter . t))))
        (with-eval-after-load 'jupyter
          (define-key jupyter-repl-mode-map (kbd "C-l") #'jupyter-repl-clear-cells)
          (define-key jupyter-repl-mode-map (kbd "TAB") #'company-complete-common-or-cycle)
          (define-key jupyter-org-interaction-mode-map (kbd "TAB") #'company-complete-common-or-cycle)
          (define-key jupyter-repl-interaction-mode-map (kbd "C-c C-r") #'jupyter-eval-line-or-region)
          (define-key jupyter-repl-interaction-mode-map (kbd "C-c M-r") #'jupyter-repl-restart-kernel)
          (define-key jupyter-repl-interaction-mode-map (kbd "C-c M-k") #'jupyter-shutdown-kernel)
          (add-hook 'jupyter-org-interaction-mode-hook (lambda () (company-mode)
						     (setq company-backends '(company-capf))))
          (add-hook 'jupyter-repl-mode-hook (lambda () (company-mode)
					  :config (set-face-attribute
						   'jupyter-repl-input-prompt nil :foreground "black")
					  :config (set-face-attribute
						   'jupyter-repl-output-prompt nil :foreground "grey")
					  (setq company-backends '(company-capf))))
          (setq jupyter-repl-prompt-margin-width 4)))

        ;; associated jupyter-stata with stata (fixes fontification if using pygmentize for html export)
        (add-to-list 'org-src-lang-modes '("jupyter-stata" . stata))
        (add-to-list 'org-src-lang-modes '("Jupyter-Stata" . stata)) 
        ;; you **may** need this for latex output syntax highlighting
        ;; (add-to-list 'org-latex-minted-langs '(stata "stata"))   
     ```
     
   5. In your `init.el`, make sure to remove `("stata" . "stata")` from `'org-babel-load-languages`. 

## Known Issues
1.  Emacs Org Mode will not currently display `html` output from any of the magics provided by `stata_kernel`.  
    - For `%browse` and `%head` use the code block option `:display text/plain` for plain text output
    - The `%help` magic doesn't provide plain text output, so just issue help commands directly (e.g. `help regress`) 
3.  Some graphical output will not export correctly.  The problem and solution are described in the link below.  

Further features and usability tips are discussed in this [blog post](https://rlhick.people.wm.edu/posts/stata_kernel_emacs.html).
