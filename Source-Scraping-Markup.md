# Overview

To expose C++ functions, classes, methods, enums to Clasp, tags are embedded within the Clasp source code that are scraped from the code using a scraper written in Common Lisp.  The scraping markup consists of a collection of C-preprocessor macros.  The scraper runs the C++ preprocessor on all of the source files which expands the macros to tags and then the scraper extracts the tags from the preprocessor output. Then the scraper generates C++ code that becomes part of the Clasp source code and informs Clasp about all exposed functions, classes, methods and enums as well as source code locations of these objects.

# Markup
## Exposing a function
Simple example:
```C++
CL_DEFUN Cons_sp cl__cons(T_sp obj1, T_sp obj2) {
  return Cons_O::create(obj1, obj2);
};
```
Prefixing the function ```cl__cons``` with ```CL_DEFUN``` binds the C++ function ```cl__cons``` to the symbol CL:CONS within Clasp. In this case the package name is obtained from the ```cl__``` prefix of the C++ function name.  The ```cl__``` prefix can be left out and then the package is inferred from the namespace of the C++ function. The C++ name is run through a demangling function within Clasp prior to internment as a symbol.  The demangler converts underscores to dashes and upcases characters among other things (see demangler below). All conversions of arguments and return values from CL to C++ and back are done automatically by the binding library within Clasp.

Complex example:
```C++
CL_LAMBDA(name &optional stream);
CL_DECLARE();
CL_DOCSTRING(R"doc(Describe a
C++ object
like CL:DESCRIBE)doc");
CL_NAME(CORE:DESCRIBE-C++-OBJECT);
CL_DEFUN void core__describe_cxx_object(T_sp obj, T_sp stream)
{
  if (obj.generalp()) {
    obj->describe(stream);
  } else if (obj.consp()) {
    obj->describe(stream);
  }
  SIMPLE_ERROR(BF("Use the CL facilities to describe this object"));
};
```
In this example, a lambda-list, docstring and the symbol that the function is fbound to are explicitly provided. The macros CL_LAMBDA(...), CL_DOCSTRING(...), CL_NAME(...) modify the CL_DEFUN that immediately follow them as long as they are within a reasonable number of source lines of the CL_DEFUN. The CL_NAME(...) macro overrides the name that would be extracted from the function prototype.

## Symbols

Example:
```C++
SYMBOL_EXPORT_SC_(ClPkg,list);
SYMBOL_SC_(CorePkg,foo);
SYMBOL_SHADOW_EXPORT_SC_(ExtPkg,bar);
```
The first case exposes the CL:LIST symbol.  The second case exposes the CORE::FOO symbol and the third case exposes a shadowing symbol EXT:BAR.
The symbol names are run through the demangler before being interned. The package identifiers ```ClPkg```, ```CorePkg```, and ```ExtPkg``` are global variables declared using the ```NAMESPACE_PACKAGE_ASSOCIATION(...)``` macro below and they indicate what package the symbol is interned within.


## Other tags
```C++
#define CL_BEGIN_ENUM(type,symbol,desc)
#define CL_VALUE_ENUM(symbol,value)
#define CL_END_ENUM(symbol)
#define CL_NAME(...)
#define CL_LISPIFY_NAME(...)
#define CL_PKG_NAME(pkgid,name)
#define CL_LAMBDA(...)
#define CL_DOCSTRING(...)
#define CL_DECLARE(...)
#define CL_DEFUN
#define CL_DEFMETHOD
#define CL_EXTERN_DEFMETHOD(type,pointer)
#define CL_EXTERN_DEFUN(pointer)
#define CL_PKG_NAME(pkg,name)
#define SYMBOL_SC_(pkg,name)
#define SYMBOL_EXPORT_SC_(pkg,name)
#define SYMBOL_SHADOW_EXPORT_SC_(pkg,name)
#define INTERN_(ns,name) (ns::_sym_##name)
#define PACKAGE_USE(name_str)
#define PACKAGE_NICKNAME(name_str)
#define NAMESPACE_PACKAGE_ASSOCIATION(x, y, z) \
```
