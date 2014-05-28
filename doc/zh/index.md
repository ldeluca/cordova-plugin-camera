<!---
    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.
-->

# org.apache.cordova.camera

这个插件提供了一个 API，拍照，从系统的图像库中选择图像。

    cordova plugin add org.apache.cordova.camera
    

## navigator.camera.getPicture

使用相机拍摄照片，或者从设备的图像库中检索一张照片。 图像作为 base64 编码的 `String`传递的成功回调 ，或作为图像文件的 URI。 该方法本身返回 `CameraPopoverHandle` 可以用于重新定位文件选择弹出的对象。

    navigator.camera.getPicture( cameraSuccess, cameraError, [ cameraOptions ] );
    

### 说明

`camera.getPicture`函数将打开该设备的默认摄像头应用程序，允许用户去抓拍图片。 默认情况下，当 `Camera.sourceType` 等于 `Camera.PictureSourceType.CAMERA`的时候会发生此行为 。 一旦用户拍摄快照照片， 摄像头应用程序关闭，并恢复该应用程序。

如果 `Camera.sourceType` 是 `Camera.PictureSourceType.PHOTOLIBRARY` 或 `Camera.PictureSourceType.SAVEDPHOTOALBUM` ，然后允许用户选择一个现有图像对话框的显示。 `camera.getPicture`函数返回 `CameraPopoverHandle` 对象，可用于重新定位的图像选择对话框，例如，当设备方向的发生变化的时候。

返回值发送到 `cameraSuccess` 回调函数，在以下格式之一当中，这取决于指定的`cameraOptions` ：

*   一个 `String` 包含的 base64 编码的照片图像。

*   一个 `String` 表示在本地存储 (默认值) 上的图像文件位置。

你可以做任何你想做的编码图像或 URI，例如：

*   呈现这个图像在一个 `<img>` 标记中，如下面的例子所示

*   保存本地的数据 （ `LocalStorage` ， [Lawnchair][1]，等等.)

*   将数据发布到远程服务器

 [1]: http://brianleroux.github.com/lawnchair/

**NOTE**： 在较新的设备上的照片分辨率是相当好。 从设备的图片库中选择没有被缩减尺寸质量较低的照片，即使`quality`参数是被指定的。 为了避免常见的内存问题，设置 `Camera.destinationType` 到 `FILE_URI` 而不是`DATA_URL`.

### 支持的平台

*   亚马逊火 OS
*   Android 系统
*   黑莓 10
*   火狐浏览器操作系统
*   iOS
*   Tizen
*   Windows Phone 7 和 8
*   Windows 8

### 亚马逊火 OS 怪癖

亚马逊火 OS 使用意向启动捕获图像，在设备上的相机活动和与低内存手机，科尔多瓦活动可能被杀。 在此情况下，当cordova活动得以恢复的时候，可能不会显示图像。

### Android的怪异

Android 使用意向启动捕获图像，在设备上的相机活动和与低内存手机，科尔多瓦活动可能被杀。 在此方案中，可能不会显示图像还原科尔多瓦活动时。

### 火狐浏览器操作系统的怪癖

目前使用[Web 活动][2]实现相机插件.

 [2]: https://hacks.mozilla.org/2013/01/introducing-web-activities/

### iOS 的怪癖

包括 JavaScript `alert()` 中任一回调的函数可能会导致问题。 换行内的警报 `setTimeout()` ，允许 iOS 图像选取器或弹出要完全关闭之前警报将显示：

    setTimeout(function() {/ / 做你的事!}，0) ；
    

### Windows Phone 7 的怪异

调用本机摄像头应用程序，同时通过 Zune 连接设备不工作，并触发错误回调。

### Tizen的怪异

Tizen 仅支持 `destinationType` 的 `Camera.DestinationType.FILE_URI` 和 `sourceType` 的`Camera.PictureSourceType.PHOTOLIBRARY`.

### 示例

拍一张照片，并检索它为 base64 编码的图像：

    navigator.camera.getPicture(onSuccess, onFail, { quality: 50,
        destinationType: Camera.DestinationType.DATA_URL
    });
    
    function onSuccess(imageData) {
        var image = document.getElementById('myImage');
        image.src = "data:image/jpeg;base64," + imageData;
    }
    
    function onFail(message) {
        alert('Failed because: ' + message);
    }
    

拍一张照片和检索图像的文件位置：

    navigator.camera.getPicture(onSuccess, onFail, { quality: 50,
        destinationType: Camera.DestinationType.FILE_URI });
    
    function onSuccess(imageURI) {
        var image = document.getElementById('myImage');
        image.src = imageURI;
    }
    
    function onFail(message) {
        alert('Failed because: ' + message);
    }
    

## CameraOptions

要自定义相机设置的可选参数。

    {quality： 75，
    destinationType： Camera.DestinationType.DATA_URL，
    sourceType： Camera.PictureSourceType.CAMERA，
    allowEdit：  true，
    encodingType： Camera.EncodingType.JPEG，
    targetWidth： 100，
    targetHeight： 100，
    popoverOptions： CameraPopoverOptions，
    saveToPhotoAlbum： false} ；
    

### 选项

*   **quality**： 保存的图像质量，表示这个质量范围在 0-100之间，在 100 哪里通常是全分辨率而不会丢失文件的压缩质量。 *(Number)*（请注意关于相机的分辨率的信息是不可用）。

*   **destinationType**： 选择返回值的格式。定义在 `navigator.camera.DestinationType` *（人数）*
    
        Camera.DestinationType = {
        DATA_URL： 0，/ / 返回图像作为 base64 编码字符串
        FILE_URI： 1，/ / 返回图像文件的 URI 
        NATIVE_URI： 2 / / 返回图像本地的 URI (例如，assets-library： / / 在 iOS上 或content： / / 在 Android 上)} ；
        

*   **sourceType**： 设置图片的来源。定义在 `navigator.camera.PictureSourceType` *（人数）*
    
        Camera.PictureSourceType = {
        PHOTOLIBRARY: 0，
        CAMERA： 1，
        SAVEDPHOTOALBUM: 2
        } ；
        

*   **allowEdit**： 允许简单编辑以前选择的图像。*(Boolean)*

*   **encodingType**： 选择返回的图像文件的编码。定义在 `navigator.camera.EncodingType` *（人数）*
    
        Camera.EncodingType = {
        JPEG: 0，/ / 返回 JPEG 编码的图像 
        PNG： 1 / / 返回 PNG 编码的图像
        } ；
        

*   **targetWidth**：缩放图像宽度的像素。必须与**targetHeight**一起使用。纵横比保持不变。*(Number)*

*   **targetHeight**：缩放图像高度的像素。必须与**targetWidth**一起使用。纵横比保持不变。*(Number)*

*   **mediaType**： 设置媒体的类型来选择。 只有当 `PictureSourceType` 是 `PHOTOLIBRARY` 或 `SAVEDPHOTOALBUM`的时候才工作 。 定义在 `nagivator.camera.MediaType` *(Number)* 
    
        Camera.MediaType = {
        PICTURE: 0，/ /只 允许选择静态图片, 默认情况 Will return format specified via DestinationType
            VIDEO: 1,      // allow selection of video only, WILL ALWAYS RETURN FILE_URI
            ALLMEDIA : 2   // allow selection from all media types
        };
        

*   **correctOrientation**： 在捕获过程中，旋转图像，使该设备的取向正确。*(Boolean)*

*   **saveToPhotoAlbum**：当捕获完成后， 将图像保存到该设备的相册里。*(Boolean)*

*   **popoverOptions**：iOS的唯一选项， 用于指定在iPad中的弹出位置。定义在`CameraPopoverOptions`.

*   **cameraDirection**： 选择相机以使用 （前面或后面-面向）。定义在 `navigator.camera.Direction` *（人数）*
    
        Camera.Direction = {
        BACK: 0，/ / 使用背面摄像头
        FRONT： 1 / / 使用前置摄像头
        } ；
        

### 亚马逊火 OSQuirks

*   任何一个 `cameraDirection` 值的结果都是一个后置图像。

*   忽略 `allowEdit` 参数。

*   `Camera.PictureSourceType.PHOTOLIBRARY`和 `Camera.PictureSourceType.SAVEDPHOTOALBUM` 都显示相同的相册。

### Android 的怪癖

*   任何 `cameraDirection` 值回朝的照片中的结果。

*   忽略 `allowEdit` 参数。

*   `Camera.PictureSourceType.PHOTOLIBRARY`和 `Camera.PictureSourceType.SAVEDPHOTOALBUM` 都显示相同的相册。

### BlackBerry 10 Quirks

*   忽略 `quality` 参数。

*   忽略 `sourceType` 参数。

*   忽略 `allowEdit` 参数。

*   `Camera.MediaType`不受支持。

*   忽略 `correctOrientation` 参数。

*   忽略 `cameraDirection` 参数。

### 火狐浏览器操作系统的怪癖

*   忽略 `quality` 参数。

*   `Camera.DestinationType`将被忽略并且等于 `1` （图像文件的 URI）

*   忽略 `allowEdit` 参数。

*   忽略 `PictureSourceType` 参数 （用户选择它在对话框窗口中)

*   忽略`encodingType`

*   忽略 `targetWidth` 和`targetHeight`

*   `Camera.MediaType`不受支持。

*   忽略 `correctOrientation` 参数。

*   忽略 `cameraDirection` 参数。

### iOS 的怪癖

*   设置 `quality` 低于 50，避免在某些设备上的内存不足引起的错误。

*   当使用 `destinationType.FILE_URI`的时候 ，照片被保存在应用程序的临时目录中。 如果存储空间是一个问题，你可以 使用`navigator.fileMgr` ApIs 删除此目录的内容。

### Tizen 怪癖

*   不支持的选项

*   始终返回一个 FILE URI

### Windows Phone 7 和 8 怪癖

*   忽略 `allowEdit` 参数。

*   忽略 `correctOrientation` 参数。

*   忽略 `cameraDirection` 参数。

*   忽略 `mediaType` 属性的 `cameraOptions` 作为 Windows Phone SDK 不提供方式从 PHOTOLIBRARY 中选择视频。

## CameraError

onError 回调函数的函数提供了一条错误消息。

    function(message) {
        // Show a helpful message
    }
    

### 参数

*   **message**： 这个消息靠设备的本机代码来提供。*(String)*

## cameraSuccess

onSuccess 提供的图像数据的回调函数。

    function(imageData) {
        // Do something with the image
    }
    

### 参数

*   **imageData**： Base64 编码的图像数据，*or*图像文件的 URI，取决于 `cameraOptions` 生效。*(String)*

### 示例

    // Show image
    //
    function cameraCallback(imageData) {
        var image = document.getElementById('myImage');
        image.src = "data:image/jpeg;base64," + imageData;
    }
    

## CameraPopoverHandle

由创建的弹出对话框的句柄`navigator.camera.getPicture`.

### 方法

*   **setPosition**: 设置弹出的位置。

### 支持的平台

*   iOS

### setPosition

设置弹出的位置。

**参数**：

*   `cameraPopoverOptions`： `CameraPopoverOptions` ，指定新的位置

### 示例

     var cameraPopoverHandle = navigator.camera.getPicture(onSuccess, onFail,
         { destinationType: Camera.DestinationType.FILE_URI,
           sourceType: Camera.PictureSourceType.PHOTOLIBRARY,
           popoverOptions: new CameraPopoverOptions(300, 300, 100, 100, Camera.PopoverArrowDirection.ARROW_ANY)
         });
    
     // Reposition the popover if the orientation changes.
     window.onorientationchange = function() {
         var cameraPopoverOptions = new CameraPopoverOptions(0, 0, 100, 100, Camera.PopoverArrowDirection.ARROW_ANY);
         cameraPopoverHandle.setPosition(cameraPopoverOptions);
     }
    

## CameraPopoverOptions

iOS 仅指定弹出的锚元素的位置和箭头方向，从 iPad 的库或专辑选择图像时的参数。

    {x: 0，
    y： 32，
    width： 320，
    height： 480，
    arrowDir： Camera.PopoverArrowDirection.ARROW_ANY
    } ；
    

### CameraPopoverOptions

*   **x**：到弹出窗口的屏幕元素上的 x 像素坐标。*(Number)*

*   **y**： 到弹出窗口的屏幕元素上的 y 像素坐标。*(Number)*

*   **width**： 宽度以像素为单位，到屏幕元素上以链接弹出窗口。*(Number)*

*   **height**： 高度以像素为单位，到屏幕元素上以链接弹出窗口。*(Number)*

*   **arrowDir**： 弹出的箭头应指向的方向。定义在 `Camera.PopoverArrowDirection` *(Number)* 
    
            Camera.PopoverArrowDirection = {
        ARROW_UP: 1，/ / 匹配 iOS UIPopoverArrowDirection 常量 
        ARROW_DOWN： 2，
        ARROW_LEFT： 4，
        ARROW_RIGHT： 8，
        ARROW_ANY： 15
        } ；
        

请注意弹出的大小可能会更改箭头的方向和屏幕的方向调整。 请确保帐户方向更改时指定的锚点的元素位置。

## navigator.camera.cleanup

删除中间由从临时存储相机所拍摄的照片。

    navigator.camera.cleanup( cameraSuccess, cameraError );
    

### 说明

删除中间打完电话后保留在临时存储的图像文件 `camera.getPicture` 。 适用时，才的价值 `Camera.sourceType` 等于 `Camera.PictureSourceType.CAMERA` 和 `Camera.destinationType` 等于`Camera.DestinationType.FILE_URI`.

### 支持的平台

*   iOS

### 示例

    navigator.camera.cleanup(onSuccess, onFail);
    
    function onSuccess() {
        console.log("Camera cleanup success.")
    }
    
    function onFail(message) {
        alert('Failed because: ' + message);
    }