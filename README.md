# Android Shell

If we type in <code>adb shell</code> (get to the Android shell), we get access to the interactive CLI inside of Android.

<img width="300" alt="Screenshot 2023-03-04 at 7 42 45 PM" src="https://user-images.githubusercontent.com/70295997/222947424-c42d7160-7ae6-4682-8c3b-4db50d005d20.png">

We have access to CLI utilities provided by <code>toybox</code>. We can also interact with Android system services and filesystem.

<img width="300" alt="Screenshot 2023-03-04 at 7 43 19 PM" src="https://user-images.githubusercontent.com/70295997/222947443-4504902c-36ee-433f-a3e5-e3ae0480db71.png">

<code>toybox</code> is where all the command-line utilities come from.

<img width="600" alt="Screenshot 2023-03-04 at 7 44 48 PM" src="https://user-images.githubusercontent.com/70295997/222947459-90423975-44b0-41f8-bf6c-51495e50ac97.png">

<img width="600" alt="Screenshot 2023-03-04 at 7 55 28 PM" src="https://user-images.githubusercontent.com/70295997/222947466-8ca9bfa8-55ef-4854-93e1-1dc872fa56e0.png">

When we have access to shell we can also interact with the framework utilities, like Activity Manager. We can start an activity using Activity Manager.
We can also interact with <code>logcat</code>, for example redirect its output to some file and then get that file from the device to our dev machine and email it to someone.

<img width="600" alt="Screenshot 2023-03-04 at 7 57 53 PM" src="https://user-images.githubusercontent.com/70295997/222947514-494fd3a0-5ace-4f5d-aabd-c5bf5c52d17e.png">

Also, when we have access to shell, we can interact with runtime utilities. For example, with Dalvik Virtual Machine we can ask it to run a random Java app, which is not an Android app but was complied to Dalvik byte code. We can ask Android to run such an app, and it can be a server, a web server for example if we compile it through Dalvik.

<img width="600" alt="Screenshot 2023-03-04 at 7 59 17 PM" src="https://user-images.githubusercontent.com/70295997/222947541-28999528-25ac-4a93-9589-110d5b878153.png">

### <code>adb shell getprop sys.boot_completed</code>
There is an interesting property that we can access through <code>shell</code>, which is <code>boot_completed</code>. When setting up a CI pipeline involving Android emulators, we come across this command. This is a flag that we can periodically query for, and wait until its state changes to True. Therefore we can be sure that our system is fully booted and proceed with installing apps on it, and perform automated tests.

### <code>adb shell setprop debug.layout true</code>
We can also set some system properties like a Debug Layout. It shows layout bounds per developer options.

### <code>adb shell pm hide/unhide</code>
We can also do <code>pm hide</code> or <code>pm unhide</code>, which is a command for Package Manager. It allows us to hide certain packages, and therefore we cannot open them. For example, if we’re developing a custom launcher for our device we can hide the default launcher, and therefore the system will not ask us which launcher we want to choose if we click on Home.

## File System

All of the installed APKs from user space are inside the /data/app folder. We can access the app at the path /data/app/com.my_app.demo which includes the package name. It’ll output the APK file which corresponds to the package name.

    generic_x86:/data/app # cd com.my_app.demo
    generic_x86:/data/app/com.my_app.demo # ls
    base.apk lib

### /data/system/<code>packages.xml</code>
- installation paths
- permissions
- user id/shared user id (system apps)

We can also extract the file called <code>packages.xml</code> which contains all the installation paths for all the installed apps. It contains all the permissions for the installed apps, including system apps (pre-installed ones). This is the thing that we can’t usually access through UI. In most cases we do not see what permissions the system apps have. They may have some permissions that we don’t want them to have, for example, camera or audio record. 
If we are curious we can view the user ids assigned to certain apps and we find out that system apps usually share the user id because they are run by a user called System.

/data/system/job/<code>jobs.xml</code>
- All background jobs (from system and user apps)
- Package names
- Constraints (idle / charging)

We can take a look at the jobs.xml file which contains all background jobs that were scheduled both by system and user apps. We can look at package names. For example, if our device’s battery life is not doing well, we can look in this place and see if any apps are triggering too many background jobs. Or, if we are using background jobs extensively, we can debug it this way. If we are debugging an app which uses some jobs, we can force our jobs to be executed with the using of the <code>-f</code> flag.

<img width="600" alt="Screenshot 2023-03-04 at 9 16 40 PM" src="https://user-images.githubusercontent.com/70295997/222948274-d7e54061-9b8a-4f31-8368-3693ccf3427e.png">

    /system
    /system/app
    /system/priv-app
    /system/framework

If we look at the file system, we can find interesting things there as well. For example, print those apps that belongs to /system/app or /system/priv-app. ‘priv’ means privileged, not private. Privileged means that these apps can access non-public hidden APIs that we don’t see in Android Studio, and system apps can’t access them because they are privileged.

In /system/framework we can find the <code>framework.jar</code> which is indeed our whole framework.
So this is the file that gets pre-loaded in the <code>zygote</code> from which every Android app is forking its initial process. And it contains other files like View.java, Context, Activity, all the other files that we are not shipping together with our app. Instead they are taken from framework.

<img width="600" alt="Screenshot 2023-03-04 at 6 19 33 PM" src="https://user-images.githubusercontent.com/70295997/222948381-aaad00c0-a969-4a43-a7aa-781c7302ecdf.png">

If we are curious and stubborn at the same time, we can compare how these files are changed by OEMs, because we can browse how this file looks in AOSP (Android Open Source Project). We can browse Activity or View code, but the code that actually ships on some devices may be changed by the manufacturers. For example, it commonly happens that some tiny little details will look different on Samsung or Huawei, because these differences reside in files like View.java which are shipped on the device.
And if we extract the <code>framework.jar</code> file, we can also take a look at internal Android classes which we cannot access through SDK.

<img width="300" alt="Screenshot 2023-03-04 at 6 22 18 PM" src="https://user-images.githubusercontent.com/70295997/222948446-eeb3b3f1-233a-4b32-ad05-9c1cf6664bba.png">

For example, if we open Activity.java file in Android Studio, there’s a field called AutofillClient() which we cannot view because it’s not meant to be used by app developers or testers. But if we’re curious, we can take a look at what looks like an AOSP. Or we can look at it on the device that we are developing/testing our app against by extracting the <code>framework.jar</code> file.

<img width="400" alt="Screenshot 2023-03-04 at 6 30 00 PM" src="https://user-images.githubusercontent.com/70295997/222948532-30194779-09dc-49f0-97c6-fcb756573367.png">

### <code>ls -la /system/bin</code>

In /system/bin we can see all the binaries we can interact with through command line. 
And also view some generic system images of an emulator, there are 27K of them.
And we can see things like Activity Manager or audio server. And things like <code>grep</code>, <code>ps</code>, <code>ls</code>, <code>pwd</code> - they are all here as well.

<img width="400" alt="Screenshot 2023-03-04 at 6 35 23 PM" src="https://user-images.githubusercontent.com/70295997/222948567-66823f47-15a9-4e03-b84d-6d2bc320fc09.png">

### <code>ls -la /system/xbin</code>

/system/xbin is another folder that contains such binaries. On an emulator we can find a binary called SwitchUser (su) which we cannot find on a production build. For example, on my OnePlus this folder is empty.

<img width="400" alt="Screenshot 2023-03-04 at 6 41 50 PM" src="https://user-images.githubusercontent.com/70295997/222948676-bf359d8f-38ed-4c5d-a904-2597c7b8b9b8.png">



















