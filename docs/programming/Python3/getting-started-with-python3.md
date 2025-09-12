## Python Fundamentals

![python-logo](https://www.python.org/static/community_logos/python-logo-master-v3-TM.png)

Python is a high-level, interpreted programming language that is widely used for web development, scientific computing, data analysis, artificial intelligence, and many other applications.    

Python is known for its simplicity and readability, making it a popular choice for beginners and experienced programmers alike. It has a large and active community, which means there are plenty of resources available for learning and troubleshooting.

Python is a versatile language that can be used for a wide range of tasks, from web development to data analysis to artificial intelligence. It is also used in many scientific and technical fields, such as data science, machine learning, and scientific computing.

In this section, we will cover the basics of Python programming, including variables, data types, control structures, functions, and modules. We will also explore some advanced topics, such as object-oriented programming and error handling.

## Sample Python Code

```python
# This is a simple Python program that prints "Hello, World!"
print("Hello, World!")
```

### Variables and Data Types
Variables are used to store values that can be used later in the program. In Python, variables are defined using the following syntax:

```python
variable_name = value
```

For example, to define a variable named `name` with the value "John", you would use the following syntax:

```python
name = "John"
```

You can then use the variable in your program by referencing it with its name:

```python
print("Hello, " + name + "!")
```

### Control Structures
Control structures are used to control the flow of execution in a program. They allow you to make decisions and repeat blocks of code based on certain conditions.

The most common control structures in Python are:

- `if` statements: Used to execute a block of code if a certain condition is true.
- `for` loops: Used to iterate over a list of values and execute a block of code for each value.
- `while` loops: Used to repeatedly execute a block of code as long as a certain condition is true.
- `break` and `continue` statements: Used to control the flow of execution within loops.

Here is an example of an `if` statement:

```py title="conditional.py"
if age >= 18:
    print("You are an adult")
else:
    print("You are a minor")
```

This script checks if the `age` variable is greater than or equal to 18. If it is, it prints "You are an adult". Otherwise, it prints "You are a minor".

### Functions
Functions are reusable blocks of code that can be called from within a program. They allow you to organize your code into smaller, more manageable pieces.

To define a function, you use the following syntax:

```python
def function_name(parameters):
    # code to be executed
```

For example, here is a function that prints a message:

```python title="hello.py"
def say_hello():
    print("Hello, World!")
```

To call a function, you simply use its name followed by parentheses:

```python
say_hello()
```

This will execute the `say_hello` function and print "Hello, World!".

In addition to defining and calling functions, you can also pass arguments to functions. Arguments are values that are passed to a function when it is called. They allow you to customize the behavior of a function based on the values passed to it.

Here is an example of a function that takes an argument:

```python
def say_hello(name):
    print("Hello, " + name + "!")
```

To call this function, you would pass a value for the `name` argument:

```python
say_hello("John")
```

This will print "Hello, John!".

### Modules
Modules are collections of functions and variables that can be used in a program. They allow you to organize your code into reusable components and make it easier to manage and maintain.

To use a module in your program, you need to import it using the `import` statement. For example, to import the `math` module, you would use the following syntax:

```python
import math
```

Once the module is imported, you can use its functions and variables by prefixing them with the module name. For example, to use the `sqrt` function from the `math` module, you would use the following syntax:

```python
math.sqrt(16)
```

This will calculate the square root of 16 and return 4.

### Error Handling
Error handling is an important aspect of programming, as it allows you to handle unexpected situations and prevent your program from crashing. In Python, you can use the `try` and `except` statements to handle errors.

The `try` statement is used to enclose the code that might raise an error. If an error occurs within the `try` block, the code execution is immediately transferred to the `except` block.

The `except` statement is used to specify the type of error that should be handled. You can also specify additional code to be executed when an error occurs.

Here is an example of using `try` and `except` to handle a `ZeroDivisionError`:

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Error: Division by zero")
```

This script attempts to divide 10 by 0, which will raise a `ZeroDivisionError`. The code execution is transferred to the `except` block, which prints "Error: Division by zero".

In addition to handling specific types of errors, you can also use a generic `except` block to handle any type of error. Here is an example:

```python
try:
    result = 10 / 0
except:
    print("An error occurred")
```

This script attempts to divide 10 by 0, which will raise a `ZeroDivisionError`. The code execution is transferred to the `except` block, which prints "An error occurred".









## Recommended YouTube Videos:

<iframe width="764" height="443" src="https://www.youtube.com/embed/BO6LjtEOGZw" title="Learn Python With 5 Projects - From Beginner to Advanced" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>