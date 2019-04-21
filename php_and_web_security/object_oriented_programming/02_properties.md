# Properties
- are class member variables
- also referred as *attributes* or *fields*
- defined by using visibility keywords *public*, *protected*, or *private*
- may include an initialization
  - must be a constant value (must not be evaluated at run-time)

PHP 5: 
  - still accept the use of the keyword *var* (instead of visibility keywords)
  - will treat the property as *public*
  - for backward compatibility with PHP 4
  - PHP 5.0 to 5.1.3: use of var issues considered deprecated and issues an E_STRICT warning
  - > PHP 5.1.3: no warning
  
Accessing non-static properties
- by using -> (Object Operator)
  - `$this->property`

Accessing static properties
- by using the :: (Double Colon)
  - `self::$property`
  
Pseudo-variable `$this`
- available inside any class method when called within an object context
- is a reference to the calling object

**Example #1 property declarations**

```php
<?php
class SimpleClass
{
   // valid as of PHP 5.6.0:
   public $var1 = 'hello ' . 'world';
   // valid as of PHP 5.3.0:
   public $var2 = <<<EOD
hello world
EOD;
   // valid as of PHP 5.6.0:
   public $var3 = 1+2;
   // invalid property declarations:
   public $var4 = self::myStaticMethod();
   public $var5 = $myVar;

   // valid property declarations:
   public $var6 = MY_CONSTANT;
   public $var7 = array(true, false);

   // valid as of PHP 5.3.0:
   public $var8 = <<<'EOD'
hello world
EOD;
}
?>
```

PHP 5.3.0
- heredocs and nowdocs can be used in any static data context, including property declarations

**Example #2 Example of using a nowdoc to initialize a property**

```php
<?php
class foo {
   // As of PHP 5.3.0
   public $bar = <<<'EOT'
bar
EOT;
   public $baz = <<<EOT
baz
EOT;
}
?>
```

# Static Keyword
- static declaration of properties or methods
  - makes them accessible without needing an instantiation of the class
- static property
  - *cannot* be accessed with an instantiated class object
- static method
  - *can* be accessed with an instantiated class object
- no visibility declaration
  - property or method will be treated as public
  - for compatibility with PHP 4
  
## Static methods
- callable without an instance of the object created
- `$this` is not available inside the static method

### Calling non-static methods statically
- PHP 5: 
  - generates an E_STRICT level warning
- PHP 7: 
  - deprecated
  - generates an E_DEPRECATED warning
  - may be removed in the future
  
**Example #1 Static method example**

```php
<?php
class Foo {
    public static function aStaticMethod() {
        // ...
    }
}

Foo::aStaticMethod();
$classname = 'Foo';
$classname::aStaticMethod(); // As of PHP 5.3.0
?>
```

## Static properties
- cannot be accessed through the object using the arrow operator (->)

Before PHP 5.6
- may only be initialized using a literal or constant
  - expressions are not allowed

PHP 5.6 and later
- the same rules apply as const expressions:
  - some limited expressions are possible
  - provided they can be evaluated at compile time

*Note*  
As of PHP 5.3.0:
- possible to reference the class using a variable
- the variable's value cannot be a keyword (e.g. *self*, *parent*, and *static)

**Example #2 Static property example**

```php
<?php
class Foo
{
    public static $my_static = 'foo';

    public function staticValue() {
        return self::$my_static;
    }
}

class Bar extends Foo
{
    public function fooStatic() {
        return parent::$my_static;
    }
}


print Foo::$my_static . "\n";

$foo = new Foo();
print $foo->staticValue() . "\n";
print $foo->my_static . "\n";      // Undefined "Property" my_static 

print $foo::$my_static . "\n";
$classname = 'Foo';
print $classname::$my_static . "\n"; // As of PHP 5.3.0

print Bar::$my_static . "\n";
$bar = new Bar();
print $bar->fooStatic() . "\n";
?>
```
