# SendBird Calls for Android

[![Platform](https://img.shields.io/badge/platform-android-orange.svg)](https://github.com/sendbird/sendbird-calls-android)
[![Languages](https://img.shields.io/badge/language-java-orange.svg)](https://github.com/sendbird/sendbird-calls-android)
[![Maven](https://img.shields.io/badge/maven-v0.8.1-green.svg)](https://github.com/sendbird/sendbird-calls-android/tree/master/com/sendbird/sdk/sendbird-calls/0.8.1)
[![Commercial License](https://img.shields.io/badge/license-Commercial-brightgreen.svg)](https://github.com/sendbird/sendbird-calls-android/blob/master/LICENSE.md)

## Introduction
`SendBird Calls` is the newest addition to our product portfolio. It enables real-time calls between users within your SendBird application. SDKs are provided for iOS, Android, and JavaScript. Using any one of these, developers can quickly integrate voice and video call functions into their own client apps allowing users to make and receive web-based real-time voice and video calls on the SendBird platform.

## Functional Overview
Our Android SDK for Calls provides the framework to make and receive voice and video calls. Direct calls in the SDK refers to one-to-one calls similar to that of the direct messages (DMs) in messaging services. To make a direct call, the caller should first initialize the call request by dialing to the callee whose all authenticated devices will be notified. The callee then can choose to accept the call from one of the devices. When the call is accepted, a connection is established between the caller and callee, and marks the start of the direct call. SendBird dashboard provides all call logs in the Calls menu for admins to review.

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
    implementation 'com.sendbird.sdk:sendbird-calls:0.8.1'
}
```

## Grant system permissions to the SDK
The SDK requires system permissions. The following permissions allow the SDK to access microphone and use audio, as shown here:

```manifest
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

The `RECORD_AUDIO` permission is one of the `dangerous` permissions and requires user agreement when your client app is first launched on the user’s device running Android 6.0 or higher.

For more information about requesting app permissions, see the Android’s Request App Permissions [guide](https://developer.android.com/training/permissions/requesting.html).

## Initialize the SendBirdCall instance in a client app
As shown below, the `SendBirdCall` instance must be initiated when a client app is launched. If another initialization with another `APP_ID` takes place, all existing data will be deleted and the `SendBirdCall` instance will be initialized with the new `APP_ID`.

```java
SendBirdCall.init(getApplicationContext(), APP_ID);
```

## Authenticate a user and register a push token

In order to make and receive calls, authenticate the user with SendBird server with the the `SendBirdCall.authenticate()` method. To receive calls while an app is in the background or closed, a device registration token must be registered to the server. Register a device push token during authentication by either providing it as a parameter in the `authenticate()` method, or after authentication has completed using the `SendBirdCall.registerPushToken()` method. For more details on registering push tokens, please refer to SendBird’s documentation [here](https://docs.sendbird.com/android/push_notifications).

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
The SDK provides two types of event handlers for various events on your client application: `SendBirdCallListener` and `DirectCallListener.`

### SendBirdCallListener
Register a device-specific `SendBirdCallListener` event handler using the `SendBirdCall.addSendBirdCallListener()` method. Before adding the `SendBirdCallListener`, a user can't receive an `onRinging()` callback event. Therefore, it is recommended to add this handler at the beginning of the app. The `SendBirdCallListener` can be removed when the app finishes as well. (Even though customer doesn't remove it, it will be removed along with termination of application. Once the listener is added, responding to device-wide events (for example. incoming calls) is then managed as shown below:

```java
SendBirdCall.addListener(UNIQUE_HANDLER_ID, new SendBirdCallListener() {
    @Override
    public void onRinging(DirectCall call) {
    }
});
```

`UNIQUE_HANDLER_ID` is any unique string value (for example, UUID).

| Method      | Invoked when                                        |
|-------------|-----------------------------------------------------|
| onRinging() | Incoming calls are received in the callee’s device. |

### DirectCallListener
Register a call-specific `DirectCallListener` event handler using the `DirectCall.addCallListener()` method. Responding to call-specific events (for example, call connected) is then managed as shown below:

```java
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
```

| Method                         | Invoked when |
|--------------------------------|--------------|
| onEstablished()                | On the caller’s device and the callee’s device, the callee has accepted the call by running the method `call.accept()`, but they are not yet connected to media devices. |
| onConnected()                  | Media devices (for example, microphone and speakers) between the caller and callee are connected and can start the call using media devices. |
| onEnded()                      | The call has ended on the caller’s device or the callee’s device. This is triggered automatically when either party runs the method `call.end()`. This event listener is also invoked if there are other reasons for ending the call. A table of which can be seen at the bottom.  |
| onRemoteAudioSettingsChanged() | On the caller’s devices, the callee changes their audio settings. |

## Make a call
Initiate a call by providing the callee’s user id into the `SendBirdCall.dial()` method. Use the `CallOptions` object to choose initial call configuration. (for example. muted/unmuted)

```java
DirectCall call = SendBirdCall.dial(CALLEE_ID, false, new CallOptions(), new DialHandler() {
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

    @Override
    public void onRemoteAudioSettingsChanged(DirectCall call) {
    }
});
```

## Receive a call
Receive incoming calls by registering the `SendBirdCallListener`. Accept or decline incoming calls using the `directCall.accept()` or the `directCall.end()` methods.

If the call is accepted, a media session will automatically be established by the SDK.

The `directCall.setListener()` must be registered in the `SendBirdCallListener` before accepting a call. Once registered, the listener `directCall.setListener()` enables reacting to mid-call events via callbacks methods.

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

        call.accept(new CallOptions());
    }
});
```

Incoming calls are received either via the application's persistent internal server connection, or (if the application is in the background) via FCM (Firebase Cloud Messaging) push notifications. FCM messages received by the SendBirdCall instance must be delivered to the SDK.

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

While a call is in progress, mute or unmute the caller’s microphone using the `directCall.muteMicrophone()` or `directCall.unmuteMicrophone()` method(s). If the callee changes their audio settings, the caller is notified via the `DirectCallListener.onRemoteAudioSettingsChanged()` listener.

```java
// Mute my microphone
call.muteMicrophone();

// Unmute my microphone
call.unmuteMicrophone();

// Receives the event
call.setListener(new DirectCallListener() {
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
    ...
});

```

## End a call
A caller can end a call using the `directCall.end()` method. The event can then be processed via the `DirectCallListener.onEnded()` listener. This listener is also called/fired when the callee ends the call.

```java
// End a call
call.end();

// Receives the event
call.setListener(new DirectCallListener() {
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
SendBird server automatically stores details of calls, which can be used later to display a call history for users. A user’s call history is available via a `DirectCallLogListQuery` instance.

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
| next()            | Used to query call history from `SendBirdCall` server. |
| hasNext()         | If true, there is more call history to be retrieved. |
| isLoading()       | If true, call history is being retrieved from `SendBirdCall` server. |
| Params.limit      | Specifies the number of call logs to return at once. |
| Params.myRole     | Returns call logs of the specified role. For example, the `setMyRole(Callee)` returns only the callee’s call logs. |
| Params.endResults | Returns the call logs for specified results. If you specify more than one result, they are processed as `OR` condition and all call logs corresponding with the specified end results will be returned. For example, `setEndResults(NO_ANSWER, CANCELED)`, only the `NO_ANSWER` and `CANCELED` call logs will be returned. |

## Additional information: call results

To access the additional information relating to why a call ended, consider that you can call `directCall.getEndResult()` whenever needed. However, it would be most relevant perhaps, to call it within the `onEnded()` callback.  

| DirectCallEndResult   | Description |
|-----------------------|-------------|
| NO_ANSWER             | The callee hasn’t either accepted or declined the call for a specific period of time. |
| CANCELED              | The caller has canceled the call before the callee accepts or declines. |
| DECLINED              | The callee has declined the call. |
| COMPLETED             | The call has ended by either the caller or callee after completion. |
| TIMED_OUT             | SendBird server failed to establish a media session between the caller and callee within a specific period of time. |
| CONNECTION_LOST       | Data streaming from either the caller or the callee has stopped due to a WebRTC connection issue while calling. |
| DIAL_FAILED           | The `dial()` method call has failed. |
| ACCEPT_FAILED         | The `accept()` method call has failed. |
| OTHER_DEVICE_ACCEPTED | When the call is accepted on one of the callee’s devices, all the other devices will receive this call result. |
