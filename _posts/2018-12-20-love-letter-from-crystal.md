---
layout: post
title:  "A Love Letter, from Crystal to a Ruby developer"
---

# A Love Letter, from Crystal to a Ruby developer

### Firstly: " and ' have distinct meanings in Crystal.

```
·› crystal run test.cr
Syntax error in test.cr:29: unterminated char literal, use double quotes for strings

p combos('input0.txt', 25)
         ^
```

### No `readlines`

```
·› crystal run test.cr
Error in test.cr:2: undefined method 'readlines' for File.class

  File.readlines(filename).grep(/\d/).map(&:to_i)
       ^~~~~~~~~
```

### Enumerable stuff.

#### For empty arrays, use '[] of <ElementType>'

```
·› crystal run test.cr
Syntax error in test.cr:8: for empty arrays use '[] of ElementType'

  search(collection, [], volume).uniq
                                     ^

·› crystal run test.cr
Error in test.cr:8: can't use Array(T) as generic type argument yet, use a more specific type

  search(collection, [] of Array, volume).uniq
                                          ^~~~~
```

#### oops, my bad

```
·› crystal run test.cr
Error in test.cr:8: undefined constant Integer

  search(collection, [] of Array(Integer, Integer), volume).uniq
                                                 ^~~~~~~
```

#### Just use `size`

```
·› crystal run test.cr
Error in test.cr:7: undefined method 'length' for Array(Int32)

  collection = containers.zip([0] * containers.length)
                                                     ^~~~~~
```

#### Different return value from `each`.  Also, `count` is different (just use `size`)

```
·› crystal run test.cr
Error in test.cr:102: undefined method 'count' for Nil

result.each { |thing| p thing }.count
                                ^~~~~

·› crystal run test.cr
Error in test.cr:102: 'Array(Array(Array(Int32)))#count' is expected to be invoked with a block, but no block was given

result.map { |thing| p thing }.count
                               ^~~~~

·› crystal run test.cr
Error in test.cr:102: undefined method 'size' for Nil

result.each { |thing| p thing }.size
                                ^~~~
```

### More enumerable stuff: `reduce`

```
·› crystal run test.cr
Error in test.cr:12: wrong number of arguments for 'Array(Int32)#reduce' (given 2, expected 0..1)
Overloads are:
 - Enumerable(T)#reduce(memo, &block)
 - Enumerable(T)#reduce(&block)

  total = containers.map { |vol, qty| vol * qty }.reduce(0, :+)
                                                  ^~~~~~

·› crystal run test.cr
Error in test.cr:12: 'Array(Int32)#reduce' is expected to be invoked with a block, but no block was given

  total = containers.map { |vol, qty| vol * qty }.reduce(:+)
                                                  ^~~~~~
```

####### Fumbling toward the equivalent of Array#combination

```
·› crystal run test.cr
Error in test.cr:140: undefined method 'combination' for Range(Int32, Int32)

    indexes.combination(usage_count).select do |usages|
            ^~~~~~~~~~~


·› crystal run test.cr
Error in test.cr:140: undefined method 'combination' for Int::UptoIterator(Int32, Int32)

    indexes.combination(usage_count).select do |usages|
            ^~~~~~~~~~~


·› crystal run test.cr
Error in test.cr:140: undefined method 'combination' for Array(Int32) (did you mean 'combinations'?)

    indexes.combination(usage_count).select do |usages|
            ^~~~~~~~~~~
```

### Procs

#### Where you'd pass `&` and the name of a function as a symbol for Ruby to turn into a Proc, use syntax as though you're invoking the method on `&` instead.  `map(&:foo)` becomes `map(&.foo)`.

```
·› crystal run test.cr
Error in test.cr:2: expected a function type, not Symbol

  File.read(filename).split("\n").grep(/\d/).map(&:to_i)
                                                  ^
```



#### Complicated Types: Defining classes and passing instances of them is much easier to think about than passing arrays of arrays of arrays.  Let type-matching strictness guide you toward defining your data structures.

```
·› crystal run test.cr
Error in test.cr:16: no overload matches 'Array(Array(Int32))#<<' with type Array(Tuple(Int32, Int32))
Overloads are:
 - Array(T)#<<(value : T)

    solutions << containers
              ^~


·› crystal run test.cr
Error in test.cr:24: can't infer block return type, try to cast the block body with `as`. See: https://github.com/crystal-lang/crystal/wiki/Compiler-error-messages#cant-infer-block-return-type

    solutions + possibilities.map { |poss| search(poss, solutions, volume) }
                              ^~~


·› crystal run test.cr
Error in test.cr:11: type must be Array(Array(Tuple(Int32, Int32))), not (Array(Array(Tuple(Int32, Int32))) | Iterator::Map(Iterator::Map(Indexable::IndexIterator(Array(Tuple(Int32, Int32))), Array(Tuple(Int32, Int32))), Array(Tuple(Int32, Int32)), Array(Tuple(Int32, Int32))))

def search(containers, solutions, volume) : Array(Array(Tuple(Int32, Int32)))
    ^~~~~~~~~~~~~~~~
```

###### Type inference: a soliloquy

```
·› crystal run test.cr
Error in test.cr:18: Can't infer the type of instance variable '@containers' of ContainerSet

The type of a instance variable, if not declared explicitly with
`@containers : Type`, is inferred from assignments to it across
the whole program.

The assignments must look like this:

  1. `@containers = 1` (or other literals), inferred to the literal's type
  2. `@containers = Type.new`, type is inferred to be Type
  3. `@containers = Type.method`, where `method` has a return type
     annotation, type is inferred from it
  4. `@containers = arg`, with 'arg' being a method argument with a
     type restriction 'Type', type is inferred to be Type
  5. `@containers = arg`, with 'arg' being a method argument with a
     default value, type is inferred using rules 1, 2 and 3 from it
  6. `@containers = uninitialized Type`, type is inferred to be Type
  7. `@containers = LibSome.func`, and `LibSome` is a `lib`, type
     is inferred from that fun.
  8. `LibSome.func(out @containers)`, and `LibSome` is a `lib`, type
     is inferred from that fun argument.

Other assignments have no effect on its type.

Can't infer the type of instance variable '@containers' of ContainerSet

    @containers = container_volumes.map { |vol| ContainerUse.new(vol) }
    ^~~~~~~~~~~
```


#### Finally, `raise` an error or a message.

```
·› crystal run test.cr
Error in test.cr:89: wrong number of arguments for 'raise' (given 0, expected 1)
Overloads are:
 - raise(exception : Exception)
 - raise(message : String)

  raise unless minimal_count == 3
  ^~~~~
```
