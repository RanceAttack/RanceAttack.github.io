---
layout: post
title: bookmark of jni
categories:
- Programming
tags:
- Android
- jni

---

##JavaVM and JNIEnv

JNI defines two key data structures, "JavaVM" and "JNIEnv". Both of these are essentially pointers to pointers to function tables. (In the C++ version, they're classes with a pointer to a function table and a member function for each JNI function that indirects through the table.) The JavaVM provides the "invocation interface" functions, which allow you to create and destroy a JavaVM. In theory you can have multiple JavaVMs per process, but Android only allows one.

The JNIEnv provides most of the JNI functions. Your native functions all receive a JNIEnv as the first argument.

**The JNIEnv is used for thread-local storage. For this reason, you cannot share a JNIEnv between threads.** If a piece of code has no other way to get its JNIEnv, you should share the JavaVM, and use GetEnv to discover the thread's JNIEnv. (Assuming it has one; see AttachCurrentThread below.)

The C declarations of JNIEnv and JavaVM are different from the C++ declarations. **The "jni.h" include file provides different typedefs depending on whether it's included into C or C++.** For this reason it's a bad idea to include JNIEnv arguments in header files included by both languages. (Put another way: if your header file requires #ifdef __cplusplus, you may have to do some extra work if anything in that header refers to JNIEnv.)

##Threads
All threads are Linux threads, scheduled by the kernel. They're usually started from managed code (using Thread.start), but they can also be created elsewhere and then attached to the JavaVM. For example, a thread started with pthread_create can be attached with the JNI AttachCurrentThread or AttachCurrentThreadAsDaemon functions. **Until a thread is attached, it has no JNIEnv, and cannot make JNI calls.**

Attaching a natively-created thread causes a java.lang.Thread object to be constructed and added to the "main" ThreadGroup, making it visible to the debugger. Calling AttachCurrentThread on an already-attached thread is a no-op.

Android does not suspend threads executing native code. If garbage collection is in progress, or the debugger has issued a suspend request, Android will pause the thread the next time it makes a JNI call.

Threads attached through JNI must call DetachCurrentThread before they exit. If coding this directly is awkward, in Android 2.0 (Eclair) and higher you can use pthread_key_create to define a destructor function that will be called before the thread exits, and call DetachCurrentThread from there. (Use that key with pthread_setspecific to store the JNIEnv in thread-local-storage; that way it'll be passed into your destructor as the argument.)


##jclass, jmethodID, and jfieldID
If you want to access an object's field from native code, you would do the following:

> * Get the class object reference for the class with **FindClass**
> * Get the field ID for the field with **GetFieldID**
> * Get the contents of the field with something appropriate, such as **GetIntField**

Similarly, to call a method, you'd first get a class object reference and then a method ID. The IDs are often just pointers to internal runtime data structures. Looking them up may require several string comparisons, but once you have them the actual call to get the field or invoke the method is very quick.

If performance is important, it's useful to look the values up once and cache the results in your native code. Because there is a limit of one JavaVM per process, it's reasonable to store this data in a static local structure.

The class references, field IDs, and method IDs are guaranteed valid until the class is unloaded. Classes are only unloaded if all classes associated with a ClassLoader can be garbage collected, which is rare but will not be impossible in Android. **Note however that the jclass is a class reference and must be protected with a call to *NewGlobalRef (see the next section).**

If you would like to cache the IDs when a class is loaded, and automatically re-cache them if the class is ever unloaded and reloaded, the correct way to initialize the IDs is to add a piece of code that looks like this to the appropriate class: