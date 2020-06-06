In the following table one can find relative performance of clasp for some tasks:

Timings for version `cclasp-boehm-0.4.2-2692-g00d68d383-cst`on `macosx 10.14.6 (mojave)` `CLASP_BUILD_MODE = 'faso'` `USE_COMPILE_FILE_PARALLEL=True`

| Compile Task (real)| clasp | sbcl | factor |
| ----------------- | ------- | ---------------- | ---------------- |
| cl-bench gabriel |35.0|0.711| |
| cl-jpeg |119.012|1.531| |
| asdf  |123.399|5.872| |
| ironclad |5355.203|53.792| | 
| clx |1241.959|14.933| |
| mcclim | |66.045| |

Note that :mcclim depends on :clx and :cl-jpeg, so that order of timings is important

Commands used:
* `(time (compile-file "/Users/karstenpoeck/lisp/compiler/clasp-karsten/src/lisp/modules/asdf/build/asdf.lisp"))`
* `(time (ql:quickload :cl-jpeg))`
* `(time (ql:quickload :ironclad :verbose t))`
* `(time (ql:quickload :clx))`
* `(time (ql:quickload :mcclim))`