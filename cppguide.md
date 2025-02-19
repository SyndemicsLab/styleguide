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

This structure is loosely based on the pitchfork repo structure found [here](https://api.csswg.org/bikeshed/?force=1&url=https://raw.githubusercontent.com/vector-of-bool/pitchfork/develop/data/spec.bs).

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

Try to avoid forward declarations when possible. This is specifically because of potentially undefined behavior and difficulty with linkage errors during compile time. While not forbidden, it is highly recommended against.

### Order of Includes

The order of includes should go:

1. Related Header - (e.g. `foo.hpp` should be first in `foo.cpp`)
2. C System Headers - (`<stdlib.h>`)
3. C++ Standard Headers - (`<iostream>`)
4. External C++ Library Headers - (`<Eigen/Eigen>`)
5. Additional Local Project Headers - (e.g. `bar.hpp` included in `foo.cpp`)

Between each of these includes should be a blank line. Thus, an example set of includes for `simdemics/src/models/RespondImpl.hpp` would look like:

```cpp
#ifndef SIMDEMICS_MODELS_REPSONDIMPL_HPP_
#define SIMDEMICS_MODELS_REPSONDIMPL_HPP_

#include <simdemics/models/Respond.hpp>

// C System Headers: #include <stdlib.h>

#include <unordered_map>

#include <Eigen/Eigen>

// Additional Local Project Headers: #include <sim/foo.hpp>

...

#endif // SIMDEMICS_MODELS_REPSONDIMPL_HPP_
```

There are several exceptions to this rule. The most common being conditional includes of headers. Just make sure to keep this include localized.

## Scoping

### Namespaces

Code should always exist in a namespace. With that, avoid using the *using-directives* (e.g. `using namespace foo`). This is to maintain readability and prevent unexpected collisions. Also do as much as possible to avoid using `inline namespace` containers. This is, again, to prevent collisions between expected namespace locations and unnecessary clutter. **NEVER** declare anything in the `std` namespace.

- Namespaces should follow the respective rules on [naming namespaces](#naming-namespaces).
- Multi-line namespaces should terminate with a comment.
- Namespaces should wrap the entire source file after includes and flags.

### Nonmember, Static, and Global Functions

Very very rarely should global functions ever be used. Instead, try to place nonmember functions inside an unnamed namespace. All static methods that belong to a class should be closely related to the instances of the class, otherwise they should be nonmember functions.

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

## Templates

## Naming

### Naming Namespaces

## CMake

All projects are expected to utilize CMake with a version targeting at least 3.20. This version is regularly updated as more CMake versions are added.
