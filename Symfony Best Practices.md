# Symfony 4.2 Best Practices

## Creating the Project
- Use Composer and Symfony Flex to create and manage Symfony applications
	- composer: package manager to manage dependencies
	- flex: composer plugin to automate some common tasks
- Use the Symfony Skeleton to create new Symfony-based projects
	- minimal and empty project
	- the absolute bare minimum amount of dependencies

### Structuring the Application

	blog/
	├─ bin/
	│	└─ console
	├─ config/
	└─ public/
	│	└─ index.php
	├─ src/
	│	└─ Kernel.php
	├─ var/
	│	├─ cache/
	│	└─ log/
	└─ vendor/

#### Application Bundles
- something that can be reused as a stand-alone piece of software

## Configuration
- recommended to split the application configuration into 3 parts
	* Infrastructure-Related Configuration
		- machine-dependent options (e.g. developement machine, production server)
		- defined as environment variables
		- **.env** and **.env.local** files at root
		- database url
		- mailer url
	* Application-Related Configuration
		- application behaviour related configuration options
		- **config/services.yaml**
		- sender of email notifications
		- may vary from one environment to another
			- **config/services_dev.yaml**
			- **config/services_prod.yaml**

		Constants vs Configuration Options
		- Use constants to define configuration options that rarely change
		- Doctrine entities and repositories cannot access the container parameters
		- Disadvantage: cannot be redefined easily in tests

	* Parameter Naming
		- should be as short as possible
		- should include a common prefix for the entire application (.app)
		- just one or two words to describe the purpose of the parameter (app.contents_dir)
		- it's OK to use dots, underscores, dashes or nothing, but always be consistent


## Organizing Your Business Logic
- business logic or domain logic
- the part of the program that encodes the real-world business rules that determine how data can be created, displayed, stored, and changed
- all the custom code that's not specific to the framework (e.g. routing and controllers)
- good examples of business logic: domain classes, Doctrine entities and regular PHP classes that are used as services
- store all code in the **src/** directory

### Services: Naming and Configuration
- Use **autowiring** to automate the configuration of application services
	- a feature to manage services with minimal configuration
	- reads the type-hints of constructor (or other methods) and automatically passes the correct services to each method
	- can also add service tags to the services needing them

### Service Format: YAML
- default services.yaml configuration: services configured automatically
- Use the YAML format to configure your own services

### Using a Persistence Layer
- Symfony is an HTTP framework
	- generate an HTTP response for each HTTP request
	- doesn't provide a way to talk to a persistence layer
- Doctrine
	- relied on by many Symfony applications
	- not enabled by default in Symfony
	- installed via composer
	- store entities in the **src/Entity/** directory
	- entities: plain PHP objects stored in some "database"
	- support 4 metadata formats: YAML, XML, PHP and annotations
- Data Fixtures
	- not enabled by default in Symfony
	- only for the **dev** and **test** environments
	- recommended to create just *one fixture class* for simplicity
	- to load fixtures:
		```bash
		php bin/console doctrine:fixtures:load
		```

### Coding Standards
- follows the *PSR-1* and *PSR-2* coding standards
- use the PHP-CS-Fixer (command-line utility)


## Controllers
- philosophy: thin controllers and fat models
- controllers should hold just the thin layer of glue-code needed to coordinate the different parts of the application
- should not contain any actual business logic
- should just call to other services, trigger some events if needed and then return a response
- should not contain any actual business logic
- extend the *AbstractController* base controller
- use *annotations* to configure routing, caching and security whenever possible
	- simplifies configuration
	- all the configuration is where it is needed and uses only one format
- aggressively decouple business logic from the framework 
- while aggressively coupling controllers and routing to the framework

### Controller Action Naming
- Don't add the *Action* suffix to the methods of the controller actions

### Routing Configuration
- To load routes defined as annotations in controllers
	```yml
	# config/routes.yaml
	controllers:
		resource: '../src/Controller/' 
		type: annotation
	```
- lots of controllers
	- reorganize them into subdirectories

### Template Configuration
- Don't use the @Template annotation to configure the template used by the controller
	- useful, but involves some magic
	- not recommended: its benefits is not worth the magic
	- used without parameters: difficult to know which template is being rendered

### Fetching Services
- When extending the base *AbstractController* class
	- can't access services directly from the container via *$this->container->get()* or *$this->get()*
	- instead, use dependency injection
- advantages: services can be *private*

### Using the ParamConverter
- Use the ParamConverter trick to automatically query for Doctrine entities when it's simple and convenient
- When using Doctrine
- automatically query for an entity and pass it as an argument to the controller
- use type-hinting to convert an id to a Doctrine entity
	- **{id}** matches the name of the property on the entity
- shows a 404 page if not found
- if id is not used or for more complex logic, query for the entity manually

#### @ParamConverter annotation
- map another routing wildcard to a property of an entity
```php
/**
 * @Route("/comment/{postSlug}/new", name="comment_new")
 * @ParamConverter("post", options={"mapping"={"postSlug"="slug"}})
 */
public function new(Request $request, Post $post) {
	// ...
}
```
- this shortcut is great for most situations
- nothing wrong with querying for entities directly (complicated ParamConverter)

### Pre and Post Hooks
- need to execute some code before or after the execution of controllers
	- use EventDispatcher component to set up before and after filters

## Templates

### Twig
- default templating format in Symfony
- largest community support of all non-PHP template engines
- used in high profile projects such as Drupal 8

### Template Locations
- Store the application templates in the *templates/* directory at the root of the project
	- single location: simplifies the work of designers
	- simplifies the notation used when referring to templates
- Use lowercased snake_case for directory and template names
	- aligns with Twig best practices
	- variables and template names use lowercased snake_case too
- Use a prefixed underscore for partial templates in template names
	- reuse template code using the include function to avoid redundant code
	- to determine those partials in the filesystem

### Twig Extensions
- Define your Twig extensions in the *src/Twig/* directory
	- will be automatically detected and configured
- extends AbstractExtension

## Forms

### Building Forms
- Define your forms as PHP classes
	- for organisation and reuse
	- extends AbstractType
- Put the form type classes in the *App\Form* namespace
	- unless you use other custom form classes like data transformers
	- createFrom(): to use the Form class

### Form Button Configuration
- Form classes should try to be agnostic to *where* they will be used
	- easier to re-use later
- Add buttons in the **templates**, not in the form classes or the controllers
	- e.g. a form create for creating blog posts may be reused for editing
		- different button labels would be needed
	- put all the view-related things in the view layer

### Validation
- Do not define your validation constraints in the form but on the object the form is mapped to
	- e.g. to validate that the title of the post edited with a form is not blank, add the validation constraint in the *Post* object

### Rendering the Form
- lot of ways to render your form
	- rendering the entire form in one line
	- rendering each part of each field independently
- the best way depends on how much customization is needed

### Handling Form Submits
- use a single action for both rendering the form and handling the form submit

## Internationalisation
- adapt the applications and their contents to the specific region or language of the users
- an opt-in feature that needs to be installed before using it
	- composer require symfony/translation

### Translation Source File Location
- Store the translation files in the **translations/** directory at the root of the project
- easier for translators to find all translations at one central location

### Translation Source File Format
- supports lots of different translation formats: PHP, Qt, .po, .mo, JSON, CSV, INI, etc
- Use the XLIFF format for your translation files
	- only XLIFF and gettext have broad support in the tools used by professional translators
	- XML-based: can validate XLIFF file contents
	- supports notes in XLIFF files

### Translation Keys
- Always use keys for translations instead of content strings
	- simplifies the management of the translation files
		- change the original contents without having to update all of the translation files
	- Keys should always describe their purpose and not their location
		- e.g. label for Username field of a form: **label.username** (not edit_form.label.username)

## Security

### Authentication and Firewalls (i.e. Getting the User's Credentials)
- authentication is configured in security.yaml
- primarily under the firewalls key
- recommended to have only one firewall entry with the anonymous key enabled
	- unless two legitimately different authentication systems and users is needed
	- most applications: one authentication system and one set of users
		- only *one* firewall entry is needed
		- use the **anonymous** key under the firewall
		- login for different sections of the site: use the **access_control** area
- Use the bcrypt encoder for hashing your users' passwords
	- sufficient for most applications
	- advantages:
		- inclusion of a salt value to protect against rainbow table attacks
		- adaptive nature, which allows to make it slower to remain resistant to brute-force search attacks
- PHP7.2 + libsodium extensions installed: use Argon2i as recommended by industry standards

### Authorization (i.e. Denying Access)
- several ways to enforce authorization
	- access_control configuration in *security.yaml*
	- @Security annotation
	- using isGranted on the **security.authorization_checker** service
- For protecting broad URL patterns, use access_control;
- Whenever possible, use the @Security annotation;
- Check security directly on the security.authorization_checker service whenever you have a more complex
situation.
- Define a custom security voter to implement fine-grained restrictions

### The @Security Annotation
- For controlling access on a controller-by-controller basis
	- e.g. @Security("is_granted('ROLE_ADMIN')")

#### Using Expressions for Complex Security Restrictions
- for security logic which is a little bit more complex:
	- use an *expression* inside @Security
	- e.g. @Security("user.getEmail() == post.getAuthorEmail()")

### Checking Permissions without @Security
- do the security check in PHP

### Security Voters
- used if the security logic is complex and can't be centralized into a method like isAuthor()
- much easier than ACLs and flexible
- create a voter class (extends Voter)

## Web Assets
- CSS, JavaScript and image files
- Store your assets in the *assets/* directory at the root of the project
- Use Webpack Encore to compile, combine and minimize web assets

## Tests
- these best practices focus solely on unit and functional tests
- Unit testing allows you to test the input and output of specific functions
- Functional testing allows you to command a "browser"
	- browse to pages on your site, click links, fill out forms
	- assert that you see certain things on the page

### Unit Tests
- used to test your "business logic"
- should live in classes that are independent of Symfony
- most popular tools are PHPUnit and PHPSpec

### Functional Tests
- good functional tests can be tough
- Don't skip the functional tests
- define some simple functional tests
	- quickly spot any big errors before they are deployed
- Define a functional test that at least checks if the application's pages are successfully loading
- smoke testing: preliminary testing to reveal simple failures severe enough to reject a prospective software release

#### Hardcode URLs in a Functional Test
- Hardcode the URLs used in the functional tests instead of using the URL generator
- If a developer mistakenly changes the path of the blog_archives route, the test will still pass, but the original (old) URL won't work!
	- bookmarks for that URL will be broken
	- lose any search engine page ranking

### Testing JavaScript Functionality
- the built-in functional testing client can't be used to test any JavaScript behavior
- consider using the Mink library from within PHPUnit
- for a heavy JavaScript front-end, conside using pure JavaScript-based testing tools
- Consider using the HautelookAliceBundle
	- to generate real-looking data for your test fixtures using Faker and Alice