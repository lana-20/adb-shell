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


