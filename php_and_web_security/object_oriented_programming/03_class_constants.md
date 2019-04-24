# Class Constants
- constant values defined for a class
- do not use `$` symbol for declaration or use
- default visibility: *public*
- value must be a constant expression (not a variable, property, or a function call)
- can also be defined for *interfaces*
- allocated once per class (not for each instance)

>= PHP 5.3.0:
- possible to reference the class using a variable
- value cannot be a keyword (*self*, *parent*, and *static*)

**Example #1 Defining and using a constant**

```php
<?php
class MyClass
{
    const CONSTANT = 'constant value';

    function showConstant() {
        echo  self::CONSTANT . "\n";
    }
}

echo MyClass::CONSTANT . "\n";

$classname = "MyClass";
echo $classname::CONSTANT . "\n"; // As of PHP 5.3.0

$class = new MyClass();
$class->showConstant();

echo $class::CONSTANT."\n"; // As of PHP 5.3.0
?>
```

>= PHP 5.3.0:
- support added for initializing constants with Heredo and Nowdoc syntax

**Example #2 Static data example**

```php
<?php
class foo {
    // As of PHP 5.3.0
    const BAR = <<<'EOT'
bar
EOT;
    // As of PHP 5.3.0
    const BAZ = <<<EOT
baz
EOT;
}
?>
```

>= PHP 5.5.0:
- special **::class** constant is available
- allows for FQN resolution at compile time

**Example #3 Namespaced ::class example**

```php
<?php
namespace foo {
    class bar {
    }

    echo bar::class; // foo\bar
}
?>
```

## Constant expression
- support added in PHP 5.6.0
- possible to provide a scalar expression involving
  - numberic and string literals, and/or
  - constants in context of a class constant

**Example #4 Constant expression example**

```php
<?php
const ONE = 1;

class foo {
    // As of PHP 5.6.0
    const TWO = ONE * 2;
    const THREE = ONE + self::TWO;
    const SENTENCE = 'The value of THREE is '.self::THREE;
}
?>
```

## Visibility modifiers
- are allowed for class constants as of PHP 7.1.0

**Example #5 Class constant visibility modifiers**

```php
<?php
class Foo {
    // As of PHP 7.1.0
    public const BAR = 'bar';
    private const BAZ = 'baz';
}
echo Foo::BAR, PHP_EOL;
echo Foo::BAZ, PHP_EOL;
?>
```

Output in PHP 7.1:

```
bar

Fatal error: Uncaught Error: Cannot access private const Foo::BAZ in ...
```
