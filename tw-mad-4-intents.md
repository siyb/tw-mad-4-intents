% MAD - Android 4: Intents
% Patrick Sturm
% 16.10.2017

## Information

* Any issues with this presentation? Write a ticket or send me a pull request ;).
* Repo: [https://github.com/siyb/tw-mad-4-intents](https://github.com/siyb/tw-mad-4-intents)

# Agenda

## Agenda

* Introduction
* Parcelables
* Intent Objects

# Introduction

## Introduction - 1 - Resources

* Resources
    * Lesson: [http://developer.android.com/guide/topics/intents/intents-filters.html](http://developer.android.com/guide/topics/intents/intents-filters.html)
    * Javadoc: [http://developer.android.com/reference/android/content/Intent.html](http://developer.android.com/reference/android/content/Intent.html)


## Introduction - 2 - General

* We have seen Intents in the past (startActivity / startActivityForResult), we are going to learn a bit more about them today
* Intents are the preferred way to communicate between separate components in Android
* Intents can store different types of data:
    * primitives (int, float, double, char ...)
    * primitive arrays
    * String / CharSequence
    * Bundle
    * Serializable / Parcelable
    * Parcelable arrays
* Since the Serializable interface uses reflections to serialize data, Android provides the Parcelable type instead (faster), use Parcelable if possible

## Introduction - 3 - Resolving Intents

* Intents are resolved by the Android system
    * That's why it's crucial to declare components in the manifest
    * Without the manifest declaration, Intents can't be resolved properly
* Ways Intents are used to communicate with other components:
    * startActivity(…) / startActivityForResult(…) 
    * startService(…) / bindService(…)
    * sendBroadcast(…) / sendOrderedBroadcast(…) / sendStickBroadCast(…) / sendOrderedStickyBroadcast(…)

## Introduction - 4 - Data transport

* In order to communicate, we need ways of communication
* Intents can transport data
* Usually primitives, but more complex structures are supported as well

## Introduction - 5 - Data transport Example

```java
private static final String INTENT_EXTRA_ID = 
  "INTENT_EXTRA_ID"; 
private static final String INTENT_EXTRA_SOMEDATA = 
  "INTENT_EXTRA_SOMEDATA"; 

Intent i = new Intent(context, MyActivity.class); 
i.putExtra(INTENT_EXTRA_ID, userID); 
// Parcelable
i.putExtra(INTENT_EXTRA_SOMEDATA, someData); 
```

# Parcelables

## Parcelables - 1 - What are parcelables

* Parcelables are Android's way of Serializing data
* Unlike Java's Serializable interface, Parcelables do not work via reflections
    * Good news: faster
    * Bad news: we have to manually manage parcelling ...
* Parcelables work sequentially
    * They do not use mappings to save data
    * If you save a String, then a long, then an int, you need to read the data in the exact same order when deserializing
* Parcelables require the static field CREATOR to work
* Some concepts may seem weird (e.g. you can't save a boolean, just a boolean array -> performance reasons)

## Parcelables - 2 - Parcelable Example

```java
public class MyParcelable implements Parcelable { 
  private String myString; 
  private long myLong; 
  
  public MyParcelable(Parcel in) { 
    myString = in.readString(); 
    myLong = in.readLong(); 
  } 

  @Override public void 
    writeToParcel(Parcel dest, int flags) { 
    dest.writeString(myString); 
    dest.writeLong(myLong); 
  } 

  // CREATOR
}
```

## Parcelables - 3 - Parcelable Example cont.

```java
  public static final 
    Parcelable.Creator<MyParcelable> CREATOR = 
    new Parcelable.Creator<MyParcelable>() { 
      public MyParcelable 
        createFromParcel(Parcel in) {
        return new MyParcelable(in); 
      }
      public MyParcelable[] newArray(int size) { 
        return new MyParcelable[size]; 
      }
  }; 
```

# Intent Objects

## Intent Object - 1 - Important Attributes

* Component name: name of the Class (Activity, Service, etc.)
* Action: a String
    * The Action to be performed (e.g. take a Picture) OR
    * The Action that was performed (in case of a broadcast)
* Data: the URI of the data delivered with this Intent (e.g. an Image MediaStore URI)
* Category: a String
    * Additional information about the Component (e.g. CATEGORY_LAUNCHER -> The Activity can be the initial Activity of an application and gets listed in the App drawer)
* Extras: all kinds of data types
    * Additional information provided by the programmer, can be about anything :)
* Flags: different flags
    * e.g. how an Activity / Service should be launched

## Intent Object - 2 - Addressing Intents

* Intents can be addressed in different ways:
    * Explicitly: defining the component, e.g startActivity(new Intent(this, MyActivity.class));
    * Implicitly: using intent filters -> Android picks the components to handle the Intent
* We know how to handle explicit Intents, now we will be looking at implicit Intents
* Android will consult the following aspects of an implicit Intent to resolve it:
    * Action
    * Data (URI / Type)
    * Category

## Intent Objects - 3 - Intent Filters

* Implicit intents are determined using Intent filters
* Intent filters are only consulted for implicit Intents
    * Intents declaring a component will always be delivered to this component, regardless of active Intent filters
* Usually, Intent filters are declared in the manifest and not in Java code
    * Exception: BroadcastReceiver (more on them later)
* Since Android uses Action, Data and Category to filter an Intent, you can specify filters for these three Intent attributes
* Can be used by Activites, Services, BroadcastReceivers
* Intent filters are used to generalize / abstract functionality
* This combination of Intent filters will be triggered when a picture needs to be shared. The application will be shown in the share menu.


## Intent Objects - 6 - Action / Category and Data

* Action - The Action to be performed
    * android:name=“android.intent.action.SEND”
    * Implies that some data needs to be delivered to someone else
    * You can provide a chooser, so that the user can pick the application that should handle the request (caller!)
* Data - The Data to be transported
    * android:mimeType
    * The mime type of the data that we wish to send, in this case, all images (image/*)
* Category - Additional Information
    * android:name=“android.intent.category.DEFAULT”
    * Tells the Android system that this filter should be an option for a default action on some data (displayed to user)



## Intent Objects - 4 - Intent Filters Example XML

```xml
<activity 
  android:label="@string/app_name" 
  android:name=".MyActivity" > 
  <intent-filter> 
    <action android:name=
      "android.intent.action.SEND" /> 
    <data android:mimeType=
      "image/*" /> 
    <category android:name=
      "android.intent.category.DEFAULT" /> 
  </intent-filter> 
</activity>
```

## Intent Objects - 5 - Intent Filters Example Java

```java
IntentFiler intentFilter = 
  new IntentFilter("MY_ACTION");
intentFilter.addCategory("MY_CAT");
```

# Any Questions?
