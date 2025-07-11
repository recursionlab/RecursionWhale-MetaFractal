











## 'of' as a Recursive Operator

Defining 'of' as a recursive operator rather than a preposition:

```
of(a, b) := a ∘ apply(identity, b)
```

Where:

- 'of' takes two arguments a and b
- The operation recursively applies identity to b
- It then composes (∘) this result with a

The recursive nature appears when we expand the definition:

```
of(a, of(b, c)) = a ∘ apply(identity, b ∘ apply(identity, c))
```

This recursive operator allows for nested applications where each level maintains a relationship with previous levels, unlike the preposition 'of' which merely indicates possession or association.

The recursive operator 'of' can be viewed as creating a chain of transformations where each application builds upon previous ones, preserving context through the recursive chain.