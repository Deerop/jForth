
An early exercise in Forth and Java. Since succeded by this much more powerful [version](https://github.com/Reschivon/RemoteJavaForth)

# JForth
A Forth implementation in Java that sticks to the elegance of a bare metal Forth interpreter as much as possible.

Also useful as a reference for programmers from higher-level languages learning the in & outs of the Forth interpreter. (If you are brand-new to Forth, [read up](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm) on the syntax before getting into the implementation)

Todo: 

The primitives can be further reduced

make linked list dictionary traversal work with here

Early binding for native java words so they can be compiled

## Motivation
The need to speed up the development process of FIRST Tech Challenge robotics code. Deploying a single edit requires 20s of build time, manual download thru a cable, repositioning the robot, and restarting the OpMode. 
However with Forth this can be reduced to nil.
- Wirelessly send Forth commands to interpreter running on robot (no touch!)
- No need to restart OpMode
- No need to build -- system maintains state between edits

To interoperate with the robot's Java code this interpreter must be written in Java.

### Interpreter Info
To imitate the "genuine" experience of making a Forth on bare metal (which is where its elegance really shines) I've forgone the fancy data structures/libraries of Java.
Because mostly high-level control code will be written in Forth, it's OK to sacrifice speed for elegance. The compute-intensive portions are in Java and C++
I have a HashMap serving as lookup table for primitive Java words; this is the only advanced-ish Java library I use.

The memory is one big integer array -- strings are stored here as Unicode characters, not String objects.

Here is the structure of one word in memory

    +-------------------+-----------+----------------- - - - - +------------+------------- - - - -
    | POINTER TO        | LENGTH OF | NAME CHARACTERS          | IMMEDIATE? | ADDRESSES OF 
    | PREVIOUS WORD	    | NAME      |     	                   |            | INSTRUCTIONS
    +--- Integer 32 ----+- Integer -+- n Integers as - - - - - +- Integer --+------------- - - - -
                               ^         Unicode Points
                     next pointer points here
                     
Pretty much everything here is heavily inspired by JonesForth

type `lit` before literals in immediate mode, and `[lit]` before literals in compiler mode. `native` will place the address of 
the native entrypoint object on the stack (defined in main) of whose fields and methods can be accessed form java code. 

Words:
**create word stringliteral literal [lit] lit branch? branch xor or and not swap dup * - + set read ] [ stack>mem return print memposition seerawmem seemem seestack**

Type `profanity` in the terminal to enable aggression upon entering invalid words. I'm not responsible if you get offended over a feature you enabled yourself

### Code Info
`Interpreter.java` is the actual code. I'll make an effort to comment it up soon.

`start.f` is the Forth code executed when the interpreter starts. It defines control flow and other words from primitives

`test.f` tests to make sure everything is working correctly. Should output alternating `11`s and `22`s

`OldInterpreter.java` is an older iteration of the interpreter. You can see that I'm over reliant on the Java type system and implement words directly in Java without understanding compiling words or memory. I suspect this is a common issue for all C-derivative programmers doing low-level for the first time. But what a learning experience this was!

There is an even earlier iteration that I made impulsively in the dark of night -- an even more naive python interpreter with words like IF that worked by scanning the source file for THEN . . .


## Credits
- [JonesForth](https://github.com/nornagon/jonesforth/blob/master/jonesforth.f) for the excellent step-by-step commentary of building an interpreter in assembly
- [eForth](http://www.exemark.com/FORTH/eForthOverviewv5.pdf) source code of high level Forth words derived from 30 or so primitives
- [StackExchange](https://softwareengineering.stackexchange.com/questions/339283/forth-how-do-create-and-does-work-exactly) article on CREATE and DOES> which got me curious about the Forth interpreter in the first place
- [Python Interpreter](https://www.openbookproject.net/py4fun/forth/forth.html) A simplified interpreter without the mindbending of managing pointers. Good starting point before attempting a full-on interpreter.
- Wise men from the Silicon Valley Forth Interest Group
- More wise men from the Forth2020 Users group

