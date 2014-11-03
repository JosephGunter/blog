---
layout: post
title: "Basic unit testing in Python"
date: 2012-07-05 19:53:18
categories: python testing
---
I have a Python 3.2 program that's in a working state, but I never wrote any tests for it. 
Now, I want to go back and provide some code coverage so that as I go back to refactor and add functionality, 
I'll be more certain I'm not breaking anything. 
This is a fairly common case--you have existing code, and you want to write some tests for it.

Environment
-----------

I'm running Python 3.2 in a Windows 7 environment, using the IDLE Python code editor. 
I typically run code directly from IDLE by using the F5 key. 
Because of how IDLE invokes the Python interpreter, some of the code examples below will run 
differently depending on whether code is invoked from IDLE or from the command line.
To enable Python from the Windows command line, it must be added to the PATH. 
To do this, open a command line window (Start -> Accessories -> Command Line) and type path <path to your python installation>. 
For example, since my python 3.2 installation can be found at C:\Python32, I issue the following path command:

```bash
path C:\Python32
```

This adds the Python interpreter to the path so it can be invoked from within your Python project directory. 
Note that it only changes the path for this one command line session, so if you close and 
re-open the command line window you'll have to re-issue the path command.

Python's UnitTest Module
------------------------

The first issue is what testing framework to use. I'm going to use Python's built-in module called UnitTest. 
It's a one-stop shop for all the testing most pure-Python programs will need. 
More advanced testing frameworks are available. The two most popular seem to be py.test and nose. 
I want to explore testing in a more 'raw' state, so I'll save these frameworks until I'm comfortable using unittest on its own.

Writing Testable Code
--------------------

It's easiest to write a test for a small, discreet bit of code that accomplishes a well-defined task. 
Python best practices dictate that code be broken into small functions that perform one task at one level of abstraction. 
If you follow this advice and write your code using many small functions that each perform one well-defined task,
 then those functions will be easy to test. 
If instead you have written large functions that combine many steps,
 then first consider breaking these functions down into smaller ones.

Pick a small function to test
--------------------------

Start small by picking a short, well-understood function that does something simple. 
I'll use a function that takes one argument, uses an if statement to choose a "rate", and returns that rate:

```python
def choose_renovation_rate(self, years_since_last_renovation):
    if years_since_last_renovation < 7:
        rate = 0
    elif years_since_last_renovation < 15:
        rate = 0.01
    elif years_since_last_renovation < 25:
        rate = 0.05
    elif years_since_last_renovation < 50:
        rate = 0.07
    else:
        rate = 0.1
    return rate
```

Create a new file to hold the test code
------------------------------------

To get started with the testing itself, create a new file,
 and for now make sure it's in the same directory as the code you wish to test. 
I called mine unit_tests.py. 
Import the testunit module, and start a new class that describes what you will be testing. 
This new class will be a sub-class of unittest.TestCase. This class will hold several small test cases.

```python
import unittest

class TestRateFunctions(unittest.TestCase):
    pass
    # test cases go here
```

Write a test function title and description
----------------------------------------

Next, inside the TestRateFunctions class I just defined, I will define a function and describe in words what it will test. 
The function names, by convention and to allow the testing machinery to run properly, should start with test_. 
For the moment, I will use a single assertTrue statement and pass it the condition True,
 just to see if everything is working.

```python
import unittest

class TestRateFunctions(unittest.TestCase):
    def test_renovation_chooser_should_return_correct_rate(self):
        # This function should return the correct renovation rate
        self.assertTrue(True)
```

This is now a trivial, but functioning test.

Run the test
-----------

To actually run the test, we need to add one line to the end of the file: unittest.main(). 
With this line in place, we can simply call this test one of two ways.

###Via the command line

After issuing the PATH commands as discussed under "Environment" above,
 in the command line terminal navigate to the directory where you created the file and call it:

```bash
C:\lighting_floor_space_stock_model>python unit_tests.py
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

The output consists of

1. A period. This represents one test, which in this case was test_renovation_chooser.
   With more tests written, these dots acts as a progress indicator for the test suite.
1. A long line of hyphens. This is just a visual separator representing the end of the execution of tests.
1. A report, which in this case `Ran 1 test in 0.000s`.
1. The phrase `OK`. We only get this if all tests pass.

###Via IDLE

In the IDLE code editor, you can just press F5 to run the code directly. This produces the following output:

```bash
.
----------------------------------------------------------------------
Ran 1 test in 0.004s

OK
Traceback (most recent call last):
  File "C:\lighting_floor_space_stock_model\unit_tests.py", line 8, in <module>
    unittest.main()
  File "C:\Python32\lib\unittest\main.py", line 124, in __init__
    self.runTests()
  File "C:\Python32\lib\unittest\main.py", line 272, in runTests
    sys.exit(not self.result.wasSuccessful())
SystemExit: False
```

As you can see, when we ran the test suite from inside IDLE,
 we got the same successful output telling us the test passed (OK!),
 but it's followed by an ugly error and a traceback. 
The error is because unittest wants to "exit" but it can't, because the default IDLE behavior is to keep running. 

There are __two simple ways__ around this error:

1.  One solution is to just catch the error and pass around it. Simply change the last line to following:

    ```python
    try: unittest.main()
    except SystemExit: pass
    ```

1.  Alternatively, we could keep the first line as-is (without adding the try/except 
    statements or the second line) and just add an argument to the original line, as follows:

    ```python
    unittest.main(exit = False)
    ```

With either of those two modifications in place, the test suite should run properly from within IDLE.

Now Test Your Function
------------------------

Now that we're sure we've got all the machinery operating correctly 
(`import unittest`, create a subclass, define a test case starting with test_,
 make sure it runs properly), we have to write code that actually tests our function.

In order to call the function in question (`choose_renovation_rate()`) inside the testing 
function (`test_renovation_chooser_should_return_correct_rate`),
we need to first make sure its parent class is available. 
In this case, `choose_renovation_rate()` is a function inside the class `FloorSpace()`,
so first I need to import the FloorSpare class with from `floor_space import *`. 
Then I can create a new variable called `self.rate` that holds the result of the 
`FloorSpace.choose_renovation_rate` function. 
The whole file now appears as follows, using `assertTrue`:

```python
import unittest
from floor_space import FloorSpace

class TestRateFunctions(unittest.TestCase):
    def test_renovation_chooser_should_return_correct_rate(self):
        # A 2-year old building has a 0% chance of renovation:
        self.rate = FloorSpace.choose_renovation_rate(self, 2)
        self.assertTrue(self.rate == 0)

unittest.main(exit = False)
```

A Quick Refactor
---------------

Let's make one short change. Since we're testing for equality (using the == operator),
 we could use the assertEqual function instead of assertTrue. 
Furthermore, we could a string argument to the end of the function that will get 
printed if the test fails, allowing us to pass more detailed and useful information to the user. 
Change the last line of the test function as follows:

```python
self.assertEqual(self.rate, 0, "2-year-old building should have a 0% renovation rate.")
```

This is such a simple test method that the message is nearly redunant to just reading the code,
 but it serves to illustrate the method of adding an information string to the assert method.

Refining the Test
----------------

Consider again the function we're testing:

```python
def choose_renovation_rate(self, years_since_last_renovation):
    if years_since_last_renovation < 7:
        rate = 0
    elif years_since_last_renovation < 15:
        rate = 0.01
    elif years_since_last_renovation < 25:
        rate = 0.05
    elif years_since_last_renovation < 50:
        rate = 0.07
    else:
        rate = 0.1
    return rate
```

We pass it the number of years since it was last renovated (in years), and it returns a renovation rate. 
That renovation rate is used by other functions to perform various other computations. 
We could test each of the if conditions, but those numbers might change if we later tune the function to match real-world data. 
It might be more useful to test, for example, that the function always returns a percentage (i.e. a float between zero and one). 
So let's test a few of the if conditions just for completeness, and add a new 
test function to check whether it always returns a percentage between 0 and 1. 
Note that instead of testing each year we put the assert methods inside a for loop.

```python
import unittest
from floor_space import FloorSpace

class TestRateFunctions(unittest.TestCase):
    def test_renovation_chooser_should_return_correct_rate(self):
        # A 2-year old building has a 0% chance of renovation:
        self.rate = FloorSpace.choose_renovation_rate(self, 2)
        self.assertEqual(self.rate, 0, "2-year-old building should have a 0% renovation rate.")

        # A 13-year old building has a 1% chance of renovation:
        self.rate = FloorSpace.choose_renovation_rate(self, 13)
        self.assertEqual(self.rate, 0.01, "13-year-old building should have a 1% renovation rate.")

        # A 55-year old building has a 10% chance of renovation:
        self.rate = FloorSpace.choose_renovation_rate(self, 55)
        self.assertEqual(self.rate, 0.1, "55-year-old building should have a 10% renovation rate.")

    def test_renovation_chooser_should_return_percentage(self):
        # The function should always return a rate such that 0 <= rate <= 1
        for years_since_renovation in range(100):
            self.rate = FloorSpace.choose_renovation_rate(self, years_since_renovation)
            self.assertGreaterEqual(self.rate,0, "renovation rate should be >= 0")
            self.assertLessEqual(self.rate,1, "renovation rate should be <= 1")

unittest.main(exit = False)
```

For a list of assert methods made available to unittest, see the documentation.

Conclusion
----------

The basic steps to writing unit tests in Python are:

1.  Write testable code by keeping functions short; each function should perform one task
1.  Create a new file for the test code (e.g. unit_tests.py)
1.  `Import unittest`
1.  Subclass unittest.TestCase (e.g. `class TestRateFunctions(unittest.TestCase)`)
1.  Create new test functions with description names starting with `test_` (e.g. 
    def test_renovation_chooser_should_return_percentage):`
1.  Make your "work" functions available to the "test" functions by importing where 
    necessary (e.g. from floor_space import FloorSpace)
1.  Add string arguments to pass relevant information to the user in the case of a failed test.
1.  Refine and refactor for readability and usefulness.

Finally, don't forget to actually run the tests frequently during development!
