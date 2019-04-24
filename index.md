---
layout: default
title:  Home
---
## Welcome!
Hey all.  This is the home page for the Ten programming language.  For
now it's just me working on the language and it isn't ready for public use,
but if you'd like to get involved then feel free to contact me
[on reddit](https://www.reddit.com/user/raystubbs).

For installation instructions and a quick intro to the language you
can check out [this page](https://github.com/ten-lang/docs/blob/master/LanguageGuide.md).

## About Ten
Ten is a general purpose scripting language inspired by Lua. It's
small, portable, and embeddable; and has some pretty interesting
features, though the greatest of its 'features' is its lack thereof.
Ten strives for utmost simplicity, providing only the essentials as
language features and leaving the rest for libraries to implement.

Besides its minimalism, Ten has a few notable features that set
it apart from other languages of the same class.

- **Undefined:** Ten has `udf` value for representing undefined
  or unknown variables.  But unlike JS and other languages in
  which this type becomes a source of uncaught bugs; Ten assigns
  special semantics to the value, preventing it from being used
  in the ways that most other values are; making it a useful tool
  for catching certain types of bugs.

- **Records:** Like Lua, Ten only has one compound data type, the
  record.  But unlike Lua's tables, records aren't simple hash
  tables; they're designed such that groups of records that maintain
  a similar set of keys can share a common lookup table, allowing
  for huge memory savings.  Check
  [here](https://github.com/ten-lang/docs/blob/master/LanguageGuide.md#record)
  for a bit more on how these work.

- **Re-Entrant Native Functions:** Ten sports a pretty nice API for
  host applications interacting with the language runtime, but its
  support for implementing re-entrant native functions is particularly
  neat.  Check out
  [this post](http://www.raystubbs.me/2019/04/20/HandlingYieldsInTen.html)
  for more on how that works.

## Samples
Here are a few code samples to give an idea of what the language
looks like:

### Hello Person
    def name: do show"What's you're name? " for input()
    show( "Hello, ", name, "!", N )

### Fizz Buzz
    each( irange( 1, 101 )
      [ num ]
        if
          num % 15 = 0: show"FizzBuzz "
          num % 3  = 0: show"Fizz "
          num % 5  = 0: show"Buzz "
        else
          show( num, " " )
    )
    show( N )

### Fibonacci
    def fibo: [ n ]
      if n < 2:
        n
      else
        this( n - 1 ) + this( n - 2 )