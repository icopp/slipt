# slipt

See ["The Roots of Lisp" by Paul Graham](http://ep.yimg.com/ty/cdn/paulgraham/jmc.ps) for implementation implementation.

## Definitions

An *atom* is anything that isn't an Array.

An *empty array* is an Array with length 0.

## Primitives

`['quote', x]` returns x.

```js
> ['quote', 'a']
'a'
> ['quote', ['a', 'b', 'c']]
['a', 'b', 'c']
```

`['atom', x]` returns `'t'` if the value of `x` is an atom or an empty array. Otherwise, it returns an empty array.

```js
> ['atom', ['quote', 'a']]
't'
> ['atom', ['quote', ['a', 'b', 'c']]]
[]
> ['atom': ['quote', []]]
't'
```

`['eq', x, y]` returns `'t'` if the values of `x` and `y` are the same atom or both an empty array, and [] otherwise.

```js
> ['eq', ['quote', 'a'], ['quote', 'a']]
't'
> ['eq'  ['quote', 'a'], ['quote', 'b']]
[]
> ['eq', ['quote', []], ['quote', []]]
't'
```

`['car', x]` expects the value of `x` to be an Array, and returns its first element.

```js
> ['car', ['quote', ['a', 'b', 'c']]]
'a'
```

`['cdr', x]` expects the value of `x` to be an Array, and returns everything after the first element

```js
> ['cdr', ['quote', ['a', 'b', 'c']]]
['b', 'c']
```

`['cons', x, y]` expects the value of `y` to be an Array, and returns an Array containing the value of `x` followed by the elements of the value of `y`.

```js
> ['cons', ['quote', 'a'], ['quote', 'b', 'c']]
['a', 'b', 'c']
> ['cons', ['quote', 'a'], ['cons', 'b', ['cons', ['quote', 'c'], ['quote', []]]]]
['a', 'b', 'c']
> ['car', ['cons', ['quote', 'a'], ['quote', ['b', 'c']]]
['a']
> ['cdr', ['cons', ['quote', 'a'], ['quote', ['b', 'c']]]
['b', 'c']
```

## Functions

`['lambda', [p_1, ..., p_n], e]` is a function, with each `p` as an atom and `e` as an expression.

`[['lambda', [p_1, ..., p_n], e], a_1, ..., a_n]` is a function call. When evaluated, each `a` is evaluated, then `e` is evaluated, and any `p` in `e` is treated as having the value of the corresponding `a`.

```js
> [['lambda', ['x'], ['cons', 'x', ['quote', 'b']]], ['quote', 'a']]
['a', 'b']
> [['lambda', ['x', 'y'], ['cons', 'x', ['cdr', 'y']]], ['quote', 'z'], ['quote', ['a', 'b', 'c']]]
['z', 'b', 'c']
```

For an expression `[f, a_1, ..., a_n]`, if `f` is not a primitive operator and the value of `f` is a function, then the expression is evaluated as if the function was in place of `f`.

```js
> [['lambda', ['f'], ['f', ['quote', 'b', 'c']]],
   ['quote', ['lambda', ['x'], ['cons', ['quote', 'a'], 'x']]]]
['a', 'b', 'c']
```

`['label', f, ['lambda', [p_1, ..., p_n], e]]` is a function, with the special property that `f` inside `e` will evaluate to the `label` expression (allowing self-recursion).

`['defun', f, [p_1, ..., p_n], e]` evaluates to `['label', f, ['lambda', [p_1, ..., p_n], e]]` and saves that expression as the value of `f`.
