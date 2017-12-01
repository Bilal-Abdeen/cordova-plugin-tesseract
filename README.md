# Cordova Tesseract-OCR Plugin - For Android and iOS

This is a Cordova/Ionic plugin for OCR process using Tesseract library for both Android and iOS. [Tesseract](https://github.com/tesseract-ocr/tesseract) is an Open Source library for OCR (Optical Character Recognition) process.

This plugin defines a global `TesseractPlugin` object, which provides an API for recognizing text on images.
```bash
  TesseractPlugin.recognizeText(imageData, language, function(recognizedText) {
    deferred.resolve(recognizedText);
  }, function(reason) {
    deferred.reject('Error on recognizing text from image. ' + reason);
  });
```

## Installation

### Before installing this plugin, make sure you have added the platform for your app:
```bash
$ ionic platform add android
```
-> substitute android with ios to build for iOS.

### 1. Download or clone this project, copy it to your app root folder and run ionic command to add the plugin:
```bash
$ git clone https://github.com/gustavomazzoni/cordova-plugin-tesseract
$ cp -rf cordova-plugin-tesseract your-project/cordova-plugin-tesseract
$ cd your-project/
$ ionic plugin add cordova-plugin-tesseract
```

### 2. For Android platform:

#### 2.1 Download or clone [tess-two project](https://github.com/rmtheis/tess-two) (it contains Tesseract library for Android) and copy the 'tess-two' folder inside of it to your android platform:
```bash
$ git clone https://github.com/rmtheis/tess-two
$ cp -rf tess-two/tess-two/ your-project/platforms/android/tess-two
```

#### 2.2 Edit `your-project/platforms/android/tess-two/build.gradle` and replace all content with:
```bash
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:0.14.0'
    }
}

apply plugin: 'android-library'

android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"

    defaultConfig {
        minSdkVersion 8
        targetSdkVersion 23
    }

    sourceSets.main {
        manifest.srcFile 'AndroidManifest.xml'
        java.srcDirs = ['src']
        resources.srcDirs = ['src']
        res.srcDirs = ['res']
        jniLibs.srcDirs = ['libs']
    }
}
```

#### 2.3 Edit `your-project/platforms/android/build.gradle` file and add 'tess-two' as a dependency to your project (after `// SUB-PROJECT DEPENDENCIES END` line):
```bash
dependencies {
    compile fileTree(dir: 'libs', include: '*.jar')
    // SUB-PROJECT DEPENDENCIES START
    debugCompile project(path: "CordovaLib", configuration: "debug")
    releaseCompile project(path: "CordovaLib", configuration: "release")
    // SUB-PROJECT DEPENDENCIES END
    compile project(':tess-two')
}
```

#### 2.4 Edit `your-project/platforms/android/cordova/lib/build.js` file that generates `settings.gradle` file:

* Edit `your-project/platforms/android/cordova/lib/builders/GradleBuilder.js` and replace the `fs.writeFileSync()` function call after `// Write the settings.gradle file.` (at line 101) with:
```bash
//##### EDITED - Added tess-two library dependency
fs.writeFileSync(path.join(this.root, 'settings.gradle'),
    '// GENERATED FILE - DO NOT EDIT\n' +
    'include ":"\n' + settingsGradlePaths.join('') +
    'include ":tess-two"');
```

#### 2.5 Your project is ready to use this plugin on Android platform. Build your project:
```bash
$ ionic build android
```

### 3. For iOS platform:

#### 3.1 Inside root directory of your ios platform, create Podfile and add [Tesseract OCR iOS](https://github.com/gali8/Tesseract-OCR-iOS) (Tesseract library for iOS7+) as a dependency:

* Create `your-project/platforms/ios/Podfile`
* Add 'TesseractOCRiOS' dependency (replace 'ocr-translation' with the name of your project):
```bash
source 'https://github.com/CocoaPods/Specs.git'
xcodeproj 'ocr-translation.xcodeproj/'

target 'ocr-translation' do

	pod 'TesseractOCRiOS', '4.0.0'

end
```

#### 3.2 Still at your ios platform folder, install the dependencies ([install the CocoaPods](https://cocoapods.org/) in case you don't have it yet) using the following commands:
```bash
$ pod install
```

#### 3.3 Your project is ready to use this plugin on iOS platform. Build your project:
```bash
$ ionic build ios
```

Your project is ready to use this plugin.

## Usage
cordova-plugin-tesseract is designed to recognize text in images in many languages, but for that to work we need to have the tessdata of the language you want the text to be recognized.

To use this plugin and recognize text in images, you need to:

### 1. Download the language
As soon as you enter on your OCR use case, call `TesseractPlugin.loadLanguage` function to download the tessdata for your language:
```bash
TesseractPlugin.loadLanguage(language, function(response) {
  deferred.resolve(response);
}, function(reason) {
  deferred.reject('Error on loading OCR file for your language. ' + reason);
});
```

### 2. Get image data from your photo
Load the image you want the text to be recognized from. On your angular Controller use [`$cordovaCamera`](http://ngcordova.com/docs/plugins/camera/) or [`cordova-plugin-camera`](https://github.com/apache/cordova-plugin-camera) plugin to take the photo or load an image:
```bash
$cordovaCamera.getPicture(options).then(function(imageData) {
  $scope.image = "data:image/jpeg;base64," + imageData;
  $scope.text = null;

  $timeout(function() {
    // DOM has finished rendering
    // insert here the call to TesseractPlugin.recognizeText function to recognize the text
    
  });
}, function(err) {
  // error
  console.log('ERROR with camera plugin. Error: ' + err);
});
```

### 3. Recognize text from image
Then, after loaded the image, just call `TesseractPlugin.recognizeText` function with the image data, the language of the text in the image and a callback function to be called after the operation is done.
```bash
TesseractPlugin.recognizeText(imageData, language, function(recognizedText) {
  $scope.text = recognizedText;
}, function(reason) {
  console.log('Error on recognizing text from image. ' + reason);
});
```

## Usage - Using Phonegap and JavaScript 
The following code was tested on PhoneGap. Moreover, it does NOT use any JS frameworks, i.e. Angular. 

### A. HTML 
<!-- Add the following to your HTML File, e.g. index.html --> 
..... 
<button class="camera-control" onclick="capturePhoto();">Capture Photo</button> 
<div style="text-align:center; margin:20px;">
        <img id="cameraPic" src="" style="width:60px;height:60px;" />
</div>
......

### B. Download the language
// Add the following to your JS script, e.g. my-app.js. 

var language = 'eng'; // for the English language 

$$(document).on('deviceready', function() {
    console.log("Device is ready!");
	abc = LoadLang(language); // Load the language, which will be used to recognise/recognize your text. 
});

function LoadLang(language) {
	TesseractPlugin.loadLanguage(language
		, function(response) {
			navigator.notification.alert(
				'language loaded successfully! - response: ' + response,  
				dummyFunction 
			);
		  
		}
		, function(reason) {
			navigator.notification.alert(
				'Error on loading OCR file for your language. ' + reason, 
				dummyFunction 
			);
		}	
	);
}

### C. Get image data from your photo
// Add the following to your JS script, e.g. my-app.js. 

// To be called by clicking on a button 
function capturePhoto(){
	navigator.camera.getPicture(uploadPhoto, onFail, { quality: 50 
		,destinationType: Camera.DestinationType.DATA_URL 
	});
}

function uploadPhoto(imageData){
    var cameraPic = document.getElementById("cameraPic");
	cameraPic.src = "data:image/jpeg;base64," + imageData;
	
	// Successful upload to the server
	navigator.notification.alert(
		'Your Photo has been uploaded',  // message
		recogniseText, 					// callback function 
	    'Photo Uploaded',              // title
	    'OK'                          // buttonName
	);

	// upload has failed Fail
	if (failedToUpload){
		navigator.notification.alert(
			'Your Photo has failed to upload',
			failedDismissed,
			'Photo Not Uploaded',
			'OK'
			);
	} 
}

### D. Recognize text from image
// Add the following to your JS script, e.g. my-app.js. 

// The following function is NOT, yet, working for me. I am still trying to fix it. Any help, please... 
function recogniseText(imageData) {
	TesseractPlugin.recognizeText(imageData, language
		, function(recognizedText) {
			navigator.notification.alert(
				'recognizing text succeded: ' + recognizedText, 
				dummyFunction 
			);
		}
		, function(reason) {
			navigator.notification.alert(
				'Error on recognizing text from image: ' + reason, 
				dummyFunction 
			);
		}
	);
}

function dummyFunction(){
} 
