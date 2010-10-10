Nodes &amp; Node Types
----------------------

A Node is an entity that is saved in the Database. It is always of a certain
Node Type. To understand the concept of Nodes and Node Types, one can compare a
Node Type to a class and a Node to an object. The Node Type defines which Fields
are available on a Node.

Some other concept from Object Oriented Programming also applies: inheretance.
Node Types can inheret from another Node Type. The effect of this, is that the
Fields of the parent Node Type are also on the child Node Type. Furthermore are
Nodes of the child Node Type also considered to be of the parent Node Type.
Actually Node Types are required to be inhereted from another Node Type, but
there is one Root Node Type that can be choosen as the parent. This Root Node
Type does nothing more than just being a Node Type. It behaves like an abstract
class, because Nodes cannot be created from the Root Node Type. Other Node Types
can also be defined as Abstract, which prevents the creation of Nodes of that
Type.

Fields
------

A Field is some information that is attached to a Node, for example a title, a
description and all other kinds of information. The available Fields are defined
by the Node Type. Fields are of a Field Type. The Field Type defines which
information can be stored, by defining its Columns. A Column is a basic storage
position for information in a Field and a Field Type can define more than one
Column. It should define at least one. For example a 'string' Field Type would
define only one String Column, but a 'latlng' Field Type would define two Float
Columns for the latitude and the longitude.

Versions
--------

A Node can have multiple Versions. The values for the Fields are stored per
Version. It is up to the implementation whether values are internally shared
over more Versions, but this should have no effect on the external workings of
the system.

Every Version can (and in almost all cases should) have a Version Label. A
Version Label is like an adhesive that can be sticked to a Version of a Node. A
Version Label has a Version Label Value and a Version Label Value Type. The
Version Label Value Type defines what the possible Version Label Values are. The
available Version Label Value Types are the same as the Field Types.

A Version Label is of a Version Label Type. There are several kinds of Version
Labels Types. These kinds are defined by the values of the following properties:

 1. Allowed amount: Single or Multiple. Single means that a Version can only
    have one Version Label of this Version Label Type.
 2. Required: True or False. True means that a Version must have at least one
    Version Label of this Version Label Type.
 3. Uniqueness: True or False. True means that for every possible Version Label
    Value, for this Version Label Type, there can only be one Version per Node.
    
    This property has an attribute: other Version Label Types that define a set,
    for which the Uniqueness works. This allows you to do some tricky things.
    One small example:
    
    Two Version Label Types: Language (Single, Required, not Unique and of
    String(5) Value Type) and Published (Single, not Required, Unique with
    respect to Language and of None Value Type). The effect is that, for every
    language, only one Version can be published.
 4. Identifying: True or False. Every Version of a Node is uniquely identified
    by the Version Labels of the Identifying Version Label Types. This means in
    practice that we need another Version Label Type in the previous example to
    make it work: Version Numer (Single, Required, Unique with respect to
    Language and of Unsigned Integer Type). Both Version Number and Language are
    Identifying. Note that Identifying implies Single and Required.

Version label Types are linked to Node Types and are only available on Versions
of Nodes of the linked Node Types.

Node Relationships
------------------

Nodes can be related. For example a Article Node can be related to a Person
Node, (in some publication database, in a CMS a User Node would probably be
much more useful). These Relationships are not expressed by wisely choosen
Fields (although that is possible and in some situations maybe more logical
than Relations), but by separed entities. These entities are called
Relationships and they behave like a special kind of Node. Like Nodes,
Relationships have a Relationship Type, and the Type defines the behavior of the
Relationship. Relationships can have Fields, just like Nodes, which are also
defined by the Relationship Type. There is a Relationship Type hierarchy, like
the Node Type hierarchy.

Like mentioned before, the behavior of a Relationship is defined by its
Relationship Type. This behavior is defined by some properties.

 1. Direction: Directed or Bi-Directional. In simple terms, this defines whether
    the Relationship would be drawn as an arrow (Directed) or a line
    (Bi-Directional). In the previous example, the Article-Person Relationship
    is a typical Directed Relationship. The Article Node points to the Person
    Node to define its author. A typical example of a Bi-Directional
    Relationship is a simple Article-Article Relationship (to be able to show a
    list of related Articles on an Article page). The Article Node one the
    A-side is related to the Article Node on the B-side in exactly the same way
    as the other way around.
 2. Endpoint Configuration. The Endpoint Configuration defines what can be at
    the Endpoints of the Relationship. If the Relationship Type has the
    Bi-Directional property, both Endpoints have the same Endpoint
    Configuration, by definition. It is possible for a Directed Relationship
    Type to have the same Endpoint Configuration for both Endpoints, but
    Directed allows two different Endpoint Configurations while Bi-Directional
    forces one Endpoint Configuration.
    
    There are three kinds of Endpoint Configuration. These define at a very
    basic level what can be linked. All these Endpoint Configuration can be
    configurated further.
     1. Node. This is the kind we already discussed, where the Relationship is
        linked to a Node. This kind has only one property: the Node Type. It
        allows only one Node Type, and only Nodes of that Type can be linked.
        Remember that Nodes of a Node Type are also considered to be of its
        parent Node Type, so the Node Type hierarcy can be used to allow a
        Relationship to link to Nodes of more Node Types. Notice furthermore
        that the Root Node Type can also be choosen here.
     2. Version. A Relation can also link to a specific Version of a Node.
        Because Node Relations have Versions themselves, but are not considered
        to be part of the Node and therefore not affected by the Versions of the
        Node, it can useful to let Relationships point to Versions. Furthermore
        it is useful to, for example, save the author of a Version. This kind
        has the same property as Node: Node Type.  This makes sure that only
        Versions of Nodes of this Node Type can be linked. The same discussion
        about Node Type Hierarchy applies here.
     3. Version Labels. A Relationship can link to a set of Version Label
        Values. This kind has two properties: Node Type and Version Label Types.
        Node Type works as expected from the kinds discussed before. The Version
        Label Types define which Version Label Labels are used to identify the
        Versions that this Relationship points to (only Version Label Types that
        are enabled on the chosen Node Type are allowed). This property doesn't
        have to contain all the identifying Version Label Types for the Node
        Type, so this can be used to point a Relationship to a group of Versions
        (for example all Versions in a certain language). It can contain all the
        identifying Version Label Types. This has the same effect as pointing to
        a Version, until the Version Labels of Versions are changed. With the
        Version Labels kind the Relationship points to the new Versions with the
        same Version Label Values as the old ones, while with the Version kind
        the Relationship points to the same Version it pointed to before the
        change.
    Besides to the kind of Endpoint Configuration, there are two other
    properties that define the behavior of the Endpoint.
     1. Required: True or False. If an Endpoint is marked as Required, it means
        that every item that a Relationship of this Relationship Type could
        point to with the active Endpoint, also should have a Relationship that
        points to it. This allows for an Article to require an Author.
     2. Amount: Single or Multiple. If an Endpoint is marked as Single,it means
        that every item that a Relationship of this Relationship Type could
        could point to with the active Endpoint, can have only one Relationship
        that points to it. This allows for an Article to have only on Author.

Namespaces &amp; Prefixes
-------------------------

Namespaces are Node Containers. A Node can be placed in a Namespace. This is
usefull to group the Nodes into logical sections, on Database Level. This is
different from normal classification, but rather useful to differentiate
content that is complety separated.

A Node Type is also placed in a container and this container is called a Prefix.
A Prefix differs in quite a few things from a Namespace.

  - A Prefix contains Node Types instead of Nodes.
  - Node Types can only be in one Prefix.
  - Node Types can only inheret from a Node Type that is in the same Prefix.

A Node Type can also be linked to Namespaces. A Namespace can only contain Nodes
of a Node Type that is linked to that Namespace.

Databases
---------

A Database is a container for everything. There is nothing which does not belong
to a Database and there is nothing that is shared over more than one Database.
Everything from the beforegoing discussion is available per Database.

