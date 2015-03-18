# jaxb2-rich-contract-plugin

## A collection of JAXB / XJC plugins to generate advanced contract scenarios from XSD

Current Version: 1.6.6

This module is a collection of several plugins for the JAXB2 (Java API for XML binding) "XSD to Java Compiler" (XJC).
These plugins are intended to add support for additional contracts to the classes generated by XJC. Currently, there are 7 plugin classes:

1. **[fluent-builder][1]**: Generates a builder class for every class generated. Builders are implemented as inner classes, static methods are provided for a fluent builder pattern in the form `MyClass.builder().withPropertyA(...).withPropertyB(...).build()`. Builders also contain "copy..." methods to initialize the builder from another instance. Partial copying is also supported in the same way as in **clone**. This is particularly useful together with `-Ximmutable` (see above), but not usable together with `-Xconstrained-properties` (see below).
2. **[immutable][2]**: Will make generated classes immutable. Only makes sense together with "fluent-builder" plugin (see below), or any other builder or initialisation facility, like the well-known "value-constructor" plugin.
3. **[group-contract][3]**: When using `<attributeGroup>` or `<group>` elements in an XSD, they are transformed as interface definitions, and any complexTypes using the groups will be generated as classes implementing this interface.
4. **[clone][4]**: Will generate a simple deep "clone" method for the generated classes based on the heuristic that it only makes sense to traverse further down in the cloned object tree for members of types that are actually cloenable themselves.
5. **[copy][5]**: Similar to "clone", will generate a simple deep "createCopy" method. The java API contract for the `java.lang.Cloneable` interface and the rules for overriding `Object.clone()` are defective by design. So the "copy" plugin uses its own API to realize the desired behavior. Also can generate a "partial createCopy" method, that takes a `PropertyTree` object which represents an include/exclude rule for nodes in the object tree to clone. Excluded nodes will not be cloned and left alone. Optionally, corresponding copy constructors can also be generated.
6. **[constrained-properties][6]**: Will generate a complexTypes element members as bound and/or constrained properties as per the JavaBeans spec.
7. **[meta][7]**: Generates a nested class representing a static metamodel of the generated classes. In the "enhanced" version, this contains information about the type and the XSD element from which the property was generated, in "simple" mode, there are only constants for the property names.




### How to get it

[Full documentation on GitHub](http://mklemm.github.io/jaxb2-rich-contract-plugin)

[Get the source on GitHub](https://github.com/mklemm/jaxb2-rich-contract-plugin)

[Download this project as a .zip file](https://github.com/mklemm/jaxb2-rich-contract-plugin/zipball/master)

[Download this project as a tar.gz file](https://github.com/mklemm/jaxb2-rich-contract-plugin/tarball/master)

Get it with Maven (Now hosted on maven central):

``` xml
    <groupId>net.codesup.util</groupId>
    <artifactId>jaxb2-rich-contract-plugin</artifactId>
    <version>1.6.6</version>
```

###  Version History

* **1.0.0**: Initial Version
* **1.0.1**: Added constrained-property plugin
* **1.0.2**: Added partial clone method generation
* **1.0.3**: Improvements in partial clone
* **1.0.4**: Added fluent builder and immutable plugins
* **1.0.5**: Added chainable fluent builder support
* **1.1.0**: New: `-Ximmutable`, Copy constructor support, fluent-builder copy from instance support, general fixes. Removed option to generate fluent builders without chained builder support.
* **1.1.1**: New: Type-safe selector support for partial clone/copy logic.
* **1.1.2**: Big fixes in selector logic
* **1.1.3**: Minor bug fixes in fluent-builder
* **1.1.4**: Fixed an error in fluent-builder where an initialization method wasn't properly overridden in derived builder classes, leading to the wrong builder type being returned when using chained sub-builders.
* **1.1.5**: Fixed error in Release Build process
* **1.1.6**: Fixed bug in group-contract plugin: Property names customised via binding info were generated incorrectly in the interface definitions.
* **1.2.0**: Major changes to the logic of partial cloning. The partial clone `PropertyTree` pattern replaces the previous `PropertyPath`, which had pretty unclear semantics. The new `PropertyTree` builders now just create a property tree, and on invocation of the "clone()" or "copyOf()" methods or the copy constructor, it is decided by an additional parameter whether the property tree should be considered an exclusion or an inclusion pattern. Additionally, the group-interface plugin has been modified to create interfaces also for the fluent builders, if the fluent-builder plugin is activated.
* **1.2.3**: Added "Copyable" interface and "createCopy" method which does the same thing as the "clone()" method, but doesn't suffer from the defective-by-design java.lang.Cloneable contract. It is planned to als add a "copyFrom" method to copy the state of another object into an existing object.
* **1.3.1**: Made fluent-builder plugin work in an "episode" (modular generation and compilation) context by also integrating compiled classes on the XJC classpath in the search for base and property classes.
* **1.3.6**: Also made group-interface work in an "episode" context, and fixed bug where empty interfaces were created if no implementation class for them could be found in the current module.
* **1.4.0**: group-interface is using its own episode file to maintain relationships to definitions in upstream modules. Command-line options for a specific plugin must now be given immediately after the plugin activation option ("-X..."). This way, name conflicts between plugin options are avoided. Static source files are generated via the JCodeModel.addResourceFile API, so a bug where the source files ended up in the root of the project tree should be fixed now. group-interface and fluent-builder now are working together more reliably.
* **1.5.0**: Added new Plugin "-Xmeta" to generate an inner class containing static meta information about the properties of a class. Internally, a common base class for plugins was extracted to help in command-line parsing and command-line documentation.
* **1.5.1**: Major updates to documentation, improvements to `-Xmeta` to expose static information about XSD definitions of properties.
* **1.5.2**:
    * Now hosted on Central
    * More updates to documentation
    * Customization of names of many generated source elements
    * Improved handling of CloneNotSupportedException in clone, copy, and fluent-builder plugins
* **1.5.3**:
	* Added maven "site" hosted on github pages
	* Improvements to javadoc comment generation
	* Improvements to documentation
* **1.5.4**:
	* Updates to generated documentation
	* changed groupId to net.codesup.util
* **1.5.5**:
	* immutable plugin: Added command line option to specify access level of default constructor
* **1.5.6**:
	* Added instance "newCopyBuilder" method generation
* **1.5.7**:
	* Fixed bug where partial copying in a builder didn't work
* **1.5.8**:
	* Bugfix: When generating builder interface, not all superinterfaces were declared in the "extends" clause.
	* Added command-line option to configure whether methods that could cause type clashes should be ommitted.
	* Added command-line option to configure suffix for instance fields of a builder holding sub-builders
* **1.5.9**:
	* fluent-builder: Added methods to initialize collection properties with an "java.util.Iterable" instance instead of collection.
	* fluent-builder: Made "add..." and "with..." methods for collection properties fall through if they are given a NULL arg for the item collection.
* **1.6.0**:
	* immutable: You can now have a "modifier" class generated that provides methods to modify the state of an otherwise immutable object anyway.
* **1.6.1**:
	* minor bugfixes
* **1.6.2**:
	* immutable: Introduced alternate collection type when generating immutable collection properties
	* made more names of generated items configurable
* **1.6.3**:
	* Added "fake" mode for immutable, only for test purposes
* **1.6.4**:
	* group-contract: when generating methods that could conflict with each other in cases where two interfaces are
	used at the same time as generic type parameter boundaries, an extra level of interfaces is declared so that the
	potentially problematic methods are in their own interface definition which can be omitted in your code if desired.
	* Issue #16 resolved.
	* clone: Resolved an issue with generating the "throws CloneNotSupportedException" declarations. Now they are only generated
	if actually needed.
	* Put "modifier" generation into separate plugin class.
* **1.6.5**:
	* fluent-builder: Changed logic of static "copyOf" method to allow widening type conversion of input parameter.
* **1.6.6**:
	* fluent-builder: Changed type parameter names to make name conflicts less likely
	




###  Usage

####  General

jaxb2-rich-contract-plugin is a plugin to the XJC "XML to Java compiler" shipped with the reference implementation of JAXB, included in all JDKs since 1.6. It is targeted on version 2.2 of the JAXB API. In order to make it work, you need to:

* Add the jar file to the classpath of XJC
* Add the JAXB 2.2 XJC API to the classpath of XJC, if your environment is running by default under JAXB 2.1 or lower.
* Add the corresponding activating command-line option to XJC's invocation, see below for details of each of the plugins
* Each of the plugins, except "-Ximmutable", has one or more sub-options to fine-control its behavior. These sub-option must be given after the corresponding main "-X..." activation option, to avoid naming conflicts. Names of sub-options can be given dash-separated or in camelCase.
* The "immutable" and "constrained-properties" plugins are mutually exclusive. An object cannot be both immutable and send change notifications.

####  From Maven

You should add "maven-jaxb2-plugin" to your `<build>` configuration. Then add "jaxb2-rich-contract-plugin" as an XJC plugin ("plugin for plugin") to the maven plugin declaration. The following example shows all possible options reflecting their default values:

``` xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.jvnet.jaxb2.maven2</groupId>
                <artifactId>maven-jaxb2-plugin</artifactId>
                <version>0.11.0</version>
                <executions>
                    <execution>
                        <id>xsd-generate</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <schemaIncludes>
                        <schemaInclude>**/*.xsd</schemaInclude>
                    </schemaIncludes>
                    <strict>true</strict>
                    <verbose>true</verbose>
                    <extension>true</extension>
                    <removeOldOutput>true</removeOldOutput>
                    <args>
						<arg>-Xfluent-builder</arg>
							<arg>-rootSelectorClassName=Select</arg>
							<arg>-newBuilderMethodName=builder</arg>
							<arg>-newCopyBuilderMethodName=newCopyBuilder</arg>
							<arg>-builderFieldSuffix=_Builder</arg>
							<arg>-generateTools=y</arg>
							<arg>-narrow=n</arg>
							<arg>-copyPartial=y</arg>
							<arg>-selectorClassName=Selector</arg>
							<arg>-builderClassName=Builder</arg>
							<arg>-builderInterfaceName=BuildSupport</arg>
						<arg>-Ximmutable</arg>
							<arg>-constructorAccess=public</arg>
							<arg>-generateModifier=y</arg>
							<arg>-modifierClassName=Modifier</arg>
							<arg>-modifierMethodName=modifier</arg>
						<arg>-Xgroup-contract</arg>
							<arg>-declareSetters=y</arg>
							<arg>-declareBuilderInterface=y</arg>
							<arg>-upstreamEpisodeFile=/META-INF/jaxb-interfaces.episode</arg>
							<arg>-downstreamEpisodeFile=/META-INF/jaxb-interfaces.episode</arg>
							<arg>-omitTypeClash=y</arg>
						<arg>-Xclone</arg>
							<arg>-cloneThrows=y</arg>
						<arg>-Xcopy</arg>
							<arg>-partial=y</arg>
							<arg>-generateTools=y</arg>
							<arg>-constructor=y</arg>
							<arg>-narrow=n</arg>
							<arg>-selectorClassName=Selector</arg>
							<arg>-rootSelectorClassName=Select</arg>
						<arg>-Xconstrained-properties</arg>
							<arg>-constrained=y</arg>
							<arg>-bound=y</arg>
							<arg>-setterThrows=n</arg>
							<arg>-generateTools=y</arg>
						<arg>-Xmeta</arg>
							<arg>-generateTools=y</arg>
							<arg>-extended=n</arg>
							<arg>-camelCase=n</arg>
							<arg>-metaClassName=PropInfo</arg>
                        <arg>...</arg>
                    </args>
                    <plugins>
                        <plugin>
                            <groupId>net.codesup.util</groupId>
                            <artifactId>jaxb2-rich-contract-plugin</artifactId>
                            <version>1.6.6</version>
                        </plugin>
                    </plugins>
                    <dependencies>
                        <!-- Put this in if your default JAXB version is 2.1 or lower,
                        or if &quot;tools.jar&quot; isn't in your classpath -->
                        <dependency>
                            <groupId>org.glassfish.jaxb</groupId>
                            <artifactId>jaxb-runtime</artifactId>
                            <version>2.2.11</version>
                        </dependency>
                        <dependency>
                            <groupId>org.glassfish.jaxb</groupId>
                            <artifactId>jaxb-core</artifactId>
                            <version>2.2.11</version>
                        </dependency>
                        <dependency>
                            <groupId>org.glassfish.jaxb</groupId>
                            <artifactId>jaxb-xjc</artifactId>
                            <version>2.2.11</version>
                        </dependency>
                    </dependencies>
                </configuration>
            </plugin>
        </plugins>
    </build>
```
Note: the `<extension/>` flag must be set to "true" in order to make XJC accept any extensions at all.

Note: jaxb2-rich-contract-plugin implements JAXB and XJC APIs version 2.2. You most likely will have to add the dependencies to these libraries to your classpath effective at XJC runtime. See the `<dependencies>` element above on how to do this.


## fluent-builder
### Motivation
There already is the widely used "fluent-api" plugin for XJC. That, however isn't a real builder pattern since there is no distinction between initialization and state change in fluent-api.

fluent-builder now creates a real "Builder" pattern, implemented as an inner class to the generated classes.

### Function
fluent-builder creates a static inner class for every generated class representing the builder, and a static method on the generated class to create a builder.

Example use in code:

        MyElement newElement = MyElement.builder().withPropertyA(...).withPropertyB(...).addCollectionPropertyA(...).build();

In addition, new instances can be created as copies of existing instances using the builder, with an optional modification by other builder methods:

        MyElement newElement = MyElement.copyOf(oldElement).withPropertyA(...).withPropertyB(...).build();

Or, similar to the java `clone()` method, creating a runtime copy of a reference:

		MyObj myObj = oldObj.newCopyBuilder().with... .build();

The "partial" copy introduced in the "copy" plugin will work here as well:

        PropertyTree selection = MyElement.Select.root().propertyA().propertyAB().build();
        MyElement newElement = MyElement.copyExcept(oldElement, selection).withPropertyA(...).withPropertyB(...).build();
		MyObj myObj = oldObj.newCopyBuilder(selection, PropertyTreeUse.EXCLUDE).with.... .build();

Often, properties of generated classes represent containment or references to generated classes in the same model.
The fluent-builder plugin lets you initialise properties of such a type (and of types declared in upstream modules
via the "episode" feature) - if it isn't an abstract type - by using sub-builders ("chained" builders) in the following
way, given that both A and B are types defined in the XSD model, and A has a property of type B, and B has three
properties of type String, x,y, and z:

        A newA = A.builder().withB().withX("x").withY("y").withZ("z").end().build();

Of course, this plugin is most useful if `immutable` is also activated.


### Limitations
* It generates a large amount of code.
* Note: Shared builder instances are NOT thread-safe by themselves.

### Usage
#### -Xfluent-builder

#### Options

##### -rootSelectorClassName=`<string>` (Select)
Name of the generated nested static "Select" entry point class to be used by client code for the "partial copy" feature. This setting will only have an effect if the "deep-copy-plugin" isn't also active. If it is, the "copy" plugin's settings will take precedence.


##### -newBuilderMethodName=`<string>` (builder)
Name of the generated static method to instantiate a new fluent builder. Can be set to handle naming conflicts.


##### -newCopyBuilderMethodName=`<string>` (newCopyBuilder)
Name of the generated instance method to instantiate a new fluent builder intitialized with a copy of the current instance.


##### -copyToMethodName=`<string>` (copyTo)
Name of the generated "copyTo" method.


##### -builderFieldSuffix=`<string>` (_Builder)
Suffix to append to the field holding the builder, change to  prevent name clashes.


##### -generateTools=`{y|n}` (y)
Generate utility classes as static source code artifacts. If no, the plugin JAR must be in compile- and runtime classpath.


##### -narrow=`{y|n}` (n)
Uses copy constructors for all child nodes in the object tree as long as they are available. This will cause the new instance to be as narrow as possible to the declared types.
Abstract types and types not generated from this XSD-model will always be copied by their "clone()"-method.


##### -copyPartial=`{y|n}` (y)
Generates an additional 'copyOf'-method  that takes a PropertyTree instance to restrict the copy operation to selected nodes in the object tree.


##### -selectorClassName=`<string>` (Selector)
Name of the generated nested "Selector" builder class, used to build up a property tree for partial copy functionality. This setting will only have an effect if the "deep-copy-plugin" isn't also active. If it is, the "copy" plugin's settings will take precedence.


##### -builderClassName=`<string>` (Builder)
Name of the generated nested builder class. Can be set to handle naming conflicts.


##### -builderInterfaceName=`<string>` (BuildSupport)
Name of the generated nested builder interface. Can be set to handle naming conflicts.


## immutable
### Motivation
Generally it is advisable to make your business classes immutable as much as possible, to minimise side effects and allow for functional programming patterns.

### Function
This plugin simply makes all "setXXX" methods "protected", thus preventing API consumers to modify state of instances of generated classes after they have been created. This only makes sense together with another plugin that allows for initialization of the instances, like e.g. the included `fluent-builder` plugin. For collection-valued properties, `-Ximmutable` wraps all collections in a `Collections.unmodifiableCollection`, so collections are also made immutable. Because JAXB serialization has a number of constraints regarding the internals of JAXB serializable objects, it wasn't advisable to just remove the setter methods or replace the collections with unmodifiable collections. So, a bit of additional code will be created that leaves the old "mutable" structure of the class intact as much as is needed for JAXB, but modifies the public interface so objects appear immutable to client code.

### Limitations
* Access level "protected" may not be strict enough to prevent state changes.
* If you activate plugins like "fluent-api" or the like, these plugins may circumvent the protection provided by the `immutable` plugin.

### Usage
#### -Ximmutable

#### Options

##### -fake=`{y|n}` (n)
Do not actually make anything immutable. For test and debug purpose only.


##### -overrideCollectionClass=`<string>` (null)
Modify collection getters to be declared to return a custom type implementing java.lang.Iterable instead of List.


##### -constructorAccess=`<string>` (public)
Generate constructors of an immutable class with the specified access level ("public", "private", "protected", "default"). By specification, JAXB needs a public no-arg constructor for marshalling and unmarshalling objects to an from XML. It turns out, however, that many implementations support protected constructors as well.
This option has been included since it doesn't make sense to construct an empty object which then cannot be modified, But anyway, use with caution.


## modifier
### Motivation
In general, you may wish to implement application logic in a way so that objects are initialized once
and then are immutable.
For traditional programming languages, like Java, for example, this is not always feasible in practice,
because legacy code and libraries have to be used.

With the `modifier` plugin, you can make the public interface of your classes immutable via the `immutable`
plugin, but at the same time provide a handle to modify the state of your objects anyway vi a reference that
needs to be queried explicitly.

This plugin is intended for use while refactoring existing code to a more "functional" and thread-friendly
code base. Eventually, your code should work so this plugin can be deactivated in your XJC configuration.


### Function
This plugin creates an inner class with public setXXX methods, and getXXX methods for collection properties that
return a writable version of the collection the property is implemented by.

If the `group-contract` plugin is also activated, these constructs will also be generated into the interfaces.


### Usage
#### -Xmodifier

#### Options

##### -modifierClassName=`<string>` (Modifier)
Name of the generated inner class that allows to modify the state of generated objects.


##### -modifierMethodName=`<string>` (modifier)
Name of the generated method that allows to instantiate the modifier class.


## group-contract
### Motivation
In most object-oriented programming languages, there are constructs to define a "contract", that concrete implementations of complex
types will implement. In Java, for example, there is the `interface`, in Scala there are "traits", and so on.
The XML Schema Definition Language (XSD) in contrast, has no explicit construct to ensure a complex type meets a
pre-defined contract. There are, however, the `group` and `attributeGroup` elements, that could be considered
a way to achieve just that: A complexType that uses a `<group>` or an `<attributeGroup>` will expose the
properties defined in these group definitions. Looking at it that way, you could say that the `complexType`
"implements" the contract defined by the `group` or `attributeGroup`.



### Function
The group-contract plugin now tries to model that case in the generated source code. For every `group`and `attributeGroup`
definition in the XSD model (or in any upstream XSD model that is included via the "episode" mechanism, for that matter),
it generates an `interface` definition with all the getter, and optionally setter, methods of the properties defined via
the `group` or `attributeGroup` definition.

Then, it declares every class that was generated from a `complexType` that uses the `group` or `attributeGroup` as implementing
just that interface. This way, all classes generated from XSD complexTypes that use the same group definitions, will
share a common contract and can be treated in a common way by client code.

If the "fluent-builder" plugin is also activated, the interface definition can optionally include the declarations of the "with..."
and "add..." methods of the generated builder class as a nested interface declaration, so you can even rely on a common
"builder" contract for classes using the same `group` and `attributeGroup` definitions.

For example, you may wish to add "XLink" functionality to your generated classes. If the group-contract plugin is
activated, you can define a complexType in XSD that supports the "simple" attributes by adding to its XSD definition:

``` xml
<complexType name="some-type">
	.... (model group of the type...)
	<attributeGroup ref="xlink:simpleAttrs"/>
</complexType>
```

Which will generate a class something like:

``` java
public class SomeType implements SimpleAttrs {
...
```

And an interface definition like:

``` java
public interface SimpleAttrs {
	String getHref();
	void setHref(final String value);
	// ... more properties ...

	// this part is generated only if fluent-builder is also active
	interface BuildSupport<TParentBuilder >{
            public SimpleAttrs.BuildSupport<TParentBuilder> withHref(final String href);
            //... more properties ...
	}
}
```

Similar effects could be achieved by subclassing complexTypes, but since there is no multiple inheritance, inheritance
hierarchies can get overly complex this way, and inheritance is less flexible than interface implementations.

**Note:** The group-contract plugin supports JAXB modular compilation, i.e. the "episode" mechanism implemented
in the JAXB reference impplementation.
However, due to the lack of extensibility of the current default episode data structures and processing, this plugin
has to manage its own "episode" file. There are two command line options to control the  names of the "upstream" episode
file, i.e. the file name the plugin should look for when using other modules, and the "downstream" file, i.e. the file
name that should be generated for use by other modules.



### Usage
#### -Xgroup-contract

#### Options

##### -declareSetters=`{y|n}` (y)
Also generate property setter methods in interface declarations.


##### -declareBuilderInterface=`{y|n}` (y)
If the "fluent builder plugin" (-Xfluent-builder) is also active, generate interface for the internal builder classes as well.


##### -supportInterfaceNameSuffix=`<string>` (Lifecycle)
If this is set, methods that could cause type conflicts when two generated interfaces are used together as type parameter bounds, will be put in another interface named the same as the original interface, but with the suffix specified here.


##### -upstreamEpisodeFile=`<string>` (/META-INF/jaxb-interfaces.episode)
Use the given resource file to obtain information about interfaces defined in an upstream module (refer to "-episode" option of XJC).


##### -downstreamEpisodeFile=`<string>` (/META-INF/jaxb-interfaces.episode)
Generate "episode" file for downstream modules in the given resource location.


## clone
### Motivation
Another way to create a deep copy of an object tree. This adheres to the `java.lang.Cloneable` contract, but isn't as versatile as `-Xcopy`.

### Function
The `clone` plugin generates a deep clone method for each of the generated classes, based on the following assumptions:

* Objects implementing `java.lang.Cloneable` and are cloneable by their "clone" Method.
* Objects not implementing `java.lang.Cloneable` or primitive types are assumed to be immutable, their references are copied over, they are not cloned.

### Bugs
The `-cloneThrows` option should in fact never have existed.

### Limitations
There is currently no way for the plugin to determine whether an object in the object graph that isn't cloneable actually is immutable so its reference can be copied. So, there is no guarantee that cloned object graphs are really independent of each other, as mandated by the `java.lang.Cloneable` contract.

### Usage
#### -Xclone

#### Options

##### -cloneThrows=`{y|n}` (y)
Declare CloneNotSupportedException to be thrown by 'clone()' (yes), or suppress throws clause and wrap all `CloneNotSupportedException`s as `RuntimeException` (no).
If you set this to `no`, the resulting code will violate the `java.lang.Cloneable` contract, since it is stated that an object that cannot be cloned should throw CloneNotSupportedException, and nothing else. This option has been added, however, to support legacy code that doesn't catch CloneNotSupportedExceptions.


## copy
### Motivation
Sometimes it is necessary to create a deep copy of an object. There are various approaches to this. The "copy" plugin defines its own interface, contract, and definitions that are somewhat different from the standard java "java.lang.Cloneable" contract. The entry point generated in the source code is called `createCopy`, there are optionally also copy constructors.

### Function
The `copy` plugin generates a deep clone method for each of the generated classes, based on the following assumptions:

* Instances of any other classes implementing the `com.kscs.util.jaxb.Copyable` interface are copyable by the same semantics as "this".
* Objects implementing `java.lang.Cloneable` and not throwing "CloneNotSupportedException" are also reliably cloneable by their "clone" Method.
* Objects not implementing `java.lang.Cloneable` or primitive types are assumed to be immutable, their references are copied over, they are not cloned.
* Optionally, generates a "partial createCopy" method that takes a `PropertyTree` instance which represents a specification of the nodes in the object tree to copy. The PropertyTree is built up by an intuitive builder pattern:

		final PropertyTree excludeEmployees = PropertyTree.builder().with("company").with("employees").build();

* There is also a type-safe way to build a PropertyPath instance by using a generated classes' `Selector` sub structure. The following will generate the same selection as above:

		final PropertyTree excludeEmployees = Business.Select.root().company().employees().build()

Then, you would partially clone an object tree like this:

		final BusinessPartner businessPartnerCopy = businessPartner.createCopy(excludeEmployees, PropertyTreeUse.EXCLUDE);

Which is the same as

		final BusinessPartner businessPartnerCopy = businessPartner.copyExcept(excludeEmployees);

This way, the copy of the original `businessPartner` will have no employees attached to the contained `company`. It is also possible to copy only a specific subset of the original object tree, excluding everything else. The inverse result of the above would be generated by:

		final BusinessPartner businessPartnerCopy = businessPartner.createCopy(excludeEmployees, PropertyTreeUse.INCLUDE);

or

		final BusinessPartner businessPartnerCopy = businessPartner.copyOnly(excludeEmployees);

which will result in a businessPartnerCopy where every property is set to null, except the company property, and in the attached company object, every property is null except "employees".

This works for single and multi-valued properties, where for multi-valued properties, the property tree applies to all elements of the list of values in the same way. As of yet, there is no way to make a tree apply only to specific indexes in generated lists.

### Limitations
* The `-narrow` option is a somewhat special use case and should be used carefully.

### Usage
#### -Xcopy

#### Options

##### -partial=`{y|n}` (y)
Generates an additional 'createCopy'-method and copy-constructor (if constructors are to generated at all) that takes a PropertyTree instance to restrict the copy operation to selected nodes in the object tree.


##### -generateTools=`{y|n}` (y)
Generate utility classes as source code. If you say "no" here, you will have to add the plugin JAR to the runtime classpath of the generated class domain.


##### -constructor=`{y|n}` (y)
Generates a copy constructor on each of the classes generated from the current XSD model.


##### -narrow=`{y|n}` (n)
Uses copy constructors for all child nodes in the object tree as long as they are available. This will cause the new instance to be as narrow as possible to the declared types.


##### -selectorClassName=`<string>` (Selector)
Name of the generated nested "Selector" builder class, used to build up a property tree for partial copy functionality. This setting will also affect the "fluent-builder" plugin if it is active and set to "copy-partial=y".


##### -rootSelectorClassName=`<string>` (Select)
Name of the generated nested static "Select" entry point class to be used by client code for the "partial copy" feature. This setting will also affect the "fluent-builder" plugin if it is active and set to "copy-partial=y".


## constrained-properties
### Motivation
Many GUI applications use data binding to connect the data model to the view components. The JavaBeans standard defines a simple component model that also supports properties which send notifications whenever the are about to be changed, and there are even vetoable changes that allow a change listener to inhibit modification of a property. While the JAvaBeans standard is a bit dated, data binding and property change notification can come in handy in many situations, even for debugging or reverse-engineering existing code, because you can track any change made to the model instance.

### Function
constrained-properties generates additional code in the property setter methods of the POJOs generated by XJC that allow `PropertyChangeListener`s and `VetoableChangeListener`s to be attached to any instance of a XJC-generated class.

Currently, **indexed properties** are NOT supported in the way specified by JavaBeans, but instead, if a property represents a collection, a collection proxy class is generated that supports its own set of collection-specific change notifications, vetoable and other. This decision has been made because by default XJC generates collection properties rather than indexed properties, and indexed properties as mandated by JavaBeans are generally considered "out of style".

### Limitations
* The JavaBeans standard is only loosely implemented in the generated classes.
* Indexed Properties as defined in JavaBeans are not supported.
* The CollectionChange behavior implemented by the classes is not yet documented and non-standard.

### Usage
#### -Xconstrained-properties

#### Options

##### -constrained=`{y|n}` (y)
switch "constrained" property contract generation on/off. Default: yes


##### -bound=`{y|n}` (y)
switch "bound" property contract generation on/off. Default: yes


##### -setterThrows=`{y|n}` (n)
Declare setXXX methods to throw PropertyVetoException (yes), or rethrow as RuntimeException (no). Default: no


##### -generateTools=`{y|n}` (y)
Generate helper classes needed for collection change event detection. Turn off in modules that import other generated modules. Default: yes


## meta
### Motivation
Sometimes, you need information about the properties of a class, or you wish to have a constant for the names of properties.
The "meta" plugin creates an inner class (the name of which can be controlled by a command-line option), and adds a constant
field for each property. If the `-extended=y` command-line option is specified, these constants will hold instances of the
`PropertyInfo` class, on which the name, type, multiplicity (collection or not) and default value (from XSD) are exposed.
Without `-extended`, the constants are simply string constants holding the property names.


### Usage
#### -Xmeta

#### Options

##### -generateTools=`{y|n}` (y)
Generate helper class used to represent extended metadata as source code.
If this is set to "n" and "-extended=y", the plugin JAR will have to be in the runtime classpath of the client application.


##### -extended=`{y|n}` (n)
Generate extended meta data for each property: Name, type, multiplicity, default value.


##### -camelCase=`{y|n}` (n)
Generate names of constant meta fields like field names, instead of Java constant name convention.


##### -metaClassName=`<string>` (PropInfo)
Name of the generated meta-information nested class.


[1]: #fluent-builder
[2]: #immutable
[3]: #modifier
[4]: #group-contract
[5]: #clone
[6]: #copy
[7]: #constrained-properties
[8]: #meta
