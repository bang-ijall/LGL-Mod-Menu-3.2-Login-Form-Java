# FloatingModMenu
Simple floating mod menu to il2cpp and other native android games. Compare to the other menus, this is the most efficient and fastest way to work and to implement menu in the game

Support both KittyMemory and MSHook and support Android 4.2.x way up to Android R preview. Sound effects included. MSHook does not support ARM64 but KittyMemory support ARM64

# What will you need?
- Android Studio 3 and up: https://developer.android.com/studio

- Apktool:

- -- Apktool.jar: https://ibotpeaches.github.io/Apktool/

- Or

- -- APK Easy Tool: https://forum.xda-developers.com/android/software-hacking/tool-apk-easy-tool-v1-02-windows-gui-t3333960

- Notepad:

- -- Notepad++ https://notepad-plus-plus.org/downloads/

- Or

- -- Sublime Text: https://www.sublimetext.com/

- Compress png - to compress your png file: https://compresspng.com/ 
- Base64 encode - to encode your file: https://www.base64encode.org/
- XMedia Recode - to convert your sound files to .ogg https://www.xmedia-recode.de/en/download.php
- Template: https://github.com/LGLTeam/Android-Mod-Menu

# Install Android Studio
If you have Android Studio installed, you can skip this steps 

Setting up Android Studio takes just a few clicks.

See the user guide: https://developer.android.com/studio/install

# Install NDK
Open Android Studio, you will be welcomed

At the bottom-right corner, click on Configure and SDK Manager

Select **Android SDK**, check **NDK (Side by side)** and click OK. It will download and install

# Open the mod menu template project 
Once you've downloaded all the necessary files, extract the template project to the folder without any spaces. If any folder has spaces, it will cause problem

On Android Studio on the welcome screen, choose **"Open an existing Android Studio project"**

Navigate to the extracted project and open it

 

It will index and Gradle will sync the project fir the first time. Please wait for a while, it will take around 5 minutes depending your computer performance

After it's done, you can start working!

On the left side, you see the Project view. Default view is Android

 

If this is somewhat confusing, change the view to Project

I will explain each of the files for you

**FloatingModMenuService.java:**

The codes of floating mod menu. You don't need to change much unless you want to redesign it. The codes are explained in the comments (//...)

- soundEnabled:
Change to false if you don't want it to play sounds

**MainActivity.java:**

Starts the main activity. It won't be used if you implement the menu in the game

**Sounds.java:**

Basically the 'GTA V' sounds, have been converted to .ogg using XMedia Recode and encoded to base64. They are automatically decoded and stored into /data/data/(package name)/cache upon startup. See StaticActivity

**StaticActivity.java:**

To initialize by game activity's OnCreate
Checks if device running Android 6.0 or above and if have overlay permission checked. Sounds being written to the cache directory.
Start() will be called when implementing the menu to the game. We will explain later

- writeToFile:
Decode base64 and write to file to a target directory

**main.cpp**

In this file, you will mostly use it to edit features, credits, icon, and implement your code for KittyMemory or MS Hooking.

- Title:
Big text

- Heading:
Little text

- Icon:
Compressed image that is encoded to base64

- IconSize:
Mod menu icon size 

- Toast:
To get text from c++ in order to show toast in java

- changeSeekBar:
Change the value with slider in real time using sliderValue. This is useful for hooking. Features MUST be count from 0

- changeToggle:
To enable/disable the mods. Features MUST be count from 0

- FeatureList:
Here you add the mod features

Usage:

```
Toggle_[feature name]
SeekBar_[feature name]_[min value]_[max value]
Spinner_(mod feature)_(Items e.g. item1_item2_item3)...
SeekBar_(mod feature)_(min)_(max)
```

Example:

```Toggle_Mod 1
Toggle_Mod 2
SeekBar_Damage_0_1000
```

Do not forget to count your features from 0 and remember them

- hack_thread:
Here you add your code for hacking with KittyMemory or Hooking. I will not teach, you must have learned it already

- JNI_OnLoad:
Initialize when the library loads

**Android.mk**

The make file for the c++ compiler. In that file, you can change the lib name on the `LOCAL_MODULE` line
When you change the lib name, change also on `System.loadLibrary("")` under OnCreate method on FloatingModMenuService.java
Both must have same name

Other than that, find out yourself. It's a lot easier if you already have the knowledge 
Most codes have the comments that will explain for you 
Enjoy working with the menu =D

# Testing the mod menu 

If you have your device with adb enabled, connected your PC or your emulator with adb enabled. Android Studio will detect and you can click Play to run your app onto your device/emulator



To use adb, you must enable USB debugging in the device system settings, under Developer options.

On Android 4.2 and higher, the Developer options screen is hidden by default. To make it visible, go to **Settings** > **About phone** and tap Build number seven times. Return to the previous screen to find Developer options at the bottom.

On some devices, the Developer options screen might be located or named differently.

# Implementing the menu in the target game 

After you finished the menu, you can build the project to APK file.
**Build** -> **Build Bundle(s)/APK(s)** -> **Build APK(s)**

If no errors occured, you did everything right and build will succed. You will be notified that it build successfully

 

Click on **locate** to show you the location of **build.apk**. It is stored at `(your-project)\app\build\outputs\apk\ app-debug.apk`



Now you will need to decompile **app-debug.apk**. Decompile the target game as well

Open the game's `androidmanifest.xml`
Add the permission besides other permissions
```
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
```

 

And add the service below the application tag (change the package name if you had changed it)
```
<service
           android:name="uk.lgl.modmenu.FloatingModMenuService"
            android:enabled="true"
            android:exported="false"/>
```

Save the **AndroidManifest.xml** file

Now we are looking for main activity, it is ususally written under application tag. The activity name may be different. If you spotted `android:name="android.intent.action.MAIN"` you will immediately know this is main activity

Be sure to enable Word wrap so it is easier to read

 

Or open the apk in APK Easy Tool and look for main activity

 

In this case, the path to main activity was `com.funcube.loa.MainActivity`. I would navigate to `(decompiled game)/smali/com/funcube/loa/` and you will see **MainActivity.smali**. If the game have multi dex, find out which smali folder has the main activity, it should be in one of these folders.

Open the main acitivity's smali file, search for OnCreate method and paste this code inside (change the package name if you had changed it)
```
    invoke-static {p0}, Luk/lgl/modmenu/StaticActivity;->Start(Landroid/content/Context;)V
```
 

Save the file

Copy your mod menu from decompiled app-debug.apk smali to the game's smali folder. Example mine is uk.lgl.modmenu, I copy the `uk` folder from **app-debug** `(app-debug\smali\uk)` to the game's decompiled directory `(game name)\smali`


 
Very important for multi dex games. Let's say if main activity is located in **smali_classes2**, I would put my mod menu in **smali_classes2**

Copy the library file (.so) from **app-debug.apk** to the target game. Make sure to copy .so to the correct architecture
armeabi-v7a is armeabi-v7a, arm64-v8a is arm64-v8a, and so on.
Putting the .so on a wrong architecture will result a crash

**MSHook does not support ARM64 but KittyMemory support ARM64**
 

Now compile and sign the apk
If compile fail, read the log and look up at Google

Your result should look like this in game:

 

Congratz!

If you face any problem, be sure to add your LOGD's in your code, recompile and capture the logcat. See what part of your code faced the problem

Thanks for reading the tutorial, if you need any help, feel free to ask =D

Do not forget to check my template again. I may change it anytime =D



# Credits/Acknowledgements
Thanks to the following individuals whose code helped me develop this mod menu

* Octowolve - https://github.com/z3r0Sec/Substrate-Hooking-Example and https://github.com/z3r0Sec/Substrate-Template-With-Mod-Menu

* VanHoevenTR A.K.A Nixi - https://github.com/LGLTeam/VanHoevenTR_Android_Mod_Menu

* MrIkso - First mod menu template https://github.com/MrIkso/FloatingModMenu

* AndnixSH - GTA V Sound effects - https://github.com/AndnixSH/Substrate-Template-With-Mod-Menu

* MJx0 A.K.A Ruit - https://github.com/MJx0/KittyMemory

The following websites were also very helpful

* Stackoverflow - https://stackoverflow.com/
