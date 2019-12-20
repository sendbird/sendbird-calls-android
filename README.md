# SendBird Calls for Android

## Introduction
SendBird Calls is a new product enabling real-time calls between users registered within a SendBird application. SDKs are provided for JavaScript, Android, and iOS. Using any one of these, developers can quickly integrate calling functions into their own applications that will allow users to make and receive real-time voice calls on the SendBird platform.

## Functional Overview
When implemented, the SendBird Calls SDK provides the framework to both make and receive one-to-one calls, referred to in the SDK as “direct calls” (analogous to “direct messages” / “DMs” in a messaging context).  Direct calls are made when a caller identifies a user on the SendBird application and initializing a call request (dialing). The callee, with the SDK’s event handlers implemented, is notified on all authenticated devices, and can choose to accept the call.  If accepted, a network route is established between the caller and callee, and the direct call begins.  Application administrators can then review call logs in the “Calls” section of the SendBird dashboard.

## SDK Prerequisites
* Android 4.1 (API level 16) or later
* Java 8 or later

## Install and configure the SDK
Download and install the SDK using Gradle.
```groovy
dependencies {
    implementation 'com.sendbird.sdk:sendbird-calls:0.6.0'
}
```

## Initialize the `SendBirdCall` instance in a client app
As shown below, the SendBirdCall instance must be initiated when a client app is launched. If another initialization with another APP_ID takes place, all existing data will be deleted and the SendBirdCall instance will be initialized with the new APP_ID.
```java
SendBirdCall.init(getContext(), APP_ID);
```

## Authenticate a user and register a push token

In order to make and receive calls, authenticate the user with SendBird server with the the `SendBirdCall.authenticate()` method. To receive calls while an app is in the background or closed, a device registration token must be registered to the server. Register a device push token during authentication by either providing it as a parameter in the authenticate() method, or after authentication has completed using the `SendBirdCall.registerPushToken()` method.
```java
// Authentication
AuthenticateParams params = new AuthenticateParams(USER_ID)
        .setAccessToken(ACCESS_TOKEN)
        .setPushToken(PUSH_TOKEN, isUnique);

SendBirdCall.authenticate(params, new AuthenticateHandler() {
    @Override
    public void onResult(User user, SendBirdException e) {
        if (e == null) {
            //The user is authenticated successfully
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
                    // The push token has been registered successfully
                }
            }
        });
    }
    ...
}
```

## Register event handlers

### SendBirdCallListener
Register a device-specific `SendBirdCallListener` event handler using the `SendBirdCall.addSendBirdCallListener()` method. Responding to device-wide events (e.g. incoming calls) is then managed as shown below:

```java
SendBirdCall.addListener(UNIQUE_HANDLER_ID, new SendBirdCallListener() {
    @Override
    public void onRinging(DirectCall call) {
    }
});
```
<br/>

| Method        | Description                                |
|---------------|--------------------------------------------|
|onRinging()    | Called when incoming calls are received.   |

### DirectCallListener
Register a call-specific `DirectCallListener` event handler using the `DirectCall.addCallListener()` method. Responding to call-specific events (e.g. call connected) is then managed as shown below:

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
<br/>

| Method                        | Description                                                                                                       |
|-------------------------------|-------------------------------------------------------------------------------------------------------------------|
|onEstablished()                | Called when the callee has accepted the call, but not yet connected to media devices.                             |
|onConnected()                  | Called when media devices between the caller and callee are connected and can start the call using media devices. |
|onEnded()                      | Called when the call has ended.                                                                                   |
|onRemoteAudioSettingsChanged() | Called when the peer changes audio settings.

## Make a call
Initiate a call by providing the callee’s user id into the `SendBirdCall.dial()` method.  Use the `CallOptions` object to choose initial call configuration (e.g. muted/unmuted) 

```java
DirectCall call = SendBirdCall.dial(CALLEE_ID, false, new CallOptions(), new DialHandler() {
    @Override
    public void onResult(DirectCall call, SendBirdException e) {
        if (e == null) {
            // The call has been created successfully.
        }
    }
});
```

> Note: Video call will be available later.

## Receive a call
Receive incoming calls by registering the `SendBirdCallListener`. Accept or decline incoming calls using the `directCall.accept()` or the `directCall.end()` methods. If the call is accepted, a media session will be established by the SDK. The `directCall.setListener()` must be registered through the event handler before accepting a call. Once registered, this listener enables reacting to mid-call events via callbacks methods.

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
            // handle non-SendBirdCall Firebase messages.
        }
    }
}
```

## Handle a current call

While a call is in progress, mute or unmute the caller’s audio using the `directCall.muteMicrophone()` or `directCall.unmuteMicrophone()` method(s). If the callee changes their audio settings, the caller is notified via the `DirectCallListener.onRemoteAudioSettingsChanged()` listener.

```java
// mute my microphone
call.muteMicrophone();

// unmute my microphone
call.unmuteMicrophone();

// receives the event
call.setListener(new DirectCallListener() {
    ...
    @Override
    public void onRemoteAudioSettingsChanged(DirectCall call) {
        if (call.isRemoteAudioEnabled()) {
            // The peer has been unmuted.            
        } else {
            // The peer has been muted.
        }
    }
    ...
});

```

## End a call
A caller ends using the `directCall.end()` method. The event is then processed via the `DirectCallListener.onEnded()` listener. This listener is also used when if the callee ends call.

```java
// End a call
call.end();

// receives the event
call.setListener(new DirectCallListener() {
    ...
    @Override
    public void onEnded(DirectCall call) {

    }
    ...
});
```

## Retrieve a call information
The local or remote user’s information is available via the directCall.getLocalUser() and directCall.getRemoteUser() methods.

## Retrieve call history
A user’s call history is available via a DirectCallLogListQuery instance.

```java
DirectCallLogListQuery.Params params = new DirectCallLogListQuery.Params();
DirectCallLogListQuery query = SendBirdCall.createDirectCallLogListQuery(params);
query.next(new DirectCallLogListQueryResultHandler() {
    @Override
    public void onResult(List<DirectCallLog> callLogs, SendBirdException e) {
        if (e == null) {

        }
    }
});
```

| Method           | Description                                                                                                                                                                                                                                                                                                       |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|next()            | Used to query call history from SendBirdCall server.                                                                                                                                                                                                                                                              |
|hasNext()         | If true, there is more call history to be retrieved.                                                                                                                                                                                                                                                              |
|isLoading()       | If true, call history is being retrieved from SendBirdCall server.                                                                                                                                                                                                                                                |
|Params.limit      | Specifies the number of call logs to return at once.                                                                                                                                                                                                                                                              |
|Params.myRole     | Returns call logs of the specified role. For example, the setMyRole(Callee) returns only the callee’s call logs.                                                                                                                                                                                                  |
|Params.endResults | Returns the call logs for specified results. If you specify more than one result, they are processed as OR condition and all call logs corresponding with the specified end results will be returned. For example, setEndResults(NO_ANSWER, CANCELED), only the NO_ANSWER and CANCELED call logs will be returned.|

## Additional information: call results
| EndResult        | Description                                                                                                            |
|------------------|------------------------------------------------------------------------------------------------------------------------|
| NO_ANSWER            | The callee hasn’t either accepted or declined the call for a specific period of time.                              |
| CANCELED             | The caller has canceled the call before the callee accepts or declines.                                            |
| DECLINED             | The callee has declined the call.                                                                                  |
| COMPLETED            | The call has ended by either the caller or callee after completion.                                                |
| TIMED_OUT            | SendBird server failed to establish a media session between the caller and callee within a specific period of time.|
| CONNECTION_LOST      | Data streaming from either the caller or the callee has stopped due to a WebRTC connection issue while calling.    |
| DIAL_FAILED          | The dial() method call has failed.                                                                                 |
| ACCEPT_FAILED        | The accept() method call has failed.                                                                               |
| OTHER_DEVICE_ACCEPTED| When the call is accepted on one of the callee’s devices, all the other devices will receive this call result.     |