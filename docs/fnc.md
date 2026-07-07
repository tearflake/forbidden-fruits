# Functional Paradigm

**Functional paradigm** expresses computation through abstraction and application, with no mutable state.

## Introduction

The functional paradigm approaches programming from a fundamentally different perspective. Rather than describing a sequence of state changes, it expresses computation as the evaluation of mathematical functions. Functions are treated as ordinary values that can be created, passed to other functions, and returned as results, enabling programs to be constructed through abstraction and composition rather than mutable state.

The framework adopts a compact representation based on the lambda calculus, one of the foundational models of computation. Although the internal evaluator employs combinatory logic as an implementation technique, programmers write ordinary lambda expressions using abstractions and applications. Like imperative programs, functional bodies operate on the framework's common symbolic expression language, allowing them to interact naturally with code written in other paradigms.

This document introduces the syntax of lambda expressions, explains their evaluation, and demonstrates the expressive power of functional programming through a series of progressively more sophisticated examples.

---

## Formal Syntax

The grammar below defines the abstract syntax of functional bodies. A functional body consists of a single lambda expression, which may be an abstraction, an application, or an ordinary symbolic expression. Although the grammar is intentionally compact, these three forms are sufficient to express arbitrary computations in the lambda calculus.

```
<fnc> := (FNC <lmbd>)

<lmbd> := <abs>
        | <app>
        | <sexpr>

<abs> := (ABS <atomic>+ <lmbd>)

<app> := (APP <lmbd> <lmbd>+)
```

As throughout the framework, the grammar is written in a relaxed Backus–Naur Form (BNF). Non-terminal symbols are enclosed in angle brackets, uppercase identifiers denote literal language constructs, and postfix operators indicate optional or repeated elements. Lambda expressions are represented uniformly as S-expressions, providing a direct correspondence between the concrete syntax and the symbolic structures manipulated by the evaluator.

---

## Informal Semantics

The functional paradigm describes computation as the evaluation of lambda expressions. Unlike the imperative paradigm, a functional body contains no mutable variables, assignments, or explicit control-flow statements. Instead, computation proceeds by constructing functions, applying them to arguments, and reducing the resulting expressions until a value is obtained.

A functional body consists of a single lambda expression (`<lmbd>`). Every lambda expression is one of three forms: an abstraction, an application, or an ordinary symbolic expression. This small set of constructs is sufficient to express arbitrary computation while remaining close to the traditional lambda calculus.

Function definition is expressed by an abstraction:

```
(ABS parameter... body)
```

An abstraction introduces one or more formal parameters and associates them with a body expression. Conceptually, an abstraction describes a function without executing it. Although the syntax permits multiple parameters for convenience, they are semantically equivalent to a sequence of nested single-parameter abstractions. For example,

```
(ABS x y z body)
```

may be understood as

```
(ABS x
    (ABS y
        (ABS z
            body)))
```

Function application is expressed using

```
(APP function argument...)
```

The first subexpression is evaluated as a function, while the remaining subexpressions provide its arguments. As with abstractions, multiple arguments are a syntactic convenience corresponding to a sequence of unary applications. Thus,

```
(APP f x y)
```

is equivalent to

```
(APP
    (APP f x)
    y)
```

The remaining form of lambda expression is the symbolic expression (`<sexpr>`). Symbolic expressions represent constants, variables, function calls, and other values supplied by the surrounding framework. Rather than introducing separate syntax for literals and primitive operations, the functional paradigm reuses the common symbolic expression language shared by every component of the framework.

Evaluation follows the semantics of the lambda calculus. Applying a function replaces its formal parameters with the supplied arguments and continues reducing the resulting expression until no further reductions are possible. The framework internally performs this process through a combinatory representation, allowing lambda expressions to be evaluated without explicitly manipulating bound variables during execution. This implementation detail is transparent to the programmer: functional bodies behave as ordinary lambda expressions regardless of the underlying reduction mechanism.

Because functions are ordinary values, they may be passed as arguments, returned as results, or composed to construct larger computations. This ability to treat functions as first-class objects is one of the defining characteristics of the functional paradigm and enables concise descriptions of algorithms that operate on other functions.

---

## Examples

The examples below illustrate the core concepts of functional programming in increasing order of abstraction. The first introduces the identity function, the simplest possible lambda abstraction. The second demonstrates function composition, showing how larger computations may be constructed by combining smaller ones. The third presents a higher-order function that accepts another function as an argument and applies it twice, illustrating that functions are first-class values within the framework. Together these examples demonstrate how expressive computations can be built from abstraction and application alone.

### Example 1 — Identity

```
(FNC
    (ABS x
        x))
```

Equivalent:

```text
λ x . x
```

---

### Example 2 — Function composition

```
(FNC
    (ABS f g x
        (APP f
            (APP g x))))
```

Equivalent:

```
λ f g x . f (g x)
```

---

### Example 3 — Applying a function twice

```
(FNC
    (ABS f x
        (APP f
            (APP f x))))
```

Equivalent:

```
λ f x . f (f x)
```

