---
title: "Building the Core Data Stack"
slug: building-the-coredata-stack
---

1. ~~Build out the Core Data Model~~
1. ~~Create the Managed Object~~
1. **Build the Core Data Stack**
    1. **Create an ItemStore class**
    1. **Implement the `NSPersistentContainer`**
    1. **Instantiate the ItemStore object**
1. Implement Saving Data
1. Implement Fetching Data
1. Implement Deleting Data

The Core Data stack consists of multiple objects that interface with our entities to save and load instances to a persistent store. The stack also includes the model file that describes our entities.

# Creating the ItemStore Class

In keeping with best practices regarding MVC and the principle of Separation of Concerns, we want to **implement Core Data components independently from Controller and View classes.**

> [action]
>
> Under the `model/` folder, create a new `Cocoa Touch Class` file with a Class name of  `ItemStore` that is a subclass of `NSObject` and import the Core Data framework:
>
```swift
import UIKit
import CoreData
>
class ItemStore: NSObject {
>
}
```

_This is the class through which our app will create and manage all Core Data components._

# Implementing NSPersistentContainer

The **NSPersistentContainer** object encapsulates the major Core Data Stack components in the application. It consists of a set of objects that facilitate **saving and retrieving data.**

It also simplifies the creation and management of the Core Data stack by handling the creation of:

- The managed object model: `NSManagedObjectModel`
- The persistent store coordinator: `NSPersistentStoreCoordinator`
- The managed object context: `NSManagedObjectContext`

`NSPersistentContainer` includes all objects needed to represent a functioning Core Data stack, including an object to manage the Core Data state as a whole, and an object representing the Data Model.

> [action]
>
> To implement `NSPersistentContainer`, add the following code inside the `/model/ItemStore` class:
>
```swift
let persistentContainer: NSPersistentContainer = {
    // creates the NSPersistentContainer object
    // must be given the name of the Core Data model file “LoanedItems”
    let container = NSPersistentContainer(name: "LoanedItems")
>
    // load the saved database if it exists, creates it if it does not, and returns an error under failure conditions
    container.loadPersistentStores { (description, error) in
        if let error = error {
            print("Error setting up Core Data (\(error)).")
        }
    }
    return container
}()
```

# Instantiating An ItemStore Object

We want to ensure that the Core Data is available from the moment our app launches, and that there are not multiple versions of the `NSPersistentContainer`, the Managed Object Context, etc.

To achieve this, it’s best to **create an instance of the `ItemStore` class in the `AppDelegate`, and to reference that instance from objects that need it.**

> [action]
>
> In the `AppDelegate.swift` file, add the following code inside the `application` function:
>
```swift
let rootViewController = window!.rootViewController as! UINavigationController
let viewController = rootViewController.topViewController as! ViewController
viewController.store = ItemStore()
```

There should now be a new Xcode error: `Value of type 'ViewController' has no member ‘store’`. Let's fix that real quick:

> [action]
>
> Inside the `/controllers/ViewController.swift` class, add the following store variable of type “Optional ItemStore”:
>
```swift
class ViewController: UIViewController {
>
    var store: ItemStore!
>
...
>
}
```

Rebuild the project, and make sure that previous error has been resolved!

With the persistent container set up, _our app will now be able to interact with Core Data!_ This means we've **built out our Core Data stack!** Now we can work on actually saving, fetching, and deleting the data in persistent storage.

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'implemented persistent container'
$ git push
```
