# Imperative Paradigm

**Imperative paradigm** expresses algorithms through mutable state and control flow.

## Introduction

The imperative paradigm is the most familiar style of programming for many developers. It models computation as a sequence of instructions that manipulate program state over time. Variables store intermediate results, control-flow constructs determine which statements are executed, and the program gradually transforms its state until a final result is produced.

Within this framework, the imperative paradigm provides a natural way to express algorithms whose logic is inherently procedural. Iterative computations, stateful transformations, and decision-making processes can be described directly using assignments, conditionals, and loops. Rather than introducing a separate expression language, however, imperative programs operate on the common symbolic expressions shared by every component of the framework. This allows imperative code to integrate seamlessly with the functional and rewriting paradigms introduced in the following chapters.

This document presents the syntax and execution model of imperative bodies before illustrating their use through several representative examples.

---

## Formal Syntax

The grammar below defines the abstract syntax of imperative bodies. An imperative body consists of an optional list of input parameters, an optional list of local variables, and one or more executable statements. Statements are evaluated sequentially until execution reaches a `RETURN` statement, which terminates the body and produces its result.

```
<imp> := (IMP (PARAMS <atomic>+)? (VARS <atomic>+)? <stmt>+)

<stmt> := <assign>
        | <if>
        | <switch>
        | <while>
        | <foreach>
        | <return>

<assign> := (ASGN <var> <sexpr>)

<if> := (IF <sexpr> <sexpr> <stmt>+)

<switch> := (SWITCH <sexpr> (CASE <atomic> <stmt>+)+)

<while> := (WHILE <sexpr> <stmt>+)

<foreach> := (FOREACH <atomic> <sexpr> <stmt>+)

<return> := (RETURN <sexpr>)
```

The notation follows a relaxed Backus–Naur Form (BNF). Non-terminal symbols are enclosed in angle brackets, literal language constructs appear in uppercase, postfix operators such as `?` and `+` denote optional and repeated elements, respectively, and indentation is used solely to improve readability. Every syntactic construct is represented explicitly as an S-expression, allowing imperative programs to be manipulated as ordinary symbolic trees.

---

## Informal Semantics

The imperative paradigm describes computation as a sequence of statements that manipulate program state. A program begins execution with a collection of input parameters, optionally introduces local variables, executes its statements in order, and eventually produces a result with a `RETURN` statement.

An imperative body consists of two parts: declarations and executable statements. The optional `PARAMS` clause names the input parameters supplied by the caller, while the optional `VARS` clause introduces local variables used during execution. Parameters and local variables together form the mutable environment of the program. Unlike the functional paradigm, variables may be assigned new values repeatedly throughout execution.

The simplest statement is assignment:

```
(ASGN variable expression)
```

The expression is evaluated in the current environment, and the resulting value replaces the previous value of the specified variable. Every subsequent statement observes the updated state.

Control flow is expressed using familiar constructs. Conditional execution is performed with `IF`, which evaluates its condition and executes its body only when the condition is true. Multi-way branching is provided by `SWITCH`, whose `CASE` clauses compare a value against a collection of alternatives. The distinguished value `else` serves as the default case when no preceding alternative matches.

Iteration is supported by two complementary looping constructs. `WHILE` repeatedly evaluates its condition before each iteration and continues executing its body while the condition remains true. `FOREACH` iterates over every element of a collection, binding the current element to the specified iteration variable during each pass through the loop. Together these constructs cover the two most common styles of iteration: condition-controlled loops and collection traversal.

Execution terminates when a `RETURN` statement is encountered. The associated expression is evaluated, its value becomes the result of the imperative body, and all remaining statements are skipped.

Expressions appearing throughout the imperative language are symbolic expressions (`<sexpr>`). Rather than defining a separate expression language for imperative code, the framework delegates expression evaluation to the shared symbolic evaluator. As a result, arithmetic operations, logical predicates, function calls, and other symbolic computations all use the same underlying representation throughout the framework. This common expression language allows the imperative, functional, and rewriting paradigms to interoperate seamlessly while sharing a unified syntax.

---

## Examples

The examples below introduce the fundamental control structures of the imperative paradigm. The first demonstrates conditional execution by computing the absolute value of a number. The second combines variable assignment and iteration to accumulate the sum of the first `n` positive integers. The third illustrates multi-way branching with a `SWITCH` statement that classifies an input according to a collection of alternatives. Together these examples present the essential building blocks for expressing stateful algorithms within the framework.

### Example 1 — Absolute value

```
(IMP
    (PARAMS x)

    (IF (lt x 0)
        (ASGN x (mul -1 x)))

    (RETURN x))
```

Equivalent:

```
if (x < 0)
    x = -x;
return x;
```

---

### Example 2 — Sum of integers

```
(IMP
    (PARAMS n)
    (VARS sum)

    (ASGN sum 0)

    (WHILE (gt n 0)
        (ASGN sum (add sum n))
        (ASGN n (sub n 1)))

    (RETURN sum))
```

Equivalent:

```
sum = 0;
while (n > 0) {
    sum += n;
    n--;
}
return sum;
```

---

### Example 3 — Classifying a value

```
(IMP
    (PARAMS color)

    (SWITCH color

        (CASE "red"
            (RETURN "stop"))

        (CASE "yellow"
            (RETURN "wait"))

        (CASE "green"
            (RETURN "go"))

        (CASE else
            (RETURN "error")))
)
```

Equivalent:

```
switch (color) {
    case "red": return "stop";
    case "yellow": return "wait";
    case "green": return "go";
    default: return "error";
}
```

