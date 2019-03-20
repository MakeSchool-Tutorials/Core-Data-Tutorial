---
title: "Saving Data"
slug: saving-data
---

Most interactions with Core Data will occur through an instance of `NSManagedObjectContext`: the portal through which our app will create new entities, save changes, and fetch from the store.

The persistent container comes with a `NSManagedObjectContext` as one of its built-in properties. This context property provides an environment — a type of temporary “scratchpad” — where Core Data objects can be created and managed entirely in memory.

When asking the context to fetch data, the context will work with its **persistent store coordinator** to bring temporary copies of its object graph and entities into memory.

However, until we tell the context to save any new changes, all persisted data remains the same. So let's work on saving that data.

# Saving Data

Each instance of `NSManagedObjectContext` is associated with a particular concurrency queue, and we must interact with a context on the queue that is associated with it.

The `viewContext` property, which is built-in to the persistent container, is associated with the Main Queue (aka, the UI Queue), and is what we'll use for saving data.

> [info]
>
> In the event the app needed to save or retrieve data from Core Data on a queue other than the Main Queue, some other context would be required. For our purposes, using the Main Queue and the viewContext will suffice.

To save any changes in memory back to the database on disk, we will need to call the `save` method on the `viewContext` property.

> [action]
>
> Implement the `save` method by adding the following code to the `/model/ItemStore` class:
>
```swift
 // MARK: - Save Core Data Context    
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

Now, whenever a user creates an item, **the app will need to save the context, and any changes it happens to contain, to the database on disk.**

# Saving Newly Created Items

Due to the architecture and user flow of the Loaner app, one logical point to save the context is when the `ViewController` and its `Collection View` are presented just after creation of a new loaned item.

> [action]
>
> To save a newly created item to Core Data, we need to override the `viewWillAppear` function in the `/controllers/ViewController.swift` class and invoke the `saveContext` function on the `store` variable:
>
```swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(true)
>
    // Save the new items in the Managed Object Context
    store.saveContext()
}
```

Now let's fix that last Xcode error we've been holding on to!

> [action]
>
> Import Core Data in `/controllers/ViewController.swift`:
>
```swift
import UIKit
import CoreData
>
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

Try building and launching the app. It should behave exactly as it did at the beginning of this tutorial. It still isn't persisting data yet quite yet though, so let's get that rolling.

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'saving items, app builds'
$ git push
```
