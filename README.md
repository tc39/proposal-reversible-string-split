# Proper String Split

![](./cover.png)

<small>Image from
<a href="https://twitter.com/DasSurma/status/1480555236473384964">@DasSurma</a></small>

## Status

Champion(s): Luca Casonato

Author(s): Luca Casonato

Stage: 0

## Motivation

The string split method in JavaScript behaves unlike the string split methods in
nearly all other languages. In JavaScript, a `splitN` (split with max N return
values), is essentially a regular split, but with the output array truncated to
the first N values.

In most other languages `splitN` instead splits the original string N amount of
times. The last value in the returned array is the "remainder" of the string.

```perl
# Perl

print join('\n', split(/\|/, 'a|b|c|d|e|f', 2))

# a
# b|c|d|e|f
```

```java
// Java

class Playground {
  public static void main(String[] args) {
    String s = "a|b|c|d|e|f";
    for(String val : s.split("|", 2)) {
      System.out.println(val);
    }
  }
}

// a
// b|c|d|e|f
```

```php
<!-- PHP -->
<?php

print join("\n", explode("|", "a|b|c|d|e|f", 2));

# a
# b|c|d|e|f
```

```ruby
# Ruby

print 'a|b|c|d|e|f'.split('|', 2)

# ["a", "b|c|d|e|f"]
```

```python
# Python

print('a|b|c|d|e|f'.split('|', 2))

# ['a', 'b', 'c|d|e|f']
```

```go
// Go

package main

import (
  "fmt"
  "strings"
)

func main() {
  fmt.Printf("%#v", strings.SplitN("a|b|c|d|e|f", "|", 2))
}

// []string{"a", "b|c|d|e|f"}
```

```rust
// Rust

fn main() {
  let v = "a|b|c|d|e|f".splitn(2, "|").collect::<Vec<_>>();
  println!("{:?}", v);
}

// ["a", "b|c|d|e|f"]
```

```js
// JavaScript

console.log("a|b|c|d|e|f".split("|", 2));

// ["a", "b"]
```

JavaScipt is definitly the odd one out here. Python's behavior is a little
weird, but at least no part of the input string is lost. All of the other tested
languages agree with each other.

This behaviour is good, because it makes `split` the reverse function to `join`.
In JS this assertion is not true for all values of N:
`val.split(sep, N).join(sep) === val`.

## Proposal

The proposal is to add a new `String.prototype.splitn` method that "splits" N
number and returns the remainder rather than splitting N + 1 times and throwing
away the remainder.

```js
console.log("a|b|c|d|e|f".splitn("|", 2));
// ["a", "b|c|d|e|f"]
```

The naming is taken from Rust and Go.

## Q&A

### Could this be an extra option for the `split` method?

Yes. This could also be an option in a new options bag for split. Example:

```js
console.log("a|b|c|d|e|f".split("|", { n: 2 }));
// or
console.log("a|b|c|d|e|f".split("|", 2, true));
// or
console.log("a|b|c|d|e|f".split("|", { n: 2, remainder: true }));
```

The former may be confusing to users though, as it is not obvious that the
return value between `split("|", 2)` and `split("|", { n: 2 })` is different.
These kinds of overloads exist on the web platform (e.g. `addEventListener`),
but the form you use does not impact behaviour.

The latter is more clear, but at the same time also less clear, because it is
not obvious what the `true` value in the third argument is.

The last option is the most clear, but is also the most verbose. The verbosity
may make it cumbersome to use.

Which of the 4 proposed options should ultimately be used should be up to the
committee as a whole. I don't really care (although I prefer the `splitn` or the
final option).
