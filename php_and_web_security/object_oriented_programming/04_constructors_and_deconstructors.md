# Constructors and Destructors

## Constructor

```php
__construct ([ mixed $args = "" [, $... ]] ) : void
```

PHP5
- allows declaration of constructor methods for classes
- a constructor method is called on each newly-created object
- suitable for initialization

**Note**
- parent constructors are not called implicitly if the child class defines a constructor
- call to **parent::__construct()** is required with the child constructor
- if no constructor in child class
  - constructor is inherited from the parent class (if not declared as private)
  
**Example #1 using new unified constructors**

```php
<?php
class BaseClass {
    function __construct() {
        print "In BaseClass constructor\n";
    }
}

class SubClass extends BaseClass {
    function __construct() {
        parent::__construct();
        print "In SubClass constructor\n";
    }
}

class OtherSubClass extends BaseClass {
    // inherits BaseClass's constructor
}

// In BaseClass constructor
$obj = new BaseClass();

// In BaseClass constructor
// In SubClass constructor
$obj = new SubClass();

// In BaseClass constructor
$obj = new OtherSubClass();
?>
```

### Old-style constructors
- For backwards compatibility with PHP 3 and 4:
  - if __construct() is not found for a given class
  - old-style constructor function (by the name of the class) is searched for
  - compatibility issue if the class had a method __construct for different semantics
- deprecated in PHP 7.0 and will be removed in a future version
- always use __construct in new code
- as of PHP 5.3.3, old-style constructors in a namespaced class are not longer treated as constructor
  - does not affect non-namespaced classes

### Constructor override
- can have different parameters than the parent __construct() method

**Example #2 Constructors in namespaced classes**

```php
<?php
namespace Foo;
class Bar {
    public function Bar() {
        // treated as constructor in PHP 5.3.0-5.3.2
        // treated as regular method as of PHP 5.3.3
    }
}
?>
```

## Destructor

```php
__destruct ( void ) : void
```

- introduced in PHP 5
- called as soon as there are no other references to a particular object
- also called in any order during the shutdown sequence

**Example #3 Destructor Example**

```php
<?php

class MyDestructableClass 
{
    function __construct() {
        print "In constructor\n";
    }

    function __destruct() {
        print "Destroying " . __CLASS__ . "\n";
    }
}

$obj = new MyDestructableClass();
```

### Destructor override
- like constructors
- parent destructors will not be called implicity
- explicit call to **parent::__destruct()** is required in the destructor body
- can be inherited from the parent's destructor if not declared in the child class
- also called is script execution is stopped with exit()
- calling exit() in destructor will halt the remaining shutdown routines

**Notes**
- HTTP headers already sent when destructors are called during script shutdown
- the working directory in script shutdown phase can be different with some SAPIs (e.g. Apache)
- attempting to throw an exception from a destructor (during script termination) causes a fatal error
