# 01 - The Basics

## Class

Definition of a class:
- begin with keyword *class*
- class name
	- not a PHP reserved word
	- starts with letter or underscore
	- followed by any number of letters, numbers, or underscores
	- regex: *^[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*$*
- curly braces
	- definition of properties (constants, variables) and methods (functions)

**Example #1 Simple Class definition**

```php
<?php
class SimpleClass
{
    // property declaration
    public $var = 'a default value';

    // method declaration
    public function displayVar() {
        echo $this->var;
    }
}
?>
```

**`$this`**
- is a pseudo-variable
- available when a method is called from within an object
- reference to the calling object
	- the object to which the method belongs, or
	- if the method is called statically, from the context of the secondary object
	- PHP 7.0.0: 
		- Calling a non-static method statically from an incompatible context results in $this being undefined inside the method
		- As of PHP 7.0.0 calling a non-static method statically has been generally deprecated (even if called from a compatible context)
	- PHP 5.6.0:
		- Calling a non-static method statically from an incompatible context has been deprecated
	- < PHP 5.6.0:
		- such calls already triggered a strict notice

**Example #2 Some examples of the $this pseudo-variable**
Assuming that error_reporting is disabled, otherwise deprecated and strict notices would be triggered.

```php
<?php
class A
{
    function foo()
    {
        if (isset($this)) {
            echo '$this is defined (';
            echo get_class($this);
            echo ")\n";
        } else {
            echo "\$this is not defined.\n";
        }
    }
}

class B
{
    function bar()
    {
        A::foo();
    }
}

$a = new A();
$a->foo();

A::foo();

$b = new B();
$b->bar();

B::bar();
?>
```

Output in PHP 5:

```
$this is defined (A)
$this is not defined.
$this is defined (B)
$this is not defined.
```

Output in PHP 7:

```
$this is defined (A)
$this is not defined.
$this is not defined.
$this is not defined.
```

## new
- keyword to create an instance of a class
- creates an object, unless the constructor throws an exception
- classes should already be defined
- name of a class can be in a string or a fully qualified name (using namespace)
- parentheses may be omitted if no arguments are passed to the constructor

**Example #3 Creating an instance**

```php
<?php
$instance = new SimpleClass();

// This can also be done with a variable:
$className = 'SimpleClass';
$instance = new $className(); // new SimpleClass()
?>
```

In class context, *new self* and *new parent* create a new object.

Assign an object to a variable
- new variable will access the same object that was assigned
- same behaviour as when intances are passed to a function

To create a copy of an object, [clone](https://www.php.net/manual/en/language.oop5.cloning.php) it.

**Example #4 Object Assignment**

```php
<?php

$instance = new SimpleClass();

$assigned   =  $instance;
$reference  =& $instance;

$instance->var = '$assigned will have this value';

$instance = null; // $instance and $reference become null

var_dump($instance);
var_dump($reference);
var_dump($assigned);
?>
```

Output:

```
NULL
NULL
object(SimpleClass)#1 (1) {
   ["var"]=>
     string(30) "$assigned will have this value"
}
```

PHP 5.3.0: New ways to create intances of an object

**Example #5 Creating new objects**

```php
<?php
class Test
{
    static public function getNew()
    {
        return new static;
    }
}

class Child extends Test
{}

$obj1 = new Test();
$obj2 = new $obj1;
var_dump($obj1 !== $obj2);

$obj3 = Test::getNew();
var_dump($obj3 instanceof Test);

$obj4 = Child::getNew();
var_dump($obj4 instanceof Child);
?>
```

Output:

```
bool(true)
bool(true)
bool(true)
```

PHP 5.4.0: possibility to access a member of a new created object in a single expression

**Example #6 Access member of newly created object**

```php
<?php
echo (new DateTime())->format('Y');
?>
```

Output: will be something like below

```
2019
```

## Properties and methods
- properties and methods live in separate "namespaces"
- both a property and a method can have the same name
- same notation for calling both a property and a method (->)
- usage will depend on context

**Example #7 Property access vs. method call**

```php
<?php
class Foo
{
    public $bar = 'property';
    
    public function bar() {
        return 'method';
    }
}

$obj = new Foo();
echo $obj->bar, PHP_EOL, $obj->bar(), PHP_EOL;
```

Output: 

```
property
method
```

Calling anonymous function assigned to a property is not directly possible. Instead, the propery has to be assigned to a variable first.

PHP 7.0.0: possible to call such a property directly by enclosing it in parentheses

**Example #8 Calling an anonymous function stored in a property**

```php
<?php
class Foo
{
    public $bar;
    
    public function __construct() {
        $this->bar = function() {
            return 42;
        };
    }
}

$obj = new Foo();

// as of PHP 5.3.0:
$func = $obj->bar;
echo $func(), PHP_EOL;

// alternatively, as of PHP 7.0.0:
echo ($obj->bar)(), PHP_EOL;
```

Output: 

```
42
```

## extends
- to inherit the methods and properties of another class
- keyword *extends* in the class declaration
- **NOT** possible to extend multiple classes
- a class can only inherit from one base class
- inherited methods and properties can be overridden 
	- redeclare them with the same name defined in the parent class
	- the parameter signature should remain the same, otherwise E_STRICT level error
	- constructor can be overridden with different parameters
	- final methods may not be overridden
- parent:: is used to access the original methods or static properties

**Example #9 Simple Class Inheritance**

```php
<?php
class ExtendClass extends SimpleClass
{
    // Redefine the parent method
    function displayVar()
    {
        echo "Extending class\n";
        parent::displayVar();
    }
}

$extended = new ExtendClass();
$extended->displayVar();
?>
```

Output:

```
Extending class
a default value
```

## ::class
- since PHP 5.5
- *class* keyword also used for class name resolution
- get a string containing the fully qualified name of the *ClassName* by using *ClassName::class*
- particularly useful for namespaced classes

**Example #10 Class name resolution**

```php
<?php
namespace NS {
    class ClassName {
    }
    
    echo ClassName::class;
}
?>
```

Output:

```
NS\ClassName
```

