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
* [Intro](#intro)
* [Source/Header Files](#sourceheader-files)
* [Classes](#classes)
* [Functions](#functions)\

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

#### Classes
##### Structs vs. Classes

* Use a `struct` only for passive objects that carry data, everything else is a `class`;
    * a `struct` should lack any functionality other than access/setting the data members;
    * in a `struct` the accessing/setting of fields is done by directly accessing the fields (no methods);
    * `struct`'s methods **should not** provide behavior but should only be used to set up the data members, e.g.:
        * constructor, destructor, `Initialize(), Reset(), Validate()`.
* If in doubt which to use, make it a `class` (typically if more functionality is required, a `class` is more appropriate)
* For naming differences between `class` and `struct`, see [Naming Rules](#naming-rules).

##### Constructors
* Generally avoid doing work in the constructor! (see [Doing work in constructors](https://google.github.io/styleguide/cppguide.html#Doing_Work_in_Constructors), for more details)
* Avoid virtual method calls in constructors!
    * If the work calls virtual functions, these calls will not get dispatched to the subclass implementations.
* Avoid initialization that can fail if you can't signal an error:
    * There is no easy way for constructors to signal errors unless exceptions are used. Exceptions are allowed in our style, ([see amendments](#amendments) TODO)! 
* Exception: if work has to be carried out in the constructor:
    * Consider the Factory Functions (`Factory Pattern`) or `Init()` methods. (#TODO) 
    * Avoid `Init()` methods on objects with no other states that affect which public methods may be called (semi-constructed objects of this form are particularly hard to work with correctly).

##### Destructors
*  If the class has virtual methods, its destructor must be virtual.

##### Member Access Control
* Data members should always be `private`, unless ...
    * They are static const (and follow the naming convention for constants). 
    * When using GTest, for technical reasons, data members of text fixtures can be protected. 

##### Declaration Order
* A class definition should usually have the following structure
    ```
    public:
        <code>
    protected:
        <code>
    private:
        <code>    
    ```
* Within each section:
    * prefer grouping similar kinds of declarations together
    * (generally) prefer the following order: 
        * types (including typedef, using directives, and nested structs and classes)
        * constants
        * factory functions
        * constructors
        * assignment operators
        * destructor
        * all other methods
        * data members

* Omit sections that would be empty.
* Group similar declarations together, placing public parts earliest.
* Do not put large method definitions inline in the class definition. 
    * only trivial or performance-critical, and very short, methods may be defined inline.

##### Copyable and Movable Types
* If you do not want to support copy/move operations on your `type`, explicitly disable the implicitly C++ generated functions by using `= delete` in the `public:` section of the class definition
* Only allow support for copying and/or moving if these operations are clear and meaningful for the implemented `type`. 
    * If a copy or move constructor is defined, define the corresponding assignment operator, and vice-versa. 
    * If the `type` is copyable, do not define move operations unless they are significantly more efficient than the corresponding copy operations. 
    * If the `type` is not copyable, but the correctness of a move is obvious to users of the `type`, make the `type` move-only by defining both of the move operations.
    * If your `type` provides copy operations, it is recommended that you design your class so that the default implementation of those operations is correct. 
    ```
    class Foo {
        public:
         Foo(Foo&& other) : field_(other.field) {}
         // Bad, defines only move constructor, but not operator=.
       
        private:
         Field field_;
       };
    ```
* Due to the risk of slicing, avoid providing an assignment operator or public copy/move constructor for a class that's intended to be derived from (and avoid deriving from a class with such members). 
    * If your base class needs to be copyable, provide a public virtual `Clone()` method, and a protected copy constructor that derived classes can use to implement it.

##### Implicit conversions
* Do not define implicit conversions! (see [pros and cons](https://google.github.io/styleguide/cppguide.html#Implicit_Conversions))
    * Type conversion operators and constructors that are callable with a single argument, must be marked  with the `explicit` keyword (since C++11) in the class definition. 
    * Exception! 
        * constructors that take a single `std::initializer_list` parameter should omit `explicit` in order to support copy-initialization, e.g.,
        ```
        MyType m = {1, 2};
        ```

##### Inheritance
* Prefer _Compostion_ to _Inhereitance_, composition is often more appropriate
* All inheritance should be public, any other inheritance is discouraged
    * Instead of private inheritance, include an instance of the base class as a member.
* Limit the use of protected members in general
    * Allow (limited usage of) protected member functions only to those members that might need to be accessed from subclasses.
* Explicitly annotate overrides of virtual functions or virtual destructors with an `override` or (less frequently) `final` specifier. 
    * Use `virtual` keyword is discouraged unless when used for overrides for older (pre-C++11) code
* For clarity, use exactly one of `override, final, or virtual` when declaring an override
* A function or destructor marked `override` or `final` that is not an override of a base class virtual function will not compile and will help catch errors; 
    * The specifiers (also) serve as documentation for the developer/reader;

###### Multiple inheritance
* Multiple inheritance is allowed only when all `super-classes`, with the possible exception of the first one, are pure interfaces.

##### Interfaces
* A class is a pure interface if it meets the following requirements:
    * It has only public pure virtual `("= 0")` methods and static methods (but see below for destructor).
    * It may not have non-static data members.
    * It need not have any constructors defined. If a constructor is provided, it must take no arguments and it must be protected.
    * If it is a subclass, it may only be derived from classes that satisfy these conditions
    * To make sure all implementations of the interface can be destroyed correctly, the interface must also declare a virtual destructor (in an exception to the first rule, this should not be pure). See _Stroustrup, The C++ Programming Language, 3rd edition, section 12.4 for details_.


##### Operator Overloading
* Define overloaded operators only if their meaning is obvious, unsurprising, and consistent with the corresponding built-in operators. 
    * E.g., use `|` as a `bitwise-` or `logical-or`, not as a shell-style pipe.
* Don't go out of your way to avoid defining operator overloads!
    * E.g. prefer to define `==`, `=`, and `<<`, rather than `Equals()`, `CopyFrom()`, and `PrintTo()`. 
* Conversely, don't define operator overloads just because other libraries expect them. 
    * E.g. if your type doesn't have a natural ordering, but you want to store it in a `std::set`, use a custom comparator rather than overloading `<`.
* Do not overload `&&`, `||`, `, (comma)`, or `unary &`. 
* Do not overload `operator""`, i.e. do not introduce user-defined literals.
* Define operators only on your own types. 
    * Define the operators in the same headers, .cc files, and namespaces as the types they operate on. 
    * The above optimizes usage and minimizes the risk of multiple definitions. 
    * Avoid (if possible) defining operators as templates, because they must satisfy this rule for any possible template arguments. 
* If you define an operator, also define any related operators that make sense, be consistent.
    * E.g., if `<` is overloaded, overload all the comparison operators, and make sure `<` and `>` never return true for the same arguments.
* Prefer to define non-modifying binary operators as non-member functions. 
    * If a binary operator is defined as a class member, implicit conversions will apply to the right-hand argument, but not the left-hand one. 
    * It will confuse your users if `a < b` compiles but `b < a` doesn't.
* For type conversion operators see [Implicit conversions](#implicit-conversions) Section. 
* For conventions on the `=` operator see the [Copyable and Movable Types](#copyable-and-movable-types) Section.
* For overloading of the `<<` operator for for use with streams, see the [Streams](#streams) Section. (#TODO) 
* See [Function Overloading](#function-overloading) Section for conventions which apply to operator overloading as well. (#TODO)

#### Functions


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
