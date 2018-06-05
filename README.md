# GCE-Math &nbsp; [![Build Status](https://travis-ci.org/kthohr/gcem.svg?branch=master)](https://travis-ci.org/kthohr/gcem) [![Coverage Status](https://codecov.io/github/kthohr/gcem/coverage.svg?branch=master)](https://codecov.io/github/kthohr/gcem?branch=master) [![License](https://img.shields.io/badge/Licence-Apache%202.0-blue.svg)](./LICENSE)

GCE-Math (**G**eneralized **C**onstant **E**xpression Math) is a templated C++ library for compile-time computation of mathematical functions.

* The library is written in C++11 ```constexpr``` format, and is C++11/14/17 compatible.
* Continued fraction and series expansions are implemented using recursive templates.
* The ```gcem::``` syntax is identical to the C++ standard library (`std::`).
* Tested and accurate to machine precision against the C++ standard library.
* Released under a permissive, non-GPL license.

## Status

The library is actively maintained, and is still being extended. A list of features includes:

* basic C++ standard library functions:
    - ```abs```, ```exp```, ```log```, ```max```, ```min```, ```pow```, ```sqrt```
* trigonometric functions:
    - basic: ```cos```, ```sin```, ```tan```
    - inverse: ```acos```, ```asin```, ```atan```
    - hyperbolic (area) functions: ```cosh```, ```sinh```, ```tanh```, ```acosh```, ```asinh```, ```atanh```
* special functions:
    - factorials and the binomial coefficient: ```factorial```, ```binomial_coef```
    - beta and gamma functions: ```beta```, ```lbeta```, ```lgamma```, ```tgamma```
    - the Gaussian error function and inverse error function: ```erf```, ```erf_inv```
    - (regularized) incomplete beta and incomplete gamma functions: ```incomplete_beta```, ```incomplete_gamma```
    - inverse incomplete beta and incomplete gamma functions: ```incomplete_beta_inv```, ```incomplete_gamma_inv```

## Syntax

GCE-Math functions are written as C++ templates with `constexpr` specifiers, the format of which might be confusing to users unfamiliar with template-based programming. For example, the [Gaussian error function](https://en.wikipedia.org/wiki/Error_function) (```erf```) is defined as:
```cpp
template<typename T>
constexpr
return_t<T>
erf(const T x);
```
where a set of internal templated ```constexpr``` functions will implement a continued fraction expansion to return a value of type ```return_t<T>```. This output type ('```return_t<T>```') is generally determined by the input type, e.g., ```int```, ```float```, ```double```, ```long double```, etc. When ```T``` is an intergral type, the output will be upgraded to ```return_t<T> = double```, otherwise ```return_t<T> = T```. For types not covered by ```std::is_integral```, recasts should be used.


## Installation

GCE-Math is a header-only library and does not require any additional libraries (beyond a C++11 compatible compiler). Simply include the gcem header files with your project.

## Examples

To calculate 10!:

```cpp
#include "gcem.hpp"

int main()
{
    constexpr int x = 10;
    constexpr int res = gcem::factorial(x);

    return 0;
}
```
Inspecting the assembly code generated by Clang:
```assembly
_main:                                  ## @main
	.cfi_startproc
## BB#0:
	push	rbp
Lcfi0:
	.cfi_def_cfa_offset 16
Lcfi1:
	.cfi_offset rbp, -16
	mov	rbp, rsp
Lcfi2:
	.cfi_def_cfa_register rbp
	xor	eax, eax
	mov	dword ptr [rbp - 4], 0
	mov	dword ptr [rbp - 8], 10
	mov	dword ptr [rbp - 12], 3628800
	pop	rbp
	ret
	.cfi_endproc
```
We see that a function call has been replaced by a numeric value (10! = 3628800).

Similarly, to compute the log Gamma function at a point:

```cpp
#include "gcem.hpp"

int main()
{
    constexpr long double x = 1.5;
    constexpr long double res = gcem::lgamma(x);

    return 0;
}
```
Assembly code:
```assembly
LCPI0_0:
	.long	1069547520              ## float 1.5
	.section	__TEXT,__literal16,16byte_literals
	.p2align	4
LCPI0_1:
	.quad	-622431863250851168     ## x86_fp80 -0.120782237635245167208
	.short	49147
	.space	6
	.section	__TEXT,__text,regular,pure_instructions
	.globl	_main
	.p2align	4, 0x90
_main:                                  ## @main
	.cfi_startproc
## BB#0:
	push	rbp
Lcfi0:
	.cfi_def_cfa_offset 16
Lcfi1:
	.cfi_offset rbp, -16
	mov	rbp, rsp
Lcfi2:
	.cfi_def_cfa_register rbp
	xor	eax, eax
	mov	dword ptr [rbp - 4], 0
	fld	dword ptr [rip + LCPI0_0]
	fstp	xword ptr [rbp - 32]
	fld	xword ptr [rip + LCPI0_1]
	fstp	xword ptr [rbp - 48]
	pop	rbp
	ret
	.cfi_endproc
```


To build the full test suite:

```bash
# clone gcem from GitHub
git clone -b master --single-branch https://github.com/kthohr/gcem ./gcem
# compile tests
cd ./gcem/tests
make
./run_tests
```

## Related libraries

For a library built on the GCEM compile-time functionality, see [StatsLib](https://github.com/kthohr/stats).

## Author

Keith O'Hara

## License

Apache Version 2
