* make sure that you have the latest quicklisp libraries (ql:update-dist "quicklisp")
* You need same changes not yet in quicklisp, so put the following in ~/quicklisp/local-projects/
  * bordeaux-threads (https://github.com/sionescu/bordeaux-threads.git , master). Does not seem to be updated in quicklisp, unless a new release of bordeaux-threads is made
  * usocket from https://github.com/usocket/usocket.git (pull request to integrate clasp has been merged)
  * ~plump from https://github.com/kpoeck/plump.git~ (bug in clasp clos is fixed, see issue #698)
  * hunchentoot from https://github.com/edicl/hunchentoot.git (pull request for clasp has been merged)

* To load:
```LISP
(load "~/quicklisp/setup.lisp")
(asdf:register-immutable-system :eclector)
(pushnew :hunchentoot-no-ssl *features*)
(ql:quickload "staple-server" :verbose t)
````


* To start (staple-server:start) 
* To stop (staple-server:stop) 