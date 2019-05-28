---
title: "Deleting Data"
slug: deleting-data
---

1. ~~Build out the Core Data Model~~
1. ~~Create the Managed Object~~
1. ~~Build the Core Data Stack~~
1. ~~Implement Saving Data~~
1. ~~Implement Fetching Data~~
1. **Implement Deleting Data**
    1. **Delete items from both the context and data source**

We've nailed saving our items, but what if we want to delete an item? Or mark it as returned?

Prior to adding Core Data, when a user deleted an item, the Collection View’s data source was updated accordingly. But now, our current Core Data implementation doesn’t know how to handle items deleted by the user, resulting in items not actually getting deleted, or marked as returned!

Try to delete an item in the app now and see what happens.

# Deleting Items

Thankfully, handling deleted items and syncing up the data source are relatively straightforward:

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

Because the `markItemAsReturned` function simply calls the `deleteItem` function, Core Data is now implemented for both user choices!

> [action]
>
> Rebuild and run the app again and try deleting items or marking them as returned. Make sure they delete, and then eject the app from memory and reopen it to see that they stay deleted.

**Now that we can delete items from persistent storage, the loaner app is now fully functional with Core Data! Congrats!**

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'Items can be deleted'
$ git push
```

# Feedback and Review - 2 minutes

**We promise this won't take longer than 2 minutes!**

Please take a moment to rate your understanding of learning outcomes from this tutorial, and how we can improve it via our [tutorial feedback form](https://forms.gle/Y7GDpuyt67T7YvJC7)

This allows us to get feedback on how well the students are grasping the learning outcomes, and tells us where we can improve the tutorial experience.

# Loaning...Pets!

# Stretch Challenges

We've loaned out some inanimate objects, now let's try loaning out our pets! Sometimes you just wanna snuggle with a puppy without the responsibility of owning one, ya know?

> [challenge]
>
> Right now `loanee` is just a class. Convert `loanee` into an Entity of its own, and then update the relationship between `loanee` and `item` to match this conversion. Note that _one_ `loanee` can have _multiple_ `item`s

What if we want to offer more than just inanimate items? What if we allowed loaning of pets?

> [challenge]
>
> Build out a `Pet` entity with the following attributes: `name` (string), `id` (integer), `species` (string), `age` (integer), `petImage` (UIImage), and `loanee`
>
> Build this out through the steps you learned earlier. Make sure you can save, fetch, and delete through Core Data

You've decided that an `Item` is too general, and that you're only going to be loaning out specific items, so you can have more granular descriptions.

> [challenge]
> You decide to loan out only the following:
>
> 1. `Jacket`: with attributes `brand` (string), `size` (string), `loanee`, `color` (string), and `defects` (a list of strings)
> 1. `Comic`: with attributes `title` (string), `issueNumber` (integer), `publisher` (string), and `writer` (it's own class with `name`, `email`, and `age` properties)
>
> Build these out through the steps you learned earlier in how to build out the Core Data stack for these new entities. Make sure you can save, fetch, and delete through Core Data
