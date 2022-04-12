# Hacking in Lisp

Author: Yuqi Liu

Date: Mon Apr. 10 2022

NOTE: This is a scatch, need to re-organized.

## Environment Setups

* sbcl: lisp implemention
* rlwrap: readline support

in REPL(Read Eval Print Loop):
exiting interactive debugger: 0 or Ctrl-D

* avoid annoying interactive debugger

`~/.sbclrc`:

```lisp
(defun print-condition-hook (condition hook)
  "Print this error message (condition) and abort the current operation."
  (declare (ignore hook))
  (princ condition)
  (clear-input)
  (abort))

;; WARN: this will
(setf *debugger-hook* #'print-condition-hook)
```

## hello program

```lisp
(defun hello ()
  "say hello to USER"
  (format t "hello ~a" (uiop:getenv "USER")))

(hello)
```

```bash
sbcl --load hello.lisp
```

If using `sbcl --script`, then should modify `hello.lisp` as:
```lisp
(require :asdf)
(defun hello ()
  "say hello to USER"
  (format t "hello ~a" (uiop:getenv "USER")))

(hello)

;; quit the lisp implementation
(uiop:quit)
```
and run:
```bash
sbcl --script hello.lisp
```

## Portacle (IDE)

download it... and run


call a private function in package `(package-name::function-name)`


`defvar` vs `defparameter` vs `setf` vs `setq`:
https://stackoverflow.com/questions/8927741/whats-difference-between-defvar-defparameter-setf-and-setq

`defparameter` always assigns a value, while `defvar` does it only once:

```lisp
> (defparameter a 1)
A
> (defparameter a 2)
A
> a
2
> (defvar b 1)
B
> (defvar b 2)
B
> b
1
```

`setf` is a macro which uses `setq` internally, but has more possibilities:

```
> (defparameter c (list 1 2 3))
C
> (setf (car c) 42)
42
> c
(42 2 3)
> (setq (car c) 42)
; in: SETQ (CAR C)
;     (SETQ (CAR C) 42)
;
; caught ERROR:
;   Variable name is not a symbol: (CAR C).
;
; compilation unit finished
;   caught 1 ERROR condition

debugger invoked on a SB-INT:COMPILED-PROGRAM-ERROR in thread
#<THREAD "main thread" RUNNING {70085E3DB3}>:
  Execution of a form compiled with errors.
Form:
  (SETQ (CAR C) 42)
Compile-time error:
  Variable name is not a symbol: (CAR C).

Type HELP for debugger help, or (SB-EXT:EXIT) to exit from SBCL.

restarts (invokable by number or by possibly-abbreviated name):
  0: [ABORT] Exit debugger, returning to top level.

((LAMBDA ()))
   source: (SETQ (CAR C) 42)
0] abort
```

`*features*` a predefined list, can add custom features

```
#+unix
(print "We are on linux)
```

## Iteration

`dolist`, `dotimes`, `loop`, `maphash` ...

* `loop`:

```lisp
(loop for x in '(1 3 5)
   do (print x))

(loop for x in '(1 3 5)
   collect (print x))

(loop for x in '(1 3 5)
   sum (print x))

(loop for x in '(1 3 5)
   count (print x))

(loop for x in '(1 3 5)
   maximize (print x))
```

* `dolist`

```lisp
(dolist (x '(1 3 5))
         x) ; this is the return value of the last eval of x
  (print x))
```

* `for`

```lisp
(ql-quickload "for")

(for:for ((x over #(0 2 4))
          (y over (list 1 3 5)))
  (format t "~a - ~a" x y))
```

* `maphash`

```lisp
(let ((ht (make-hash-table)))
  (setf (gethash :a ht) 1)
  (setf (gethash :b ht) 2)
  (loop :for k :being :the :hash-key
     :using (:hash-value v)
     :of ht
     do (print (list k v))))

;; ==>
(let ((ht (make-hash-table)))
  (setf (gethash :a ht) 1)
  (setf (gethash :b ht) 2)
  (maphash (lambda (k v)
              (print (list k v)))
           ht))
;; ==>
(ql:quickload "alexandria")
(let ((ht (make-hash-table)))
  (setf (gethash :a ht) 1)
  (setf (gethash :b ht) 2)
  (alexandria:hash-table-keys ht))

;; ==>
(ql:quickload "for")
(let ((ht (make-hash-table)))
  (setf (gethash :a ht) 1)
  (setf (gethash :b ht) 2)
  (for:for ((key/value over ht))
    (print key/value)))
;; or using trivial-do https://github.com/yitzchak/trivial-do/
```

* `dotimes`

```lisp
(dotimes (i 3)
  (print i))

(loop repeat 3
   do (print "hello"))

;; REPL: READ EVAL PRINT LOOP
(loop (print (eval (read))))
```

### LOOP: Hight-level Overview

2 rules:
* respect the order
* don't nest accumulating clauses

```lisp
;; Iteration:
;; LOOP overview
(loop
  ;; Initialize variables we loop over
  :for x :in '(1 3 5)
  :for i :from 1 :to 2 ;; ranges: to,below,downto...
  :for y := i :then 99

  ;; Use intermediate variables
  :with z := "z" ;; set once, not iterated.

  ;; Initial clause
  :initially (format t "initially, i = ~S" i)

  ;; Body
  ;; Conditionals: if/else, when
  ;; While, until, repeat
  :if (> i 90)
  :do (return i)  ;; early exit

  ;; Main clauses: ;; do, collect... into,
  ;; count, sum, maximize
  ;; thereis, always, never
  :sum x :into res

  ;; Final clause
  :finally (return (list i res)))
```

## All named functions

* named functions (`defun`)
* `apropos`: find definition
* `documentation`: get the documents
* `inspect`: check the details

difference between `#'` and `'` while calling with functions with `funcall`:
* `#'`: respects lexcial environment
* `'`: mostly refers to top-level functions that live in packages

```lisp
(defun hello (name &key (happy t happy-p))
  "Say hello to NAME."
  (format t "happy is: ~S~&" happy)
  (format t "Hello ~a" name)
  (when happy-p
    (if happy
        (format t " :)")
        (format t " :(((("))))

(flet ((hello (x)
         (format nil "*** hello ~a was overriden!~&" x)))
  (format t "What is 'hello?~&")
  (describe 'hello)
  (format t "What is #'hello? ~a" #'hello)
  (describe #'hello)
  (print (hello "me"))
  (print "Calling 'hello:")
  (funcall 'hello "with quote")
  (print "Calling #'hello:")
  (funcall #'hello "with #'"))
```

`flet` ~ `let`
`labels` ~ `let*`

### multiple return values

`values` print the multiple values, and return the first elements
using `nth-value` to get the respectivly elements
using `multiple-value-bind` to bind multiple elements of returned from `values`

### higher order functions

1. accept function as arguments
  a. reference functions
  b. funcall

```lisp
(defun compute (x y &key operation #'+)
  (funcall operation x y))

(compute 1 2) ;; => 3
(compute 1 2 :operation #'-) ;; => -1

(member 2 '(1 2 3)) ;; => (2 3)
(member "foo" '("rst" "foo") :test #'string-equal) ;; => ("foo")
```

2. return functions
  a. anonymous / lambda functions

```lisp
(lambda (x)
  (1+ x))

(compute 1 2 :operation (lambda (x y)
                           (+ 10 x y)) ;; => 13
;; or
(defun add10 (x y)
   (+ 10 x y))
(compute 1 2 :operation #'add10)

(mapcar (lambda (x y) (+ x y))
  '(1 2 3) '(10 20 30)) ;; => (11 22 33)
```
  b. `setf` functions

```lisp
(let ((counter 0))
  (defun counter-inc ()
    (incf counter))

  (defun counter-init ()
    (setf counter 0))

  (defun counter-value ()
     counter)

  (defun (setf counter-value) (new-value)
    (setf counter new-value)))

(counter-inc) ;; => 1

(setf (counter-value) 9) ;; => 9
```

`defmethod` define a generic function

```lisp
(defmethod hello (name)
  (print name))

(defmethod hello ((obj hash-table))
  (format t "rrr we have a HT"))

(hello (make-hash-table))
;; => rrr we have a HT
;; NIL
(hello "me")
;; "me"
;; "me"
```

optional:

```lisp
(defgeneric hello (sthg)
  (:documentation "doc for hell"))
```

## create a new project:

* `my-project.asd`:

```lisp
(in-package #:asdf-user)

(defsystem :my-project
  :depends-on (:alexandria :str :cl-ppcre :clingon)
  ;; flat source tree:
  ;; - .asd
  ;; - .lisp
  :components ((:file "my-project") ;; .lisp file
               (:static-file "README.md")))

#|
With .lisp file in a src/ directory:

  :components ((:module "src"
                :components
                ((:file "utils")
                 (:file "my-project")
                 (:file "config")))
                (:module-2 ...))
```

* `my-project.lisp`:

```lisp
(defpackage #:mypackage
  (:use :cl)
  (:export :my-entry-point))

(in-package :mypackage)

(defun my-entry-point ()
  "Say hello to current user."
  (format t "Hello ~a!" (uiop:getenv "USER")))
```

systems: like Debian packages
packages: containers for symbols. Namespaces
https://dnaeon.github.io/common-lisp-lookup-tables-alists-and-plists/
https://gigamonkeys.com/book/beyond-lists-other-uses-for-cons-cells.html

* alists: association lists, a `list` of `cons`es representing an associated
  of keys with values, where the `car` of each `cons` is the key and the `cdr`
  is the value associated with that key

* plists: property list
  - a listcontaining an even number of elements that are alternating names(sometimes
  called indicators or keys) and values(sometimes called properties). When there is
  more than one name and value pair with the identical name in a property list, the
  first such pair determines the property
  - the component of the symbol containing a property list