

## Introduction:

Android 9 (Android Pie) introduced new background restrictions to improve battery life and overall system performance. These restrictions limit the ability of apps to run in the background and execute background tasks, which helps prevent apps from draining the device's resources and negatively impacting its performance.

## Background Limitations in Android 9:

- **Background Execution Limits:** Android 9 enforces strict limitations on background execution. Apps running in the background are subject to more aggressive restrictions on background processes and background services, which may result in limitations to the resources available for background tasks.

- **Background Location Access:** Android 9 restricts access to location updates in the background, preventing apps from continuously tracking a user's location without their knowledge or consent. This restriction helps protect user privacy and conserve battery life.

- **JobScheduler Restrictions:** Android 9 introduces limitations on the JobScheduler API, which apps use to schedule background tasks. The new restrictions limit the frequency and execution time of these scheduled tasks, making it more challenging for apps to run tasks in the background.

## Recommended Approach for Background Functionality:

To ensure that apps can continue to function properly with background functionality on Android 9 devices, developers can consider the following approaches:

- **WorkManager API:** For non-continuous background tasks, developers can use the WorkManager API introduced in Android 9. The WorkManager API allows scheduling tasks that need to be executed in the background under the constraints and limitations imposed by the system. This helps optimize background task execution based on system conditions like device charging status and network connectivity.

- **Foreground Service:** For use cases requiring continuous background execution and data consistency, implementing the functionality as a Foreground Service is recommended. A Foreground Service runs in the background with a visible notification to the user, indicating that the app is performing a task. By using a Foreground Service, an app can have a higher priority and is less likely to be terminated by the system due to background restrictions, ensuring uninterrupted operation.

## Testing Background Functionality:

If your app relies on background processes or services for smooth execution and data consistency, it is essential to conduct thorough testing. Ensure that your tests encompass different device configurations and scenarios, both with and without the SYNC option enabled.

## SYNC Option and Background Tests:

If your app implements tests using ASYNC functionality and you find that they run smoothly with data consistency in the background, it's important to consider the impact of the SYNC option on your app's behavior. The SYNC option in Android allows apps to synchronize data and execute background tasks even when the device is under background restrictions or in a power-saving mode.

For the successful execution of your background tests, it is crucial to inform users or testers about the significance of enabling the SYNC option on their Android devices. By having the SYNC option turned on, your app can temporarily bypass certain background limitations and ensure that scheduled sync tasks or specific background operations run as intended.

## Background Execution Optimization

To ensure smooth and efficient background execution, we have implemented several strategies:

1. **Async Test Execution:** All tests have been updated to run asynchronously. This adjustment has proven successful in allowing tasks to execute seamlessly in the background.

2. **Access to Parameters on Application Start:** We have obtained access to specific parameters required for background tasks right at the application's startup. This approach ensures that essential data is available when background operations commence.

3. **Custom Background Threads and Routines:** We explored various background-related threads and routines to identify the most efficient method for handling background tasks. Through rigorous testing, we have optimized the selection of threads and routines to achieve optimal performance.

4. **Foreground Services and Workers:** We experimented with both foreground services and background workers to maintain smooth execution in the background. After comprehensive testing, we have determined the most suitable approach for different scenarios.

5. **Demo Application for Testing:** To validate the effectiveness of our background optimizations, we developed a dedicated demo application. This application allows us to thoroughly test and fine-tune background tests, foreground services, and background services to ensure smooth functionality.

Our continuous efforts to enhance background execution aim to provide users with a seamless experience while optimizing battery usage and overall system performance. Through thorough testing and optimization, we strive to deliver a robust background execution solution tailored to our application's needs.




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
 implementation 'com.gitlab.Ambeent.ambeent-android-sdk:ambeentutil:v0.2.32'
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
## **Example Code**

```markdown
# MainFragment Documentation

## Overview

The `MainFragment` is a Kotlin class representing a fragment in an Android application. This fragment is responsible for displaying the main user interface and executing various tests related to network and location services. The fragment provides checkboxes and buttons to enable or disable specific tests, and it shows the progress of ongoing tests.

## Table of Contents

1. [Imports](#imports)
2. [Constants](#constants)
3. [Class Definition](#class-definition)
   - [Properties](#properties)
   - [Methods](#methods)
4. [Initialization](#initialization)
5. [Lifecycle](#lifecycle)
6. [Permissions Handling](#permissions-handling)
7. [Conclusion](#conclusion)

<a name="imports"></a>
## Imports

```kotlin
// List of required imports

package net.ambeent.sdk.android.ui.main

import android.Manifest
import android.app.Activity
import android.content.Context
import android.content.pm.PackageManager
import android.location.Geocoder
import android.location.Location
import android.location.LocationManager
import android.location.LocationRequest
import android.net.wifi.WifiManager
import android.os.Bundle
import android.provider.Settings
import android.telephony.TelephonyManager
import android.util.Log
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.Button
import android.widget.CheckBox
import android.widget.ProgressBar
import android.widget.TextView
import androidx.appcompat.widget.SwitchCompat
import androidx.core.app.ActivityCompat
import androidx.core.content.ContextCompat
import androidx.fragment.app.Fragment
import androidx.lifecycle.Observer
import androidx.lifecycle.ViewModelProvider
import com.google.android.gms.location.FusedLocationProviderClient
import com.google.android.gms.location.LocationCallback
import com.google.android.gms.location.LocationServices
import com.google.android.material.snackbar.Snackbar
import com.wireless.ambeentutil.Ambeent.RegisterDeviceCallback
import com.wireless.ambeentutil.Ambeent.getConnectionBSSID
import com.wireless.ambeentutil.AmbeentDeviceId
import com.wireless.ambeentutil.AmbeentExceptions
import com.wireless.ambeentutil.ambpkg.AmbBuilder
import com.wireless.ambeentutil.ambpkg.responseType.Failure
import com.wireless.ambeentutil.ambpkg.responseType.InProgress
import com.wireless.ambeentutil.ambpkg.responseType.MessageType
import com.wireless.ambeentutil.ambpkg.responseType.Success
import net.ambeent.sdk.android.App.Companion.ambeentSdk
import net.ambeent.sdk.android.R
```

The class `MainFragment` imports various classes and libraries required for implementing the functionalities.

<a name="constants"></a>
## Constants

```kotlin
const val PERMISSION_LOCATION_SERVICES = 100
```

`PERMISSION_LOCATION_SERVICES` is a constant representing the permission code used for requesting location services permission.

<a name="class-definition"></a>
## Class Definition

```kotlin
class MainFragment : Fragment() {
```

The class `MainFragment` is defined as a subclass of `Fragment`.

<a name="properties"></a>
### Properties

```kotlin
    private val PUBLIC_TESTING_COMPANY_ID = "3c7b06b3-6acc-4245-bcd2-d0b0dd7baf51"
    // Other public testing company IDs are commented out
    private val PUBLIC_TESTING_CUSTOMER_ID = "Milleni@v0.2.29"

    // ... (other properties)
```

The class contains private properties `PUBLIC_TESTING_COMPANY_ID` and `PUBLIC_TESTING_CUSTOMER_ID`, representing the company and customer IDs used for testing.

```kotlin
    private lateinit var viewModel: MainViewModel
    private lateinit var builder: AmbBuilder
    private lateinit var locationSwitch: SwitchCompat
    private lateinit var testRouterModelButton: CheckBox
    private lateinit var testTraceRouteButton: CheckBox
    private lateinit var testNetworkDiscoveryButton: CheckBox
    private lateinit var progressTextField: TextView
    private lateinit var progressBar: ProgressBar
    private lateinit var testWifiScanButton: CheckBox
    private lateinit var upload_download_test: CheckBox
    private lateinit var executeTestsButton: Button
    private lateinit var wifiManager: WifiManager
    private lateinit var applicationContext: Context
    private lateinit var telephony: TelephonyManager
    private lateinit var geocoder: Geocoder
    private lateinit var fusedLocationClient: FusedLocationProviderClient
    private lateinit var locationRequest: LocationRequest
    private lateinit var locationCallback: LocationCallback
    private lateinit var currentLocation: Location
    private var isRequestingLocationUpdates = false
    private lateinit var modemMac: String
```

The class defines various properties to reference UI components and other services like Wi-Fi, telephony, geocoder, and location services.

<a name="methods"></a>
### Methods

```kotlin
    private fun isLocationPermissionGranted(): Boolean {
        // Function to check if the location permission is granted
    }

    fun getDeviceId(context: Context): String {
        // Function to get the Android device ID
    }

    private fun locationEnabled(): Boolean {
        // Function to check if location services are enabled
    }
```

These methods are utility functions to check location permissions and availability of location services.

```kotlin
    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
    ): View {
        // Function to inflate the fragment's layout
    }
```

This method is overridden to inflate the fragment's layout.

```kotlin
    fun initializeUI(ambeentDeviceId: AmbeentDeviceId, location: Location): Boolean {
        // Function to initialize the UI components and set up the tests

        // ... (implementation)

        return true
    }
```

This method is responsible for initializing the UI components and setting up the tests based on the provided `AmbeentDeviceId` and `Location`.

```kotlin
    override fun onActivityCreated(savedInstanceState: Bundle?) {
        // Function called when the activity is created

        // ... (implementation)
    }

    override fun onResume() {
        // Function called when the fragment is resumed

        // ... (implementation)
    }

    override fun onRequestPermissionsResult(
        requestCode: Int, permissions: Array<String>, grantResults: IntArray
    ) {
        // Function to handle permission request results

        // ... (implementation)
    }
```

These methods are overridden to handle the activity's lifecycle events and permissions results.

<a name="initialization"></a>
## Initialization

To use the `MainFragment` in your Android application, follow these steps:

1. Include the required imports in your project.

2. Create an instance of the `MainFragment` in your activity or fragment.

3. Optionally, pass the necessary parameters such as the `AmbeentDeviceId` and `Location` to the `initializeUI` method of the `MainFragment`.

4. Add the `MainFragment` to your activity or fragment using the `FragmentManager`.

<a name="lifecycle"></a>
## Lifecycle

The `MainFragment` follows the standard Android fragment lifecycle. The key lifecycle methods are:

- `onCreateView`: Inflates the fragment's layout.

- `onActivityCreated`: Called when the activity that

 hosts the fragment is created. It initializes the UI components and sets up tests.

- `onResume`: Called when the fragment is resumed. It checks for location permissions and updates the UI accordingly.

<a name="permissions-handling"></a>
## Permissions Handling

The `MainFragment` requests location services permission using `PERMISSION_LOCATION_SERVICES` code. It handles the result of permission requests in the `onRequestPermissionsResult` method.

<a name="conclusion"></a>
## Conclusion

The `MainFragment` class is an essential part of the Android application and provides a user interface for executing network and location-based tests. It initializes the UI components, checks for required permissions, and enables the user to trigger various tests. The results of these tests are shown to the user using progress bars and messages. The class relies on other classes and libraries, such as `AmbeentDeviceId`, `AmbeentExceptions`, and `AmbBuilder`, to perform the tests and communicate with the Ambeent SDK.

This documentation helps developers understand the purpose and functionality of the `MainFragment` class, allowing them to use and modify it effectively in their projects.
```

Please note that in this documentation, I have provided a detailed explanation of the code structure, the purpose of each method, and the usage of the class. Additionally, I have included sections on initialization, lifecycle, permissions handling, and a conclusion to summarize the class's role and importance in an Android application. Feel free to modify or expand this documentation based on your specific needs.

## **Support or Contact**

Having trouble with Ambeent Android SDK? **[Contact us](mailto:sales@ambeent.ai)** and we’ll help you sort it out.
