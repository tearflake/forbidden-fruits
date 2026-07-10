#  Combining Paradigms

Finally, we unify the **imperative, functional, and rewriting paradigms**, allowing functions written in one style to invoke or cooperate with functions written in another.

# Introduction

The previous chapters presented three complementary approaches to computation: imperative programming for stateful algorithms, functional programming for abstraction and composition, and rewriting for symbolic transformation. Each paradigm excels at expressing a particular class of problems, yet real applications often benefit from combining these styles rather than relying exclusively on one of them.

The framework therefore provides a unified programming model in which all three paradigms coexist within a common module system. Functions implemented using different evaluation strategies share the same symbolic expression language and may invoke one another transparently. This integration allows each component of a program to be implemented using the paradigm that best matches its intended behavior while preserving a uniform interface between functions.

This chapter introduces the module system that organizes programs, describes how functions are defined and imported, and illustrates how imperative, functional, and rewriting bodies cooperate within a single application.

---

## Formal Syntax

The grammar below defines the top-level organization of complete programs. A combined program consists of one or more modules containing function definitions, nested modules, and import declarations. Each function body is implemented using exactly one of the three supported paradigms: imperative, functional, or rewriting.

```
<start> := <module>

<module> := (MODULE <atomic> <unit>+)

<unit> := <module>
        | <exports>
        | <import>
        | <function>
        
<exports> := (EXPORTS <atomic>+)

<import> := (IMPORT <atomic>? <path>)

<function> := (FUNCTION <atomic> <body>)

<body> := <imp>
        | <fnc>
        | <rwr>
```

The grammar is expressed in the same relaxed Backus–Naur Form (BNF) employed throughout the framework. Non-terminal symbols are enclosed in angle brackets, literal language constructs appear in uppercase, and postfix operators indicate optional and repeated elements. Since every component is represented as an S-expression, complete programs form uniform symbolic trees whose structure is independent of the evaluation strategy used by individual functions.

---

## Informal Semantics

The three programming paradigms presented in the previous chapters are designed to operate together rather than in isolation. A complete program is organized into modules, each of which may contain nested modules, imports, exported symbols, and function definitions. Although individual functions may be implemented using different evaluation strategies, they all participate in the same module system and communicate through a uniform calling interface.

The outermost construct of a program is a module:

```
(MODULE name ...)
```

A module establishes a namespace that groups related definitions and provides a unit of organization for larger programs. Modules may be nested to form hierarchical program structures, allowing functionality to be partitioned into logical components.

A module may optionally declare its public interface using

```
(EXPORTS symbol...)
```

Only exported symbols are intended to be visible outside the module, while all other definitions remain internal implementation details. Explicit exports clearly separate the public API of a module from its private implementation and support the construction of reusable libraries.

External functionality is made available through import declarations of the form

```
(IMPORT alias path)
```

which associate a local module name with another module identified by its path. Imported definitions are subsequently referenced through the specified alias, ensuring that symbols originating from different modules remain unambiguous and avoiding unnecessary name collisions.

Functions are introduced using

```
(FUNCTION name body)
```

where the body is implemented using exactly one of the three supported paradigms: imperative (`IMP`), functional (`FNC`), or rewriting (`RWR`). Since all paradigms share the same symbolic expression language, functions written in different styles communicate naturally without requiring conversion between representations.

Function invocation is uniform throughout the framework. Calls appearing inside imperative code, lambda expressions, or rewrite rules are resolved through the module system to the corresponding function definition. Once located, the framework evaluates the function according to the semantics of its body, regardless of the paradigm in which it was implemented. From the caller's perspective, invoking an imperative function is no different from invoking a functional or rewriting function.

This separation between interface and implementation is one of the central design principles of the framework. Modules define clear boundaries between components, exports specify the public interface of each module, imports establish dependencies, and function bodies remain free to employ the programming paradigm best suited to the problem they solve. Consequently, imperative algorithms, higher-order functional abstractions, and symbolic rewrite systems can coexist within the same program while sharing a common expression language and a consistent execution model.

---

## Examples

The examples below illustrate the interaction. The first demonstrates ordinary imperative function calls within a module. The second shows that functions are first-class values by passing an imperative function to a higher-order functional definition. The final example invokes a rewriting function from imperative code, allowing symbolic simplification to be integrated seamlessly into an algorithm. Together these examples demonstrate that the module system provides a unified programming environment in which the three paradigms cooperate through a common expression language and a uniform function interface.

### Example 1 — Calling an imperative function

```
(COMBO Math
    (MODULE main

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
                    (THEN (ASGN x (stdlib/mul -1 x))))

                (RETURN x)))

        (FUNCTION distance
            (IMP
                (PARAMS a b)

                (RETURN
                    (abs (stdlib/sub a b)))))
    )
)
```

The program returns absolute distance between two sizes.

---

### Example 2 — Functional composition

```
(COMBO Functional
    (MODULE main

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
(COMBO Algebra
    (MODULE main

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

