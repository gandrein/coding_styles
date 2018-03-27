# Coding style guidelines

When available, the prevailing style agreed by the community is used. If a different decision was made with respect to a community style rule, they are outlined in the respective language section with _our_ rationale for it. 

## Table of contents

* [General Rules](#general-rules)
* [C++ Coding Style](#c-coding-style)
* [Python Coding Style](#python-coding-style)
* [TCL Coding Style](#tcl-coding-style)
* [Git Naming Style](#git-naming-style)
* [References](#references)

### General rules
* [KISS "Rules"](https://en.wikipedia.org/wiki/KISS_principle)
* [Don't repeat yourself](http://en.wikipedia.org/wiki/Don't_repeat_yourself).
* Be consistent!
* Check [Zen of Python](https://www.python.org/dev/peps/pep-0020/) ...
* Limit lines to 120 characters. *
* ... and read [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)

*compromise between modern-day screens and old-style rule.

---

### C++ Coding Style
* [Intro](#intro)
* [Source/Header Files](#sourceheader-files)
* [Classes](#classes)
* [Functions](#functions)
* [Naming](#naming)
* [Comments](#comments)
* [Formatting](#formatting)
* [Amendments](#amendments)

#### Intro
* We follow the [Google C++ Style](https://google.github.io/styleguide/cppguide.html). This document will keep the structure of the original document as much as possible. For clarity, we succinctly describe the style here. A few exceptions to the official style are considered as explained in the [Amendments section](#amendments). 

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

* Use a `struct` only for passive objects that carry data, everything else is a `class`
    * a `struct` should lack any functionality other than access/setting the data members
    * in a `struct` the accessing/setting of fields is done by directly accessing the fields (no methods)
    * `struct`'s methods **should not** provide behavior but should only be used to set up the data members, e.g.:
        * constructor, destructor, `Initialize(), Reset(), Validate()`.
* If in doubt which to use, make it a `class` (typically if more functionality is required, a `class` is more appropriate)
* For naming differences between `class` and `struct`, see [Naming Rules](#naming-rules).

##### Constructors
* Generally avoid doing work in the constructor! (see [Doing work in constructors](https://google.github.io/styleguide/cppguide.html#Doing_Work_in_Constructors), for more details)
* Constructors should never call virtual functions!
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
    * They are `static const` (and follow the naming convention for constants).
    * When using GTest, for technical reasons, data members of text fixtures can be `protected`.

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
* Multiple inheritance is allowed only when all `super-classes`, with the possible exception of the first one, are pure interfaces!

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
* Do not overload operator `""`, i.e. do not introduce user-defined literals.
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

##### Parameter Ordering
* When defining a function, parameter order is: inputs, then outputs.
* Put all input-only parameters before any output parameters. In particular, do not add new parameters to the end of the function just because they are new; place new input-only parameters before the output parameters.
* Output arguments are harder to understand than input arguments; they should be avoided where possible.
* Try to limit the number of arguments to a **maximum** of three (Clean Code, Robert C. Martin)
    > A function should have preferably no arguments (niladic). Otherwise, preferably one (monadic), two is okay (dyadic), three (triadic) should be avoided where possible, and you should never have four or more (polyadic).

##### Write Short Functions
* Try to adhere to these rules (Clean Code, Robert C. Martin)
    * Functions should be small
    * Functions Should Do Only One Thing
    * One level of Abstraction per Function
* **Refactor** any long and complicated functions when working with some code. Do not be intimidated by modifying existing code!
    > Even if your long function works perfectly now, someone modifying it in a few months may add new behavior. This could result in bugs that are hard to find. Keeping your functions short and simple makes it easier for other people to read and modify your code.

##### Have No Side Effects (non-Google)
* Avoid passing boolean flags or output arguments to your function (Clean Code, Robert C. Martin)
    >  Side effects are lies. Your function promises to do one thing, but it also does other hidden things that the name of the function does not imply

##### Reference Arguments
* All parameters passed by reference must be labeled `const`.
* There are some instances where using const `T*` is preferable to const `T&` for input parameters. For example:
  * You want to pass in a null pointer.
  * The function saves a pointer or reference to the input.
* Try to limit the above occurrences, since using `const T*` instead communicates to the reader that the input is somehow treated differently. So if you choose `const T*` rather than `const T&`, do so for a concrete reason;

##### Function overloading
* Use overloaded functions (including constructors) only sparingly!
* Instead, consider qualifying the name with some information about the arguments, e.g., AppendString(), AppendInt() rather than just Append(). If you are overloading a function to support variable number of arguments of the same type, consider making it take a `std::vector` so that the user can use an initializer list to specify the arguments.

##### Default arguments
* Default arguments are allowed on non-virtual functions when the default is guaranteed to always have the same value.
* Prefer overloaded functions if the readability gained with default arguments doesn't outweigh the downsides.
* Default arguments are banned on virtual functions, where they don't work properly.

##### Trailing Return Type Syntax
* Use trailing return types only where using the ordinary syntax (leading return types) is impractical or much less readable.

#### Naming

##### General Naming Rules
* Names should be descriptive; avoid abbreviations.
* If abbreviations are used, do not use ones that are ambiguous or unfamiliar to readers outside your project. _Universally-known_ abbreviations are allowed.
* Give as descriptive a name as possible, within reason. Think about your next reader, it might be you!
* Do not abbreviate by deleting letters within a word. 

##### File Names
* Filenames should be all lowercase and can include underscores (`_`).
* See also [Amendments](#amendments) for extension conventions and path specific structure.

##### Type Names
* Type names start with a capital letter and have a capital letter for each new word, with no underscores (this is applicable to acronyms as well).
* The names of all types — `classes, structs, type aliases, enums`, and type template parameters — have the same naming convention.

##### Variable Names
* The names of variables (including function parameters) and data members are all lowercase, with underscores between words (this is applicable to acronyms as well).
* Member variables (of classes or structs) follow the same rule as above.
* Variables declared `constexpr` or `const`, and whose value is fixed for the duration of the program, are named with a leading "k" followed by mixed case, e.g. `const int kDaysInAWeek = 7;`

##### Function Names

* Function names should start with a capital letter and have a capital letter for each new word (CamelCase). No underscores allowed.
* Capitalize acronyms as single words (i.e. `StartRpc()`, not `StartRPC()`).


##### Namespace Names
* Namespace names are all lower-case. Top-level namespace names are based on the project name. Avoid collisions between nested namespaces and well-known top-level namespaces, like `std`.
* Choose unique project identifiers e.g. `websearch::index`, `websearch::index_util` over collision-prone names like `websearch::util`.
* The name of a top-level namespace should usually be the name of the project or team whose code is contained in that namespace.
* The code in that namespace should usually be in a directory whose basename matches the namespace name (or subdirectories thereof).
* For internal namespaces, be wary of other code being added to the same internal namespace causing a collision.
* Also see the [Scoping - Namespaces Section](#namespaces).

##### Enumerator Names

* Enumerators (for both scoped and unscoped enums) should be named preferably like constants e.g. `kEnumName`. Second accepted version is all caps, like macros i.e., `ENUM_NAME`. However, try to adhere to the first option as much as possible.

##### Macro Names

* You're not really going to define a macro, are you? If you do, they're like this: `MY_MACRO_THAT_SCARES_SMALL_CHILDREN`. So, if they are absolutely needed, then they should be named with all capitals and underscores.

#### Comments

**Remember**: while comments _may be_ very important, the **best code** is self-documenting (see [amendments](#comments_1) for the more on this). Giving sensible names to types and variables is much better than using obscure names that you must then explain through comments.

If you still decide to write comments, the following rules describe what you should comment and where. When writing your comments, make sure you write for your audience: the next contributor who will need to understand your code. Be generous — the next one may be you! 

##### Comment Style
Use `/* */` syntax, as most static code analysis tools, like for MISRA C, allow only this style.

##### File Comments
* Start each file with license boilerplate. Choose the appropriate boilerplate for the license used by the project (for example, Apache 2.0, BSD, LGPL, GPL).
* File comments describe the contents of a file.
  * If a file declares, implements, or tests exactly one abstraction that is documented by a comment at the point of declaration, file comments are not required.
* If a `.h` declares multiple abstractions, the file-level comment should broadly describe the contents of the file, and how the abstractions are related.
* Do not duplicate comments in both the .h and the .cc. Duplicated comments diverge.

##### Class Comments
* Every non-obvious class declaration should have an accompanying comment that describes what it is for.
* The class comment should provide the reader with enough information to know how and when to use the class, as well as any additional considerations necessary to correctly use the class.
    * **Avoid** creating examples of class-usage as it is time consuming, will diverge with time and clutters the code.
* Comments describing the use of the class should go together with its interface definition.
* Comments about the class operation and implementation should accompany the implementation of the class's methods.

##### Function Comments

* We might have comments on the function declaration (e.g. on the `.h` file) or in the function definition (e.g. on the `.cpp` file) but never in both.

###### Function Declarations

* Almost every function declaration should have comments immediately _preceding it_ that describe what the function does and how to use it.
  * Omit such comments if the function is simple and obvious!!!
  * Omit self-evident function comments: such as /\* Constructor \*/ or /\* Destructor \*/
* Comments should be descriptive ("Opens the file") rather than imperative ("Open the file").
* Types of things to mention in comments at the function declaration:
  * What the inputs and outputs are.
  * For class member functions: whether the object remembers reference arguments beyond the duration of the method call, and whether it will free them or not.
  * If the function allocates memory that the caller must free.
  * Whether any of the arguments can be a null pointer.
  * If there are any performance implications of how a function is used.
  * If the function is re-entrant. What are its synchronization assumptions?
* When documenting function overrides, focus on the specifics of the override itself, rather than repeating the comment from the overridden function.


###### Function Definitions

If there is anything tricky about how a function does its job, the function definition should have an explanatory comment. 
* E.g. you might describe any coding tricks you use, give an overview of the steps you go through.

**NOTE:** Rather than using a comment, think if you can have a better implementation.

##### Variable comments

* In general the actual name of the variable should be descriptive enough to give a good idea of what the variable is used for.
* The purpose of each class data member must be clear. If there are any invariants not clearly expressed by the type and name, they must be commented, e.g. existence and meaning of sentinel values, such as `nullptr` or `-1`.
* All global variables should have a comment describing what they are! And (if unclear) why it needs to be global.

##### Other comments (scenarios)

* In your implementation you should have comments in tricky, non-obvious, interesting, or important parts of your code.
* Do not state the obvious! In particular, don't literally describe what code does, unless the behavior is non-obvious to a reader who understands C++ well.
* Pay attention to punctuation, spelling, and grammar; it is easier to read well-written comments than badly written ones. In many cases, complete sentences are more readable than sentence fragments.
* Shorter comments, such as comments at the end of a line of code, can sometimes be less formal, but you should be consistent with your style.
* Use `TODO` comments for code that is temporary, a short-term solution, or good-enough but not perfect.
  * `TODO`s should include the string `TODO` in all caps, followed by the name, e-mail address, bug ID, or other identifier of the person or issue with the best context about the problem referenced by the `TODO`. E.g. `// TODO(bug 12345): remove the "Last visitors" feature`
* Mark deprecated interface points with `DEPRECATED` comments. 
  * The comment goes either before the declaration of the interface or on the same line as the declaration.
  * After the word `DEPRECATED`, write your name, e-mail address, or other identifier in parentheses.
  * A deprecation comment must include simple, clear directions for people to fix their call-sites.

#### Formatting

Most of the styling can be handled by an auto-formating tool/linter. Therefore, in this section only highlights or some aspects that are not handled by such tools are discussed. Example of such tools that support the Google Style are
 * [Artistic Style](http://astyle.sourceforge.net/astyle.html) 
 * [ClangFormat](https://clang.llvm.org/docs/ClangFormat.html)

Use an IDE that supports automatic style formatting. E.g. with QT Creator use the [Beautifer plugin](http://doc.qt.io/qtcreator/creator-beautifier.html) with [Artistic Style](http://astyle.sourceforge.net/astyle.html) or [ClangFormat](https://clang.llvm.org/docs/ClangFormat.html). Eclipse supports importing of styles via an `.xml` file.

##### Line Length
\* As opposed to Google Style, we chose to limit the line length to 120 characters rather than the standard old-fashioned 80 characters.

##### Non-ASCII Characters
Non-ASCII characters should be rare, and must use UTF-8 formatting. See more details on [Google C++ Style](https://google.github.io/styleguide/cppguide.html#Non-ASCII_Characters)

##### Spaces vs Tabs
Use only spaces, and indent two spaces at a time. Set your editor to emit spaces when you hit the tab key.

##### Lambda Expressions
* Format parameters and bodies as for any other function, and capture lists like other comma-separated lists.
* Short lambdas may be written inline as function arguments.

##### Function Declarations and Definitions
Unused parameters that might not be obvious should comment out the variable name in the function definition:
```
class Circle : public Shape {
 public:
  void Rotate(double radians) override;
};

void Circle::Rotate(double /*radians*/) {}
```

```
// Bad - if someone wants to implement later, it's not clear what the
// variable means.
void Circle::Rotate(double) {}
```
##### Function Calls
* If having multiple arguments in a single line decreases readability due to the complexity or confusing nature of the expressions that make up some arguments, try creating variables that capture those arguments in a descriptive name:
```
int my_heuristic = scores[x] * y + bases[x];
bool result = DoSomething(my_heuristic, x, y, z);
```

* If there is still a case where one argument is significantly more readable on its own line, then put it on its own line. The decision should be specific to the argument which is made more readable rather than a general policy.

* Sometimes arguments form a structure that is important for readability. In those cases, feel free to format the arguments according to that structure:
```
// Transform the widget by a 3x3 matrix.
my_widget.Transform(x1, x2, x3,
                    y1, y2, y3,
                    z1, z2, z3);
```

##### Braced Initializer List Format

* Format a braced initializer list exactly like you would format a function call in its place.
* If the braced list follows a name (e.g. a type or variable name), format as if the `{}` were the parentheses of a function call with that name. If there is no name, assume a zero-length name.
```
// Examples of braced init list on a single line.
return {foo, bar};
functioncall({foo, bar});
std::pair<int, int> p{foo, bar};
```

##### Conditionals
* Short conditionals not allowed
```
if (x == kFoo) return new Foo();

if (condition)
  DoSomething();  // 2 space indent.
```
* Only accepted forms are
```
if (condition) {
  DoSomething();  // 2 space indent.
}

// Curly braces around both IF and ELSE required because
// one of the clauses used braces.
if (condition) {
  foo;
} else {
  bar;
}
```

##### Loops and Switch Statements
* Fall-through from one case label to another must be annotated using the `ABSL_FALLTHROUGH_INTENDED`; macro (defined in `absl/base/macros.h`). `ABSL_FALLTHROUGH_INTENDED`; should be placed at a point of execution where a fall-through to the next case label occurs. A common exception is consecutive case labels without intervening code, in which case no annotation is needed.
```
switch (x) {
  case 41:  // No annotation needed here.
  case 43:
    if (dont_be_picky) {
      // Use this instead of or along with annotations in comments.
      ABSL_FALLTHROUGH_INTENDED;
    } else {
      CloseButNoCigar();
      break;
    }
  case 42:
    DoSomethingSpecial();
    ABSL_FALLTHROUGH_INTENDED;
  default:
    DoSomethingGeneric();
    break;
}
```
* Single-statement loops must use braces!
```
for (int i = 0; i < kSomeNumber; ++i) {
  printf("I take it back\n");
}

```
* Empty loop bodies should use either an empty pair of braces or continue with no braces, rather than a single semicolon.
```
while (condition) {
  // Repeat test until it returns false.
}
for (int i = 0; i < kSomeNumber; ++i) {}  // Good - one newline is also OK.
while (condition) continue;  // Good - continue indicates no logic.

while (condition);  // Bad! - looks like part of do/while loop.
```

##### Pointer and Reference Expressions
* When declaring a pointer variable or argument, you may place the asterisk adjacent to either the type or to the variable name;
* It is allowed (if unusual) to declare multiple variables in the same declaration; 
* It is **disallowed** if any of the variables have pointer or reference decorations. Such declarations are easily misread. 
```
int x, *y;  // Disallowed - no & or * in multiple declaration
char * c;  // Bad - spaces on both sides of *
const string & str;  // Bad - spaces on both sides of &
```

##### Variable and Array Initialization
* Your choice of `=`, `()`, or `{}`.
* Be careful when using a braced initialization list `{...}` on a type with an `std::initializer_list` constructor. A nonempty braced-init-list prefers the `std::initializer_list` constructor whenever possible. Note that empty braces `{}` are special, and will call a default constructor if available. To force the non-`std::initializer_list` constructor, use parentheses instead of braces.
* The brace form prevents narrowing of integral types. This can prevent some types of programming errors.
```
int pi(3.14);  // OK -- pi == 3.
int pi{3.14};  // Compile error: narrowing conversion.
```

##### Preprocessor Directives
* The hash mark that starts a preprocessor directive should always be at the beginning of the line.
* Even when preprocessor directives are within the body of indented code, the directives should start at the beginning of the line.
```
// Good - directives at beginning of line
  if (lopsided_score) {
#if DISASTER_PENDING      // Correct -- Starts at beginning of line
    DropEverything();
# if NOTIFY               // OK but not required -- Spaces after #
    NotifyClient();
# endif
#endif
    BackToNormal();
  }
```

##### Class Format
* The `public:`, `protected:`, and `private:` keywords should be indented one space.
* Except for the first instance, these keywords should be preceded by a blank line. This rule is optional in small classes.

##### Namespace Formatting
* The contents of namespaces are not indented
* Namespaces do not add an extra level of indentation
* When declaring nested namespaces, put each namespace on its own line

##### Horizontal Whitespace
* Don't introduce trailing whitespace.

##### Vertical Whitespace
* Minimize use of vertical whitespace.
* This is more a principle than a rule: don't use blank lines when you don't have to. In particular:
    * don't put more than one or two blank lines between functions
    * resist starting functions with a blank line
    * don't end functions with a blank line
    * be discriminating with your use of blank lines inside functions.

* The basic principle is: The more code that fits on one screen, the easier it is to follow and understand the control flow of the program. Of course, readability can suffer from code being too dense as well as too spread out, so use your judgement. But in general, minimize use of vertical whitespace.
* Some rules of thumb to help when blank lines may be useful:
    * Blank lines at the beginning or end of a function very rarely help readability.
    * Blank lines inside a chain of if-else blocks may well help readability.


#### Amendments
##### File naming
* Source code files should have **.cpp** extension
* Header files that contain only definitions should have **.h** extension
* Header files that contain both definitions **AND** implementation should have **.hpp** extension

##### Functions
* Avoid using `switch` statements. Tolerated only when working with polymorphic objects.
* Consider changing the function signature to replace a `bool` argument with an `enum` argument. This will make the argument values self-describing.
* For functions that have several configuration options, consider defining a single class or `struct` to hold all the options, and pass an instance of that.
* In C++, you can implement a deprecated function as an inline function that calls the new interface point.
  * New code should not contain calls to deprecated interface points.

##### Comments
* "_The only truly good comment is the comment you found a way not to write._" [Clean Code, by Robert C. Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) 
    * opt for better coding rather than bad code amended with comments; code changes while comments tend to diverge/degrade with time (comments don't compile :) ). 
    * Read [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) Chapter 4: Comments;
    * This is also a strong rationale: [Software is the design.](https://www.developerdotstar.com/printable/mag/articles/reeves_design.html)
* Summary: keep comments to a minimum and don't make it a habit

##### Formatting 
* We chose to limit line length to 120 characters rather than the standard old-fashioned 80 characters.

---

### Python Coding Style
* Before you start, check [Zen of Python](https://www.python.org/dev/peps/pep-0020/).
* Follow [PEP8](http://www.python.org/dev/peps/pep-0008/).

#### Automatic formatting
* Use an editor/IDE that supports a linter with [PEP8](http://www.python.org/dev/peps/pep-0008/) rules in place, e.g. [Sublime-Text](https://www.sublimetext.com/) with [Anaconda](http://damnwidget.github.io/anaconda/)
* Or use a command line linter that can check [PEP8](http://www.python.org/dev/peps/pep-0008/) rules

---

### TCL Coding Style
* TODO: e.g. follow [this](http://www.cs.columbia.edu/~hgs/etc/tcl-style.txt)

---

### Git Naming Style
* TODO

### References
[1. Clean Code, Robert C. Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
[2. Google C++ Style](https://google.github.io/styleguide/cppguide.html)
[3. What Is Software Design? By Jack W. Reeves](https://www.developerdotstar.com/printable/mag/articles/reeves_design.html)
[4. Wikipedia: KISS Principle](https://en.wikipedia.org/wiki/KISS_principle)
[5. Wikipedia: Don't repeat yourself](http://en.wikipedia.org/wiki/Don't_repeat_yourself)
[6. Zen of Python](https://www.python.org/dev/peps/pep-0020/)
[7. PEP8](http://www.python.org/dev/peps/pep-0008/)

## License
See [License](LICENSE)
