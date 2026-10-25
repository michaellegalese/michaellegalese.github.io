# Concepts

These pages explain why L4 works the way it does: the choices behind its syntax, how it models legal rules, and what its type system guarantees. Read them after you have tried a feature. For what a keyword does, see the [Reference](/l4/reference.md); for step-by-step guides, see the [Tutorials](/l4/tutorials.md).

## Language design

- [Design principles](/l4/concepts/language-design/principles.md): the five principles behind L4, and the style of writing that follows from them.
- [Reading like the law](/l4/concepts/language-design/linguistic-syntax.md): how L4 borrows from English grammar so that code can be checked against its source text.

## Legal modeling

- [Definitions and duties](/l4/concepts/legal-modeling/constitutive-vs-regulative.md): why L4 keeps rules that define things apart from rules that impose duties.
- [Exceptions and defaults](/l4/concepts/legal-modeling/default-reasoning.md): how to encode a general rule followed by its exceptions.
- [How obligations unfold](/l4/concepts/legal-modeling/regulative-rules.md): a contract as something that waits for events and moves through time.

## Types

- [Types as legal categories](/l4/concepts/type-system/algebraic-types.md): records and enumerations as the shapes of legal definitions.
- [Exhaustiveness](/l4/concepts/type-system/exhaustiveness.md): why L4 checks that a determination handles every case.
- [Type theory](/l4/concepts/type-system/type-theory.md): the formal foundations, for readers who know type theory.
