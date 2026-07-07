#  Combining Paradigms

Finally, we unify the **imperative, functional, and rewriting paradigms**, allowing functions written in one style to invoke or cooperate with functions written in another.

# Introduction

The previous chapters presented three complementary approaches to computation: imperative programming for stateful algorithms, functional programming for abstraction and composition, and rewriting for symbolic transformation. Each paradigm excels at expressing a particular class of problems, yet real applications often benefit from combining these styles rather than relying exclusively on one of them.

The framework therefore provides a unified programming model in which all three paradigms coexist within a common module system. Functions implemented using different evaluation strategies share the same symbolic expression language and may invoke one another transparently. This integration allows each component of a program to be implemented using the paradigm that best matches its intended behavior while preserving a uniform interface between functions.

This document introduces the module system that organizes programs, describes how functions are defined and imported, and illustrates how imperative, functional, and rewriting bodies cooperate within a single application.

---

## Formal Syntax

The grammar below defines the top-level organization of complete programs. A combined program consists of one or more modules containing function definitions, nested modules, and import declarations. Each function body is implemented using exactly one of the three supported paradigms: imperative, functional, or rewriting.

```
<combo> := (COMBO <module>)

<module> := (MODULE <atomic> <unit>+)

<unit> := <module>
        | <import>
        | <function>

<import> := (IMPORT <path> <atomic>?)

<function> := (FUNCTION <atomic> <body>)

<body> := <imp>
        | <fnc>
        | <rwr>
```

The grammar is expressed in the same relaxed Backus–Naur Form (BNF) employed throughout the framework. Non-terminal symbols are enclosed in angle brackets, literal language constructs appear in uppercase, and postfix operators indicate optional and repeated elements. Since every component is represented as an S-expression, complete programs form uniform symbolic trees whose structure is independent of the evaluation strategy used by individual functions.

---

## Informal Semantics

The three programming paradigms presented in the previous chapters are designed to operate together rather than in isolation. A combined program organizes functions into modules, allows modules to import one another, and permits imperative, functional, and rewriting bodies to coexist within the same program. From the perspective of a caller, the implementation paradigm of a function is an implementation detail: every function is invoked using the same call syntax regardless of how it is defined internally.

The outermost construct of a program is

```
(COMBO ...)
```

which contains one or more modules. A module provides a namespace for related functions and may contain nested modules, imports, and function definitions. Modules serve both as a mechanism for organizing larger programs and as the basis for qualified function names.

Functions are introduced using

```
(FUNCTION name body)
```

where the body may be an imperative (`IMP`), functional (`FNC`), or rewriting (`RWR`) definition. Since all three paradigms share the same symbolic expression language, functions written in different styles communicate naturally without requiring explicit conversion between representations. A function written in one paradigm may therefore call, receive, or return functions implemented in another.

External modules are made available through `IMPORT` declarations. Imported modules contribute additional functions that may be referenced either through qualified names, such as

```
stdlib/add
```

or, depending on the import declaration, through locally visible aliases. This mechanism allows programs to be organized into reusable libraries while avoiding unnecessary name collisions.

Function invocation is performed uniformly throughout the framework. Whether a call appears inside imperative code, a lambda expression, or a rewrite rule, the dispatcher resolves the referenced function, binds its arguments, and evaluates its body according to the semantics of the paradigm in which it was defined. The caller does not need to know whether the callee is imperative, functional, or rewriting; the framework transparently selects the appropriate evaluation strategy.

This separation between interface and implementation is one of the principal design goals of the framework. Imperative functions naturally express stateful algorithms and control flow, functional functions excel at abstraction and higher-order computation, and rewriting functions provide concise descriptions of symbolic transformations. By allowing these paradigms to interoperate within a single module system, the framework encourages each problem to be solved using the style that best matches its nature rather than forcing every computation into a single programming model.

---

## Examples

The examples below illustrate this interaction. The first demonstrates ordinary imperative function calls within a module. The second shows that functions are first-class values by passing an imperative function to a higher-order functional definition. The final example invokes a rewriting function from imperative code, allowing symbolic simplification to be integrated seamlessly into an algorithm. Together these examples demonstrate that the module system provides a unified programming environment in which the three paradigms cooperate through a common expression language and a uniform function interface.

### Example 1 — Calling an imperative function

```
(COMBO
    (MODULE Math

        (IMPORT stdlib)

        (FUNCTION main
            (IMP
                (PARAMS a b)

                (RETURN
                    (distance a b))))
        
        (FUNCTION abs
            (IMP
                (PARAMS x)

                (IF (stdlib/lt x 0)
                    (ASGN x (stdlib/mul -1 x)))

                (RETURN x)))

        (FUNCTION distance
            (IMP
                (PARAMS a b)

                (RETURN
                    (CALL abs (stdlib/sub a b)))))
    )
)
```

The program returns absolute distance between two sizes.

---

### Example 2 — Functional composition

```
(COMBO
    (MODULE Functional

        (IMPORT stdlib)

        (FUNCTION main
            (IMP
                (PARAMS expr)

                (RETURN
                    (fourthPower expr))))

        (FUNCTION twice
            (FNC
                (ABS f x
                    (APP f
                        (APP f x)))))

        (FUNCTION square
            (IMP
                (PARAMS x)

                (RETURN (stdlib/mul x x))))

        (FUNCTION fourthPower
            (IMP
                (PARAMS x)

                (RETURN
                    (twice square x))))
    )
)
```

Here the functional function `twice` receives the imperative function `square` as an argument and applies it twice.

---

### Example 3 — Imperative code using a rewrite system

```
(COMBO
    (MODULE Algebra

        (FUNCTION main
            (IMP
                (PARAMS expr)

                (RETURN
                    (simplify expr))))

        (FUNCTION simplify
            (RWR
                (RULE
                    (VARS x)
                    (READ ("start" x))
                    (WRITE x))

                (RULE
                    (VARS x)
                    (READ ("add" x 0))
                    (WRITE x))

                (RULE
                    (VARS x)
                    (READ ("mul" x 1))
                    (WRITE x))))
    )
)
```

Evaluating

```
("mul" ("add" "a" 0) 1)
```

produces

```
"a"
```

