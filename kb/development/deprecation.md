# Deprecations

To ensure backwards-compatibility between versions of Mbed TLS, elements of the public interface may not be removed except during the release of a new major version. Instead, they may be 'deprecated', indicating to users that they will be removed in the future.

When a build is made with `MBEDTLS_DEPRECATED_WARNING` defined, a compiler warning will be generated to warn a user that a function or constant will be removed at some point in the future, notifying users that they should change to a replacement function or constant at their own convenience. Note: this only works when using GCC-like compilers that recognize `__attribute__((deprecated))`.

To find examples of past deprecations, look at the previous major version of Mbed TLS.

## Deprecating functions

To deprecate a function, the following actions must be taken:
* In the header file (e.g. `include/mbedtls/xyz.h`):
    * Surround the function declaration with `#if !defined(MBEDTLS_DEPRECATED_REMOVED)`.
    * Add a line to the docstring consisting of the keyword `\deprecated` followed by the reason for deprecation and (where applicable) the function it is superseded by.
    * Add the `MBEDTLS_DEPRECATED` annotation to the function prototype (so `int foo();` becomes `int MBEDTLS_DEPRECATED foo();`).
    * Make sure the header file includes `mbedtls/platform_util.h` (this contains the definition of `MBEDTLS_DEPRECATED`).
* In the source file (e.g. `library/xyz.c`):
    * Surround the function definition with `#if !defined(MBEDTLS_DEPRECATED_REMOVED)`.
    * If the function is superseded, ensure there is no code duplication between the old function and the new one. Typically this means refactoring the old function to be a simple wrapper around the new function.
* In a new file in ChangeLog.d (e.g. `ChangeLog.d/change-xyz-api.txt`)
    * Add an entry under the heading `New deprecations` that describes the deprecation. See [the ChangeLog readme](https://github.com/Mbed-TLS/mbedtls/blob/development/ChangeLog.d/00README.md) for more information on the format of ChangeLog entries.
* In the function's unit tests (see [the test guidelines](test_suites.md) for more information on unit tests):
    * Add `MBEDTLS_TEST_DEPRECATED` to the `depends_on:` line in the `.function` file that tests the deprecated function.
    * If there are test functions that can test either the deprecated function or the replacement, they may use the directive `#if defined(MBEDTLS_TEST_DEPRECATED)` to select the function to use.

## Deprecating constants

Numeric and string constants can also be deprecated by wrapping them in the macros `MBEDTLS_DEPRECATED_NUMERIC_CONSTANT()` and `MBEDTLS_DEPRECATED_STRING_CONSTANT()` respectively. For example, the code:
```c
const int X = 42;
```
would become:
```c
const int X = MBEDTLS_DEPRECATED_NUMERIC_CONSTANT(42);
```
The file where the constant is defined must include `mbedtls/platform_util.h` as it contains the definition of `MBEDTLS_DEPRECATED_NUMERIC_CONSTANT`.

Any testcases (in `.data` files) that use the deprecated constant must have `MBEDTLS_TEST_DEPRECATED` added to their `depends_on:` line.

## Partial deprecation

From time to time it is necessary to deprecate one specific aspect of a function. For example, calling a function in a particular mode or with a particular parameter may be deprecated. In this case, simply add a line to the function's doxygen description explaining the deprecation. For example:
```c
/**
 * \brief           This function divides x by y
 *
 * \deprecated      It is deprecated to call this function with y = 0.
 *                  Future versions of this function will return an error
 *                  when called with y = 0, rather than returning INT_MAX as
 *                  is the current behaviour.
 *
 * ...
 */
```
