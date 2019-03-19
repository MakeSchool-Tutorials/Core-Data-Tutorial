---
title: "Deleting Data"
slug: deleting-data
---

We've nailed saving our items, but what if we want to delete an item? Or mark it as returned?

Prior to adding CoreData, when a user deleted an item, the Collection View’s data source was updated accordingly. But now, our current CoreData implementation doesn’t know how to handle items deleted by the user, resulting in items not actually getting deleted, or marked as returned!

# Deleting Items

Thankfully, handling deleted items and syncing up the data source are relatively straightforward goals that we can achieve:

> [action]
>
> Change the `/controllers/ViewController.swift` class’s `deleteItem` function to the following:
>
```swift
// Identify the item by its index
func deleteItem(at index: Int) {
    // Delete the user-selected item from the context
    let viewContext = store.persistentContainer.viewContext
    viewContext.delete(items[index])
>
    // Delete the user-selected item from the data source
    items.remove(at: index)
    collectionView.deleteItems(at: [IndexPath(row: index, section: 0)])
>
    // Save changes to the Managed Object Context
    store.saveContext()
}
```

Because the `markItemAsReturned` function simply calls the `deleteItem` function, CoreData is now implemented for both user choices!

Rebuild and run the app again and try deleting items or marking them as returned. Make sure they delete, and then eject the app from memory and reopen it to see that they stay deleted.

**Congrats! The loaner app is now fully functional with CoreData!**

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'Items can be deleted'
$ git push
```