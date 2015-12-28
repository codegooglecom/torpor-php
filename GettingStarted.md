# Installation #
After downloading one of the [distribution packages](http://code.google.com/p/torpor-php/downloads/list), depending on whether it was Complete or Code Only, the relevant PHP files will be in either a `Torpor` folder or `Torpor/src`.

These source files should be placed so as to be accessible to the PHP include path (or the path modified to incorporate the installation directory).  The main `Torpor.php` file will affect an inclusion of necessary supporting files (in the event that `__autoload()` is not in play) without any path qualifications.

It is recommended to run the provided `test.php` (again, located in either `Torpor` or `Torpor/src/test/` depending on the package) in your target environment to confirm successful installation, dependencies, and operation.  Be advised that command line and web server invocations _frequently differ_ in the available environments and settings and may produce different or unexpected results such as failure to include proper files and locate dependent packages.  The successful execution should be confirmed in the same conditions you expect to use the package.

# Configuration #
All configuration is performed via XML, including optional settings, data engine access, and descriptions of the data objects themselves.  This XML must be prepared according to the specifications outlined in the **[Configuration XML](Reference#Configuration_XML.md)** section of this documentation and will be validated against a schema file prior to loading (included as `TorporConfig.xsd` in all packages).  It is recommended to test this validation manually while making changes as well in order to ensure successful loading (which will separate syntax errors from logic errors, making debugging easier).  The included `validateXML.php` script is provided for this purpose.

Torpor will look in the PHP include path for the file `TorporConfig.xml` by default, which can be overridden by passing configuration details as an argument to the `Torpor::initialize()` method (more on that below).

The easiest way to establish the  configuration file is by using one of the data repository extraction scripts (available only in the more complete packages, under `Torpor/tools`), which will to the best of its abilities describe the layout of your repository in compatible XML.  In many cases the advanced settings will _not_ be available, depending on whether the target repository supports foreign constraints or references and their introspection (MySQL MyISAM type tables, for example).  Thus it will be required to manually review the generated configuration and insert those relationships.  Same goes for indeterminate data types, or special extensions or settings.

For brevity, unless otherwise specified when running the extraction script, default options are omitted.  Only those elements or attributes which modify the defaults will be output, resulting in smaller and more readable XML.  This is the recommended format for maintainability in production environments, but does require a better familiarity with the [configuration specification](Reference#Configuration_XML.md).  During development it can be useful to have both the compact and verbose versions available in order to explicitly review or refer to all of the internals.

This XML is provided to the `initialize()` method of a Torpor instance, and can be passed either as a string containing the XML, a file pointer, file name, or URI which will attempt to be read in their turn.  For the best performance it is recommended to use file based access rather than reading from URIs; depending on the `<TimeToLive/>` setting for the configuration data there may be as many as 1 configuration URI request for every initialization of a Torpor instance and may degrade performance.

Every data object (be it `Grid` or `Column`) may have 2 names configured - one by which it is known to the repository, and the other by which it is referenced from within Torpor.  The latter may be omitted, and will fall back to the repository name (suitably sanitized for use as a PHP object/function name).  The reason for this distinction is to be able to refer to a plural-named `"Users"` repository in the single of `"User"` to avoid confusion:

```
$userObject = $torpor->newUser();
$userSet    = $torpor->newUserSet();
// as opposed to
$userObject = $torpor->newUsers()
$userSet    = $torpor->newUsersSet();
// etc.
```

Any alias name may be used and need not be related to the original, and this is the name by which it will be known throughout the Torpor instance (including as related objects, within sets, etc.).  More information on names and aliases (including multiple references between identical grids in different contexts) can be found in the **Names and Aliases** section of the **[Reference - Configuration XML](Reference#Configuration_XML.md)** page.

A word about security: Torpor supports encryption for repository access credentials in order to make the files safe for distribution without exposing pertinent details, and all extraction scripts output these encrypted by default.  However, since Torpor is also open source, it is possible to extract the routines required for maintaining the encryption and gain access to the contents.  The values are stored encrypted in memory insofar as it's possible to do so, but there are points during which those values must be decrypted for communication to the data engine drivers, and may be acquired either from the resulting resource objects themselves or by dumping memory contents. The most secure installation practices should include different credentials between development/staging/production environments, read access constraints on configuration files, or remote access controls (retrieving either the entire configuration or specific values via URI - while keeping performance in mind, of course) to limit distribution of sensitive data.

## Hierarchy ##
Some configuration settings work in a hierarchical fashion, inherited by all descendants unless overridden.  This is used for specifying different class prototypes (other than `Grid` and `Column`) for Grids, Columns, and Column data types.  For example, each Grid may have its own `class` attribute which specifies the type of object it should be instantiated as.  However, if no such attribute is provided and the global `<GridClass/>` option is set, the `<GridClass/>` value will be used.

For Columns this is slightly more complex, as overrides exist at several points.  In order of authority, this is checked first at the individual `<Column/>` definition, the containing `<Grid/>`'s `<DataTypeColumn/>` mapping and `<Columns[@class]>` attribute, the global `<DataTypeColumn>` mapping, and lastly the `<ColumnClass/>`.  This makes it possible to handle columns individually, as specific data types of a `<Grid/>` (or simply as members of that `<Grid/>`), as a designated data type, or by virtue of being a Column in this Torpor instance at all.  This flexibility makes extension of the base classes extremely powerful and easy to target in scope.

The special keyword `"none"` may be used at any point in the class override hierarchy to opt out of the inheritance (though not entirely - only up to the global `<GridClass/>` or `<ColumnClass/>` designation; if these are provided, `"none"` will result in the corresponding global option rather than the Torpor default of `Grid` and `Column` respectively).  This opt-out is all or nothing, rather than simply moving it up one in the inheritance tree.  `"none"` may also be used as the definition in the hierarchy, opting its descendants out by default (which they may then again override, but only by specifying the class name directly - XPath methods of inheritance which could opt them back in to to the hierarchy at a specific level are not currently supported).

More information on this can be found on the **[Extending Base Classes](ExtendingBaseClasses.md)** page.

# Conventions #
(Coming soon)
## Of this Documentation ##
## Of Torpor ##
Singleton invocation
Compound method names:
```
<verb>[<adjective>]<noun>[<qualifier>]
new<Grid>
new<Grid>From<Grid>
new<Grid>Set
```
etc.


Previous: [Overview](Overview.md)

Next: [Illustrated Features](IllustratedFeatures.md)