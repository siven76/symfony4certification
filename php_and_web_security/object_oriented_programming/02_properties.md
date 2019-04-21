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
  - cannot be accessed with an instantiated class object
  - a static method can
