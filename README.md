# Coding style guidelines

When available, the prevailing style agreed by the community is used. If a different decision was made with respect to a community style rule, they are outlined in the respective language section with _our_ rationale for it. 

## Table of contents

* [General Rules](#general-rules)
* [C++](#cpp)
* [Python](#python)
* [TCL](#tcl)
* [Git](#git)
* [References](#references)

### General rules
* [KISS "Rules"](https://en.wikipedia.org/wiki/KISS_principle)
* [Don't repeat yourself](http://en.wikipedia.org/wiki/Don't_repeat_yourself).
* Be consistent!
* Check [Zen of Python](https://www.python.org/dev/peps/pep-0020/) ...
* Limit lines to 80 characters.
* ... and read [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)

### C++
#### Intro
* We follow the [Google C++ Style](https://google.github.io/styleguide/cppguide.html). For clarity, we succinctly describe the style in here. This document will keep the structure of the original document as much as possible. A few exceptions exist as explained in the amendments section below. 

#### Source/Header Files

* Source files should have the `.cpp` suffix ([see amendments](#amendments)).
* Header files should have the `.h` suffix.
* Header files that contain both definitions **AND** implementation should have `.hpp` extension ([see amendments](#amendments)).
* Header files should be self-contained (compile on their own).
* Non-header files that are meant for inclusion should end in `.inc` and be used sparingly.
* All header files should have `#define` guards to prevent multiple inclusion. The format of the symbol name should be `<PROJECT>_<PATH>_<FILE>_H_`.
* Prefer placing the definitions for template and inline functions in the same file as their declarations.
* Avoid using forward declarations where possible.
* Define functions `inline` only when they are small, say, 10 lines or fewer. 
* In the `.h` file, only `#include` the headers you need.
    * When making use of an imported function within a header, always `#include` that header file in the `.cpp` file as well! 
* Order of includes in the `.cpp` file (separated by an empty line):
    * `module_dir/submodule/server.h`
    * C system files.
    * C++ system files.
    * Other libs `.h` files.
    * Your project's `.h` files.
    * Conditional Includes
    * _Example_, the includes in a _google-awesome-project/src/foo/internal/fooserver.cc_ might look like this:
        ```
        #include "foo/server/fooserver.h"
        
        #include <sys/types.h>
        #include <unistd.h>
        
        #include <hash_map>
        #include <vector>
        
        #include "base/basictypes.h"
        #include "base/commandlineflags.h"
        #include "foo/server/bar.h"
	
        #ifdef LANG_CXX11
        #include <initializer_list>
        #endif  // LANG_CXX11
        ```

#### Scoping
##### Namespaces

* Namespaces should have unique names based on the project name, and as much as possible based on the file's path.
* Top-level namespace names are based on the project name.
* Terminate namespaces with comments as shown in the given example: 
    ```
    namespace <mynamespace> {
        ...
        code
        ...
       }  // namespace mynamespace
    ```
* Do not use `using`-directives (e.g. `using namespace foo`).
* Do not use inline namespaces.
* Follow the rules on [Namespace Names](#namespace-names) (TODO).
* Do not declare anything in namespace `std`, including forward declarations of standard library classes.
* Do not use Namespace aliases at namespace scope in header files except in explicitly marked internal-only namespaces.

##### Static Variables and Unnamed Namespaces

* Use internal linkage for code that does not need to be referenced elsewhere!
  * Place such code in in an unnamed namespace or declare them `static`.
    *   Preferably use unnamed/anonymous namespace

##### Nonmember, Static Member, and Global Functions

* Prefer placing non-member functions in a namespace.
* Don't use completely global functions ([see amendments](#amendments) TODO).
* Prefer grouping functions with a namespace instead of using a class as if it were a namespace.
* Static methods of a class should generally be closely related to instances of the class or the class's static data.

##### Local Variables

* Initialize variables in the declaration!
* Variables needed for `if`, `while` and `for` statements should normally be declared within those statements, so that such variables are confined to those scopes.
    * There is one caveat: if the variable is an object, the classes' constructor is invoked every time it enters scope and the destructor is invoked every time it goes out of scope. This is generally undesired if the object is expensive to build. For this reason, for own type of objects, the above rule should not be followed. For more details see [this](https://google.github.io/styleguide/cppguide.html#Local_Variables).

##### Static and Global Variables

* Variables of class type with `static` storage duration are forbidden!
* Global Objects are forbidden ([see amendments](#amendments) TODO)!

#### Variable Names
##### Namespace Names

#### Amendments
##### File naming
* Source code files should have **.cpp** extension
* Header files that contain only definitions should have **.h** extension
* Header files that contain both definitions **AND** implementation should have **.hpp** extension

Open issues list:

- [ ] Folder structure research: no standard yet? Which one to pick ?
	+ Google way: keep .cpp & .h in the same folder
- [ ] Google research question: if inline is need for class methods or not?

#### Automatic formatting
* Use an editor/IDE that supports automatic style formatter, e.g. in QT Creator use the [Beautifer plugin](http://doc.qt.io/qtcreator/creator-beautifier.html) with [Artistic Style](http://astyle.sourceforge.net/astyle.html)
* Or use an external tool that can check source files, e.g. [Artistic Style](http://astyle.sourceforge.net/astyle.html)



### Python
* Before you start, check [Zen of Python](https://www.python.org/dev/peps/pep-0020/).
* Follow [PEP8](http://www.python.org/dev/peps/pep-0008/).

#### Automatic formatting
* Use an editor/IDE that supports a linter with [PEP8](http://www.python.org/dev/peps/pep-0008/) rules in place, e.g. [Sublime-Text](https://www.sublimetext.com/) with [Anaconda](http://damnwidget.github.io/anaconda/)
* Or use a command line linter that can check [PEP8](http://www.python.org/dev/peps/pep-0008/) rules

### Git
* TODO

### TCL
* TODO: e.g. follow [this](http://www.cs.columbia.edu/~hgs/etc/tcl-style.txt)

### References

## License
See [License](LICENSE)
