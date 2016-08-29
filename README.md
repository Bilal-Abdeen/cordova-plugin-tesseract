# Cordova Plugin Tesseract-OCR - For Android and iOS

This is a Cordova/Ionic plugin for OCR process using Tesseract library for both Android and iOS. [Tesseract](https://github.com/tesseract-ocr/tesseract) is an Open Source library for OCR (Optical Character Recognition) process.

## Using this plugin

### Before installing this plugin, make sure you have added the platform for your app:
```bash
$ ionic platform add android
```
substitute android with ios to build for iOS.

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

* Edit `your-project/platforms/android/cordova/lib/build.js` and replace the `fs.writeFileSync()` function call after `// Write the settings.gradle file.` (at line 273) with:
```bash
//##### EDITED - Added tess-two library dependency
fs.writeFileSync(path.join(projectPath, 'settings.gradle'),
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
