## AssetsPickerViewController

[![Version](https://img.shields.io/cocoapods/v/AssetsPickerViewController.svg?style=flat)](http://cocoapods.org/pods/AssetsPickerViewController)
[![License](https://img.shields.io/cocoapods/l/AssetsPickerViewController.svg?style=flat)](http://cocoapods.org/pods/AssetsPickerViewController)
[![Platform](https://img.shields.io/cocoapods/p/AssetsPickerViewController.svg?style=flat)](http://cocoapods.org/pods/AssetsPickerViewController)

Customizable assets picker controller that supports selecting multiple photos and videos, fully written in Swift.


## Comment

This project is still under development so many features will be added, and will get better in the near future.

If you found any bugs - even in develop branch, do not hesitate raise an issue for it.

Any advice, suggestions, and pull request will be greatly appreciated.


## Screenshots

iOS friendly UI for Album & Asset

![albums_portrait](https://cloud.githubusercontent.com/assets/20486591/26525542/43036a42-4395-11e7-98f0-5bf3f40f923d.PNG)
![photos_portrait](https://cloud.githubusercontent.com/assets/20486591/26525538/42b1d6dc-4395-11e7-9c16-b9abdb2e9247.PNG)


For landscape, keeps focusing indexes even during decelerating.

![photos_landscape](https://cloud.githubusercontent.com/assets/20486591/26525541/42f44f4e-4395-11e7-80e2-e1dd890a4d16.PNG)


Handles empty or no permisson cases.

![no_photos](https://cloud.githubusercontent.com/assets/20486591/26525540/42f25e82-4395-11e7-9dc2-73e04bcc9f00.PNG)
![no_permission](https://cloud.githubusercontent.com/assets/20486591/26525539/42e6759a-4395-11e7-9bae-1b90f6d3ec44.PNG)


Customizable Album & Asset Layout

![customize_album](https://cloud.githubusercontent.com/assets/20486591/26616647/1d343c24-460b-11e7-94cf-3b46a0f2e0a2.png)
![customize_asset](https://cloud.githubusercontent.com/assets/20486591/26616648/1d385746-460b-11e7-9324-62ea634e2fcb.png)


## Features Done

- iOS friendly UI for album & photo controllers

- select album

- select multiple photos and videos

- realtime synchronization for library change in albums & photos

- option to show/hide empty albums

- option to show/hide "Hidden" album

- customizable album cell

- customizable album sorting by PHFetchOptions or filter block

- customizable album filtering by PHFetchOptions or filter block

- customizable asset cell

- customizable asset sorting by PHFetchOptions

- customizable asset filtering by PHFetchOptions


## Features To-do

- iPad support

- single select mode with crop

- enhance example codes

- support many languages(German, French, Spanish, Chinese, Japanese, etc)


## Basic Usage

To run the example project, clone the repo, and run `pod install` from the Example directory first.

```swift
// to show
let picker = AssetsPickerViewController()
picker.pickerDelegate = self
present(picker, animated: true, completion: nil)

// to handle
extension SimpleExampleController: AssetsPickerViewControllerDelegate {
    
    func assetsPickerCannotAccessPhotoLibrary(controller: AssetsPickerViewController) {}
    func assetsPickerDidCancel(controller: AssetsPickerViewController) {}
    func assetsPicker(controller: AssetsPickerViewController, selected assets: [PHAsset]) {
        // do your job with selected assets
    }
    func assetsPicker(controller: AssetsPickerViewController, shouldSelect asset: PHAsset, at indexPath: IndexPath) -> Bool {
        return true
    }
    func assetsPicker(controller: AssetsPickerViewController, didSelect asset: PHAsset, at indexPath: IndexPath) {}
    func assetsPicker(controller: AssetsPickerViewController, shouldDeselect asset: PHAsset, at indexPath: IndexPath) -> Bool {
        return true
    }
    func assetsPicker(controller: AssetsPickerViewController, didDeselect asset: PHAsset, at indexPath: IndexPath) {}
}
```

## Bonus

### Basic

To hide empty albums,
```swift
pickerConfig.albumIsShowEmptyAlbum = false
```

To show "Hidden" albums,
```swift
pickerConfig.albumIsShowHiddenAlbum = true
```

### Appearence

To apply custom album cell,
```swift
pickerConfig.albumCellType = CustomAlbumCell.classForCoder()
// and implement your own UICollectionViewCell which conforms to AssetsAlbumCellProtocol
```

To apply custom asset cell,
```swift
pickerConfig.assetCellType = CustomAssetCell.classForCoder()
// and implement your own UICollectionViewCell which conforms to AssetsPhotoCellProtocol
```

### Sorting

To sort albums by PHFetchOptions,
```swift
let options = PHFetchOptions()
options.sortDescriptors = [NSSortDescriptor(key: "estimatedAssetCount", ascending: true)]
        
pickerConfig.albumFetchOptions = [
    .smartAlbum: options
]
```

To sort by block for a certain reason,
```swift
pickerConfig.albumComparator = { (albumType, leftEntry, rightEntry) -> Bool in
    // return: Is leftEntry ordered before the rightEntry?
    switch albumType {
    case .smartAlbum:
        return leftEntry.album.assetCollectionSubtype.rawValue < rightEntry.album.assetCollectionSubtype.rawValue
    case .album:
        return leftEntry.result.count < rightEntry.result.count     // ascending order by asset count
    case .moment:
        return true
    }
}
```

To sort assets by PHFetchOptions,
```swift
let options = PHFetchOptions()
options.sortDescriptors = [
    NSSortDescriptor(key: "pixelWidth", ascending: true),
    NSSortDescriptor(key: "pixelHeight", ascending: true)
]

pickerConfig.assetFetchOptions = [
    .smartAlbum: options
]
```

### Filtering

To filter albums by PHFetchOptions,
```swift
let options = PHFetchOptions()
options.predicate = NSPredicate(
    format: "assetCollectionSubtype = %d OR assetCollectionSubtype = %d",
    PHAssetCollectionSubtype.smartAlbumUserLibrary.rawValue,
    PHAssetCollectionSubtype.smartAlbumSelfPortraits.rawValue)                              // shows only Camera Roll & Selfies
        
pickerConfig.albumFetchOptions = [
    .smartAlbum: options                                                                    // apply to smart albums only
]
```

To filter albums by block for a certain reason,
```swift
let smartAlbumFilter: ((PHAssetCollection, PHFetchResult<PHAsset>) -> Bool) = { (album, fetchResult) in
    // filter by album object
    if album.assetCollectionSubtype == .smartAlbumBursts { return false }
    if album.assetCollectionSubtype == .smartAlbumTimelapses { return false }
    if album.assetCollectionSubtype == .smartAlbumFavorites { return false }
            
    // filter by fetch result
    if fetchResult.count > 50 {
        return true     // only shows albums that contains more than 50 assets
    } else {
        return false    //
    }
}
pickerConfig.albumFilter = [
    .smartAlbum: smartAlbumFilter
]
```

To filter assets by PHFetchOptions,
```swift
let options = PHFetchOptions()
options.predicate = NSPredicate(format: "mediaType = %d", PHAssetMediaType.video.rawValue)
options.sortDescriptors = [NSSortDescriptor(key: "duration", ascending: true)]
        
pickerConfig.assetFetchOptions = [
    .smartAlbum: options,
    .album: options
]
```

## Requirements

Xcode8, Swift 3, iOS 9.0


## Installation

AssetsPickerViewController is available through [CocoaPods](http://cocoapods.org). To install
it, simply add the following line to your Podfile:

```ruby
pod "AssetsPickerViewController"
```

## Author

DragonCherry, dragoncherry@naver.com


## License

AssetsPickerViewController is available under the MIT license. See the LICENSE file for more info.

MIT License

Copyright (c) 2017 DragonCherry

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
