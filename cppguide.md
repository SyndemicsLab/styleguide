# Syndemics Lab C++ Style Guide

The motivation for the Syndemics Lab C++ Style Guide is due to public deployment of code. With an intent to distribute and utilize open source software, this style guide intends to regularize the syntax for easy uptake and transition between repositories. It is designed to be maintained by the `.clang-format` found in each project and all pull request workflow CI/CD checks require passing the style check before approval. This guide is heavily based off the [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) while modifying a few style changes to make our code easier to use/read.

## C++ Version Support

All projects must support C++20 or later. While we do not currently require C++24 support we regularly update our compilers so as to make use of new STL functionalities.

## Repo Structure

All repositories must have the following directories:

- `include/<projectname>`
- `src/` - (if not a headerless library)
- `tests/`
- `tools/`
- `extras/` - (if additional, non-library features such as language bindings or benchmarking are required)
- `docs/`
- `build/`

The following files are expected to exist in the root of the repository:

- `.clang-format`
- `.gitignore`
- `CMakeLists.txt`
- `Doxyfile`
- `LICENSE`
- `README.md`

This structure is loosely based on the pitchfork repo structure found [here](https://raw.githubusercontent.com/vector-of-bool/pitchfork/refs/heads/develop/data/spec.bs).

## Headers

In general, there should be a header file for each source file. Headers are denoted through the extension `.hpp` and can contain virtual function/class definitions or implementation details. Template definitions should not be contained in headers but instead in their own `.tpp` files (see [Templates](#templates)) to keep the logic separated.

### Header Guards

While we acknowledge the regular use of `#pragma once` in modern C++ development, we utilize the more traditional `#define` guard style as it is more portable across compilers. This means **ALL** headers should contain a header guard following the pattern: `<PROJECT>_<PATH>_<FILE>_HPP_`. An example is given below for the file `simdemics/include/simdemics/models/Respond.hpp`.

```cpp
#ifndef SIMDEMICS_MODELS_RESPOND_HPP_
#define SIMDEMICS_MODELS_RESPOND_HPP_

// Header Content Goes Here

#endif // SIMDEMICS_MODELS_RESPOND_HPP_
```

### Includes

If a symbol is defined somewhere else, a file should directly include the header file (**NEVER THE SOURCE FILE**) that properly defines that symbol. Any headers that are unnecessary for the code to compile and run should be removed from the list of includes.
**Note: Do not rely on transitive includes.**

All project header files should be included without the use of UNIX directory aliases (i.e. never use `./` or `../` in an include).

#### Note on Forward Declarations

Try to avoid forward declarations when possible. This is specifically because of potentially undefined behavior and difficulty with linkage errors during compile time. While not forbidden, it is highly discouraged.

### Order of Includes

The order of includes should go:

1. Related Header - (e.g. `foo.hpp` should be first in `foo.cpp`)
2. C System Headers - (`<stdlib.h>`)
3. C++ Standard Headers - (`<iostream>`)
4. External C++ Library Headers - (`<Eigen/Eigen>`)
5. Additional Local Project Headers - (e.g. `bar.hpp` included in `foo.cpp`)

Between each of these includes should be a blank line. Thus, an example set of includes for `simdemics/src/models/RespondImpl.hpp` would look like:

```cpp
#ifndef SIMDEMICS_MODELS_RESPONDIMPL_HPP_
#define SIMDEMICS_MODELS_RESPONDIMPL_HPP_

#include <simdemics/models/Respond.hpp>

// C System Headers: #include <stdlib.h>

#include <unordered_map>

#include <Eigen/Eigen>

// Additional Local Project Headers: #include <sim/foo.hpp>

...

#endif // SIMDEMICS_MODELS_RESPONDIMPL_HPP_
```

There are several exceptions to this rule, the most common being conditional includes of headers. Just make sure to keep this include localized.

## Scoping

### Namespaces

Code should always exist in a namespace. With that being said, avoid *using-directives*, e.g. `using namespace foo`. This is to maintain readability and prevent unexpected name collisions. Also, do as much as possible to avoid using `inline namespace` containers. This is, again, to prevent collisions between expected namespace locations and to reduce unnecessary clutter. **NEVER** declare anything in the `std` namespace.

- Namespaces should follow the respective rules on [naming namespaces](#naming-namespaces).
- Multi-line namespaces should terminate with a comment.
- Namespaces should wrap the entire source file after includes and flags.

### Nonmember, Static, and Global Functions

Global functions should be used *very, very rarely*. Instead, try to place non-member functions inside an unnamed namespace. All static methods that belong to a class should be closely related to the instances of the class, otherwise they should be non-member functions.

If you define a nonmember function only needed in the source `.cpp` file, use internal linkage.

### Local Variables

Local variables should be declared as close as possible to their first use. This is so that it is easier for the reader to determine the use and what it is initialized to. That being said, initialization should be used instead of separating declaration from assignment.

## Classes

### Constructors

Avoid virtual method calls in constructors and avoid initialization that can fail if you are unable to signal an error. Instead, make use of factory patterns and the return type as necessary.

### Copyable and Movable Classes

Classes should explicitly declare whether a class is copyable or movable. This means choosing one of the four default layouts below:

```cpp
class Neither {
public:
    Neither(const Neither &other) = delete;
    Neither &operator=(const Neither &other) = delete;

    Neither(Neither &&other) = delete;
    Neither &operator=(Neither &&other) = delete;
};

class CopyOnly {
public:
    CopyOnly(const CopyOnly &other) = default;
    CopyOnly &operator=(const CopyOnly &other) = default;

    CopyOnly(CopyOnly &&other) = delete;
    CopyOnly &operator=(CopyOnly &&other) = delete;
};

class MoveOnly {
public:
    MoveOnly(const MoveOnly &other) = delete;
    MoveOnly &operator=(const MoveOnly &other) = delete;

    MoveOnly(MoveOnly &&other) = default;
    MoveOnly &operator=(MoveOnly &&other) = default;
};

class Both {
public:
    Both(const Both &other) = default;
    Both &operator=(const Both &other) = default;

    Both(Both &&other) = default;
    Both &operator=(Both &&other) = default;
};
```

The only exceptions to not including one of the four declarations are:

- The class has no private sections
- If the class is subclass to a base class that is clearly not copyable or movable

### Structs vs. Classes

Only use a struct as a way to carry public data. All other containers should be classes. If in doubt, make it a class.

### Inheritance

Try to restrict inheritance to an "is-a" case: (e.g. `Bar` subclasses `Foo` if it can be said that `Bar` is a kind of `Foo`.) Any overrides should be explicitly notated as such in the subclass. Do not use virtual when declaring an override.

### Private vs. Public

Unless they are constants, all class data members should be `private`. The only exception is data members of a text fixture in "Google Test".

### Order of a Class

Classes should follow the order outlined in the example below, note that empty sections should be omitted:

```cpp
class Foo{
public:
    // 1. types and aliases
    // 2. Static Constants
    // 3. Factories
    // 4. Constructors and Operators (Copy, Move, Operator Overloading)
    // 5. Destructors
    // 6. All other Member Functions
protected:
private:
    // 7. Data Members
};
```

## Functions

Functions should be considered completely black box to any calling code. This means that all behavior should be handled and no hard breaks should occur. That being said, it is highly encouraged to write small and focused functions. This is to allow for easier/more concise debugging.

### Inputs and Outputs

It is preferred that outputs from a function are obtained through the return value rather than output parameters if possible. Avoid returning raw pointers as much as possible.

### Function Overloading and Default Arguments

Overloading functions should be done only if a reader can understand what is going on without needing to know which overload is being used. Default arguments serve a similar purpose to function overloading, thus they should only be used when they provide an increase in readability over function overloading. When in doublt, use function overloading.

## C++ Features

### Casting

We prefer using C++ style casts (e.g. `static_cast<float>(double_value)`) over the C style cast (e.g. `(int)x = 1.3`).

### Incrementing

We prefer using the prefix increment form unless you explicitly need postfix semantics. In other words, use `++i` instead of `i++` whenever possible.

### Portability

Write code that can be run on any type of architecture. **DO NOT** rely on CPU features.

### Macros

Avoid macros. Instead, write inline functions, enums, and const variables.

### Lambdas

Always use explicit capture when the labmda will leave the current scope. Otherwise lambdas are encouraged, especially when being used with `std::function` and `std::bind` to create generalized callback mechanisms.

## Templates

Try to avoid template metaprogramming when possible. That being said, templates make C++ extremely powerful and are the only way many projects have been able to be implemented. The problem lies in the fact that templates are well understood by a very very small portion of the community and extremely difficult to debug. Not to mention, templates become a very easy temptation to become overly clever and miss important details.

If you decide to use template programming, do as much as possible to simplify the code as much as possible. In addition, make sure to well document the code and hide it as much as possible inside implementation details.

### Aliases

When possible, `using` is prefered to `typedef` because it is more consistent with the rest of C++ and works with templates.

### Switch Statements

All switch statements should have a default case. Note fallthrough statements using the `[[fallthrough]]` attribute.

## Inclusive Language

In all code, including naming and comments, use inclusive language and avoid terms that other programmers might find disrespectful or offensive (such as "master" and "slave", "blacklist" and "whitelist", or "redline"), even if the terms also have an ostensibly neutral meaning. Similarly, use gender-neutral language unless you're referring to a specific person (and using their pronouns). For example, use "they"/"them"/"their" for people of unspecified gender (even when singular), and "it"/"its" for software, computers, and other things that aren't people.

## Naming

Naming is arguably the most controversial part of any style guide. Yet, we recognize that naming is key to any developer quickly and easily identifying the tools they are working with. That being said, the first, most fundamental rule regarding naming is use names that describe the purpose of the object. Abbreviations are ok if its listed in sources like Wikipedia.

### Naming Files

Filenames should be lowercase. In addition, the default extension for any source files is `.cpp` the extension for headers is `.hpp` and the extension for files containing templates is `.tpp`. If they contain multiple words, they should be separated with underscores. Examples include:

- `hello_world.cpp`
- `my_script_header.hpp`
- `my_template_implementations.tpp`

### Naming Types

Type Names should be CamelCased. That is, they should start with a capital letter and have another capital letter for each new word without any space between the words (e.g. `MyNewlyNamedClass`).

```cpp
class RespondModel {}
struct DataContainer {}
using MyMap = std::map<int, int>;
enum class MyEnums{}
```

### Naming Variables

Variables should be in snake case. This means they should be all lowercase with underscores between words. For private data members of a class (as all data members should be) we append an underscore.

```cpp
std::string my_variable;

class MyClass{
private:
    std::string _my_classname;
};
```

### Naming Functions

Functions are a bit weird because they are mixed case. That being said, the vast majority of functions should be CamelCased similar to [Naming Types](#naming-types). The only exception is accessor functions (i.e. getters and setters) which may be written in snake case like their variables.

### Naming Namespaces

Namespaces should follow snake case rules. Top-level namespace names should be the same as the project name and should avoid collisions with well-known top-level namespaces.

### Naming Constants and Enums (and Avoiding Macros)

Constants and Enumerations should take the same naming convention. That is, they are CamelCased with a leading `k` before the variable.

```cpp
enum class MyEnum {
    kValueOne = 0,
    kNextValue,
    kLastValue,
};

const int kMyConstant = 10;
```

Previously, enums were named similarly to macros (i.e. fully capitalized). As we are avoiding macros in our code, we are attempting to remove the macro naming convention as well.

## Comments

Comments should exist throughout the code explaining any complex portion of code. That being said, commented code should not be left in files. For consistency sake, we only utilize the `//` style comment syntax due to the common acceptance.

### License Comments

All files should have the boilerplate outlined below:

```cpp
////////////////////////////////////////////////////////////////////////////////
// File: <filename>                                                           //
// Project: <project name>                                                    //
// Created Date: <dd MMM yyyy>                                                //
// Author: <author name>                                                      //
// -----                                                                      //
// Last Modified: <date>                                                      //
// Modified By: <author name>                                                 //
// -----                                                                      //
// Copyright (c) 2025 Syndemics Lab at Boston Medical Center                  //
// -----                                                                      //
// HISTORY:                                                                   //
// Date         By  Comments                                                  //
// ----------   --- --------------------------------------------------------- //
////////////////////////////////////////////////////////////////////////////////
```

### Function Comments

Comments for a function should be immediately preceding the declaration and include:

- A brief description of the expected functionality
- A list of the parameters and their expected values
- A description of the expected return value

### TODO Comments

Use TODO comments for code that is temporary, a short-term solution, or good-enough but not perfect. `TODO` should be in all capital letters followed by the associated bug/task and specific anticipated addressed date.

## Formatting

Line length is 80 characters. Set your editor so that one tab is equal to 4 spaces. Utilize UTF-8 Encoding and LF line endings (why microsoft even uses CRLF still is beyond the writer's understanding).

### Looping and Branching

At a high level, looping or branching statements consist of the following components:

- One or more statement keywords (e.g. if, else, switch, while, do, or for).
- One condition or iteration specifier, inside parentheses.
- One or more controlled statements, or blocks of controlled statements.

For these statements:

- The components of the statement should be separated by single spaces (not line breaks).
- Inside the condition or iteration specifier, put one space (or a line break) between each semicolon and the next token, except if the token is a closing parenthesis or another semicolon.
- Inside the condition or iteration specifier, do not put a space after the opening parenthesis or before the closing parenthesis.
- Put any controlled statements inside blocks (i.e. use curly braces).
- Inside the controlled blocks, put one line break immediately after the opening brace, and one line break immediately before the closing brace.

```cpp
if (condition) {
    DoOneThing(); 
    DoAnotherThing();
} else if (int a = f(); a != 3) {  
    DoAThirdThing(a);
} else {
    DoNothing();
}

while (condition) {
    RepeatAThing();
}

do {
    RepeatAThing();
} while (condition);

for (int i = 0; i < 10; ++i) {
    RepeatAThing();
}
```

### Return Statements

Do not put parenthesis around return values.

### Preprocessor Directives

The hash mark goes at the beginning of the line, even if the code is indented.

```cpp
class MyClass {
public:
#if DEBUG_MODE
    void PrintDebug();
#endif
};
```

### Class Format

The `public`, `protected`, and `private` sections should be on the same indentation depth as the class declaration. The sub-elements should thus be indented.

```cpp
class MyClass {
public:
    void MyFunction();
};
```

### Namespace Format

Content in namespaces should not be indented.

## CMake

All projects are expected to utilize CMake with a version targeting at least 3.20. This version is regularly updated as more CMake versions are added.
