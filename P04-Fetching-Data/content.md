---
title: "Fetching Data"
slug: fetching-data
---

Before we can present saved data to the user, the app must retrieve it from CoreData.

Remember that `fetchRequest` function in `Item+CoreDataProperties.swift`? The `viewContext` object has a built-in `fetch` function that accepts an `NSFetchRequest` result object produced by invoking the `Item` class’s auto-generated `fetchRequest` function.

# Creating and Sending a Fetch Request

> [action]
>
> To implement the `viewContext.fetch()` function, add the following to the `/model/ItemStore.swift` class implementation:
>
```swift
func fetchPersistedData(completion: @escaping (FetchItemsResult) -> Void) {
>
    let fetchRequest: NSFetchRequest<Item> = Item.fetchRequest()
    let viewContext = persistentContainer.viewContext
>
    do {
        let allItems = try viewContext.fetch(fetchRequest)
        completion(.success(allItems))
    } catch {
        completion(.failure(error))
    }
}
```

Notice that gives us an error for using an undeclared type **FetchItemsResult**. This will populate an array with the fetch results, or throw an error on failure.

> [action]
>
> Inside the `/model/ItemStore.swift` file — **but outside of the `ItemStore` class implementation** — create the `FetchItemsResult` enum:
>
```swift
enum FetchItemsResult {
    case success([Item])
    case failure(Error)
}
```

# Fetching and Updating the Data Source

Now that we’ve created our mechanism for fetching persisted data, we need to focus on the following:

- Calling the `fetch` function at the appropriates point in the lifecycle of the app’s relevant UI presentation objects.
- Update the Collection View’s data source so that it contains all of the latest changes made to the data store.

To achieve this, we will create and call a method in our `ViewController` class that fetches the latest persisted data and updates the Collection View’s data source.

> [action]
>
> In the `/controllers/ViewController.swift` class implementation, add this `updateDataSource` function:
>
```swift
// populate an array with fetched results on success, or to delete all items from that array on failure
private func updateDataSource() {
    self.store.fetchPersistedData {
>
        (fetchItemsResult) in
>
        switch fetchItemsResult {
        case let .success(items):
            self.items = items
        case .failure(_):
            self.items.removeAll()
        }
        // reload the collection view's data source to present the current data set to the user
        self.collectionView.reloadSections(IndexSet(integer: 0))
    }
}
```
>
> Now in the `ViewController` class, call the `updateDataSource` function at the end of the `viewDidLoad` and `viewWillAppear` functions:
>
```swift
override func viewDidLoad() {
>
...
>
  updateDataSource()
}
>
...
>
override func viewWillAppear(_ animated: Bool) {
>
...
>
  updateDataSource()
}
```

Build and run the project, and validate that items saved are persisted even after the app has been wiped from memory:

> [action]
>
> Run the Loaner app, add 2 items into the app, and then use the Fast App Switcher to eject it from memory. Reopen the app and make sure the items persisted.

Try saving items in the app even after the app is ejected from memory!

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'Items persist in the app'
$ git push
```
