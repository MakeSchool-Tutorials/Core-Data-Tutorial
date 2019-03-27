---
title: "Core Data Model"
slug: core-data-model
---

1. **Build out the Core Data Model**
    1. **Add a Core Data Model file**
    1. **Create an entity**
    1. **Add attributes to the entity**
1. Create the Managed Object
1. Build the Core Data Stack
1. Implement Saving Data
1. Implement Fetching Data
1. Implement Deleting Data

Much of Core Data’s functionality depends on the schema we use to describe the application’s entities, properties, and the relationships between them.

Core Data uses a schema called a **managed object model** — an instance of `NSManagedObjectModel`.

# Adding the Core Data Model File

To start the Core Data implementation, we will need to add a new Data Model file to our existing model group.

> [action]
>
> Select `File --> New --> File..`, make sure that the iOS template is highlighted, search for `Data Model` and select that template.
>
> When prompted to `Save As`, name the new model file `LoanedItems.xcdatamodeld`, and save it under the `model/` group
>
> ![LoanedItems](assets/01_adding-the-core_LoanedItems-ModelFile.png)

At runtime, Core Data will use the `LoanedItems.xcdatamodeld` file (which describes the managed objects) to programmatically create an instance of `NSManagedObjectModel`, which is an in-memory representation of the `LoanedItems.xcdatamodeld` file.

# Modeling Entities

The **managed object model** allows Core Data to map records in a persistent store into managed objects that we use in our application.

The managed object model is a collection of **entity description objects**, which are instances of `NSEntityDescription`. An **entity** is the mapping between a database table and a Swift type (class).

An entity description describes an entity in terms of:

- its name
- the name of the class used to represent the entity in our application
- its properties (attributes and relationships)

> [info]
>
> We can think of entities as tables in a database. Their names are also the names of the classes (subclasses of `NSManagedObject`) used to represent the entities in our application.

Let's create an entity of our own:

> [action]
>
> 1. Select the `LoanedItems.xcdatamodeld` file in the `Project Navigator` pane to display Core Data’s `Data Model Editor`.
> 1. Click the `Add Entity` button in the lower left corner of the `Model Editor` to create a new entity (or find it in Xcode's Editor dropdown menu). A new entity with the default name `Entity` should appear in the `Entities` list in the `Model Editor`’s navigator pane.
>1. Change the new entity’s name to `Item` by clicking on `Entity` and changing the `Name` field in the `Datal Model Inspector`

![renamed_entity](assets/02_modeling-entities_renamed_entity.png)

# Adding Attributes

**Attributes** are the properties of a Swift type. The attributes we add to an entity are also represented as properties in the `NSManagedObject` subclasses. Let's give our new entity some attributes:

> [action]
>
> Add `notes`, `itemImage`, `itemTitle`, and `loanee` attributes to the `Item` entity. Note that attribute order may differ, but the **Type assigned to each Attribute should match the Type shown for each in the list below**:
>
> ![attributes](assets/03_adding-attributes_attributes.png)

Double check that the attributes have the correct name and Type!

What's it mean to be a `Transformable` type though? We'll cover that shortly, but first we need to talk about optionality.

# Optional & Non-Optional Attributes

The Data Model Editor makes all attributes `optional` by default.

Core Data was designed to handle the transaction details of databases, such as SQLite, where it is common to specify whether or not the insertion of NULL values is allowed for a given column. Therefore,
_marking an attribute as `optional` does not imply that it is a Swift `optional`._ It means that during runtime Core Data will create a database record that _optionally allows NULL values if the property does not contain data._

We don't need this feature, or the complexities it can add, so let’s change all our attributes to non-optional:

> [action]
>
> Select all the attributes, open the Data Model Inspector, and uncheck the Optional checkbox.

The end result of this should look like the below image:

![optional_attributes_unchecked](assets/04_optional-&-non_unchecked.png)

**If an attribute is not optional, it must have a non-nil value when we save changes to the Managed Object Context.** For other cases though, Core Data does not care if the attribute is `nil`.

Because these properties are not optional, we will need to provide values for them at object creation time.

# Transformable Attributes

Notice that the values for the `loanee` and `itemImage` attributes are both of the `Transformable` type.

Core Data is only capable of storing certain data types. However, neither `Loanee` or `UIImage` can be used by Core Data directly. Declaring them as `Transformable` lets Core Data _convert the objects into a type it can store, and then convert it back in to the original types when loading from the filesystem._

Transformable attributes require an `NSValueTransformer` subclass to handle conversions.

> [info]
>
> If not specified, the system will use a default transformer which uses archiving to convert the objects to and from NSData objects.
>
> Read more about `NSCoding` [here](https://developer.apple.com/documentation/foundation/nscoding)

Since `UIImage` conforms to `NSSecureCoding` (an extension of `NSCoding`), the default transformer will be fine.

But because `Loanee` does not conform to `NSCoding`, we must modify it to conform to the `NSCoding` protocol.

> [action]
>
> Update the `Loanee.swift` class to conform to the `NSCoding` protocol:
>
```swift
import UIKit
>
class Loanee: NSObject, NSCoding {
>
    var name: String
    var contactNumber: String?
>
    // Protocol requires having Keys for our variables
    enum Keys: String {
        case name = "name"
        case contactNumber = "contactNumber"
    }
>
    init(name: String, contactNumber: String?) {
>
        /** For Future Feature: Ability to access Contacts app:
         init(name: String, profileImage: UIImage, contactNumber: String?) {
>
         self.profileImage = profileImage
         **/
>
        self.name = name
        self.contactNumber = contactNumber
    }
>
    // Protocol requires an encode method to encode our variables based on the Key
    func encode(with aCoder: NSCoder) {
        aCoder.encode(name, forKey: "name")
        aCoder.encode(contactNumber, forKey: "contactNumber")
    }
>
    // Protocol requires an init method which acts as a decoder for our variables based on the Key
    required init?(coder aDecoder: NSCoder) {
        name = aDecoder.decodeObject(forKey: "name") as! String
        contactNumber = aDecoder.decodeObject(forKey: "contactNumber") as? String
        super.init()
    }
}
```

Congrats on **Implementing a Core Data model using Xcode's Model editor!** In the next chapter, we will create a custom Managed Object subclass to represent the `Item` entity!

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'Created entity and attributes'
$ git push
```
