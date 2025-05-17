\newpage
\subsection{9. mobile malware development trick. Android permissions. Simple Android (Java/Kotlin) example.}

﷽

![malware](./images/9/2025-05-17_13-06.png){height=400px}    

In this section, we will discuss how to handle permissions in Android, particularly focusing on the `CAMERA` permission using modern `ActivityResultContracts`. Permissions are a crucial aspect of Android security, as they allow or deny access to sensitive resources like the camera, microphone, or location services.     

Let's dive into the code example based on the Camera permission request to understand how this works in Android applications.     

### permissions in Android

In Android, permissions are used to restrict access to system resources that might affect user privacy or system stability. For example, the `CAMERA` permission allows an app to access the device's camera hardware.      

There are two main types of permissions in Android:

- *Normal Permissions* - these do not affect user privacy and are automatically granted by the system (e.g., `INTERNET`)      

- *Dangerous Permissions* - these could affect the user’s privacy or security, so they must be explicitly granted by the user (e.g., `CAMERA`, `LOCATION`, `READ_CONTACTS`).      

So few words about modern permissions handling in Android (ActivityResultContracts). Android has shifted to a more modern way of handling permissions using the [ActivityResultContracts API](https://developer.android.com/reference/androidx/activity/result/contract/ActivityResultContracts). This allows for cleaner and more flexible management of permissions. In this example, we'll use ActivityResultContracts.RequestPermission to request and handle the `CAMERA` permission.     

### practical example

Let me show you simple example code: handling camera permission request in Android.     

Your project looks like there (`HackCam`):     

![malware](./images/9/2025-05-17_13-12.png){width="80%"}       

How it works? First of all checking permission logic: when the app starts, the `checkCameraPermission()` function is called:     

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    checkCameraPermission()  // check permission when the app starts

    cameraButton = findViewById(R.id.camButton)  // button to trigger camera permission check

    cameraButton.setOnClickListener {
        checkCameraPermission()  // check permission on button click
    }
}
```

If the `CAMERA` permission is already granted, the app will show a Toast saying that the permission is granted.      

If the `CAMERA` permission is not granted, it will request the permission from the user using `requestPermissionLauncher.launch()`.     

```kotlin
private fun checkCameraPermission() {
    when {
        ContextCompat.checkSelfPermission(this, android.Manifest.permission.CAMERA) == PackageManager.PERMISSION_GRANTED -> {
            // permission is already granted
            Toast.makeText(this, "Hack Camera permission already granted", Toast.LENGTH_SHORT).show()
        }
        else -> {
            // request permission if not granted
            requestPermissionLauncher.launch(android.Manifest.permission.CAMERA)
        }
    }
}
```

Checking Permission with `ContextCompat` logic: `ContextCompat.checkSelfPermission` checks if the app already has permission to access the camera. If the permission is granted, the app can proceed with using the camera. If the permission is not granted, we request it using the `ActivityResultLauncher`:     

```kotlin
private val requestPermissionLauncher = registerForActivityResult(
    ActivityResultContracts.RequestPermission()) { isGranted: Boolean ->
        if (isGranted) {
            Toast.makeText(this, "Hack Camera permission granted", Toast.LENGTH_SHORT).show()
        } else {
            Toast.makeText(this, "Hack Camera permission denied", Toast.LENGTH_SHORT).show()
        }
}
```

The `cameraButton` can be clicked multiple times to trigger the permission check again.     

### why use ActivityResultContracts?

In previous Android versions, permissions were requested and handled using `requestPermissions()` and `onRequestPermissionsResult()`. However, with `ActivityResultContracts`, Android has introduced a more streamlined and modern approach for managing permissions.      

Benefits of ActivityResultContracts:

*Cleaner Code:* - the API provides a more straightforward way of handling permission requests and results.    

*Separation of Concerns:* - the result handler is separated from the logic that triggers the permission request.     

*Flexible:* - you can handle permissions for different types of requests (e.g., location, camera, etc.) using different contracts.    

But in future examples i will use third party kotlin libraries (from github also) to request permissions.     

Full source code looks like this:    

```kotlin
package com.example.hackcam

import android.content.pm.PackageManager
import android.os.Bundle
import android.widget.Button
import android.widget.Toast
import androidx.activity.ComponentActivity
import androidx.activity.result.contract.ActivityResultContracts
import androidx.core.content.ContextCompat

class MainActivity : ComponentActivity() {
    private lateinit var cameraButton: Button

    // activity result launcher to request permission
    private val requestPermissionLauncher = registerForActivityResult (
        ActivityResultContracts.RequestPermission()) {isGranted: Boolean ->
            if (isGranted) {
                Toast.makeText(this, "Hack Camera permission granted", Toast.LENGTH_SHORT).show()
            } else {
                Toast.makeText(this, "Hack Camera permission denied", Toast.LENGTH_SHORT).show()
            }
    }

    // checking cam permission
    private fun checkCameraPermission() {
        when {
            ContextCompat.checkSelfPermission(this, android.Manifest.permission.CAMERA) == PackageManager.PERMISSION_GRANTED -> {
                // permission is granted
                Toast.makeText(this, "Hack Camera permission already granted", Toast.LENGTH_SHORT).show()
            } else -> {
                requestPermissionLauncher.launch(android.Manifest.permission.CAMERA)
            }
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        checkCameraPermission()
        cameraButton = findViewById(R.id.camButton)

        cameraButton.setOnClickListener {
            checkCameraPermission()
        }
    }
}
```

To test this example, we can install on the real device or just use virtual device:     

![malware](./images/9/2025-05-17_13-05.png){width="80%"}       

![malware](./images/9/2025-05-17_13-36.png){width="80%"}       

### demo

Install the app on an Android device:     

![malware](./images/9/2025-05-17_13-35.png){height="30%"}       

When the app starts, it will automatically check if the `CAMERA` permission is granted:     

![malware](./images/9/2025-05-17_13-06.png){width="80%"}       

If not granted, it will prompt the user to grant permission.      

Clicking the `Meow` button will recheck the permission status and notify the user if the permission is granted or denied:     

![malware](./images/9/2025-05-17_13-09.png){height="30%"}       

Managing permissions in Android is a crucial part of building secure and user-friendly applications. The new `ActivityResultContracts` API simplifies the process of requesting and handling permissions. In this example, we showed how to request the `CAMERA` permission, handle the result, and provide feedback to the user with Toast messages. This approach works seamlessly with modern Android development, ensuring a better user experience and cleaner code.     

In the following simple examples I will show how attackers can steal data from a user's device with their consent.     

I hope this section with practical example is useful for entry level Android software engineers.    

[ActivityResultContracts API](https://developer.android.com/reference/androidx/activity/result/contract/ActivityResultContracts)     