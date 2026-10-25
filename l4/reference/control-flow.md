# Control flow

L4 has no statements, only expressions, so choosing between results is done with expressions that give a value.
`IF` and `BRANCH` choose by conditions; `CONSIDER` chooses by the shape of a value. In each, every outcome must have the same type.

## Pages

- [IF](/l4/reference/control-flow/IF.md): `IF … THEN … ELSE`, one condition and two outcomes
- [BRANCH](/l4/reference/control-flow/BRANCH.md): several conditions, tried from top to bottom, with `OTHERWISE`
- [CONSIDER](/l4/reference/control-flow/CONSIDER.md): pattern matching with `WHEN … THEN` and `OTHERWISE`, and the check for missing branches

A function can also be written as [several clauses](/l4/reference/functions/DECIDE.md#several-clauses), one per case, which means the same as a `CONSIDER`.
Conditions are combined with [AND, OR, NOT, IMPLIES](/l4/reference/operators/logical.md) and [UNLESS](/l4/reference/operators/UNLESS.md).

The [Foundation course, module 3](/l4/courses/foundation/module-3-control-flow.md) teaches these step by step.
