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
* Follow [Google C++ Style](https://google.github.io/styleguide/cppguide.html), with the exception of the amendments below.

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
