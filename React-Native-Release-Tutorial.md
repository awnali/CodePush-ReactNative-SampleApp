**Release Build for IOS**

 1. Open the project in Xcode,
    SampleCodePushApp/ios/SampleCodePushApp.xcodeproj
 2. Go to Product->Scheme->Edit Scheme
 
<img width="1149" alt="screen shot 2017-08-08 at 3 14 50 pm" src="https://user-images.githubusercontent.com/627361/29069538-6583e994-7c4c-11e7-92f2-5708c004eafd.png">

 Change it from Debug to Release and Run application,
 
<img width="896" alt="screen shot 2017-08-08 at 3 18 14 pm" src="https://user-images.githubusercontent.com/627361/29069663-e0052ae8-7c4c-11e7-9dfc-94b959391801.png">

Run application from xcode in your simulator, and it should work like charm. If you want to run it on physical device you just have to sign to application and remove the test case project from build process.

**Release Build for Android**

    keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

It will generate the  my-release-key.keystore file, in my case it was [ENTER PATH].

Copy the my-release-key.keystore file to your project’s android/app folder (Don’t forget to add this in your gitignore file)

Edit the file ~/.gradle/gradle.properties and add the following:

    MYAPP_RELEASE_STORE_FILE=my-release-key.keystore
    MYAPP_RELEASE_KEY_ALIAS=my-key-alias
    MYAPP_RELEASE_STORE_PASSWORD=*****
    MYAPP_RELEASE_KEY_PASSWORD=*****

Edit your android/app/build.gradle file and add following lines under defaultConfig section:

    signingConfigs {
            release {
                if (project.hasProperty('MYAPP_RELEASE_STORE_FILE')) {
                    storeFile file(MYAPP_RELEASE_STORE_FILE)
                    storePassword MYAPP_RELEASE_STORE_PASSWORD
                    keyAlias MYAPP_RELEASE_KEY_ALIAS
                    keyPassword MYAPP_RELEASE_KEY_PASSWORD
                }
            }
    }

And add following line under buildTypes release:

    signingConfig signingConfigs.release

Now you are ready to release build:

    cd android && ./gradlew assembleRelease

    cd .. && react-native run-android --variant=release

Make sure your app wasn't installed previously otherwise you’ll get following error:

    Execution failed for task ':app:installRelease'.

If so then uninstall the app and run the last command again.

Now your signed APK should be **android/app/build/outputs/apk/app_release.apk**
