---
title: "Appendix"
slug: appendix
---

We covered a lot of terminology in this tutorial. Feel free to use this page as a reference:

- **Attributes** - the properties of a Swift type
- **Core Data** - a framework from Apple that allows you to create and describe your model objects and their relationships to one another.
- **Entity** - the mapping between a database table and a Swift type (class).
- **Managed Object Context** - The object used to create and fetch managed objects and to manage undo and redo operations. [Docs on `NSManagedObjectContext`](https://developer.apple.com/documentation/coredata/nsmanagedobjectcontext)
- **Managed Object Model** - a set of entity description objects that as a whole, form a blueprint describing the managed objects in your app. It describes the entities in the stores, and allows Core Data to map records in a persistent store into managed objects to use in an application
- **NSEntityDescription** - A description of an entity in Core Data. [Docs](https://developer.apple.com/documentation/coredata/nsentitydescription) for more info
- **NSManagedObjectModel** - A programmatic representation of the `.xcdatamodeld` file describing your objects. [Docs](https://developer.apple.com/documentation/coredata/nsmanagedobjectmodel) for more info
- **NSPersistentContainer** - encapsulates the majority of Core Data Stack components in the application. It consists of a set of objects that facilitate saving and retrieving data.
- **Persistent Store Coordinator** - Aggregates all the stores. [Docs on `NSPersistentStoreCoordinator`](https://developer.apple.com/documentation/coredata/nspersistentstorecoordinator)
- **Transformable** - an object type that  lets Core Data convert the objects into a type it can store, and then convert it back in to the original types when loading from the filesystem. Transformable attributes require an [NSValueTransformer](https://developer.apple.com/documentation/foundation/nsvaluetransformer) subclass to handle conversions.
- **viewContext** - a property which is built-in to the persistent container, and is associated with the Main Queue (aka, the UI Queue). It is what we use in the tutorial for saving/fetching data.
