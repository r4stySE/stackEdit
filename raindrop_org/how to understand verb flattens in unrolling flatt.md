<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# how to understand verb flattens in unrolling flattens

In “unrolling **flattens**,” **flattens** means “turns nested structure into a simple, one‑level sequence.”

Example with arrays:

```powershell
@(1, @(2,3), 4)
```

If something **flattens** this, you end up conceptually with:

```powershell
1
2
3
4
```

So when we say “unrolling flattens,” we mean:

- You had an outer container (array, collection, etc.) holding items.
- The process **removes that outer layer** and **emits each inner item individually**, instead of the outer container as a single object.

