# trnql-sample-app

[TRNQL](http://trnql.com) is a developer platform which helps developer to quickly and easily integrate signals derived from sensors, phone data, and cloud services into apps.

This is simple basic application which uses ```SmartLocation``` provided by TRNQL. It tells you about your latitude, longitude and address.

Below is the Quick approach, which will help to you to develop such application. This will help you to understand TRNQL and will enable you to make your own SMART apps.

## Quick start to TRQNL
### Prerequisite

* Android Studio, to develop, build and run android application.

* TRNQL, android SDK, [download link](http://trnql.com/download_request/)

### Creating application
 * Create a project in Android Studio.
 * Unzip the files form the TRNQL SDK that you downloaded.
 * Copy ```lib-release.aar``` file to ```libs/``` folder of the application that you created.

 ```lib-release.aar``` contains all the library files and packages required for compilation and development of application using TRNQL.

 <sub>There is java docs available in the TRNQL SDK which you can using a reference while developing.</sub>

 * To enable our application to use ```lib-release.aar``` we need to modify ```build.gradle``` which is created when you create Android Project.

 There are 2 ```build.gradle``` files so which one to modify?

 In current version of Android Studio, separate menu list is created for Gradle Script files. The file which we want to modify is ```build.gradle (Module:app)``` another ```build.gradle (Project:project_name )``` is for the whole project and not the application.

 If you do not see ```Gradle script``` listed differently, then the file which we would like to modify will is the ```build.gradle``` available in ```app/```  folder.

 * What to write in ```build.gradle``` ?

  After ```apply plugin: 'com.android.application'```
  place the following code in order to let application know where the ```lib-release.aar``` file is located. This also includes gradle dependencies for build script.

  ```
  repositories {
      jcenter()
      flatDir {
          dirs 'libs' //locate the lib-release.aar file in libs folder
      }
  }
  buildscript {
      repositories {
          jcenter()
      }
      dependencies {
          classpath 'com.android.tools.build:gradle:1.2.3'
      }
  }

  ```

  Now in the ```dependencies``` block located at the end of ```build.gradle``` file we need to add following dependencies in order to compile the TRNQL SDK code and use their packages.

  ```
  compile 'com.trnql:lib-release:1.0.0@aar' // only for the latest version Android Studio
  compile 'com.android.support:appcompat-v7:22.2.0'
  compile 'com.google.android.gms:play-services:7.5.0'

  ```

  * In order to use various services provided by the smart phones we need to define permissions for those services in ```app/src/main/AndroidManifest.xml```.

  Following are the permission that you need to add in ```app/src/main/AndroidManifest.xml``` before the end of ```</application>``` tag.

  ```
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
  <uses-permission android:name="com.google.android.gms.permission.ACTIVITY_RECOGNITION" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS" />
  <uses-permission android:name="android.permission.USE_CREDENTIALS" />
  <uses-permission android:name="android.permission.READ_PHONE_STATE" />
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  ```
  After defining permission that we need to use services, we also need to define which services we need to use.
  Following is the list of services that you can use using TRNQL sdk. Depending upon need we need to use the service.
  For eg. in the above above example ```trnql-sample-app``` I am only using Smart location so I only need to define that tag before the end of ```</application>``` tag.

  #### SmartLocation Service
  ```
  <service
         android:name="com.trnql.smart.location.SmartLocationService"
         android:label="TRNQL_SDK - SmartLocation Service" />
  ```

  #### SmartActivity Service
  ```
  <service
         android:name="com.trnql.smart.activity.SmartActivityService"
         android:label="TRNQL_SDK - SmartActivity Service" />
  ```
  #### Internet Recognition Service
  ```
  <service
         android:name="com.trnql.smart.activity.ActivityRecognitionIntentService"
         android:label="TRNQL_SDK - SmartActivity Intent Service for GMS" />
  ```
  #### SmartWeather Service
  ```
  <service
         android:name="com.trnql.smart.weather.SmartWeatherIntentService"
         android:label="TRNQL_SDK - SmartWeather Intent Service" />
  ```

  Inorder to use Google Play Services we need to add meta information about it, so we need to add following code in ```app/src/main/AndroidManifest.xml``` before end ```</application>``` tag.

  ```
  <meta-data
       android:name="com.google.android.gms.version"
       android:value="@integer/google_play_services_version" />
  ```

  Now we are all ready to develop application activities and required fragments.

  In the above application you can see how easy it is to get Location and address. If you are making fragment then you need to extends it at as ```SmartFragment``` or if as an activity then you need to extend it as ```SmartActivity```. Depending upon the class you extend you need to override few methods from Abstract Classes. For example in order to extends LocationFragement as SmartFragment, I had to override following methods.

  ```

      @Override
      public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
          View view = inflater.inflate(R.layout.fragment_location, container, false);
          location_latitude = (TextView) view.findViewById(R.id.location_latitude);
          location_longitude = (TextView) view.findViewById(R.id.location_longitude);
          location_address = (TextView) view.findViewById(R.id.location_address);
          location_accuracy = (TextView) view.findViewById(R.id.location_accuracy);
          return view;
      }
    ```
    ```
      @Override
      protected void smartLatLngChange(LocationEntry value) {
          location_latitude.setText(String.format("Latitude:   %s", String.valueOf(value.getLatitude())));
          location_longitude.setText(String.format("Longitude:   %s", String.valueOf(value.getLongitude())));
          location_accuracy.setText(String.format("Accuracy:   %s", String.valueOf(value.getAccuracy())));
      }
    ```
    ```
      @Override
      protected void smartAddressChange(AddressEntry value) {
          location_address.setText(String.format("Address:   %s", value.toString()));
      }
    ```
