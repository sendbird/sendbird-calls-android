# [Sendbird](https://sendbird.com) Calls SDK for Android

[![Platform](https://img.shields.io/badge/platform-android-orange.svg)](https://github.com/sendbird/sendbird-calls-android)
[![Languages](https://img.shields.io/badge/language-java-orange.svg)](https://github.com/sendbird/sendbird-calls-android)
[![Maven](https://img.shields.io/badge/maven-v1.4.1-green.svg)](https://github.com/sendbird/sendbird-calls-android/tree/master/com/sendbird/sdk/sendbird-calls/1.4.1)
[![Commercial License](https://img.shields.io/badge/license-Commercial-brightgreen.svg)](https://github.com/sendbird/sendbird-calls-android/blob/master/LICENSE.md)

## Table of contents

  1. [Introduction](#introduction)
  1. [Before getting started](#before-getting-started)
  1. [Getting started](#getting-started)
  1. [Configure the application for the SDK](#configure-the-application-for-the-sdk)
  1. [Make your first call](#make-your-first-call)
  1. [Implementation guide](#implementation-guide)  
  1. [Appendix](#appendix)
  1. [Troubleshooting](#troubleshooting)  

<br />

## Introduction

**SendBird Calls** is the latest addition to our product portfolio. It enables real-time calls between users within a Sendbird application. SDKs are provided for iOS, Android, and JavaScript. Using Sendbird SDK helps developers to quickly integrate voice and video call functions into their client apps. This allows users to make and receive web-based real-time voice and video calls on Sendbird platform.

> If you need any help in resolving any issues or have questions, please visit [our community](https://community.sendbird.com)

### How it works

Sendbird Calls SDK for Android provides a framework to make and receive voice and video calls. “Direct calls” in the SDK refers to one-to-one calls. To make a direct voice or video call, the caller specifies the user ID of the intended callee, and dials. Upon dialing, all of the callee’s authenticated devices will receive notifications for an incoming call. The callee then can choose to accept the call from any one of the devices. When the call is accepted, a connection is established between the devices of the caller and the callee. This marks the start of a direct call. Call participants can mute themselves, or call with either or both of the audio and video by using output devices such as speaker and microphone for audio, and front, rear camera for video. A call may be ended by either party. The [Sendbird Dashboard](https://dashboard.sendbird.com/auth/signin) displays call logs in the Calls menu for dashboard owners and admins to review.

<br />

## Before getting started

This section shows the prerequisites you need to check to use Sendbird Calls SDK for Android.

### Requirements

The minimum requirements for Calls SDK for Android are:

- Android 4.1 (API level 16) or later
- Java 8 or later

```groovy
// build.gradle(app)
android {
    defaultConfig {
        minSdkVersion 16
    }

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

<br />

## Getting started

This section gives you information you need to get started with Sendbird Calls SDK for Android.

### Install and configure the SDK

Download and install the SDK using `Gradle`.

```groovy
dependencies {
    implementation 'com.sendbird.sdk:sendbird-calls:1.4.1'
}
```

### Grant system permissions to the SDK

The SDK requires system permissions. The following permissions allow the SDK to access the microphone and use audio, as shown here:

```manifest
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

The `CAMERA` and `RECORD_AUDIO` permissions are classified as `dangerous` and require users to grant them explicitly when an app is run for the first time on devices running Android 6.0 or higher.

For more information about requesting app permissions, see  Android’s Request App Permissions [guide](https://developer.android.com/training/permissions/requesting.html).

### (Optional) Configure ProGuard to shrink code and resources

When you build your APK with `minifyEnabled true`, add the following line to the module's ProGuard rules file.
```
# SendBird Calls SDK
-keep class com.sendbird.calls.** { *; }
-keep class org.webrtc.** { *; }
-dontwarn org.webrtc.**
-keepattributes InnerClasses
```

<br />

## Make your first call

Follow the step-by-step instructions below to authenticate and make your first call. 

### Step 1: Initialize the SendBirdCall instance in a client app

As shown below, the `SendBirdCall` instance must be initiated when a client app is launched. Initialize the `SendBirdCall` instance with the `APP_ID` of the Sendbird application you would like to use to make a call.

```java
SendBirdCall.init(getApplicationContext(), APP_ID);
```

> Note: If another initialization with another `APP_ID` takes place, all existing data will be deleted and the `SendBirdCall` instance will be initialized with the new `APP_ID`.

### Step 2: Authenticate a user and register a push token

In order to make and receive calls, users must first be authenticated on Sendbird server using the `SendBirdCall.authenticate()` method. To receive calls while an app is either in the background or closed entirely, a device registration token must be registered. A push token may be registered during authentication as a parameter in the `authenticate()` method, or after authentication as a parameter in  the `SendBirdCall.registerPushtokensToken()` method. 

For more details on registering a push token, refer to [Push notifications for Android](https://sendbird.com/docs/chat/v3/android/guides/push-notifications).

```java
// Authentication
AuthenticateParams params = new AuthenticateParams(USER_ID)
        .setAccessToken(ACCESS_TOKEN)
        .setPushToken(PUSH_TOKEN, isUnique);

SendBirdCall.authenticate(params, new AuthenticateHandler() {
    @Override
    public void onResult(User user, SendBirdException e) {
        if (e == null) {
            // The user is authenticated successfully.
        }
    }
});

// Update push token
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    ...
    @Override
    public void onNewToken(@NonNull String token) {
        SendBirdCall.registerPushToken(token, isUnique, new CompletionHandler() {
            @Override
            public void onResult(SendBirdException e) {
                if (e == null) {
                    // The push token has been registered successfully.
                }
            }
        });
    }
    ...
}
```

### Step 3: Add an event handler

There are two types of event handlers the SDK provides for a client app to respond to various events: `SendBirdCallListener` and `DirectCallListener`.

#### SendBirdCallListener

Register a device-specific `SendBirdCallListener` event handler using the `SendBirdCall.addListener()` method. It is recommended to add the event handler during initialization because it is a prerequisite for detecting `onRinging()` event. The code below shows the way device-wide events such as incoming calls are handled once `SendBirdCallListener` is added. 

```java
SendBirdCall.addListener(UNIQUE_HANDLER_ID, new SendBirdCallListener() {
    @Override
    public void onRinging(DirectCall call) {
    }
});
```

`SendBirdCallListener` is removed upon terminating the app.

`UNIQUE_HANDLER_ID` is any unique string value such as UUID.

| Method|Invoked when|
|---|---|
| onRinging() | Incoming calls are received on the callee’s device.|

#### DirectCallListener

Register a call-specific `DirectCallListener` event handler using the `DirectCall.addCallListener()` method. Responding to call-specific events, such as establishing a sucessfull call connection, is then handled as shown below:

```java
directCall.setListener(new DirectCallListener() {
    @Override
    public void onEstablished(DirectCall call) {}

    @Override
    public void onConnected(DirectCall call) {}

    @Override
    public void onEnded(DirectCall call) {}

    @Override
    public void onRemoteAudioSettingsChanged(DirectCall call) {}

    @Override
    public void onRemoteVideoSettingsChanged(DirectCall call) {}

    @Override
    public void onCustomItemsUpdated(DirectCall call, List<String> updatedKeys) {}

    @Override
    public void onCustomItemsDeleted(DirectCall call, List<String> deletedKeys) {}

    @Override
    public void onReconnecting(DirectCall call) {}

    @Override
    public void onReconnected(DirectCall call) {}

    @Override
    public void onAudioDeviceChanged(DirectCall call, AudioDevice currentAudioDevice, Set<AudioDevice> availableAudioDevices) {}

    @Override
    public void onRemoteRecordingStatusChanged(DirectCall call) {}
});
```

|Method|Invocation criteria|
|---|---|
|onEstablished()|The callee accepted the call using the method `directCall.accept()`, but neither the caller or callee’s devices are as of yet connected to media devices. |
|onConnected()| Media devices (e.g. microphone and speakers) between the caller and callee are connected and the voice or video call can begin. |
|onEnded()| The call has ended on either the caller or the callee’s devices. This is triggered automatically when either party runs the method `directCall.end()`. This event listener is also invoked if the call is ended for other reasons. See the bottom of this readme for a list of all possible reasons for call termination.|
|onRemoteAudioSettingsChanged()| The other party changed their audio settings. |
|onRemoteVideoSettingsChanged()| The other party changed their video settings. |
|onCustomItemsUpdated()| One or more of `DirectCall`’s custom items (metadata) have been updated. |
|onCustomItemsDeleted()| One or more of `DirectCall`’s custom items (metadata) have been deleted. |
|onReconnecting()| `DirectCall` started attempting to reconnect to the other party after a media connection disruption. |
|onReconnected()| The disrupted media connection reconnected. |
|onAudioDeviceChanged()| The audio device used in the call has changed. |
|onRemoteRecordingStatusChanged()| The other user’s recording status has been changed. |

### Step 4: Make a call

First, prepare the `DialParams` call parameter object to initiate a call. The parameter contains the intended callee’s user id and the `CallOptions` object. The `CallOptions` is used to set the call’s initial configuration, such as mute or unmute. Once prepared, the `DialParams` object is then passed into the `SendBirdCall.dial()` method to start making a call.

```java
DialParams params = new DialParams(CALLEE_ID);
params.setVideoCall(true);
params.setCallOptions(new CallOptions());

DirectCall call = SendBirdCall.dial(params, new DialHandler() {
    @Override
    public void onResult(DirectCall call, SendBirdException e) {
        if (e == null) {
            // The call has been created successfully.
        }
    }
});

call.setListener(new DirectCallListener() {
    @Override
    public void onEstablished(DirectCall call) {
    }

    @Override
    public void onConnected(DirectCall call) {
    }

    @Override
    public void onEnded(DirectCall call) {
    }
});
```

### Step 5: Receive a call

Register `SendBirdCallListner` first to receive incoming calls. Accept or decline incoming calls by using the `directCall.accept()` or the `directCall.end()` methods. If the call is accepted, a media session will automatically be established.

Before accepting any calls, the `directCall.setListener()` must be registered in the `SendBirdCallListener`. Once registered, `directCall.setListener()` enables reacting to in-call events through callbacks methods.

```java
SendBirdCall.addListener(UNIQUE_HANDLER_ID, new SendBirdCallListener() {
    @Override
    public void onRinging(DirectCall call) {
        call.setListener(new DirectCallListener() {
            @Override
            public void onEstablished(DirectCall call) {
            }

            @Override
            public void onConnected(DirectCall call) {
            }

            @Override
            public void onEnded(DirectCall call) {
            }

            @Override
            public void onRemoteAudioSettingsChanged(DirectCall call) {
            }
        });

        call.accept(new AcceptParams());
    }
});
```

<br />

## Implementation guide

### Make a call

First, prepare the `DialParams` call parameter object to initiate a call. The parameter contains the intended callee’s user id and the `CallOptions` object. The `CallOptions` is used to set the call’s initial configuration, such as mute or unmute. Once prepared, the `DialParams` object is then passed into the `SendBirdCall.dial()` method to start making a call.

```java
DialParams params = new DialParams(CALLEE_ID);
params.setVideoCall(true);
params.setCallOptions(new CallOptions());

DirectCall call = SendBirdCall.dial(params, new DialHandler() {
    @Override
    public void onResult(DirectCall call, SendBirdException e) {
        if (e == null) {
            // The call has been created successfully.
        }
    }
});

call.setListener(new DirectCallListener() {
    @Override
    public void onEstablished(DirectCall call) {
    }

    @Override
    public void onConnected(DirectCall call) {
    }

    @Override
    public void onEnded(DirectCall call) {
    }
});
```

### Receive a call

Register `SendBirdCallListner` first to receive incoming calls. Accept or decline incoming calls by using the `directCall.accept()` or the `directCall.end()` methods. If the call is accepted, a media session will automatically be established.

Before accepting any calls, the `directCall.setListener()` must be registered in the `SendBirdCallListener`. Once registered, `directCall.setListener()` enables reacting to in-call events through callbacks methods.

```java
SendBirdCall.addListener(UNIQUE_HANDLER_ID, new SendBirdCallListener() {
    @Override
    public void onRinging(DirectCall call) {
        call.setListener(new DirectCallListener() {
            @Override
            public void onEstablished(DirectCall call) {
            }

            @Override
            public void onConnected(DirectCall call) {
            }

            @Override
            public void onEnded(DirectCall call) {
            }

            @Override
            public void onRemoteAudioSettingsChanged(DirectCall call) {
            }
        });

        call.accept(new AcceptParams());
    }
});
```
When the app is in the foreground, incoming call events are received through the SDK’s persistent internal server connection. However, when the app is closed or in the background, incoming calls are received through the Firebase Cloud Messaging’s (FCM) push notifications. The FCM messages received by `SendBirdCall` must be delivered to the SDK through the `SendBirdCall.handleFirebaseMessageData()` method.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    @Override
    public void onMessageReceived(@NonNull RemoteMessage remoteMessage) {
        if (SendBirdCall.handleFirebaseMessageData(remoteMessage.getData())) {
        } else {
            // Handle non-SendBirdCall Firebase messages.
        }
    }
}
```

### Handle a current call

During an ongoing call, a caller may mute or unmute their microphone by using the `directCall.muteMicrophone()` or `directCall.unmuteMicrophone()` methods. 
If the callee changes their audio settings, the caller is notified through the `DirectCallListener.onRemoteAudioSettingsChanged()` listener. The caller may start or stop video by using the `directCall.startVideo()` or `directCall.stopVideo()` methods. 

If the callee changes their video settings, the caller is notified through the `DirectCallListener.onRemoteVideoSettingsChanged()` listener. Switching between the front and the back cameras is done using the `directCall.switchCamera(CompletionHandler)`.

```java
// mutes my microphone
directCall.muteMicrophone();

// unmutes my microphone
directCall.unmuteMicrophone();

// starts to show video
directCall.startVideo();

// stops showing video
directCall.stopVideo();

// switches camera
directCall.switchCamera(new CompletionHandler() {
    @Override
    public void onResult(SendBirdException e) {
        if (e != null) {
            Log.e(TAG, "switchCamera(e: " + e.getMessage() + ")");
        }
    }
});

// receives the event
directCall.setListener(new DirectCallListener() {
    ...
    @Override
    public void onRemoteAudioSettingsChanged(DirectCall call) {
        if (call.isRemoteAudioEnabled()) {
            // The peer has been unmuted.
            // Consider displaying an unmuted icon.
        } else {
            // The peer has been muted.
            // Consider displaying and toggling a muted icon.
        }
    }

    @Override
    public void onRemoteVideoSettingsChanged(DirectCall call) {
        if (call.isRemoteVideoEnabled()) {
            // The peer has started video.
        } else {
            // The peer has stopped video.
        }
    }
    ...
});
```

### End a call

A caller may end a call using the `directCall.end()` method. The event can then be processed through the `DirectCallListener.onEnded()` listener. This listener is also triggered if the callee ends the call.

```java
// End a call
directCall.end();

// Receives the event
directCall.setListener(new DirectCallListener() {
    ...
    
    @Override
    public void onEnded(DirectCall call) {
        // Consider releasing or destroying call-related views from here.
    }
    ...
    
});
```

### Mirror a SendBirdVideoView

Calls SDK for Android automatically mirrors SendBirdVideoView when the current camera is front-facing for a user’s local video view. By using the code below, you can manually set the current user’s local video view as mirrored or reversed when the camera is facing the user.

```java
SendBirdVideoView videoView;
videoView.setMirror(true);  // or false
```

### Retrieve a call information

User information of a local or remote user can be accessed by using the `directCall.getLocalUser()` and `directCall.getRemoteUser()` methods.

### Retrieve call history

Sendbird server automatically stores details of calls, which can later be used to display a call history for users. A user’s call history can be retrieved by using the `DirectCallLogListQuery.next()` method.

```java
DirectCallLogListQuery.Params params = new DirectCallLogListQuery.Params();
DirectCallLogListQuery query = SendBirdCall.createDirectCallLogListQuery(params);

query.next(new DirectCallLogListQueryResultHandler() {
    @Override
    public void onResult(List<DirectCallLog> callLogs, SendBirdException e) {
        if (e == null) {
            if (query.hasNext() && !query.isLoading()) {
                // query.next() can be called once more.
                // If a user wants to fetch more call logs.
            }
        }
    }
});
```

|Method|Description|
|---|---|
|next()|Used to query the call history from Sendbird Calls server.|
|hasNext()|If **true**, there are additional call history entries yet to be retrieved. |
|isLoading()|If **true**, the call history is being retrieved from the server. |
|Params.limit|Specifies the number of call history entries to return at once. |
|Params.myRole|Returns the call history of the specified role. For example, the `setMyRole(Callee)` returns only the callee’s call history.|
|Params.endResults|Filters the results based on the end result of a call, such as `COMPLETED`,`NO_ANSWER`, etc. If multiple values are specified, they are processed as an `OR` condition. For example, for `setEndResults(NO_ANSWER, CANCELED)`, only the history entries that resulted in `NO_ANSWER` or `CANCELED` will be returned. |

### Timeout options

The following table lists a set of methods of the `SendBirdCall` class.

|Method|Description|
|---|---|
|setRingingTimeout(int timeout)| Sets the time limit for an unanswered call in seconds. The default value is **60** seconds.|
|setCallConnectionTimeout(int timeout)| Sets the time limit for a connecting call in seconds. The default value is **60** seconds.|

### Sound effects

#### Sound types

| Type | Description |
|---|---|
| DIALING | Refers to a sound that is played on a caller’s side when the caller makes a call to a callee. |
| RINGING | Refers to a sound that is played on a callee’s side when receiving a call. |
| RECONNECTING | Refers to a sound that is played when a connection is lost, but immediately tries to reconnect. Users are also allowed to customize the ringtone. |
| RECONNECTED | Refers to a sound that is played when a connection is re-established. |

#### Add sound

|Method|Description|
|---|---|
|addDirectCallSound| Adds a specific sound to a direct call such as a ringtone or an alert tone with an Android resource ID. |

|Parameter|Type|Description|
|---|---|---|
|soundType|SoundType| Specifies the sound type to be used according to the event. |
|resId|int| Specifies the Android resource ID. |

#### Remove sound

|Method|Description|
|---|---|
| removeDirectCallSound | Removes a specific sound from a direct call. |

|Parameter|Type|Description|
|---|---|---|
|soundType|SoundType| Specifies the sound type to be used according to the event. |
|resId|int|Specifies the Android resource ID. |

<br />

## Appendix

### Call results

Information relating the end result of a call can be obtained at any time through the `directCall.getEndResult()` method, best invoked within the `onEnded()` callback.  

|DirectCallEndResult|Description|
|---|---|
|NO_ANSWER|The callee failed to either accept or decline the call within a specific amount of time. |
|CANCELED|The caller canceled the call before the callee could accept or decline. |
|DECLINED|The callee declined the call. |
|COMPLETED|The call ended after either party ended it |
|TIMED_OUT|Sendbird Calls server failed to establish a media session between the caller and callee within a specific amount of time. |
|CONNECTION_LOST|The data stream from either the caller or the callee has stopped due to a `WebRTC` connection issue. |
|DIAL_FAILED|The `dial()` method call has failed. |
|ACCEPT_FAILED|The `accept()` method call has failed. |
|OTHER_DEVICE_ACCEPTED |The incoming call was accepted on a different device. This device received an incoming call notification, but the call ended when a different device accepted it. |

### Encoding configurations

|Category|Value|Note|
|---|---|---|
|Frames per second |24| |
|Maximum resolution|720p| 1280 x 720; standard HD |
|Audio codec|OPUS| |
|Video codec|VP8| |

### Thread options

As shown below, there are two types of `ThreadOption` in the SendbirdCall SDK: `UI_THREAD` and `HANDLER`. If `ThreadOption` is set to `UI_THREAD`, every callback will be called on the UI thread, and vice versa. `UI_THREAD` is set by default.

```java
SendBirdCall.Options.setThreadOption(SendBirdCall.Options.ThreadOption.UI_THREAD, null);

Handler myHandler = new Handler();
SendBirdCall.Options.setThreadOption(SendBirdCall.Options.ThreadOption.HANDLER, myHandler);
```

### Android SDK sizes

|File|Raw files|Compiled size |
|---|---|---|
|Calls SDK|1.77MB|1.18MB|
|WebRTC SDK|26.8MB|12MB|
