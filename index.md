## Set up Ambeent library on Android

### Add the token to gradle.properties
```
 authToken= <YOUR-TOKEN>
```

**[Get Your Token](mailto:sales@ambeent.ai)**

### Then use authToken as the username in your build.gradle:
```gradle
allprojects {
    repositories {
        ...
        maven {
            url "https://jitpack.io"
            credentials { username <YOUR-TOKEN> }
        }
    }
}
```
### Edit your app build.gradle
1. Mind that your `minSDKVersion` should not be lower than `23`.
2. Add these lines into the `dependencies block;
```gradle
dependencies {        
 implementation 'com.gitlab.Ambeent.ambeent-android-sdk:ambeentutil:v1.7.4'
}
```
3. Add this lines into the android block;
```gradle
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

Add CleartextTraffic functionality to enable optimization feature for the Android 9 (API 28) and above

```xml
<application
      ...
      android:usesCleartextTraffic="true"
      ...>
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
Ambeent ambeentSdk = new Ambeent(getApplicationContext(),
                             companyId, 
                             customerId);
```
Call sense function of library to calculate fidelity and current channel and best channel values. First five parameters of sense function are boolean values for discover network, detect router model, streaming, traceRoute, speedtest. Need to enter username and password for login your router interface.If your username and password is default, you can pass blank. Last parameter is an interface for defining succes or failure situations of modem detection. Sense function returns an integer array:

```java
int[] output = ambeent.sense(boolean discoverNetwork, boolean measureSpeed, boolean detectRouterModel, boolean streaming, boolean traceRoute, boolean speedTest,  String username, String password,
      new Ambeent.AmbeentCallback() {
        
        //Brand and model are defined as supported
        @Override
        public void routerDetected(String brand, String model) {
            Log.e("AmbeentInterface", "routerDetected");
        }

        @Override
        public void onFailure(String brand, String model) {
            Log.e("AmbeentInterface", "onFailure");
        }

        @Override
        public void undefinedRouter(String brand, String model, String[] strings) {
            Log.e("AmbeentInterface", "undefinedRouter");
        }

      }
);

String fidelity = output[0];  
String bestChannel = output[1];
String currentChannel = output[2];
```
**Speed Test Function**
Call setSpeedTestValues function to set your own speed test values.

```java
ambeentSdk.setSpeedTestValues(long downloadSpeed, long uploadSpeed, long ping);
```

**Modem Rating**
```java
ambeentSdk.modemRating(callback: StatisticsInterface);
```
with a callback to get 
```java
ModemInfo(staMac: String, modemMac: String)
```
for the connected router

```java
try {
  getModemRating(new StatisticsInterface() {
  @Override public Void onModemRating(ModemRating modemRating) { return null; }
  @Override public Void onFailure() { return null; }
 });
}
catch (WiFiNotEnabledException e) { e.printStackTrace(); }
catch (WiFiConnectionNullException e) { e.printStackTrace(); }
catch (WiFiBssidNullException e) { e.printStackTrace(); }
catch (LocationNotGrantedException e) { e.printStackTrace(); }
catch (LocationServiceNotEnabledException e) { e.printStackTrace(); }
```
**Recommendation**

Call getRecommendation function of library to get recommendation of given mac adress. Last parameter is an interface for recommendation result.
```java
   ambeent.getRecommendation(String modemMac,new Ambeent.AmbeentRecommendation(){

        @Override
        public void recommendation(String recommendation) {
            Log.e("AmbeentRecommendation", "recommendation");
        }
        
        @Override
        public void failure(String message) {
            Log.e("AmbeentRecommendation", "failure");
        }
       }
   );
```
**Register Device**
```java
ambeentSdk.registerDevice(String null);
```
you can register client stations(mobile phones) to see their brand, model, os and varios information on Ambeent Dashboard.

Also, if you register, their Firebase token, you can send manual or automatic notifications through our dashboard.

**Optimization**
First make following initialization
```java
private LifecycleRegistry lifecycleRegistry;
..
lifecycleRegistry = new LifecycleRegistry(this);
lifecycleRegistry.markState(Lifecycle.State.CREATED); 
..
...
@Override
  public Lifecycle getLifecycle(){
    return lifecycleRegistry;
  }  
```
Need to enter username and password for login your router interface. If your username and password is default, you can pass blank.
See the sample optimization function below
```java
  public void optimize(){
    lifecycleRegistry.markState(Lifecycle.State.STARTED);

    // Handle each state of optimization with observing liveData
    MutableLiveData<String> liveData = new MutableLiveData<String>();
    Handler handler = new Handler(Looper.getMainLooper());
    handler.post(new Runnable() {
        public void run() {
            liveData.observe(AmbeentModule.this, new Observer<String>() {
                @Override
                public void onChanged(String s) {
                    Log.d("TAG",""+s);
                }
            });
        }
    });

    handler.post(new Runnable() {
        public void run() {
          ambeent.setChannel(
                  bestChannel
                  liveData,  
                  getApplicationContext(),
                  Brand,
                  Model,
                  username,
                  password);
          }
    });

  }  
```

### Exceptions
```java
Ambeent.LocationNotGrantedException
Ambeent.LocationServiceNotEnabledException
Ambeent.WiFiNotEnabledException
Ambeent.WiFiConnectionNullException
Ambeent.WiFiBssidNullException
```

### Known Issues

You should use 16.0.8 android service version for Google Play Services library.
```gradle
implementation 'com.google.android.gms:play-services-analytics:16.0.8'
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

Having trouble with Ambeent Android SDK? **[Contact us](mailto:sales@ambeent.ai)** and we’ll help you sort it out.
