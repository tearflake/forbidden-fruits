# Combo Programming Framework Tutorial

## Introduction

Programming languages traditionally encourage a single dominant style of computation. Imperative languages describe programs as sequences of instructions that modify state. Functional languages emphasize mathematical functions and immutable values. Rule-based systems, in contrast, express computation through symbolic transformations.

Each of these paradigms has strengths. Imperative programming naturally describes stateful algorithms and procedural workflows. Functional programming excels at abstraction, composition, and higher-order computation. Rewriting systems provide elegant descriptions of symbolic reasoning, simplification, and formal transformation. Yet many practical problems contain elements of all three.

This framework is built on the observation that these paradigms need not compete with one another. Instead, they can coexist within a single programming model built upon a common symbolic representation. Rather than introducing separate languages for different styles of computation, the framework provides multiple evaluation strategies operating on the same underlying expressions.

The result is a small language kernel capable of expressing procedural algorithms, functional abstractions, and symbolic transformations while maintaining a uniform representation of programs and data.

---

## Design Philosophy

The framework is founded upon three complementary ideas.

First, **everything is represented as symbolic expressions**. Programs, expressions, rewrite rules, function applications, and modules all share the same tree-based representation. This uniformity simplifies parsing, analysis, transformation, and evaluation, while allowing every component of the framework to operate on the same data structures.

Second, **evaluation is separated from representation**. The symbolic expression itself does not determine how it should be evaluated. Instead, the enclosing function selects one of several evaluation strategies. An imperative body executes statements sequentially, a functional body evaluates lambda expressions, and a rewriting body repeatedly transforms symbolic expressions using rewrite rules. Different paradigms therefore share the same syntax while differing only in their operational semantics.

Third, **paradigms cooperate rather than compete**. Functions written in different styles may invoke one another transparently. An imperative algorithm may delegate symbolic simplification to a rewrite system. A functional abstraction may receive an imperative function as an argument. A rewrite rule may construct expressions that are subsequently evaluated by another paradigm. Each component contributes the style of computation best suited to its purpose.

These principles encourage programs whose structure reflects the nature of the problem rather than the limitations of a particular programming language.

---

## Symbolic Expressions

One of the defining characteristics of the framework is its use of **symbolic expressions**, or **S-expressions**, as the uniform representation of programs and data.

An S-expression is a tree represented using a simple parenthesized notation. Every expression consists of an operator followed by zero or more operands, each of which is itself another symbolic expression. For example,

```
(add
    (mul 2 x)
    1)
```

represents the expression

```
2 * x + 1
```

without introducing operator precedence, associativity rules, or multiple syntactic forms. The tree structure of the expression is immediately visible from its nesting.

The framework deliberately adopts this representation not because it resembles a traditional programming language, but because it provides a single, uniform structure for every component of the system. Function definitions, imperative statements, lambda expressions, rewrite rules, module declarations, and ordinary data are all represented using exactly the same notation. There is no distinction between "program syntax" and "expression syntax"; everything is an S-expression.

This uniformity greatly simplifies the implementation of the framework. Parsing becomes straightforward because every construct follows the same recursive structure. Analysis and transformation tools operate on a single abstract representation rather than many unrelated syntactic forms. Most importantly, the three programming paradigms share exactly the same symbolic language, allowing them to exchange expressions without conversion or translation.

The use of symbolic expressions also reflects one of the central design principles of the framework: programs are themselves symbolic objects. They may be inspected, transformed, generated, simplified, or rewritten using the same mechanisms that manipulate ordinary expressions. This property is particularly valuable for the rewriting paradigm, whose rules operate directly on symbolic trees, but it also benefits imperative and functional code by providing a common representation for every computation.

---

## Architecture

The framework is organized into four closely related components.

### Imperative Paradigm

The imperative subsystem provides mutable variables, sequential execution, conditionals, loops, and explicit control flow. It is intended for algorithms that naturally manipulate state or perform step-by-step computations.

See **Imperative Paradigm** for a complete description of its syntax, semantics, and examples.

---

### Functional Paradigm

The functional subsystem represents computation using lambda expressions consisting of abstractions and applications. Functions are first-class values that may be composed, passed as arguments, and returned from other functions.

See **Functional Paradigm** for an introduction to lambda expressions and functional evaluation.

---

### Rewriting Paradigm

The rewriting subsystem performs computation by repeatedly applying symbolic transformation rules. It is particularly suitable for simplification, normalization, symbolic algebra, logical reasoning, and other forms of declarative computation.

See **Rewriting Paradigm** for a complete description of rewrite rules and the rewriting process.

---

### Combining Paradigms

The module system integrates the three paradigms into a unified programming environment. Functions implemented using different evaluation strategies share a common symbolic expression language and communicate through a uniform function interface.

See **Combining Paradigms** for the organization of complete programs and the interaction between modules and functions.

---

## Choosing a Paradigm

The framework does not prescribe a single style of programming. Instead, it encourages selecting the paradigm that most naturally expresses each component of a problem.

Use the imperative paradigm when the solution is most easily described as a sequence of state changes or procedural steps.

Use the functional paradigm when computation is naturally expressed through abstraction, composition, or higher-order functions.

Use the rewriting paradigm when the problem is most naturally formulated as a collection of symbolic transformation rules or algebraic identities.

Because all three paradigms coexist within the same framework, these choices are local rather than global. Different parts of the same program may employ different programming styles without sacrificing interoperability or requiring translation between incompatible representations.

---

## A Small Example

Consider the task of simplifying and evaluating a symbolic mathematical expression.

An imperative function may orchestrate the overall algorithm, reading input and controlling the evaluation process.

Whenever an expression requires simplification, the imperative code invokes a rewriting function that repeatedly applies algebraic identities until a normal form is reached.

If the algorithm requires repeated application of a transformation, a higher-order functional definition may express this behavior succinctly by receiving the transformation itself as an argument.

Although each component uses a different evaluation strategy, all three manipulate the same symbolic expressions and interact through ordinary function calls. The resulting program combines procedural control flow, functional abstraction, and declarative symbolic reasoning into a coherent whole.

---

## Learning Path

Readers new to the framework are encouraged to study the documents in the following order:

1. [**Imperative Paradigm**](./imp.md), introducing sequential algorithms and mutable state.
2. [**Functional Paradigm**](./fnc.md), introducing lambda expressions and higher-order functions.
3. [**Rewriting Paradigm**](./rwr.md), introducing symbolic transformation through rewrite rules.
4. [**Combining Paradigms**](./combo.md), demonstrating how the three styles cooperate within a unified module system.

Together these documents provide a complete introduction to the framework and its underlying programming model.

