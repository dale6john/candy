# Candy
_Programming Puzzle Language._

This is a stack based sequential programming language.  A language lacking in syntactic sugar.

Currently Interpreted with perl.  Candidate for translation to C or assembly.

## Stack

* `1`     push 1 onto the current stack
* `X`     push X (see Registers) onto the current stack
* `12`    push 1, then push 2 onto stack
* `12+`   the '+' adds the top 2 items, pushes result
* `12+?`  the '?' pops the top and prints the result
* `1!`    prints '1' without a line-feed
* `#99?`  prints '99', numbers > 9 require '#' prefix
* `5R`    creates the range 5,4,3,2,1 onto stack
* `5_`    creates the range 4,3,2,1,0 onto stack
* `1m?`   'm' unary negation
* `5n5O`  creates the range 5,4,3,2,1,0,-1,-2,-3,-4,-5 on stack
* `12W!?` 'W' swaps the top two stack elements,  prints 1,2
* `1D+?`  'D' duplicates top stack element, prints 2
* `3r?`   'r' unary square root
* `S`     replace the stack with its sum
* `P`     replace the stack with its product
* `5RP?`  gives 120, 1 * 2 * 3 * 4 * 5
* `a,b,c` set to alternate stacks, 'a' is the default stack
* `Cb`    cartesian product of current stack with 'b', also Ca,Cc
* `5Rb3_Ca(!?)`  outputs 15 elements of (1..5) x (0..2)


## Registers

A,X,Y,Z are specialized registers

* 'A' manages stack interactions
* 'Z' is a counter
* 'X', 'Y' are general purpose

### examples:
* `1=`    push 1, then pop 1 into A
* `1=x`   same, but then set X to the value of A
* `12!?`  prints `...2   1`
* `12=x=XA!?` swaps the top two stack elements, the hard way (see W)
  * `push 1`,
  * `push 2`, 
  * `pop 2 into A`, 
  * `X <- A`, 
  * `pop 1 into A`,  (stack is now empty)
  * `push X (2)`, 
  * `push A (1)`, 
  * `pop and print 1,2`
* `0=z`   set z to 0
* `i`     increment z
* `d`     decrement z
* `p`     pop and add to z

## Control Flow

* `(`     pushes PC and stack frame pointer to loop stack
* `)`     if stack is empty, 
  * pop loop stack, no-op, continue past loop structure
  * otherwise:
  * pop PC and stack frame pointer from loop stack, this jumps
    back to the character right after the '('
* `{`     pop value off stack, if non-zero (true)
  * continue execution, into 'then' portion, immediatly follows.
  * otherwise
  * if zero, skip to the '|' or the '}' and continue there
  * Note that nesting is allowed
* `|`     if we naturally arrive here, skip to the next unmatched '}'
* `}`     no-op on execution, only used as a reference for '{', '|'
* `(?)`    loops printing each stack element until stack is empty
* `{?|?}`  true,false actions, in this case print on both true and false
* `{?}`    true action, pull item off stack, if true, pull another and print it

### Iterating over stack

* `5R(?)`    print 1,2,3,4,5  note that '?' consumes a stack element
* `1{#10|0}?`  print 10
* `0{#10|0}?`  print 0
* `3_(=bA{1|0})bS`  map 2,1,0 (stack a) to 1,1,0 (stack b), and
                then sum stack b
                Note that interpreting the ')' puts the stack
                frame back to 'a', which was the stack frame
                at the '('

Advanced
--------


Examples
--------

Hello, World:
```
candy -e '"hello, world\n"(;)'
```

looped Hello, World:
```
candy -i 5 -e 'R(pb"hello, world\n"(;))'
```

[Count sum of two squares](http://codegolf.stackexchange.com/questions/64812/count-sums-of-two-squares)

./doStk.pl -i25 '~AmWObAmAOxCa(sWs+Xe{i})Z?'  # (count sums sq)
./doStk.pl -i0,25 '=x=cAXO(0=z~x!aAmAObAmAOCa(sWs+Xe{i})Z?)'

[Count divisors of a number](http://codegolf.stackexchange.com/questions/64944/count-the-divisors-of-a-number)

./doStk.pl -i12 '~xR(XW%{i})Z'   # (count divisors)
./doStk.pl -i12 '~xR(XW/~ALep)Z'
./doStk.pl -i12 '~xR(XW/~ALep)Z?'

TODO
----
zip
search stack
print stack size
subroutines


Full Instruction List
---------------------

|OP  |Long Op  |details  |
|----|---------|---------|
| `?` |  println |  |
| `p` |  print |  |
| `;` |  printChr |  |
| `~` |  peekA |  |
| `=` |  popA |  |
| `A` |  pushA |  |
| `X` |  pushX |  |
| `Y` |  pushY |  |
| `Z` |  pushZ |  |
| `0..9` |  digit0-9 |  |
| `#` |  number |  used to generate numbers > 9 |
| `x` |  XGetsA |  |
| `y` |  YGetsA |  |
| `z` |  ZGetsA |  |
| `i` |  incrZ |  |
| `d` |  decrZ |  |
| `h` |  popAddZ |  |
| `j` |  jumpSub |  |
| `.` |  retSub |  |
| `@1` |  label | |
| `D` |  dupl |  |
| `m` |  negate |  |
| `r` |  root |  |
| `s` |  sqr |  |
| `L` |  floor |  |
| `!` |  not |  |
| `e` |  equal |  |
| `+,-,*,/,%` | add,sub,mult,div,mod |  |
| `W` |  swap |  |
| `O` |  rangeP | range using a pair off the stack |
| `_` |  range0 | range starting at 0 |
| `R` |  range1 | range starting at 1 |
| `S` |  sum |  |
| `P` |  prod |  |
| `F` |  find |  search stack, return 0 or 1 (unimplemente) |
| `&` |  stackSz |  |
| `C` |  cart | cartesian product |
| `I` |  zip |  zip lists (unimplemented) |
| `a` |  stack1 |  |
| `b` |  stack2 |  |
| `c` |  stack3 |  |
| `K` |  getSP | push current stack frame id onto stack |
| `k` |  setSP | pull next frame id from stack |
| `{` |  if |  |
| `|` |  else |  |
| `}` |  endif |  |
| `(` |  while |  |
| `)` |  endwhile |  |
| ` ` |    noOp | includes space, tab and linefeed |
| `fglopqtuvw` | | future |
| `BDEGHIJMNQRTUV` | | future |
| `$^[]\:'<>,` | | future |
