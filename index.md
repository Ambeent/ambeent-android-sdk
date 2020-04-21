## Set up Ambeent library on Android

### Add the token to gradle.properties
```
 authToken=<TOKEN>
```

### Then use authToken as the username in your build.gradle:
```
authToken=<TOKEN>
### Then use authToken as the username in your build.gradle:```allprojects {
    repositories {
        ...
        maven {
            url "https://jitpack.io"
            credentials { username authToken }
        }
    }
}
```
### Edit your app build.gradle
1. Set the `minSDKVersion` to `16`.
2. Add these lines into the `dependencies block;
```xml
dependencies {        
 Implementation 'com.gitlab.Ambeent.ambeent-android-sdk:ambeentutil:v0.2'
}
```
3. Add this lines into the android block;
```xml
android {
...
 compileOptions { 
   sourceCompatibility JavaVersion.VERSION_1_8
   targetCompatibility JavaVersion.VERSION_1_8
 }
...
}
```
4. Finally clean and rebuild the project.

### Edit your app manifest
Following permissions are required for the full functionality of the library.
Add following statements into AndroidManifest.xml, just before <application ...tag.

```xml
<uses-permission android:name="android.permission.ACTIVITY_RECOGNITION">
</uses-permission>
<uses-permission android:name="android.permission.INTERNET">
</uses-permission>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION">
</uses-permission>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION">
</uses-permission>
<uses-permission android:name="android.permission.ACTIVITY_RECOGNITION">
</uses-permission>
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE">
</uses-permission>
<uses-permission android:name="android.permission.WAKE_LOCK">
</uses-permission>
```

### Location Permission
Following method can be used to ask user give your app location permission. Define it anywhere:

```java
public boolean checkLocationPermission() {
  if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED) {  
   AlertDialog.Builder builder;
   builder = new AlertDialog.Builder(this);
   builder.setTitle("Wifi Location Access Required")
   .setCancelable(false)
   .setMessage("For the full functionality, please give permission.")
   .setNeutralButton("OK", new DialogInterface.OnClickListener() {
    public void onClick(DialogInterface dialog, int which) {
     ActivityCompat.requestPermissions(MainActivity.this,
       new String[]{Manifest.permission.ACCESS_FINE_LOCATION},    56);
   }
 })
   .show();
   return false;
 }else return true;      
}
```

In order to ensure user has given the access, it is a good idea to call this function before using the library, preferably onstart of the related activity/fragment
```java
checkLocationPermission();
```

### Integration Example

Define library constructor with app context, company id and customer id values.
Company Id generated for `<COMPANYNAME>` is `<COMPANYID>`
Customer Id can be anything to distinguish user, like user’s app id, user id, firebase token, for you to match user’s metrics, or can be left empty.
```java
Ambeent ambeent = new Ambeent(getApplicationContext(),
                             companyId, 
                             customerId);
```
Call sense function of library to calculate fidelity and current channel and best channel values. First three parameters of sense function are boolean values for discover network, measure speed and detect router model. Last parameter is an interface for defining succes or failure situations of modem detection. Sense function returns an integer array:

```java
int[] output = ambeent.sense(boolean discoverNetwork, boolean measureSpeed, boolean detectRouterModel);

String fidelity = output[0];  
String bestChannel = output[1];
String currentChannel = output[2];
ambeent.traceRoute("www.google.com")
```

### Exceptions
Sense method throw following exceptions:
```java
Ambeent.LocationNotGrantedException
Ambeent.LocationServiceNotEnabledException
Ambeent.WiFiNotEnabledException
Ambeent.WiFiConnectionNullException
Ambeent.WiFiBssidNullException
```

### Known Issues

You should use 16.0.8 android service version for Google Play Services library.
```java
Implementation 'com.google.android.gms:play-services-analytics:16.0.8'
```
Location is need to be checked. If it is disabled then you may want to ask user to enable it with following function.

```java
Context context;
public boolean isLocationServiceEnabled(Context context){
 LocationManager locationManager = null;
 boolean gps_enabled == false, network_enabled = false;
 if (locationManager == null )
  locationManager = (LocationManager)context.getSystemService(Context.LOCATION_SERVICE);
try {
gps_enabled = locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER);
}catch (Exception ex){
    Log.e(TAG, ex);
}
try {
   network_enabled = locationManager.isProviderEnabled(LocationManager.
      NETWORK_PROVIDER);
}catch (Exception ex){
    Log.e(TAG, ex);
}
return gps_enabled | | network_enabled;   
}
```

### Support or Contact

Having trouble with Ambeent Android SDK? Contact us via **sales@ambeent.ai** and we’ll help you sort it out.
