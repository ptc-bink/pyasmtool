* [python bytecode explained](#s1)
  * [Introduction to bytecode](#s1-1)
  * [Disassembling of python code](#s1-2)


# <a id='s1' />python bytecode explained


## <a id='s1-1' />Introduction to bytecode

Python is an interpreted language; When a program is run, the python interpreter is first parsing your code and checking for any syntax errors, then it is translating the source code into a series of bytecode instructions; these bytecode instructions are then run by the python interpreter. This text is explaining some of the features of the python bytecode.

The byte code deals with two entities, a memory store that keeps functions and data items, and a stack used for evaluating expression.
The python interpreter works as a [stack machine](https://en.wikipedia.org/wiki/Stack\_machine) when it executes the byte code; 

The purpose of some the bytecode instructions is to populate the stack,
- THE [LOAD\_CONST](https://docs.python.org/3/library/dis.html#opcode-LOAD\_CONST) instructions takes a constant and pushes its value to the stack.
- The [LOAD\_FAST](https://docs.python.org/3/library/dis.html#opcode-LOAD\_FAST) instruction takes a variable and pushes a reference to this variable to the stack

Other bytecode instructions pop one or several values of a stack, perform some operation on them (like adding them) and push the result back to it,
- [BINARY\_ADD](https://docs.python.org/3/library/dis.html#opcode-BINARY\_ADD) pops two values off the stack, and pushes the sum of these values back to the stack.
- [UNARY\_NEGATE](https://docs.python.org/3/library/dis.html#opcode-UNARY\_NEGATIVE) pops the top entry from the stack, and pushes the negated numeric value back to it.

A very important sequence is the function call sequence.
- The lowest position on the stack must be a function object, this is put onto the stack by the [LOAD\_GLOBAL](https://docs.python.org/3/library/dis.html#opcode-LOAD\_GLOBAL) opcode,
- next come the arguments of the function call
- the next instruction is a function call opcode [CALL\_FUNCTION](https://docs.python.org/3/library/dis.html#opcode-CALL\_FUNCTION); This opcode comes with a parameter that specifies the number of parameters / number of stack entries that will be passed to the function call; these parameters will be poped off the stack, the return value of the function call will be pushed onto the stack.

Here is a [reference](https://docs.python.org/3/library/dis.html#python-bytecode-instructions) of the instructions.


## <a id='s1-2' />Disassembling of python code

You can examine the pyhon bytecode of a function by means of a dissassembler, as part of the python standard library you have the [dis](https://docs.python.org/3/library/dis.html) package, that can show you the bytecode of a python function.

I have written a disassembler that is producing a combined lising for a given python function, this means that you have a line of the python source code, followed by the python bytecode instructions that this source line translates into; I hope that this combined listing will make it much easier to comprehend, what the byte code instructions mean. I think that this will illustrate the conecpts, that were explained in the previous section.

Let's look at an example:
(There is one limitation, the tool can't be used, if running python code by means of the [exec](https://docs.python.org/3/library/functions.html#exec) built-in function) 



__Source:__

```python
import prettydiasm

def calculator(op, num_one, num_two):
    if op == 1:
        return num_one + num_two
    elif op == 2:
        return num_one - num_two
    elif op == 3:
        return num_one * num_two
    elif op == 4:
        return num_one / num_two
    else:
        raise ValueError("Invalid operation")

print( "prettydiasm.prettydis(calculator, show_opcode_as_links=True):", prettydiasm.prettydis(calculator, show_opcode_as_links=True) )


```

__Result:__
<pre>
> File path: /Users/michaelmo/mystuff/pyasmtools/calc.py 
> 
> calc.py:3 def calculator(op, num_one, num_two):
> 
> calc.py:4 	    if op == 1:
> 
>   4           0 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     0 (op)
>               2 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     1 (1)
>               4 <a href="https://docs.python.org/3/library/dis.html#opcode-COMPARE_OP">COMPARE_OP</a>     2 (==)
>               6 <a href="https://docs.python.org/3/library/dis.html#opcode-POP_JUMP_IF_FALSE">POP_JUMP_IF_FALSE</a>    16
> 
> calc.py:5 	        return num_one + num_two
> 
>   5           8 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     1 (num_one)
>              10 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     2 (num_two)
>              12 <a href="https://docs.python.org/3/library/dis.html#opcode-BINARY_ADD">BINARY_ADD</a>
>              14 <a href="https://docs.python.org/3/library/dis.html#opcode-RETURN_VALUE">RETURN_VALUE</a>
> 
> calc.py:6 	    elif op == 2:
> 
>   6     >>   16 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     0 (op)
>              18 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     2 (2)
>              20 <a href="https://docs.python.org/3/library/dis.html#opcode-COMPARE_OP">COMPARE_OP</a>     2 (==)
>              22 <a href="https://docs.python.org/3/library/dis.html#opcode-POP_JUMP_IF_FALSE">POP_JUMP_IF_FALSE</a>    32
> 
> calc.py:7 	        return num_one - num_two
> 
>   7          24 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     1 (num_one)
>              26 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     2 (num_two)
>              28 <a href="https://docs.python.org/3/library/dis.html#opcode-BINARY_SUBTRACT">BINARY_SUBTRACT</a>
>              30 <a href="https://docs.python.org/3/library/dis.html#opcode-RETURN_VALUE">RETURN_VALUE</a>
> 
> calc.py:8 	    elif op == 3:
> 
>   8     >>   32 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     0 (op)
>              34 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     3 (3)
>              36 <a href="https://docs.python.org/3/library/dis.html#opcode-COMPARE_OP">COMPARE_OP</a>     2 (==)
>              38 <a href="https://docs.python.org/3/library/dis.html#opcode-POP_JUMP_IF_FALSE">POP_JUMP_IF_FALSE</a>    48
> 
> calc.py:9 	        return num_one * num_two
> 
>   9          40 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     1 (num_one)
>              42 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     2 (num_two)
>              44 <a href="https://docs.python.org/3/library/dis.html#opcode-BINARY_MULTIPLY">BINARY_MULTIPLY</a>
>              46 <a href="https://docs.python.org/3/library/dis.html#opcode-RETURN_VALUE">RETURN_VALUE</a>
> 
> calc.py:10 	    elif op == 4:
> 
>  10     >>   48 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     0 (op)
>              50 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     4 (4)
>              52 <a href="https://docs.python.org/3/library/dis.html#opcode-COMPARE_OP">COMPARE_OP</a>     2 (==)
>              54 <a href="https://docs.python.org/3/library/dis.html#opcode-POP_JUMP_IF_FALSE">POP_JUMP_IF_FALSE</a>    64
> 
> calc.py:11 	        return num_one / num_two
> 
>  11          56 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     1 (num_one)
>              58 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     2 (num_two)
>              60 <a href="https://docs.python.org/3/library/dis.html#opcode-BINARY_TRUE_DIVIDE">BINARY_TRUE_DIVIDE</a>
>              62 <a href="https://docs.python.org/3/library/dis.html#opcode-RETURN_VALUE">RETURN_VALUE</a>
> 
> calc.py:13 	        raise ValueError("Invalid operation")
> 
>  13     >>   64 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_GLOBAL">LOAD_GLOBAL</a>     0 (ValueError)
>              66 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     5 ('Invalid operation')
>              68 <a href="https://docs.python.org/3/library/dis.html#opcode-CALL_FUNCTION">CALL_FUNCTION</a>     1
>              70 <a href="https://docs.python.org/3/library/dis.html#opcode-RAISE_VARARGS">RAISE_VARARGS</a>     1
>              72 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     0 (None)
>              74 <a href="https://docs.python.org/3/library/dis.html#opcode-RETURN_VALUE">RETURN_VALUE</a>
> prettydiasm.prettydis(calculator, show_opcode_as_links=True): None
</pre>


__Source:__

```python
import prettydiasm

def fac(arg_n=7):
    if arg_n == 1:
        return arg_n
    return arg_n * fac(arg_n - 1)

print( "prettydiasm.prettydis(fac, show_opcode_as_links=True):", prettydiasm.prettydis(fac, show_opcode_as_links=True) )

```

__Result:__
<pre>
> File path: /Users/michaelmo/mystuff/pyasmtools/fac_rec.py 
> 
> fac_rec.py:3 def fac(arg_n = 7):
> 
> fac_rec.py:4 	    if arg_n == 1:
> 
>   4           0 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     0 (arg_n)
>               2 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     1 (1)
>               4 <a href="https://docs.python.org/3/library/dis.html#opcode-COMPARE_OP">COMPARE_OP</a>     2 (==)
>               6 <a href="https://docs.python.org/3/library/dis.html#opcode-POP_JUMP_IF_FALSE">POP_JUMP_IF_FALSE</a>    12
> 
> fac_rec.py:5 	        return arg_n
> 
>   5           8 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     0 (arg_n)
>              10 <a href="https://docs.python.org/3/library/dis.html#opcode-RETURN_VALUE">RETURN_VALUE</a>
> 
> fac_rec.py:6 	    return arg_n * fac(arg_n - 1)
> 
>   6     >>   12 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     0 (arg_n)
>              14 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_GLOBAL">LOAD_GLOBAL</a>     0 (fac)
>              16 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     0 (arg_n)
>              18 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     1 (1)
>              20 <a href="https://docs.python.org/3/library/dis.html#opcode-BINARY_SUBTRACT">BINARY_SUBTRACT</a>
>              22 <a href="https://docs.python.org/3/library/dis.html#opcode-CALL_FUNCTION">CALL_FUNCTION</a>     1
>              24 <a href="https://docs.python.org/3/library/dis.html#opcode-BINARY_MULTIPLY">BINARY_MULTIPLY</a>
>              26 <a href="https://docs.python.org/3/library/dis.html#opcode-RETURN_VALUE">RETURN_VALUE</a>
> prettydiasm.prettydis(fac, show_opcode_as_links=True): None
</pre>


__Source:__

```python
import prettydiasm

def fac_iter(arg_n: int) -> int:
    res = 1
    for cur_n in range(1,arg_n):
        res *= cur_n
    return res

print( "prettydiasm.prettydis(fac, show_opcode_as_links=True):", prettydiasm.prettydis(fac_iter, show_opcode_as_links=True) )



```

__Result:__
<pre>
> File path: /Users/michaelmo/mystuff/pyasmtools/fac_iter.py 
> 
> fac_iter.py:3 def fac_iter(arg_n: int) -> int:
> 
> fac_iter.py:4 	    res = 1
> 
>   4           0 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     1 (1)
>               2 <a href="https://docs.python.org/3/library/dis.html#opcode-STORE_FAST">STORE_FAST</a>     1 (res)
> 
> fac_iter.py:5 	    for cur_n in range(1,arg_n):
> 
>   5           4 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_GLOBAL">LOAD_GLOBAL</a>     0 (range)
>               6 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_CONST">LOAD_CONST</a>     1 (1)
>               8 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     0 (arg_n)
>              10 <a href="https://docs.python.org/3/library/dis.html#opcode-CALL_FUNCTION">CALL_FUNCTION</a>     2
>              12 <a href="https://docs.python.org/3/library/dis.html#opcode-GET_ITER">GET_ITER</a>
>         >>   14 <a href="https://docs.python.org/3/library/dis.html#opcode-FOR_ITER">FOR_ITER</a>    12 (to 28)
>              16 <a href="https://docs.python.org/3/library/dis.html#opcode-STORE_FAST">STORE_FAST</a>     2 (cur_n)
> 
> fac_iter.py:6 	        res *= cur_n
> 
>   6          18 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     1 (res)
>              20 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     2 (cur_n)
>              22 <a href="https://docs.python.org/3/library/dis.html#opcode-INPLACE_MULTIPLY">INPLACE_MULTIPLY</a>
>              24 <a href="https://docs.python.org/3/library/dis.html#opcode-STORE_FAST">STORE_FAST</a>     1 (res)
>              26 <a href="https://docs.python.org/3/library/dis.html#opcode-JUMP_ABSOLUTE">JUMP_ABSOLUTE</a>    14
> 
> fac_iter.py:7 	    return res
> 
>   7     >>   28 <a href="https://docs.python.org/3/library/dis.html#opcode-LOAD_FAST">LOAD_FAST</a>     1 (res)
>              30 <a href="https://docs.python.org/3/library/dis.html#opcode-RETURN_VALUE">RETURN_VALUE</a>
> prettydiasm.prettydis(fac, show_opcode_as_links=True): None
</pre>

