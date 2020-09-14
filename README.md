# Simple Camera by CameraX

## premise

**API minimum version**

Android API minimum version is `21` (`android 5 lollipop`)

**Java8 compile**

CameraX 使用到部分Java8函數庫，需要設置Java8 compile。

在app的build.gradle中的`android` 結構下新增Java8 compile設定

```
android {
    ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ...
}
```

**dependencies library**

在app的build.gradle中dependencies新增以下相關依賴

| 依賴函式庫                         | 說明|
| -------------------------------- | ------------------------------------------------- |
| androidx.camera:camera-core      | CameraX core library                              |
| androidx.camera:camera-camera    | CameraX core library using camera2 implementation |
| androidx.camera:camera-lifecycle | CameraX Lifecycle Library                         |
| androidx.camera:camera-view      | CameraX View class                                |

```
dependencies {
    implementation "androidx.camera:camera-core:1.0.0-beta08"
    implementation "androidx.camera:camera-camera2:1.0.0-beta08"
    implementation "androidx.camera:camera-lifecycle:1.0.0-beta08"
    implementation "androidx.camera:camera-view:1.0.0-alpha15"
}
```

## Permission

```

<manifest xmlns:android="http://schemas.android.com/apk/res/android" ...>
    <uses-feature android:name="android.hardware.camera.any" />

    <uses-permission android:name="android.permission.CAMERA" />

</manifest>
```

**權限授權**

```
private fun checkPermissions() = REQUIRED_PERMISSIONS.all {
    ContextCompat.checkSelfPermission(this, it) == PackageManager.PERMISSION_GRANTED
}

```

**權限要求**

```
ActivityCompat.requestPermissions(this, REQUIRED_PERMISSIONS, REQUEST_CODE_PERMISSIONS)
```

```

**權限檢查**
 if (requestCode == REQUEST_CODE_PERMISSIONS) {

 } else {

 }
 ```

## Camera Provider

```
val cameraProviderFuture = ProcessCameraProvider.getInstance(this)

cameraProviderFuture.addListener(Runnable {
    

    // Preview
    val preview = Preview.Builder()
        .build()
        .also {
            it.setSurfaceProvider(viewFinder.createSurfaceProvider())
        }

    val cameraSelector = CameraSelector.DEFAULT_BACK_CAMERA // Select back camera

    try {
         // Used to bind the lifecycle of cameras to the lifecycle owner
        val cameraProvider: ProcessCameraProvider = cameraProviderFuture.get()
        cameraProvider.unbindAll() // Unbind use cases before rebinding
        cameraProvider.bindToLifecycle(this, cameraSelector, preview) // Bind use cases to camera
    } catch(exc: Exception) {
        // TODO ...
    }

   }, ContextCompat.getMainExecutor(this))
```

初始化Preview

```
val preview = Preview.Builder()
    .build()
    .also {
        it.setSurfaceProvider(viewFinder.createSurfaceProvider())
    }
```

預設鏡頭設置
```
val cameraSelector = CameraSelector.DEFAULT_BACK_CAMERA 預設為後鏡頭
// val cameraSelector = CameraSelector.DEFAULT_BACK_CAMERA 預設為前鏡頭
```

相關設置綁定到ProcessCameraProvider元件上

```
val cameraProvider: ProcessCameraProvider = cameraProviderFuture.get()
// 綁定前需要先取消綁定
cameraProvider.unbindAll() // Unbind use cases before rebinding
cameraProvider.bindToLifecycle(this, cameraSelector, preview) // Bind use cases to camera
```

預設鏡頭:

| 鏡頭   | 參數                                |
| ----- | ----------------------------------- |
| 前鏡頭 | CameraSelector.DEFAULT_FRONT_CAMERA |
| 後鏡頭 | CameraSelector.DEFAULT_BACK_CAMERA  |

## Image Capture

```
val imageCapture = imageCapture ?: return
val file = File(
    getOutputDirectory(),
     "photo.jpg")

val outputOptions = ImageCapture.OutputFileOptions.Builder(file).build()

imageCapture.takePicture(
    outputOptions, ContextCompat.getMainExecutor(this), object : ImageCapture.OnImageSavedCallback {
        override fun onError(exc: ImageCaptureException) {
            // TODO ...
        }

        override fun onImageSaved(output: ImageCapture.OutputFileResults) {
            // TODO ...
        }
    })
```

建立一個要存放捕捉照片的File

```
val file = File(
    getOutputDirectory(),
    <檔案名稱> + ".<副檔名>")
```

將Camera擷取到影像存放到剛剛建立File上

```
val outputOptions = ImageCapture.OutputFileOptions.Builder(file).build()

val imageCapture = imageCapture ?: return
imageCapture.takePicture(
    outputOptions, ContextCompat.getMainExecutor(this), object : ImageCapture.OnImageSavedCallback {
        override fun onError(exc: ImageCaptureException) {
            // TODO ...
        }

        override fun onImageSaved(output: ImageCapture.OutputFileResults) {
            // TODO ...
        }
    })
```

## Reference

1. [Getting Started with CameraX](https://codelabs.developers.google.com/codelabs/camerax-getting-started/#0)
