# Learn X in Y minutes elisp
``` emacs-lisp
(+ 2 2)
(+ 2 (+ 1 1))
(+ 3 (+ 1 2))

;; 定义一个变量my-name，值为"Bastien"
(setq my-name "Bastien")

;; 在光标处插入字符串
(insert "Hello!")
(insert "Hello" " world!")
(insert "Hello, I am " my-name)

;; 定义一个函数
(defun hello () (insert "Hello, I am" my-name) )
;; 调用一个函数
(hello)
;; 定义一个带参数的函数
(defun hello (name) (insert "Hello " name))
;; 调用一个带参数的函数
(hello "you")

;; 创建一个新的窗口
(switch-to-buffer-other-window "*test*")

;; 使用progn命令将s表达式结合起来
(progn
  (switch-to-buffer-other-window "*test*")
  (hello "you"))
(progn
  (switch-to-buffer-other-window "*test*")
  (erase-buffer)
  (hello "there"))
(progn
  (switch-to-buffer-other-window "*test*")
  (erase-buffer)
  (hello "you")
  (other-window 1))

;; 使用let将一个值和一个局部变量绑定，然后将很多s表达式组合起来
(let ((local-name "you"))
  (switch-to-buffer-other-window "*test*")
  (erase-buffer)
  (hello local-name)
  (other-window 1))

;; 格式化字符串
(format "Hello %s!\n" "visitor")
(defun hello (name)
  (insert (format "Hello %s!\n" name)))
(hello "you")

(defun greeting (name)
  (let ((your-name "Bastien"))
    (insert (format "Hello %s!\n\nI am %s."
		    name
		    your-name
		    ))))
(greeting "you")

(read-from-minibuffer "Enter your name:")

(defun greeting (from-name)
  (let ((your-name (read-from-minibuffer "Enter your name:")))
    (insert (format "Hello!\n\nI am %s and you are %s."
		    from-name
		    your-name))))
(greeting "Bastien")

(defun greeting (from-name)
  (let ((your-name (read-from-minibuffer "Enter your name:")))
    (switch-to-buffer-other-window "*test*")
;;    (erase-buffer)
    (insert (format "Hello!\n\nI am %s and you are %s."
		    from-name
		    your-name))
    (other-window 1)))
(greeting "Bastien")

;; 定义一个列表
(setq list-of-names '("Sarah" "Chloe" "Mathilde"))
;; 获取列表中的第一个元素
(car list-of-names)
;; 获取剩下的元素
(cdr list-of-names)
;; 添加元素
(push "Stephanie" list-of-names)
;; 使用函数hello遍历列表
(mapcar 'hello list-of-names)

(defun greeting()
  (switch-to-buffer-other-window "*test*")
  (erase-buffer)
  (mapcar 'hello list-of-names)
  (other-window 1))
(greeting)

(defun replace-hello-by-bonjour()
  (switch-to-buffer-other-window "*test*")
  (goto-char (point-min))
  (while (search-forward "Hello" nil t)
    (replace-match "Bonjour"))
  (other-window 1))
(replace-hello-by-bonjour)

(defun boldify-names ()
  (switch-to-buffer-other-window "*test*")
  (goto-char (point-min))
  (while (re-search-forward "Bonjour \\(.+\\)!" nil t)
    (add-text-properties (match-beginning 1)
			 (match-end 1)
			 (list 'face 'bold)))
  (other-window 1))
(boldify-names)
```
