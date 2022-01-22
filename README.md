# Flutter Maplibre GL
## This is a fork of [flutter-mapbox-gl](https://github.com/flutter-mapbox-gl/maps) replaceing mapbox with the open source [Maplibre](https://github.com/maplibre)
> **Please note that this project is community driven and is not an official MapLibre product.** 


This Flutter plugin allows to show embedded interactive and customizable vector maps inside a Flutter widget. For the Android and iOS integration, we use [maplibre-gl-native](https://github.com/maplibre/maplibre-gl-native). For web, we rely on [maplibre-gl-js](https://github.com/maplibre/maplibre-gl-js). This project only supports a subset of the API exposed by these libraries. 

![screenshot.png](screenshot.png)


## How to use
This project is not yet available on pub.dev. You can use it by referencing it in your pubspec.yaml like this:
```
dependencies:
    ...
    maplibre_gl:
      git:
        url: https://github.com/m7mdra/maps.git
        ref: master
        
```
## Supported API

| Feature | Android | iOS | Web |
| ------ | ------ | ----- | ----- |
| Style | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Camera | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Gesture | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| User Location | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Style DSL   | :x:  | :x:  | :x:  |
| Raster Layer  | :x:  | :x:  | :x: | 
| Symbol Layer | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Circle Layer | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Line Layer | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Fill Layer | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Vector Source   |  :x:  | :x:  | :x:  |
| Raster Source  |  :x:  | :x:  | :x:  |   |   |   |   |
| GeoJson Source  | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Image Source   | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Expressions   |  :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Symbol Annotation | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Circle Annotation | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Line Annotation | :white_check_mark:   | :white_check_mark: | :white_check_mark: |
| Fill Annotation | :white_check_mark:   | :white_check_mark: | :white_check_mark: |

## Map Styles

Map styles can be supplied by setting the `styleString` in the `MapOptions`. The following formats are supported:

1. Passing the URL of the map style. This can be one of the built-in map styles, also see `MapboxStyles` or a custom map style served remotely using a URL that start with 'http(s)://' or 'mapbox://'
2. Passing the style as a local asset. Create a JSON file in the `assets` and add a reference in `pubspec.yml`. Set the style string to the relative path for this asset in order to load it into the map.
3. Passing the style as a local file. create an JSON file in app directory (e.g. ApplicationDocumentsDirectory). Set the style string to the absolute path of this JSON file.
4. Passing the raw JSON of the map style. This is only supported on Android.  

## Offline Sideloading

Support for offline maps is available by side loading the required map tiles and including them in your `assets` folder.

* Create your tiles package by following the guide available [here](https://docs.mapbox.com/ios/maps/overview/offline/).

* Place the tiles.db file generated in step one in your assets directory and add a reference to it in your `pubspec.yml` file.

```
   assets:
     - assets/cache.db
```

* Call `installOfflineMapTiles` when your application starts to copy your tiles into the location where Mapbox can access them.  **NOTE:** This method should be called **before** the Map widget is loaded to prevent collisions when copying the files into place.
 
```
    try {
      await installOfflineMapTiles(join("assets", "cache.db"));
    } catch (err) {
      print(err);
    }
```

## Downloading Offline Regions

An offline region is a defined region of a map that is available for use in conditions with limited or no network connection. Tiles for selected region, style and precision are downloaded from Mapbox using proper SDK methods and stored in application's cache. 

* Beware of selecting big regions, as size might be significant. Here is an online estimator https://docs.mapbox.com/playground/offline-estimator/.

* Call `downloadOfflineRegionStream` with predefined `OfflineRegion` and optionally track progress in the callback function.

```      
    final Function(DownloadRegionStatus event) onEvent = (DownloadRegionStatus status) {
      if (status.runtimeType == Success) {
        // ...
      } else if (status.runtimeType == InProgress) {
        int progress = (status as InProgress).progress.round();
        // ...
      } else if (status.runtimeType == Error) {
        // ...
      }
    };

    final OfflineRegion offlineRegion = OfflineRegion(
      bounds: LatLngBounds(
        northeast: LatLng(52.5050648, 13.3915634),
        southwest: LatLng(52.4943073, 13.4055383),
      ),
      id: 1,
      minZoom: 6,
      maxZoom: 18,
      mapStyleUrl: 'mapbox://styles/mapbox/streets-v11',
    );

    downloadOfflineRegionStream(offlineRegion, onEvent);
```


## Location features
### Android
Add the `ACCESS_COARSE_LOCATION` or `ACCESS_FINE_LOCATION` permission in the application manifest `android/app/src/main/AndroidManifest.xml` to enable location features in an **Android** application:
```
<manifest ...
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

Starting from Android API level 23 you also need to request it at runtime. This plugin does not handle this for you. The example app uses the flutter ['location' plugin](https://pub.dev/packages/location) for this.

### iOS
To enable location features in an **iOS** application:

If you access your users' location, you should also add the following key to `ios/Runner/Info.plist` to explain why you need access to their location data:

```
xml ...
    <key>NSLocationWhenInUseUsageDescription</key>
    <string>[Your explanation here]</string>
```

[Recommended](https://docs.mapbox.com/help/tutorials/first-steps-ios-sdk/#display-the-users-location)  explanation about "Shows your location on the map and helps improve the map".


## Contributing

We welcome contributions to this repository! If you're interested in helping build this Mapbox-Flutter integration, please read [the contribution guide](https://github.com/tobrun/flutter-mapbox-gl/blob/master/CONTRIBUTING.md) to learn how to get started.
