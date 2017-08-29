![BEEPING](https://github.com/Beeping/beeping-assets/blob/master/beeping-logo-blue.png?raw=true)

## Beeping Android SDK
Beeping Android SDK allows any app to detect and react to audible and inaudible audio cookies called beeps.
This document will show you how to integrate our SDK into your app, configure it, and test to make sure it’s working properly.

## System Requirements

* Android Studio
* Minimum SDK version: 15 (Android 4.0)

## App Requirements

* Your App must request an AppId from our Development Team. You will need this key to initialize the Beeping SDK. *Get in touch at dev@beeping.io.*

## Install the SDK on your App

#### 1. Clone this repo to your local

* Run command `git clone https://github.com/Beeping/beeping-sdk-android.git` in your desired folder.

![1.1](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/git/1.png?raw=true)

![1.2](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/git/2.png?raw=true)

#### Beeping SDK Library as AAR file

Your cloned repo contains the Beeping SDK Library as an AAR:

`AndroidBeepingCore.aar`
  
![1.3](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/git/3.png?raw=true)

**What is an AAR?**<br>
*It's a format to compile an Android Library into an Android Archive (AAR) file that you can use as a dependency for an Android app module.*

#### 2. Add the SDK `AndroidBeepingCore.aar` to your project

In Android Studio, once you have created or selected your project, the way to add the Beeping SDK Library is by creating a New Module. Follow the instructions below to do so:
* Go to the top menu bar and select "File", and then click on "Project Structure".
![2.1](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/1.png?raw=true)
* Inside the "Project Structure" window, click on the "+" sign on the top-left corner to add a "New Module".
![2.2](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/2.png?raw=true)
* Inside the "Create New Module" window, select the image with the title "Import .JAR/.AAR Package" and click "Next".
![2.3](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/3.png?raw=true)
* The next window will ask you to select the location of your `AndroidBeepingCore.aar` file. Select and click "OK".
![2.4](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/4.png?raw=true)
* Once selected, your window should look like the one below. Then click "Finish".
![2.5](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/5.png?raw=true)
* Android Studio should automatically start syncing your Gradle project. Just click "OK" to close the window.
![2.6](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/6.png?raw=true)
* Now your new `AndroidBeepingCore` library should show in your Android Project directory.
![2.7](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/7.png?raw=true)

#### 3. Add AndroidBeepingCore to your App Dependencies

Once the `AndroidBeepingCore` library had been added, we need to add it to your App Dependencies. Follow the instructions below to do so:
* Find your App Gradle file `build.graddle` in your project directory: `MyApplication/app/build.graddle`.
* Once in the file, add this line of code `compile project(':AndroidBeepingCore')` within `dependencies {}`:
```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.android.support.constraint:constraint-layout:1.0.0-beta5'
    testCompile 'junit:junit:4.12'

    // Add AndroidBeepingCore dependency here!
    compile project(':AndroidBeepingCore')
}
```
The result should look like the image below:
![3.1](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/8.png?raw=true)
* Once that's added, Android Studio will prompt you to sync your project one more time. Click "Sync Now".
* If no "Sync Now" prompt appears, you can sync your gradle files manually by:
  * On the top menu bar, select "Tools".
  * Select Android at the bottom.
  * Click "Sync Project with Gradle Files"
![3.2](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/9.png?raw=true)
* Done! Once your gradle files are synced, the Beeping Android SDK has been successfully added to your project.
  * To make sure Android Studio has added the module dependency correctly, check that `':AndroidBeepingCore'` is included in the Settings Gradle file `MyApplication/settings.gradle`:
```
include ':app', ':AndroidBeepingCore'
```
![3.3](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/installation/10.png?raw=true)
   
## Configure your App

#### 1. Configure the MainActivity file

* In order to use the Beeping Android SDK functions, you must modify your `MainActivity.java` file. You can find it by going to `MyApplication/app/arc/main/java/../MainActivity`.
![1.1](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/configure/1.png?raw=true)
* Add these lines of code below your `import` imported packages:
```
import com.beeping.AndroidBeepingCore.*;
import org.json.JSONObject;
import android.util.Log;
```
  * `import com.beeping.AndroidBeepingCore.*;` imports the Beeping SDK contents into the file.
  * `import org.json.JSONObject;` allows you to handle the JSONObject returned by the Beeping SDK via the Callback function.
  * `import android.util.Log;` *(OPTIONAL)* allows logging the response of the Beeping SDK to assure its working.

* You must implement `BeepEventListener` interface as part of your `MainActivity` function, by adding `implements BeepEventListener`:
```
public class MainActivity extends Activity implements BeepEventListener {}
```
![1.2](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/configure/2.png?raw=true)
Don't worry if you get this warning:
```
Class 'MainActivity' must either be declared abstract or implement abstract method 'onBeepResponseEvent(JSONObject)' in 'BeepEventListener'
```
This is just a reminder that `onBeepResponseEvent(JSONObject)` is required as part of the `BeepEventListener`. We will implement this later.

#### 2. Configure the AndroidManifest file

* Beeping Android SDK requires permissions to:

    * Record Audio
    * Write to Storage
    
To add these permissions, add the code lines below to your `AndroidManifest.xml` file. You can find it by going to `MyApplication/app/arc/main/java/../MainActivity`.

```
<manifest...>

  <uses-permission android:name="android.permission.RECORD_AUDIO"></uses-permission>
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>

  <application> ... </application>
  
</manifest>
```
![1.3](https://github.com/Beeping/beeping-assets/blob/master/android-sdk/configure/3.png?raw=true)
<br>
Done! Your app is configured to start implementing the Beeping Android SDK.

## Using the SDK

#### 1. Initializing Beeping
```
new BeepingCore(appIdString, this);
```
*IMPORTANT: This method requires an AppId. Please contact our team if you don't have one already.<br>
The 2nd argument passed to the BeepingCore constructor is your callback.<br>
We will explain more in the callback method.*

#### 2. Using Beeping methods

* Listening for beeps:
```
  beeping.startBeepingListen();
```
*This function triggers your app to start listening for beeps.<br>
When we successfully detect and retrieve a beep, the callback is triggered.*

* Stop listening for beeps:
```
  beeping.stopBeepingListen();
```
*As the name suggests, this function triggers your app to stop listening for beeps.*

* Garbage collection:
```
  beeping.dealloc();
```

* Callback function when beep detected/retrieved:
```
  onBeepResponseEvent(JSONObject beep) {}
```
*IMPORTANT: This function is required when implementing `BeepEventListener` class in your MainActivity.<br>
When we successfully detect and retrieve a beep, the callback is triggered.<br>
This method will respond with a Beep JSONObject (sample provided below).*

#### Sample Response Beep JSONObject ()
```
  {
      "type": "url",
      "data": "https://www.youtube.com/watch?v=DiTECkLZ8HM",
      "url": "https://www.youtube.com/watch?v=DiTECkLZ8HM",
      "title": "SPIDER-MAN: HOMECOMING - Official Trailer #2 (HD)",
      "brand": "YouTube",
      "imgSrc": "https://i.ytimg.com/vi/DiTECkLZ8HM/maxresdefault.jpg",
      "ogType": "video",
      "_id": "C9bPJJNtij6JAhKeS",
      "avatar": "https://s3.amazonaws.com/beepingfiles/images/beeping_avatar.png",
      "init": 0,
      "final": 10000,
      "createdAt": "2017-03-22T19:18:45.178Z",
      "updatedAt": "2017-03-22T19:18:45.180Z"
  }
```
*NOTICE: Please be advised that the `url` field will be deprecated by July 1st 2017. It will be replaced by `data` field. Both fields will be working until the aforementioned date.*

#### Beep JSONObject () Legend
`type`: determines the content type of the beep (options: `url`, `deeplink`, `text`)<br>
`data`: actual contents of the beep.<br>
`url`: this field is being replaced by the data field.<br>
`title`: title of the beep given by its author at creation.<br>
`brand`: name of the beep author or brand.<br>
`imgSrc`: preview image for beep.<br>
`ogType`: content source type if `type: url` (example: `video`,`website`).<br>
`_id`: beep object identifier.<br>
`avatar`: avatar image for author or brand.<br>
`init`: initial time for beep frame delivery (in seconds).<br>
`final`: final time for beep frame delivery (in seconds).<br>
`createdAt`: date and time when beep was created.<br>
`updatedAt`: date and time when beep was last updated.<br>

## Sample Android App with Beeping Android SDK

* In `AndroidManifest.xml` add:
```
<manifest...>

  <uses-permission android:name="android.permission.RECORD_AUDIO"></uses-permission>
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>

  <application> ... </application>
  
</manifest>
```

* In `MainActivity.java` add:
```
  import com.beeping.AndroidBeepingCore.*;
  import org.json.JSONObject;
  import android.util.Log;

  public class MainActivity extends Activity implements BeepEventListener {
  
      BeepingCore beeping;
  
      protected void onCreate(Bundle savedInstanceState) {
      
          // Initialize BeepingCore instance
          beeping = new BeepingCore(appIdString, this);
          
          // Start listening for beeps
          beeping.startBeepingListen();
      }
  
      public void onDestroy() {
      
          super.onDestroy();
          
          // Stop listening for beeps
          beeping.stopBeepingListen();
          
          // Deallocating memory
          beeping.dealloc();
      }
  
      public void onBeepResponseEvent(JSONObject beep) {
          Log.d("APP", "onBeepingEvent: " + beep.toString());
      }
      
  }
```

## Implementing Background Listening mode
Beeping SDK allows you to keep detecting beeps even when the app is hidden in the background.

#### 1. Create an Auto button
* In your `MainActivity.java` class, add your desired button to toggle background listening:
```
import android.widget.Button;

public class MainActivity extends Activity implements BeepEventListener {

  private Button buttonAutoListen;

  protected void onCreate() {
    
    // attach to correspoinding view
    buttonAutoListen = (Button) findViewById(R.id.button_auto_listen);
}
```
* In your `activity_main.xml` file, add your corresponding button view:
```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/relay_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    
    <Button
      android:id="@+id/button_auto_listen"
      android:layout_width="50dp"
      android:layout_height="30dp"
      android:background="@drawable/button_auto_listen_off"
      android:text="auto"
      android:textAllCaps="false"
      android:textColor="@color/colorCharcoal"
      android:textSize="12sp"
      android:alpha="0.0"/>
      
</RelativeLayout>
```
#### 2. Use SharedPreferences to save persistently
Your app MUST use Android's SharedPreferences as described below in our for the Beeping SDK to correctly handle background listening.
Failure to do so can lead to failures when using background mode.
* Use `autoListen` as the name of the SharedPreferences file.
* Use `isAuto` as the key of the key-boolean pair saved into the above file.

```
import android.content.SharedPreferences;

private boolean isAuto;

public class MainActivity extends Activity implements BeepEventListener {

  protected void onCreate() {

    // create, edit and save to sharedPreferences
    SharedPreferences sharedPrefsAuto = getSharedPreferences("autoListen", Context.MODE_PRIVATE);
    SharedPreferences.Editor editor = sharedPrefsAuto.edit();
    editor.putBoolean("isAuto", true);
    editor.apply();
    
    // retrieve sharedPreferences
    SharedPreferences sharedPrefsAuto = getSharedPreferences("autoListen", Context.MODE_PRIVATE);
    isAuto = sharedPrefsAuto.getBoolean("isAuto", false);
  
  }

}
  
```
*For more info on how to use SharedPreferences, visit the Android Documentation: https://developer.android.com/reference/android/content/SharedPreferences.html*

#### 3. Add clicking behavior to your button
In `MainActivity.java`, implement the following:
* Define a `Boolean` variable to store your button's state (`isBackgroundListening`).
* Reflects button's state on your variable (`isBackgroundListening`) and the SharedPreferences file.
```
private boolean isBackgroundListening;

public void setUpOnClicks() {

  buttonAutoListen.setOnClickListener(new View.OnClickListener() {
    
    // button is OFF, turning ON //
    if (!isBackgroundListening) {
    
      // change button's look
      Drawable myDrawable = getResources().getDrawable(R.drawable.button_auto_listen_on);
      int myColor = getResources().getColor(R.color.colorWhite);
      buttonAutoListen.setBackground(myDrawable);
      buttonAutoListen.setTextColor(myColor);
      
      // set flag "true"
      isBackgroundListening = true;
      
      // set shared prefs for auto listen to "true"
      SharedPreferences sharedPrefsAuto = getSharedPreferences("autoListen", Context.MODE_PRIVATE);
      SharedPreferences.Editor editor = sharedPrefsAuto.edit();
      editor.putBoolean("isAuto", true);
      editor.apply();

    // button is ON, turning OFF //
    } else {
    
      // change button's look
      Drawable myDrawable = getResources().getDrawable(R.drawable.button_auto_listen_off);
      int myColor = getResources().getColor(R.color.colorCharcoal);
      buttonAutoListen.setBackground(myDrawable);
      buttonAutoListen.setTextColor(myColor);
      
      // set flag "false"
      isBackgroundListening = false;
      
      // shared prefs for auto listen to "false"
      SharedPreferences sharedPrefsAuto = getSharedPreferences("autoListen", Context.MODE_PRIVATE);
      SharedPreferences.Editor editor = sharedPrefsAuto.edit();
      editor.putBoolean("isAuto", false);
      editor.apply();
    }

  });
}
```
#### 4. Add correct entry and exit behaviors for app
* Start listening on app re-entry:
```
public BeepingCore beeping;

@Override
  protected void onResume() {
    super.onResume();
    AppController.getInstance().frontActivityMain();
    if (beeping != null) {
        
        // start listening
        beeping.startBeepingListen();
    }

    // fetch background listening state
    SharedPreferences sharedPrefsAuto = getSharedPreferences("autoListen", Context.MODE_PRIVATE);
    isAutoListen = sharedPrefsAuto.getBoolean("isAuto", false);
    
  }
```
* Stop listening on app exit ONLY when background mode OFF:
```
public BeepingCore beeping;
private boolean isBackgroundListening;

@Override
  protected void onStop() {
    super.onStop();
    
    // only stop listening if background mode OFF
    if (isBackgroundListening == false) {
      if (beeping != null) {
      
        // stop listening
        beeping.stopBeepingListen();
      }
    }
}
```

## Testing your Beeping-enabled App

#### 1. Create your first Beep

* Go to [www.beeping.io](https://www.beeping.io)
![3.7](https://github.com/Beeping/beeping-assets/blob/master/ios-sdk/beeping/1.png?raw=true)
* Enter any URL in the entry box and press **"CREATE"**.
![3.8](https://github.com/Beeping/beeping-assets/blob/master/ios-sdk/beeping/2.png?raw=true)
* A new Beep Card should appear below the entry box.
![3.9](https://github.com/Beeping/beeping-assets/blob/master/ios-sdk/beeping/3.png?raw=true)

#### 2. Ready your Beeping-enabled app

* Open your app.
* Set your app in listening mode `beeping.startBeepingListen();`
* Make sure the device microphone is not blocked in any way.

#### 3. Playing and Detecting your first Beep

* Back at [www.beeping.io](https://www.beeping.io).
* Make sure your computer speakers are not muted. 
* Press the **PLAY** icon on the right side of the beep card.
* Or press the **DOWNLOAD** icon (upside-down arrow) to download and play the beep on an external player.
![3.10](https://github.com/Beeping/beeping-assets/blob/master/ios-sdk/beeping/4.png?raw=true)
* If successfully executed, your app should be:
  * Detecting the inaudible beep.
  * Decoding the token or BeepId.
  * Beeping SDK calls Beeping API to retrieve Beep JSONObject.
  * Beeping SDK delivers Beep JSONObject to the app via Callback function.

*NOTICE: Beeps created at [www.beeping.io](https://www.beeping.io) are inaudible to human hearing but will be detected by your Beeping-enabled app and device.*

### THE END