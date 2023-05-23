***Note: The [`AMBEENTZERO`](https://gitlab.com/Ambeent/ambeent-android-sdk/-/tree/AMBEENTZERO) Branch will be used until the SDK is deployed into the GitLab packages.***
## Set up Ambeent library for Android

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
 implementation 'com.gitlab.Ambeent.ambeent-android-sdk:ambeentutil:v1.10.1'
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
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE">
</uses-permission>
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE">
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

## Requesting Location Permission

To enable location-based features in our application, we need to request the user's permission to access their location. Here's how you can ask the user to grant location permission in your application:

```java
// Check if location permission is granted
private fun isLocationPermissionGranted(): Boolean {
    val permissionStatus = ContextCompat.checkSelfPermission(
        requireContext().applicationContext, Manifest.permission.ACCESS_FINE_LOCATION
    )
    return permissionStatus == PackageManager.PERMISSION_GRANTED
}

// Request location permission
private fun requestLocationPermission() {
    ActivityCompat.requestPermissions(
        requireActivity(),
        arrayOf(
            Manifest.permission.ACCESS_FINE_LOCATION,
            Manifest.permission.ACCESS_COARSE_LOCATION
        ),
        PERMISSION_LOCATION_SERVICES
    )
}

// Handle permission request result
override fun onRequestPermissionsResult(
    requestCode: Int,
    permissions: Array<String>,
    grantResults: IntArray
) {
    when (requestCode) {
        PERMISSION_LOCATION_SERVICES -> {
            if (grantResults.isNotEmpty() && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                // Location permission granted
                // Perform your location-based operations here
            } else {
                // Location permission denied
                // Handle the case where the user denied location permission
            }
        }
        // Handle other permission request codes if any
    }
} 
```

In your code snippet, you can use the isLocationPermissionGranted() function to check if the location permission is already granted. If not, you can call the requestLocationPermission() function to request the permission from the user. The onRequestPermissionsResult() method will handle the result of the permission request.

Note: Make sure to add the necessary permissions to your AndroidManifest.xml file.



### Integration Example
Import: Add following import statemetents
```java
import com.wireless.ambeentutil.Ambeent;
#AmbeentExceptions required on version v1.9.5 and afterwards
import com.wireless.ambeentutil.AmbeentExceptions;
 
```

Initialize with app context, company id.

```java

    ambeentSdk.registerDevice(getDeviceId(applicationContext),
        PUBLIC_TESTING_COMPANY_ID, //The companyID to be used by the company using the SDK
        object : RegisterDeviceCallback {
            override fun onDeviceRegistered(deviceId: String): String? {
                testRouterModelButton?.isEnabled = true
                testTraceRouteButton?.isEnabled = true
                testNetworkDiscoveryButton?.isEnabled = true
                testWifiScanButton?.isEnabled = true
                upload_download_test?.isEnabled = true
                initializeUI(AmbeentDeviceId(deviceId))
                Log.e("TAG", "onDeviceRegistered: The device id is not null $deviceId")

                return deviceId
            }

            override fun onDeviceRegistrationFailed(t: Throwable) {
                Log.e("TAG", "onDeviceRegistrationFailed: " + t.cause + "  " + t.message)
                // handle error
                //
            }
        })



```
In the given code snippet, the lines:
```kotlin
testRouterModelButton?.isEnabled = true
testTraceRouteButton?.isEnabled = true
testNetworkDiscoveryButton?.isEnabled = true
testWifiScanButton?.isEnabled = true
upload_download_test?.isEnabled = true
```
are enabling the UI buttons. These buttons were previously disabled, and now they are being enabled. This means that the user can interact with these buttons and trigger the corresponding actions associated with them.

The purpose of disabling these buttons initially is to prevent the user from interacting with them until the device receives its device ID from the server.

Once the ***device registration is successful***, and the onDeviceRegistered callback is triggered, the device ID is obtained. At this point, the UI buttons are enabled using the code mentioned above. This ensures that the user can only interact with these buttons after the device registration is complete and the device ID is available.


The initializeUI function is also called, passing the obtained device ID as a parameter. 

If the ***device registration fails***, the onDeviceRegistrationFailed callback is triggered
In the onDeviceRegistrationFailed method, the Throwable object t represents the exception or error that occurred during the device registration process. Based on the provided code snippet, there are a few potential reasons why the device registration could fail, such as:

>1. ***No Internet Connection:*** If the device does not have an active internet connection, the registration process will fail. This could be due to network connectivity issues, Wi-Fi or cellular data being turned off, or the device being in an area with no network coverage.

>2. ***Missing Company ID:*** The PUBLIC_TESTING_COMPANY_ID is used as a parameter in the ambeentSdk.registerDevice method. If this company ID is missing or not provided correctly, it can result in a failed registration. The company ID is likely required by the server to associate the device with the correct company or organization.

>3. ***Server Issues:*** There could be server-side problems that prevent the device registration from completing successfully. This could include server downtime, maintenance, or other issues on the server that affect the registration process.



```markdown
Create an instance of `AmbBuilder` and initialize it with the required parameters:
```

```java
builder = AmbBuilder(
    ambeentDeviceId, // Parameter: ambeentDeviceId (Type: AmbeentDeviceId)
    LocationServices.getFusedLocationProviderClient(requireContext().applicationContext), // Parameter: FusedLocationProviderClient (Type: FusedLocationProviderClient)
    wifiManager, // Parameter: wifiManager (Type: WifiManager)
    applicationContext, // Parameter: applicationContext (Type: Context)
    telephony, // Parameter: telephony (Type: TelephonyManager)
    geocoder // Parameter: geocoder (Type: Geocoder)
)

```
> 1. ***ambeentDeviceId (Type: AmbeentDeviceId):*** This parameter represents the device ID specific to the Ambeent SDK 

>2. ***LocationServices.getFusedLocationProviderClient(requireContext().applicationContext) (Type: FusedLocationProviderClient):*** This parameter is a FusedLocationProviderClient instance obtained from the LocationServices API. It is used to access the device's fused location provider, which provides a simplified interface for retrieving the device's location.

>3. ***wifiManager (Type: WifiManager):*** This parameter represents a WifiManager instance. It is used to manage Wi-Fi connectivity on the device, including accessing information about available Wi-Fi networks, connecting to a specific network, or configuring Wi-Fi settings.

>4. ***applicationContext (Type: Context):*** This parameter represents the application's context. It is used to access various resources and services provided by the Android framework, such as accessing system services or retrieving application-specific information.

>5. ***telephony (Type: TelephonyManager):*** This parameter represents a TelephonyManager instance. It is used to access telephony-related information and services on the device, such as retrieving network operator details, signal strength, or call state.

>6. ***geocoder (Type: Geocoder):*** This parameter represents a Geocoder instance. It is used for converting between geographic coordinates (latitude and longitude) and human-readable addresses or place names. It enables geocoding (address to coordinates) and reverse geocoding (coordinates to address) functionality.



Set the desired test options by toggling the checkboxes and calling the appropriate builder methods:

```java
testRouterModelButton?.setOnClickListener {
    if (testRouterModelButton.isChecked) {
        builder.setDetectRouterModel(true)
    } else {
        builder.setDetectRouterModel(false)
    }
    // ...
}

testTraceRouteButton?.setOnClickListener {
    if (testTraceRouteButton.isChecked) {
        builder.setTraceRoute(true)
    } else {
        builder.setTraceRoute(false)
    }
    // ...
}

testNetworkDiscoveryButton?.setOnClickListener {
    if (testNetworkDiscoveryButton.isChecked) {
        builder.setDiscoverNetwork(true)
    } else {
        builder.setDiscoverNetwork(false)
    }
    // ...
}

testWifiScanButton?.setOnClickListener {
    if (testWifiScanButton.isChecked) {
        builder.isTestWifi = true
    } else {
        builder.isTestWifi = false
    }
    // ...
}

upload_download_test?.setOnClickListener {
    if (upload_download_test.isChecked) {
        builder.setSpeedTest(true)
    } else {
        builder.setSpeedTest(false)
    }
    // ...
}


```
The code snippet contains click listeners for different buttons. Each click listener checks the checked status of its associated button and performs a specific action using the `builder` object. These actions include enabling or disabling certain functionalities such as detecting router models, performing trace routes, discovering networks, testing Wi-Fi, and conducting speed tests. The specific action taken depends on whether the button is checked or not.


Finally, execute the tests by calling the executeTest() method on the builder:

```java
executeTestsButton?.setOnClickListener {
    // Check if location services are enabled
    if (!locationEnabled()) {
        executeTestsButton.isEnabled = false
        val snackBar = Snackbar.make(
            requireView(), "Please enable location services", Snackbar.LENGTH_LONG
        )
        snackBar.show()
        return@setOnClickListener
    }
    
    // Check if location permission is granted
    if (!isLocationPermissionGranted()) {
        val snackBar = Snackbar.make(
            requireView(), "Please grant location permission", Snackbar.LENGTH_LONG
        )
        snackBar.show()
        return@setOnClickListener
    }
    
    // Build and execute the tests
    val testRunner = builder.build()
    progressTextField.visibility = View.VISIBLE
    progressBar.visibility = View.VISIBLE
    var thread = testRunner.executeTest { msg, data ->
        // Handle different message types (FAILURE, INPROGRESS, SUCCESS)
        when (msg) {
            MessageType.FAILURE -> {
                // Handle failure response
                if (data is Failure) {
                    // Enable/disable buttons, show error message, etc.
                    progressBar.visibility = View.GONE
                    // ...
                }
            }
            
            MessageType.INPROGRESS -> {
                // Handle in-progress response
                if (data is InProgress) {
                    // Disable buttons, update progress, etc.
                    progressBar.visibility = View.VISIBLE
                    progressTextField.text = """
                        ${data.status}
                        ${data.currentTest}
                        ${data.completedTests}/${data.totalTests}
                    """.trimIndent()
                }
            }
            
            MessageType.SUCCESS -> {
                // Handle success response
                if (data is Success) {
                    // Enable buttons, show success message, etc.
                    progressBar.visibility = View.GONE
                    // ...
                }
            }
        }
    }
}

```
The provided code snippet sets a click listener for the `executeTestsButton`. Here's a brief explanation of what the code does:

- It first checks if location services are enabled. If not, it disables the `executeTestsButton`, displays a snackbar message requesting the user to enable location services, and returns from the click listener.

- It then checks if location permission is granted. If not, it displays a snackbar message requesting the user to grant location permission and returns from the click listener.

- If both location services are enabled and permission is granted, it proceeds to build and execute the tests using the `builder` object.

- During the test execution, the visibility of `progressTextField` and `progressBar` is set to visible.

- A thread is created to execute the tests, and within this thread, different message types (FAILURE, INPROGRESS, SUCCESS) are handled.

- For FAILURE messages, appropriate actions can be taken, such as updating the UI to show error messages and hiding the progress bar.

- For INPROGRESS messages, the UI can be updated to show the current test status, progress, and the number of completed tests out of the total.

- For SUCCESS messages, the UI can be updated to show success messages, enable buttons, and hide the progress bar.

Overall, this code snippet handles the button click event, performs pre-checks related to location services and permissions, executes tests asynchronously, and updates the UI based on different message types received during the test execution.





## **Known Issues**

You should use 16.0.8 android service version for Google Play Services library.
```gradle
implementation 'com.google.android.gms:play-services-analytics:16.0.8'
```


## **Support or Contact**

Having trouble with Ambeent Android SDK? **[Contact us](mailto:sales@ambeent.ai)** and we’ll help you sort it out.
