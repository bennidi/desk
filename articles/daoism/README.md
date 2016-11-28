# Abstract #

> The DAO pattern is a widely used approach to encapsulate data persistence behind common abstractions.
Still, many proposed instances of that pattern neither use its full potential nor do they rely on very DRY implementations. This article discusses the DAO pattern in general and from an architectural design perspective and shows how DAOs can be built on top of a generic persistence provider by means of delegation.

 

# Layers of data persistence #

Data persistence is a fundamental funtionality of most modern software systems. After all, programs are data transformations and as such they are built around data and this data is usually not volatile but meant to be used multiple times over a longer period of time. Hence, loading and storing data becomes an essential part of many programs.

Storage and retrieval of data are complex operations that potentially involve many devices and require combined efforts of many software and hardware components of the entire computer system. These components are organized into different layers, the top level layer usually being a well designed driver/protocol (JDBC,ODBC,NTFS etc.) that offers access to the high-level operations for its respective storage technology.

Inside an application one or more of those drivers are used to connect with (multiple) storage technologies and provide data persistence functionality to the rest of the application. Additionally, there is a multitude of libraries and frameworks that provide more sophisticated and application specific data persistence functionality built on top of those drivers – object relational mappers being one of the most popular examples.

# Why that DAO?

Besides those many layers of abstraction built around data persistence which are mostly ready to be used out-of-the-box, it is still necessary to create some form of design to plug-in those technologies into the application. It should leave enough flexibility to allow the data persistence aspect to evolve easily with the changing requirements, hide any unnecessary technical complexity and adapt well with the rest of the application. There are people who argue that, for example, the JPA EntityManager is already such an abstraction but in reality it is still a technical mechanism with some of the ORM related technical details inevitably surfacing in the API. For example, the entity lifecycle is not a natural property of domain objects but a technical necessity of ORM. Also, exception handling should be done consistently and in one place instead of in every piece of code that deals with persistent data. Additionally, official specifications are not always precise about what behaviour is required and a vendor might choose to either throw an exception or return a null value. So each vendor comes with its own peculiarities, strengths and weaknesses and the ideal of being able to switch from one to the other without any change in code isn’t applicable to reality.

This is why there should be a dedicated, custom data persistence abstraction in any serious software system. Designing such an abstraction is not necessarily very difficult and if done right, will provide many benefits. One of those abstractions is the well-known DAO pattern. It is one of the older and actually not too exciting patterns but it is quite solid and not overly complex in its implementation. Still, even experienced developers get it wrong or at least don’t use its full potential. Redundant and semantically varying implementations of basic CRUD and other persistence methods, lack of static typing, DAOs with thousands of LOC, scattered access to technology specific components on multiple layers are only some possible flaws. So, using even a simple pattern can go wrong, which is why I want to share some of my learnings regarding the use of DAO pattern. I found it surprising how much one can get out of this simple design when some basic principles are followed.

## Use a DAO for every persistence operation, don’t work around it

This might seam quite obvious but is easily violated when the software process lacks good code reviews, a new developer joins the team, communication between team members doesn’t work etc. If you promote the concept of a special class of objects dedicated to handle the persistence aspect of your application, then only these objects and nobody else should be in charge of it. Generally speaking, if you decide to use a pattern, use it consistently. This also helps to keep the team aligned with the design concepts of the system.

Persistence APIs are potentially rich and complex in semantics so it is easy to write code that looks about the same but executes differently under certain conditions. When the calls to the technical frameworks are centralized in a few places like some abstract DAO, developers are less likely to get their persistence operations wrong and may instead focus on getting their work done instead of browsing technical APIs. Changes in the object model are more likely to affect related DAOs and not dependent services. Implementing work arounds or using a special feature of technology XY can be done consistently in one place.

## Align your DAOs with your domain model

From my experience, persistence is an essential feature of the applications domain model, ie. it’s the business objects which come with a persistence aspect. Aligning the DAO hierarchy with that of the domain model is a natural way to keep the single DAOs focused – they will only contain persistence methods dealing with their corresponding business object class(es) and inherit the more general functions from their superclass. Thus, it is always clear which DAO to use or where to add a new finder method for a special use case, which prevents persistence code from scattering into random places.

Naturally, every other object can be persistent and sometimes it makes sense to treat data and the object model separately. Even for those objects the DAO pattern can be used but maybe with slightly different interface (see first rule). You don’t have a domain model? Then be referred to the DDD resources at the bottom of this article.

## Don’t expose technology specific components

A DAO is a technology agnostic component for data persistence – it’s terminology consists of “load”, “store”, “update” etc. disregarding whatever storage technology may be under the hood. Honoring this principle makes it possible to plug-in different but similar storage technologies using potentially the same interface or a similar interface. Whether the DAO loads from a key-value store, an RDBMS using JDBC or JPA does not necessarily be visible from outside. This encapsulation allows to use a variety of storage technologies without forcing every developer to learn its details. It will also make storage migrations feasible and reduce maintenance costs. Of course, this approach has limitations and not all storage technologies can be reasonably unified without loosing their respective special features and it has to be carefully decided whether aunification brings net benefits.

## Test your DAOs

Having dedicated components for data persistence allows to test this whole aspect of the application independently. In fact, testing of basic CRUD methods can be done without the developer having to implement anything at all. More specialized data persistence code can be tested in dedicated unit tests and many bugs originating from incorrect transaction handling, invalid data etc. can be identified close to their source.

# Daoism – DAO for Java

Besides the many libraries and approaches already out ther, I want to present the solution that I have come up with over the years and which I finally github(ed) (Daoism). My goal was to make writing new DAOs as easy as possible, providing as much persistence functionality as possible using a clear and expressive interface and still leave room for extension. All of that without any magic like reflection, byte-code enhancement, annotation processing behind the scence. Just plain old Java classes using basic OO features.

One cornerstone of the design is that all DAOs function by delegating their work to a shared, stateless persistence provider. This persistence provider is the actual implementation of a specific persistence technology, like JPA, JDBC etc. Concrete DAOs will inherit all basic CRUD methods and a generic way for executing arbitrary queries from a common base class, the TypedDao which delegates to its underlying persistence provider. 


![Class Diagram of Doaism core abstractions](daoism-class-diag.png?raw=true, "Class Diagram of Doaism core abstractions")

Writing a new DAO is a matter of extending TypedDao. Generic unit tests for all CRUD methods can be created in the same way, just extend the common base class. The design works with persistent domain objects (not generic data records like ActiveRecord or any other untyped tuples), so its interfaces are built around object types using Java Generics.


```java

@Service public class VServerDao extends TypedDao<String, VServer> {
    @Autowired private DbPersistenceProvider persistenceProvider
    public VServerDao() { super(String.class, VServer.class); }
    @Override protected IPersistenceProvider getPersistenceProvider() { return persistenceProvider; } 

}


```

The CRUD test provides generic tests for all standard persistence operations using the entities generated by addEntities() and createValidEntity()

- findById()
- findAll()
- persistSingle()
- persistAll()
- count()
- remove()
- removeAll()


```java


public class VServerCrudTest extends SpringAwareCrudTest<String, VServer>{
    @Autowired private VServerDao dao;
    @Override protected VServer createValidEntity() { VServer vserver = new VServer(); vserver.setHost(System.currentTimeMillis() + ""); return vserver; }
    @Override protected void modifyEntity(VServer which) { which.setHost(UUID.randomUUID().toString()); }
    @Override protected ITypedDao<String, VServer> getDao() { return dao; }
    @Override protected void addEntities(List<VServer> entities) { entities.add(createValidEntity()); entities.add(createValidEntity()); entities.add(createValidEntity()); }


```

Getting these tests for free, is a great win, especially when frequent changes are made to the persistence model.  With all components being Java POJOs it is very easy to use Daoism in any managed environment like Spring or a J2EE container – in the project I use Spring to wire up my DAOs in the unit tests. Note: I do not show the code of how to wire-up the DAOs in a specific environment.

The way how delegation is used to push the actual work to the lowest layer and then have other classes built a less verbose interface on top of it, has prooven to be a good design and even if you don’t feel like using Daoism in your project, it might be worth to take half an hour and browse the source.

References

[1](http://tutorials.jenkov.com/java-persistence/dao-design-pattern.html) Jakob Jenkov ideas on using DAO pattern. He discusses many additional details not mentioned here

[2](http://www.infoq.com/minibooks/domain-driven-design-quickly) Domain Driven Design Quickly – A comprehensive summary of Eric Evans book

[3](http://dddcommunity.org/) Domain Driven Design community provides many resources on the ideas around DDD