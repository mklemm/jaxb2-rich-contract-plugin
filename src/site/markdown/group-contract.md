## group-contract
### Motivation
Out of the box, the only polymorphism supported by classes generated from an XSD is the `<extension/>` notion, transformed directly into an inheritance relationship by XJC. However, pure inheritance relationships are often inflexible and do not always reflect the intention of generating a "contract" that implementing classes must follow. With this plugin, it is possible for your application code to treat classes using one or more specific model or attribute groups in a common way. Objects can be initialised or used via the interface.

### Function
For definition of contracts, two additional XSD constructs, the `<group>` and `<attributeGroup>`, are readily available in XSD, but they're currently ignored by standard XJC and simply treated as an inclusion of elements or attributes into a generated class definition. The group-interface plugin changes that and generates an `interface` definition for each `<group>` or `<attributeGroup>` found in your model, defines the attributes or elements declared in the groups as get and set methods on the interface, and makes each generated class using the group or attributeGroup implement this interface. New in version 1.2: The group-interface plugin will generate interfaces for the generated fluent builder classes, if the fluent-builder plugin is also activated.

### Usage
#### -Xgroup-contract

#### Options

##### -declareSetters=`{y|n}` (y)
Also generate property setter methods in interface declarations.


##### -declareBuilderInterface=`{y|n}` (y)
If the "fluent builder plugin" (-Xfluent-builder) is also active, generate interface for the internal builder classes as well.


##### -upstreamEpisodeFile=`<string>` (/META-INF/jaxb-interfaces.episode)
Use the given resource file to obtain information about interfaces defined in an upstream module (refer to "-episode" option of XJC).


##### -downstreamEpisodeFile=`<string>` (/META-INF/jaxb-interfaces.episode)
Generate "episode" file for downstream modules in the given resource location.

