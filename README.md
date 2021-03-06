
# Logic Tester

this little library can test your logic expressions.

to write a testable logic expression, one can use the following replacements for the default binary connectives:

| python | bianry connective | 
| --- | --- |
| not	(Not(a,b))	| 		¬				|
| and	(And(a,b))	| 		^				|
| or 	(Or(a,b))	| 		v 				|
| Xor(a,b)			| 		⊕ 				|
| Xnor(a,b)			| 		⇔				|
| Implies(a,b)		| 		->				|

To start testing a logical expression, one writes a function like this, with as many parameters as will be used in the expression (This is important):

```python
from logictester import *


def test1(a,b,c,d):
	return Implies(a,b and c) or (c and Xor(a,d))

```

it's important that the result of this expression will be returned. within the function you wrote you can do anything as long as the return value is a boolean. In most cases a one-line function as above will suffice.

now comes the magic:

to generate a truth table of this expression, call the logic function with as the first and only argument the function:
```python
logic(test1)
```

so you get:
 
```python
from logictester import *

def test1(a,b,c,d):
	return Implies(a,b and c) or (c and Xor(a,d))


print(logic(test1))
```

the truth table of this function will be printed like this:    
a b c d | o  
0 0 0 0 | True  
0 0 0 1 | True  
0 0 1 0 | True  
0 0 1 1 | True  
0 1 0 0 | True  
0 1 0 1 | True  
0 1 1 0 | True  
0 1 1 1 | True  
1 0 0 0 | False  
1 0 0 1 | False  
1 0 1 0 | True  
1 0 1 1 | False  
1 1 0 0 | False  
1 1 0 1 | False  
1 1 1 0 | True  
1 1 1 1 | True  
True count:  11  
False count:  5  

the ammount of arguments to the logic_tester is arbitrary. it will automatically be determined. This doesn't mean any number of arguments is possible. this is due to the fact that the algorithm used is O(2^n) which is *SLOW*. so to not-overflow your terminal and keep execution time reasonable, be reasonable with the ammount of arguments.

---

One can also test for equivalence of two functions with logictester. this can be done as follows: 

```python

from logictester import *

def test1(a,b,c,d):
	return Implies(a,b and c) or (c and Xor(a,d))

def test2(a,b,c,d):
	return Implies(a,b and c) or (d and a)

def test3(a,b,c,d):
	return Implies(a,b and c) or d


print(is_equivalent(test1,test2)) 	
#will return False

print(is_equivalent(test2,test3))
#will return True

print(logic(test2) == logic(test3))
#works too. will also return True

```

---

One can find the differences between two expressions (which is actually used to test for equivalence. if there are no differences the expressions are equivalent) by using the find_differences funtion on the logic object:


```python

from logictester import *

def test1(a,b,c,d):
	return Implies(a,b and c) or (c and Xor(a,d))

def test2(a,b,c,d):
	return Implies(a,b and c) or (d and a)

def test3(a,b,c,d):
	return Implies(a,b and c) or d


print(logic(test1).find_differences(test2)) 	
#will return [(1, 0, 0, 1), (1, 0, 1, 0), (1, 0, 1, 1), (1, 1, 0, 1)]
#since these are the input cases in which test1 and test2 differ

print(logic(test2).find_differences(test3))
#will return an empty list ([])
```

---

logic tester now also includes autmatic simplification and generation of logic expressions. to use this feature just make a new expression function, use logic() on it and use the find_expression method. this method will be the simpelest possible disjunctive normal form of the expression. (bugesting needed, haven't proven the working but i'm yet to find a casse in which is doesnt work.)

```python
from logictester import *

def test1(a,b,c,d):
	return Implies(a,b and c) or d


print(logic(test1).find_expression())
#prints (a) v (¬a ^ ¬d) v (¬a ^ ¬b ^ ¬c ^ d)

```