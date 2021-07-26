# UITableViewDiffableDataSource-Swift
![platforms](https://img.shields.io/badge/platforms-iOS-333333.svg)  

## Context  
A diffable data source object is a specialized type of data source that works together with your table view object. It provides the behavior you need to manage updates to your table view’s data and UI in a simple, efficient way.  
https://developer.apple.com/documentation/uikit/uitableviewdiffabledatasource  

## Requirement
- Xcode Version 12.4 (12D4e)
- Swift 5  
- iOS 13 and onward

### iPhone Image
<table border="0">
    <tr>
        <tr>
            <th>Gif image</th>
            <th>Standard</th>
            <th>Dark</th>
        </tr>
        <td><img src="https://github.com/YamamotoDesu/UITableViewDiffableDataSource-Swift/blob/main/RocketSim%20Recording%20-%20iPhone%2012%20-%202021-07-27%2000.06.51.gif" width="300"></td>
        <td><img src="https://user-images.githubusercontent.com/47273077/126889714-397a476a-7154-4023-947b-411e753a5e5d.png" width="300"></td>
        <td><img src="https://user-images.githubusercontent.com/47273077/126889687-a6773867-d972-41cb-88c3-20463e7e833a.png" width="300"></td>
    </tr>
</table>

### iPad Image
<table border="0">
    <tr>
        <tr>
            <th>Standard</th>
            <th>Dark</th>
        </tr>
        <td><img src="https://user-images.githubusercontent.com/47273077/126889504-a57cef79-78b6-40ac-914c-335af1b73edf.png" width="600"></td>
        <td><img src="https://user-images.githubusercontent.com/47273077/126889569-7821efaa-9f7f-41bc-aa75-87f074b11fb2.png" width="600"></td>
    </tr>
</table>
<table border="0">

# Sourcecode
## To connect a diffable data source to a table view
```swift

    lazy var dataSource = configureDataSource()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        //Assign the diffable data source to your table view.
        tableView.dataSource = dataSource
        
        //Generate the current state of the table data by creating a snapshot
        var snapshot = NSDiffableDataSourceSnapshot<Section, String>()
        snapshot.appendSections([.all])
        snapshot.appendItems(restaurantNames, toSection: .all)
        
        //Call the apply() function of the data source to populate the data
        dataSource.apply(snapshot, animatingDifferences: false)
        
        
    }
    
    func configureDataSource() -> UITableViewDiffableDataSource<Section, String> {
        let cellIdentifier = "favoritecell"
        //Create a UITableViewDiffableDataSource object to connect with your table andprovide the configuration of the table view cells.
        let dataSource = UITableViewDiffableDataSource<Section, String>(
            tableView: tableView,
            cellProvider: { tableView, indexPath, restaurantName in
                let cell = tableView.dequeueReusableCell(withIdentifier: cellIdentifier, for: indexPath) as! RestaurantTableViewCell
                cell.nameLabel.text = restaurantName
                cell.thumbnailImageView.image = UIImage(named: self.restaurantImages[indexPath.row])
                cell.locationLabel.text = self.restaurantLocations[indexPath.row]
                cell.typeLabel.text = self.restaurantTypes[indexPath.row]
                cell.heartMark.isHidden = !self.restaurantIsFavorites[indexPath.row]
                cell.heartMark.tintColor = UITraitCollection.isDarkMode ? .systemYellow : .blue
                return cell
                
            }
        )
        return dataSource
        
    }
```
    
## Returns the swipe actions to display on the leading edge of the row  
```swift
        override func tableView(_ tableView: UITableView, leadingSwipeActionsConfigurationForRowAt indexPath: IndexPath) -> UISwipeActionsConfiguration? {
        
        // Get the selected restaurant
        guard let restaurant = self.dataSource.itemIdentifier(for: indexPath) else {
            return UISwipeActionsConfiguration()
        }
        
        // Favorite action
        let favoriteAction = UIContextualAction(style: .normal, title: "") { (action, sourceView, completionHandler) in
            
            let cell = tableView.cellForRow(at: indexPath) as! RestaurantTableViewCell
            cell.heartMark.isHidden = !restaurant.isFavorite
            cell.heartMark.tintColor = UITraitCollection.isDarkMode ? .systemYellow : .blue
            self.restaurantIsFavorites[indexPath.row] = !restaurant.isFavorite
            tableView.reloadData()
            
            // Call completion handler to dismiss tbe action button
            completionHandler(true)
        }
        
        favoriteAction.backgroundColor = UIColor.systemYellow
        favoriteAction.image = UIImage(systemName: "heart.fill")
        
        let swipeConfiguration = UISwipeActionsConfiguration(actions: [favoriteAction])
        return swipeConfiguration
    }
```

# Something notes
##  To prevent content from becoming overly wide for iPad
```swift
tableView.cellLayoutMarginsFollowReadableWidth = true
```
<table border="0">
<tr>
    <caption>iPad Image</caption>
    <tr>
        <th>tableView.cellLayoutMarginsFollowReadableWidth = false</th>
        <th>tableView.cellLayoutMarginsFollowReadableWidth = true</th>
    </tr>
    <td><img src="https://user-images.githubusercontent.com/47273077/126890181-a65afe2c-01f9-4e3d-9427-b99493329514.png" width="400"></td>
    <td><img src="https://user-images.githubusercontent.com/47273077/126889569-7821efaa-9f7f-41bc-aa75-87f074b11fb2.png" width="400"></td>
    </tr>
</table>

##  Check current userinterfacestyle programmatically
```swift
//-------acronym--------------
    // Called when the iOS interface environment changes.
    override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
      super.traitCollectionDidChange(previousTraitCollection)

        self.heartMark.tintColor = UITraitCollection.isDarkMode ? .systemYellow : .systemPink
    }
//---------------------


extension UITraitCollection {

    public static var isDarkMode: Bool {
        if #available(iOS 13, *), current.userInterfaceStyle == .dark {
            return true
        }
        return false
    }
}
```

