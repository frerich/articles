Haskell types, their instances, and what they do
================================================



This is a collection of standard or at least widely used Haskell types, together
with a one-sentence explanation of how their instances behave. These are only
mental aids; the claims only have to work in my head, and not necessarily in
Haskell. For example, I will often refer to the "value contained" in a monadic
action. This makes this a *bad* place to start about learning about instances,
it is only a reference and nothing else.



## Maybe

```haskell
data Maybe a = Nothing
             | Just a
```

- Eq, Ord: Compare `Just` values; `Nothing` is smaller than any `Just`.
- Monoid: Ignore `Nothing`, `mappend` `Just` contents
- Functor: Map over `Just` contents, do nothing for `Nothing`
- Applicative: "Value contained": values in `Just`. If there is a `Nothing`, the
  entire result is.
- Monad
      - `join`: flatten `Maybe (Maybe a)`. `Nothing` if there's a `Nothing`.
        `join (Just (Just 1)) = Just 1`
      - `>>=`: Extract `Just` contents and pass them on; if a `Nothing` is
        encountered, the overall result is `Nothing`
- Alternative: Ignore `Nothing`, return first `Just` value
- MonadPlus: Like Alternative

### Newtypes

- `First`
      - Monoid: Like Alternative
- `Last`
      - Monoid: "Reversed Alternative": return *last* `Just` value



## List

Lists are similar to `Maybe` but with the ability to hold multiple values, and
behave accordingly.

```haskell
data List a = []
            | a : List a
```

- Eq, Ord: Compare element-wise from left to right. `[]` is smaller than any
  non-empty list.
- Monoid: Concatenation
- Functor: Map over list elements
- Applicative: Do the computation for every combination of lists involved
- Monad
      - `join`: Flatten list of lists. `join [[1],[2],[3,4]] = [1,2,3,4]`
      - `>>=`: Run each of the following computations for each of the bound
        values.
- Alternative: Like monoid
- MonadPlus: Like Monoid

### Newtypes

- `zipList`
      - Applicative: Do the computation pairwise (triplewise, ...)
      - Monad: Does not exist



## Reader/`(r ->)`


- Functor: Function composition, `fmap = (.)`. Picture a function `a -> b` as
  a collection of `b` indexed by `a`. Feeding an `a` to the function looks up
  the corresponding `b`. `fmap` thus modifies the contents of the container.
- Applicative
      - "Value contained" is the action applied to the environment.
      - Functions are related to [SKI calculus][ski calculus],
        `K = pure`, `S = (<*>)`
- Monad
      - `join`: `join f x = f x x`
      - Value extracted and piped on by `>>=`: LHS applied to the environment



## IO

- Functor: modify result of a computation
- Monad: `>>=` passes on the value generated by the LHS



## ST

Like IO, but with a function to extract a pure value.



## STM

Run and combine computations in an atomic environment, otherwise similar to IO.

- Alternative: Return the first action that finishes, retry if none does
- MonadPlus: Like Alternative



[ski calculus]: https://en.wikipedia.org/wiki/SKI_calculus