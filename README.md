# SendBird Calls for Android

[![Platform](https://img.shields.io/badge/platform-android-orange.svg)](https://github.com/sendbird/sendbird-calls-android)
[![Languages](https://img.shields.io/badge/language-java-orange.svg)](https://github.com/sendbird/sendbird-calls-android)
[![Maven](https://img.shields.io/badge/maven-v1.1.3-green.svg)](https://github.com/sendbird/sendbird-calls-android/tree/master/com/sendbird/sdk/sendbird-calls/1.1.3)
[![Commercial License](https://img.shields.io/badge/license-Commercial-brightgreen.svg)](https://github.com/sendbird/sendbird-calls-android/blob/master/LICENSE.md)

## Introduction
`SendBird Calls` is the latest addition to our product portfolio. It enables real-time calls between users within a SendBird application. SDKs are provided for iOS, Android, and JavaScript. Using any one of these, developers can quickly integrate voice and video call functions into their own client apps, allowing users to make and receive web-based real-time voice and video calls on the SendBird platform.

> If you need any helps or have any issue / question, please visit [our community](https://community.sendbird.com) 

## Functional Overview
The SendBird Calls Android SDK provides a framework to make and receive voice and video calls. “Direct calls” in the SDK refers to one-to-one calls, comparable to “direct messages” (DMs) in messaging services. To make a direct voice or video call, the caller specifies the user ID of the intended callee, and dials. Upon dialing, all of the callee’s authenticated devices will receive incoming call notifications. The callee then can choose to accept the call from any one of the devices. When the call is accepted, a connection is established between the caller and the callee. This marks the start of the direct call. Call participants may mute themselves, as well as select the audio and video hardware used in the call. Calls may be ended by either party. The SendBird Dashboard displays call logs in the Calls menu for application owners and admins to review.

## SDK Prerequisites
* Android 4.1 (API level 16) or later
* Java 8 or later

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

## Install and configure the SDK
Download and install the SDK using `Gradle`.

```groovy
dependencies {
    implementation 'com.sendbird.sdk:sendbird-calls:1.1.3'
}
```

## Grant system permissions to the SDK
The SDK requires system permissions. The following permissions allow the SDK to access the microphone and use audio, as shown here:

```manifest
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

The `CAMERA` and `RECORD_AUDIO` permissions are classified as `dangerous`  and require users to grant them explicitly when an app is run for the first time on devices running Android 6.0 or higher.

For more information about requesting app permissions, see  Android’s Request App Permissions [guide](https://developer.android.com/training/permissions/requesting.html).

## (Optional) Configure ProGuard to shrink code and resources
When you build your APK with `minifyEnabled true`, add the following line to the module's ProGuard rules file.
```
# SendBird Calls SDK
-keep class com.sendbird.calls.** { *; }
-keep class org.webrtc.** { *; }
-dontwarn org.webrtc.**
-keepattributes InnerClasses
```

## Initialize the SendBirdCall instance in a client app
As shown below, the `SendBirdCall` instance must be initiated when a client app is launched. If another initialization with another `APP_ID` takes place, all existing data will be deleted and the `SendBirdCall` instance will be initialized with the new `APP_ID`.

```java
SendBirdCall.init(getApplicationContext(), APP_ID);
```

## Authenticate a user and register a push token

In order to make and receive calls, users must first be authenticated on the SendBird server using the `SendBirdCall.authenticate()` method. To receive calls while an app is either in the background or closed entirely, a device registration token must be registered. A push tokens may be registered during authentication as a parameter in the `authenticate()` method, or after authentication as a parameter in  the `SendBirdCall.registerPushToken()` method. For more details on registering push tokens, please refer to SendBird’s documentation [here](https://docs.sendbird.com/android/push_notifications).

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

## Register event handlers
The SDK provides two types of event handlers for various events that client apps may respond to: `SendBirdCallListener` and `DirectCallListener.`

### SendBirdCallListener
Register a device-specific `SendBirdCallListener` event handler using the `SendBirdCall.addListener()` method. Prior to this, the `onRinging()` event cannot be detected. It is therefore recommended that this event handler be added during  initialization.  `SendBirdCallListener` is removed upon app termination. After `SendBirdCallListener` is added, responding to device-wide events (e.g. incoming calls) is handled as shown below:

```java
SendBirdCall.addListener(UNIQUE_HANDLER_ID, new SendBirdCallListener() {
    @Override
    public void onRinging(DirectCall call) {
    }
});
```

`UNIQUE_HANDLER_ID` is any unique string value (e.g. UUID).

| Method      | Invoked when                                        |
|-------------|-----------------------------------------------------|
| onRinging() | Incoming calls are received on the callee’s device. |

### DirectCallListener
Register a call-specific `DirectCallListener` event handler using the `DirectCall.addCallListener()` method. Responding to call-specific events (e.g. sucessfull call connection) is then handled as shown below:

```java
directCallsetListener(new DirectCallListener() {
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
});
```

| Method                         | Invocation Criteria |
|--------------------------------|--------------|
| onEstablished()                | The callee accepted the call using the method `directCall.accept()`, but neither the caller or callee’s devices are as of yet connected to media devices. |
| onConnected()                  | Media devices (e.g. microphone and speakers) between the caller and callee are connected and the voice or video call can begin. |
| onEnded()                      | The call has ended on either the caller or the callee’s devices. This is triggered automatically when either party runs the method `directCall.end()`. This event listener is also invoked if the call is ended for other reasons. See the bottom of this readme for a list of all possible reasons for call termination.  |
| onRemoteAudioSettingsChanged() | The other party changed their audio settings. |
| onRemoteVideoSettingsChanged() | The other party changed their video settings. |
| onCustomItemsUpdated()         | One or more of `DirectCall`’s custom items (metadata) have been updated. |
| onCustomItemsDeleted()         | One or more of `DirectCall`’s custom items (metadata) have been deleted. |
| onReconnecting()               | `DirectCall` started attempting to reconnect to the other party after a media connection disruption. |
| onReconnected()                | The disrupted media connection reconnected. |
| onAudioDeviceChanged()         | The audio device used in the call has changed. |


## Make a call
Initiate a call by first preparing the `DialParams` call parameter object.  This contains the intended callee’s user id, whether or not it is a video call, as well as a `CallOptions` object.  `CallOptions` is used to set the call’s initial configuration (e.g. muted/unmuted). Once prepared, the `DialParams` object is then passed into the `SendBirdCall.dial()` method to starting making a call.
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

## Receive a call
Receive incoming calls by first registering `SendBirdCallListener`. Accept or decline incoming calls using the `directCall.accept()` or the `directCall.end()` methods. If the call is accepted, a media session will automatically be established.

Before accepting any calls, the `directCall.setListener()` must be registered upfront in the `SendBirdCallListener` . Once registered,  `directCall.setListener()` enables reacting to in-call events via callbacks methods.

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

When the app is in the foreground, incoming call events are received via the SDK’s persistent internal server connection. However, when the app is closed or in the background, incoming calls are  recieved via FCM (Firebase Cloud Messaging) push notifications. FCM messages received by `SendBirdCall` must be delivered to the SDK via the `SendBirdCall.handleFirebaseMessageData()` method.

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

## Handle a current call

During an ongoing call, mute or unmute the caller’s microphone using the `directCall.muteMicrophone()` or `directCall.unmuteMicrophone()` methods. If the callee changes their audio settings, the caller is notified via the `DirectCallListener.onRemoteAudioSettingsChanged()` listener. The caller may start or stop video using the `directCall.startVideo()` or `directCall.stopVideo()` methods. If the callee changes their video settings, the caller is notified via the `DirectCallListener.onRemoteVideoSettingsChanged()` listener. Switching between the front and the back cameras is done using `directCall.switchCamera(CompletionHandler)`.

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

## End a call
A caller may end a call using the `directCall.end()` method. The event can then be processed via the `DirectCallListener.onEnded()` listener. This listener is also triggered if the callee ends the call.

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

## Retrieve a call information
The local or remote user’s information is available via the `directCall.getLocalUser()` and `directCall.getRemoteUser()` methods.

## Retrieve call history
The SendBird server automatically stores details of calls, which can be used later to display a call history for users. A user’s call history is available via a `SendBirdCall.DirectCallLogListQuery()` instance.

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

| Method            | Description |
|-------------------|-------------|
| next()            | Used to query the call history from `SendBirdCall` server. |
| hasNext()         | If true, there are additional call history entries yet to be retrieved. |
| isLoading()       | If true, the call history is being retrieved from the server. |
| Params.limit      | Specifies the number of call history entries to return at once. |
| Params.myRole     | Returns the call history of the specified role. (e.g. the `setMyRole(Callee)` returns only the callee’s call history.) |
| Params.endResults | Filters the results based on the call end result (e.g. `COMPLETED`,`NO_ANSWER`,etc.) If multiple values are specified, they are processed as an `OR` condition. For example, `setEndResults(NO_ANSWER, CANCELED)`, only the history entries that resulted in `NO_ANSWER` or `CANCELED` will be returned. |

## Additional information: call results

Information relating the end result of a call can be obtained at any time via the  `directCall.getEndResult()`  method, best invoked within the `onEnded()` callback.  

| DirectCallEndResult   | Description |
|-----------------------|-------------|
| NO_ANSWER             | The callee failed to either accept or decline the call within a specific amount of time. |
| CANCELED              | The caller canceled the call before the callee could accept or decline. |
| DECLINED              | The callee declined the call. |
| COMPLETED             | The call ended after either party ended it |
| TIMED_OUT             | The SendBird server failed to establish a media session between the caller and callee within a specific amount of time. |
| CONNECTION_LOST       | The data stream from either the caller or the callee has stopped due to a WebRTC connection issue. |
| DIAL_FAILED           | The `dial()` method call has failed. |
| ACCEPT_FAILED         | The `accept()` method call has failed. |
| OTHER_DEVICE_ACCEPTED | The incoming call was accepted on a different device. This device received an incoming call notification, but the call ended when a different device accepted it. |

## Additional information: encoding configurations

| Category           | Value | Note                    |
|--------------------|-------|-------------------------|
| Frames per second  | 24    |                         |
| Maximum resolution | 720p  | 1280 x 720; standard HD |
| Audio codec        | OPUS  |                         |
| Video codec        | VP8   |                         |

## Additional information: thread options

As shown below, there are two types of `ThreadOption`s in the SendBirdCall SDK: `UI_THREAD` and `HANDLER`. If `ThreadOption` is set to `UI_THREAD`, every callback will be called on the UI thread, and vice versa. `UI_THREAD` is set by default. 

```java
SendBirdCall.Options.setThreadOption(SendBirdCall.Options.ThreadOption.UI_THREAD, null);

Handler myHandler = new Handler();
SendBirdCall.Options.setThreadOption(SendBirdCall.Options.ThreadOption.HANDLER, myHandler);
```
