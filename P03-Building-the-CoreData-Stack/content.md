---
title: "Building the CoreData Stack"
slug: building-the-coredata-stack
---

The CoreData stack consists of multiple objects that interface with our entities to save and load instances to a persistent store. The stack also includes the model file that describes our entities.

# Creating the Store Class

In keeping with best practices regarding MVC and the principle of Separation of Concerns, we want to **implement CoreData components independently from Controller and View classes.**

> [action]
>
> Under the `model/` folder, create a new `Cocoa Touch Class` file with a Class name of  `ItemStore` that is a subclass of `NSObject` and import the CoreData framework:
>
```swift
import UIKit
import CoreData
>
class ItemStore: NSObject {
>
}
```

_This is the class through which our app will create and manage all CoreData components._

# Implementing NSPersistentContainer

The **NSPersistentContainer** object encapsulates the major CoreData Stack components in the application. It consists of a set of objects that facilitate **saving and retrieving data.**

It also simplifies the creation and management of the Core Data stack by handling the creation of:

- The managed object model `NSManagedObjectModel`
- The persistent store coordinator `NSPersistentStoreCoordinator`
- The managed object context `NSManagedObjectContext`

`NSPersistentContainer` includes all objects needed to represent a functioning CoreData stack, including an object to manage the CoreData state as a whole, an object representing the Data Model, and so on.

> [action]
>
> To implement `NSPersistentContainer`, add the following code inside the `/model/ItemStore` class:
>
```swift
let persistentContainer: NSPersistentContainer = {
    // creates the NSPersistentContainer object
    // must be given the name of the CoreData model file “LoanedItems”
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

# Instantiating A Store Object

We want to ensure that the CoreData is available from the moment our app launches.

We also want to ensure there are not multiple versions of the `NSPersistentContainer`, the Managed Object Context, etc.

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

There should now be a new Xcode error: “Value of type 'ViewController' has no member ‘store’". Let's fix that real quick:

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

Rebuild the project, and make sure that previous error has been removed!

# Working With the Context

With the persistent container set up, _our app will now be able to interact with CoreData!_

Most interactions with CoreData will occur through an instance of a subclass of `NSManagedObjectContext`, the portal through which our app will create new entities, save changes, and fetch from the store.

The persistent container comes with a `NSManagedObjectContext` as one of its built-in properties. This context property provides an environment — a type of temporary “scratchpad” — where CoreData objects can be created and managed entirely in memory.

When asking the context to fetch data, the context will work with its **persistent store coordinator** to bring temporary copies of its object graph and entities into memory.

However, until we tell the context to save any new changes, all persisted data remains the same.

## The viewContext and Concurrency
Each instance of `NSManagedObjectContext` is associated with a particular concurrency queue, and we must interact with a context on the queue that is associated with it.

The `viewContext` property, which is built-in to the persistent container, is associated with the Main Queue (aka, the UI Queue).

In the event the app needed to save or retrieve data from CoreData on a queue other than the Main Queue, some other context would be required.

For our purposes, using the Main Queue and the viewContext will suffice.

# Saving Data

To save any changes any changes in memory back to the database on disk, we will need to call the `save` method on the `viewContext` property.

> [action]
>
> Implement the `save` method by adding the following code to the `/model/ItemStore` class:
>
```swift
 // MARK: - Save CoreData Context    
func saveContext() {
    let viewContext = persistentContainer.viewContext
    if viewContext.hasChanges {
        do {
            try viewContext.save()
        } catch {
            // Replace this implementation with code to handle the error appropriately.
            // fatalError() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
            let nserror = error as NSError
            fatalError("Unresolved error \(nserror), \(nserror.userInfo)")
        }
    }
}
```

Now, whenever a user creates or deletes an item, **the app will need to save the context, and any changes it happens to contain, to the database on disk.**

## Saving Newly Created Items

Due to the architecture and user flow of the Loaner app, one logical point to save the context is when the `ViewController` and its `Collection View` are presented just after creation of a new loaned item.

> [action]
>
> To save a newly created item to CoreData, we need to override the `viewWillAppear` function in `/controllers/ViewController.swift` class and invoke the `saveContext` function on the `store` variable:
>
```swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(true)

    // Save the new items in the Managed Object Context
    store.saveContext()
}
```

Now let's fix that last Xcode error we've been holding on to!

> [action]
>
> Import CoreData in `/controllers/ViewController.swift`:
>
```swift
import UIKit
import CoreData

class ViewController: UIViewController {
>
...
>
}
```
>
> In the same file, add a call to the `NSEntityDescription.insertNewObject` method so that the item returned in the `createNewItem` function is a Managed Object that is also inserted into the active context.
>
```swift
func createNewItem() -> Item {
   let newItem = NSEntityDescription.insertNewObject(forEntityName: "Item", into: store.persistentContainer.viewContext) as! Item
   return newItem
}
```

Try building and launching the app. It should behave exactly as it did at the beginning of this tutorial. It still isn't persisting data yet, so let's get that rolling.

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'implementing CoreData stack, app builds'
$ git push
```
