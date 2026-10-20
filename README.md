# Programming Language Interpreter

This is an interpreter and typechecker for a small class-based object-oriented
language called "Moe", built for my CS 3520 final project. The language has classes, fields,
methods, inheritance, and `super` calls, and it is statically typed, so a program
gets typechecked before it runs. You hand it a list of class definitions and an
expression, and it parses the program, checks the types, and evaluates it.

I started from the course's starter code (the `.bak` files) and added five new
features to both the interpreter and the typechecker. I wrote it in shplait, a
teaching language built on Rhombus and Racket.

Here is a program it can run:

```
run_t_prog(
  [
    'class Posn(x :: Int,
                y :: Int):
       extends Object
       method mdist(arg :: Int) :: Int:
         x + y
       method addDist(arg :: Posn) :: Int:
         arg.mdist(0) + this.mdist(0)',
    'class Posn3D(z :: Int):
       extends Posn
       method mdist(arg :: Int) :: Int:
         z + super.mdist(arg)'
  ],
  '(new Posn3D(5, 3, 1)).addDist(new Posn(2, 7))'
)
// => '18'
```

## Features

- **Local bindings with let.** I added an environment and threaded it through the
  interpreter, so you can write `let x :: Int = 10: x + x`. Shadowing works, so an
  inner let correctly overrides an outer one.
- **Field access without `this`.** Fields can be read directly by name. The
  interpreter checks local variables first and falls back to the current object's
  fields. Methods can also name their argument something other than `arg`.
- **If/then.** The tricky part was typechecking. When the two branches return
  different object types, I find the least common ancestor in the class hierarchy
  and use that as the type of the whole expression.
- **Null.** Works as both an expression and a value, and typechecks as an object
  but never as an Int. Calling a method or reading a field on null is a runtime
  error.
- **Type casting.** Written for extra credit. `(Posn3D)p` checks at runtime that
  the value is an object, and checks at compile time that the cast is to a
  subclass.

## How It Works

- `inherit_parse.rhm` and `typed_parse.rhm` parse the surface syntax
- `typed_class.rhm` typechecks it, handling subtyping and inheritance
- `inherit.rhm` resolves `super` calls into static sends
- `class.rhm` evaluates the result

Each file ends with its own tests. I added about 50 on top of the starter tests,
for 198 total.

## Technology Used

- shplait
- Rhombus
- Racket / DrRacket

## Author

Created by Julia Duffy for CS 3520, Fall 2025
