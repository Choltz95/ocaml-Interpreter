=== CSC 254 A3: Interpretation ===
Chester Holtz - 28264729 - choltz2@u.rochester.edu
Lee Murphy - 28250920 - lmurp14@u.rochester.edu
    
In this assignment we where required to implement an interpreter for programs written in an extended calculator language. A tokenizer, parser, and parse tree generator were provided. We implemented an ast generator, evaluator, repl, and simple variable matching.

== Files ==
README.txt - This file
run - bash script to pipe --debug option and file input to the program
interpreter.ml - parser logic to construct the syntax tree from tokens passed by the scanner and recover from any errors
input.txt - sample input that results in the successful construction of the ast

== Description ==
This assignment tasked us with implimenting a syntax error recovery mechanism for an extended, turing complete calculator language. The calculator language we implimented this for supports ifs, whiles, assignment, and simple number and boolean operations. We where provided with the fundamental components of a scanner and parser for this language and where required to first extend the parser to include certain functionality, exception-based syntax error recovery to out put helpful error messages and to construct a syntax tree given a correct program. This assignment was completed in the C++ language.

As in the A1 language assignment, we represemt an abstract syntax tree as a recursive linear list of parenthesis where a node is denoted by a set of open parenthesis and its children are nested - i.e., the tree 

            a
          / | \
         b  c  d
        /|  |
       e f  g

would be represented by the string (a (b e f) (c g) d).

The LL(1) grammar representing our language is provided below, along with the partial first and follow sets used to complete the syntax recovery process.

GRAMMAR
P	→ 	SL $$
SL	→ 	S SL  |  ε
S	→ 	id := E  |  read id  |  write E  |  if C SL end  |  while C SL end
C	→ 	E ro E
E	→ 	T TT
T	→ 	F FT
F	→ 	( E )  |  id  |  lit
TT	→ 	ao T TT  |  ε
FT	→ 	mo F FT  |  ε
ro	→ 	==  |  !=  |  <  |  >  |  <=  |  >=
ao	→ 	+  |  -
mo	→ 	*  |  /

== Approach ==
Instead of building our syntax tree as an explicit data structure stored in memory, we instead accumulate a string through the parse process. We solve the inherint scope problem a parse tree by constructing our tree bottom up and conglomerating subtrees at key points. 
We implimented a recursive solution to error recovery providing recovery and errors for unexpected eof, unknown tokens, and general programming errors.

== EXTRA CREDIT ==
As we tokenize, we record the current position of the getc buffer in order to provide the programmer with an estimation of the column and line an arbitrary error occurs. This was done for extra credit.
We highlighted errored tokens in red to make it more apparent to the user as well.

== Exception Handling ==
A compiler will detect a syntax error immediately after the invalid token and exit with error, instead, we impliment a recovery system that prevents our compiler/interpreter from exiting and continues to parse the program to look for further errors - although evaluation at this point is impossible. The primary purpose of this system is to modify the input program as we parse and hopefully continue to produce useful error messages while avoiding spurious cascading errors that might cause the programmer to miss real ones. 
Exception-Based Error Recovery is an alternative to Wirth's technique and is quite simple to impliment. Essentially, we attach an exception handler to case branches that we want to recover from in the event of an error. We determine how to procede in the event of an error based on the first and follow sets of the currently parsed token. If the next token is in the first of the current, we throw out the current token and recursively call the current terminal block. If the next token is in the follow set, we post a useful error message to the console, notify the program that we no longer need to construct an ast for the program, and continue to parse. If the next token is in neither of the above sets, we return unexpected eof or break from the statement. 

== Instructions ==
In this project, we include a run script which takes either one or two arguments. The user can provide -d, or --debug to run the program in debug mode to print out a trace of predictions, matches, and the process of ast construction for the program. The user should always specify a file containing their program to interpret since inline interpretation forgoes the eof, which is mandatory for the construction of the ast.

== Example Output (good) ==
 »

== Output for debug mode ==
