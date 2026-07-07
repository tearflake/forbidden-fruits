# Rewriting Paradigm

**Rewriting paradigm** transforms symbolic expressions declaratively using rewrite rules.

## Introduction

The rewriting paradigm views computation as a process of symbolic transformation. Instead of executing instructions or evaluating function applications, a program consists of rules that describe how one symbolic expression may be transformed into another. Computation emerges from the repeated application of these rules until the expression can no longer be simplified or transformed.

This declarative style is particularly well suited to domains in which knowledge is naturally expressed as identities, equivalences, or transformation laws. Symbolic algebra, logical reasoning, expression simplification, and formal deduction are all examples of problems that can often be described more naturally by rewrite rules than by explicit algorithms.

Within the framework, rewriting bodies share the same symbolic representation as the imperative and functional paradigms, allowing symbolic transformations to participate seamlessly in larger programs. This document introduces the syntax of rewrite rules, explains the rewriting process, and demonstrates several representative applications of rule-based computation.

---

## Formal Syntax

The grammar below defines the abstract syntax of rewriting bodies. A rewriting body consists of one or more rewrite rules, each specifying a pattern to be recognized and the symbolic expression that replaces it. Rules may optionally declare pattern variables that are bound during matching and substituted into the replacement expression.

```
<rwr> := (RWR <rule>+)

<rule> := (RULE (VARS <atomic>+)? (READ <sexpr>) (WRITE <sexpr>))
```

The notation follows the same relaxed Backus–Naur Form (BNF) used throughout this document. Non-terminal symbols are enclosed in angle brackets, literal language constructs appear in uppercase, and postfix operators describe optional and repeated elements. Like every other component of the framework, rewrite rules are represented as S-expressions, enabling symbolic transformation to operate directly on the program's underlying representation.

---

## Informal Semantics

The rewriting paradigm describes computation as a sequence of symbolic transformations. Rather than executing statements or applying functions, a rewriting body repeatedly searches an expression for patterns that match predefined rules and replaces the matched subexpressions with their corresponding replacements. Computation therefore proceeds by gradually transforming one symbolic expression into another until no further rules apply.

A rewriting body consists of one or more rewrite rules. Each rule has the form

```
(RULE
    (VARS ...)
    (READ pattern)
    (WRITE replacement))
```

where the optional `VARS` clause declares the pattern variables used by the rule. Pattern variables act as placeholders that may match arbitrary symbolic expressions during rewriting. Every occurrence of the same variable within a pattern must match the same expression, ensuring that the replacement is constructed consistently from the matched values.

The `READ` clause specifies the pattern to be recognized, while the `WRITE` clause specifies the expression that replaces the matched pattern. During rewriting, the values bound to pattern variables are substituted into the replacement expression. Rules therefore describe *what* a symbolic expression should become, rather than *how* that transformation should be carried out.

Unlike imperative programs, rewrite rules have no prescribed execution order beyond the rewriting strategy itself. Each rule is an independent declaration describing a valid transformation. The rewriting engine repeatedly traverses the expression tree, searches for matching patterns, applies the first applicable rule, and continues until no rule can be applied anywhere within the expression. At that point the expression is said to be in **normal form**, and evaluation terminates.

Because patterns and replacements are ordinary symbolic expressions (`<sexpr>`), rewrite rules operate directly on the same representation used throughout the framework. Constants, variables, nested expressions, and function calls may all appear inside patterns and replacement expressions, making rewriting a natural mechanism for expressing symbolic computation.

The rewriting paradigm is particularly well suited to problems that are naturally described by algebraic identities or transformation systems rather than explicit algorithms. Expression simplification, symbolic algebra, logical reasoning, compiler optimizations, normalization procedures, and formal deduction systems can often be expressed as concise collections of rewrite rules. In many cases, adding new behavior simply means introducing additional rules without modifying existing ones.

---

## Examples

The examples below illustrate several typical applications of rewriting. The arithmetic example simplifies expressions using elementary identities such as adding zero or multiplying by one. The Boolean example demonstrates logical simplification by repeatedly reducing nested expressions. Finally, the Peano arithmetic example defines addition entirely by recursive symbolic transformation, showing that computation itself can emerge from the repeated application of simple rewrite rules.

### Example 1 — Arithmetic identities

```
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
        (READ ("add" 0 x))
        (WRITE x))

    (RULE
        (VARS x)
        (READ ("mul" x 1))
        (WRITE x))

    (RULE
        (VARS x)
        (READ ("mul" 1 x))
        (WRITE x)))
```

Evaluation:

```
("start"
    ("add" 0
           ("mul" 1 4)))

↓

("add" 0
       ("mul" 1 4)))

↓

("add" 0 4)

↓

4
```
---

### Example 2 — Boolean simplification

```
(RWR
    (RULE
        (VARS x)
        (READ ("start" x))
        (WRITE x))

    (RULE
        (READ ("not" true))
        (WRITE false))

    (RULE
        (READ ("not" false))
        (WRITE true))

    (RULE
        (VARS x)
        (READ ("and" true x))
        (WRITE x))

    (RULE
        (VARS x)
        (READ ("or" false x))
        (WRITE x)))
```

Evaluation:

```
("start"
    ("not"
           ("and" true
                  ("or" false true))))

↓

("not"
       ("and" true
              ("or" false true)))

↓

("not"
       ("and" true
              true))

↓

("not" true)

↓

"false"
```

---

### Example 3 — Peano arithmetic

```
(RWR
    (RULE
        (VARS x)
        (READ ("start" x))
        (WRITE x))

    (RULE
        (VARS x)
        (READ ("add" "zero" x))
        (WRITE x))

    (RULE
        (VARS x y)
        (READ ("add" ("succ" x) y))
        (WRITE ("succ" ("add" x y)))))
```

Evaluation:

```
("start"
    ("add" ("succ" ("succ" "zero"))
           ("succ" "zero")))

↓

("add" ("succ" ("succ" "zero"))
       ("succ" "zero"))

↓

("succ"
    ("add" ("succ" "zero")
           ("succ" "zero")))

↓

("succ"
    ("succ"
        ("add" "zero"
             ("succ" "zero"))))

↓

("succ"
    ("succ"
        ("succ" "zero")))
```

