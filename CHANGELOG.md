# Changelog

## v1.11.1 (Jun 16, 2023)

### 1.11.1 (Jun 16, 2023)
* Added `DirectCallListener.onCalleeDialReceived` to notify the caller when the calle has received the call. This event listener will be called when the callee has received the `SendBirdCallListener.onRinging` event.

## v1.11.0 (Jun 09, 2023)

### 1.11.0 (Jun 9, 2023 UTC)
For 1.11.0, the SDK supports Huawei mobile service(HMS) Push kit for huawei devices.
* Added functions related to registering and unregistering HMS tokens.
  * Added `SendBirdCalls.registerPushToken(String, PushTokenType, Boolean, CompletionHandler)` to register a given push token.
  * Added `SendBirdCalls.unregisterPushToken(String, PushTokenType, CompletionHandler)` to unregister a given push token.
  * Added `SendBirdCalls.unregisterAllPushTokens(PushTokenType, CompletionHandler)` to unregister all given push token type.
  * Deprecated `SendBirdCalls.registerPushToken(String, Boolean, CompletionHandler)`, `SendBirdCalls.unregisterPushToken(String, CompletionHandler)`, and `SendBirdCalls.unregisterAllPushTokens(CompletionHandler)`.
* Adjusted the log level of logs that may be misunderstood as indicating serious issues with the SDK.

## v1.10.9-test (Jun 08, 2023)


### 1.10.9 (May 18, 2023 UTC)
* Added `retrieveMissedDirectCalls()` in SendbirdCall

### 1.10.8 (Apr 21, 2023)
* Improved connection quality listener in Direct Calls.

### 1.10.7 (Apr 12, 2023)
* Added a feature to detect connection qualities in ongoing Direct Calls.
  * Added `ConnectionQualityListener`
  * Added `ConnectionMetrics`
  
### 1.10.6 (Mar 15, 2023 UTC)
* Added a feature to kick other participants in a room with the same user id as the current user.
    * Added Room.EnterParams.kickSiblings
    
### 1.10.5 (Mar 9, 2023, UTC)
* Add interface related to Bluetooth device connection
    * Added directCall.fetchBluetoothDevices.
    * Added localParticipant.fetchBluetoothDevices.
* Improved stability

### 1.10.4 (Feb 28, 2023 UTC)
* Improved stability

### 1.10.3 (Feb 23, 2023 UTC)
* Improved stability

### 1.10.2 (Feb 14, 2023 UTC)
* Added room event listeners to detect local participant’s connection with the server
    * Added `RoomListener.onLocalParticipantDisconnected`
    * Added `RoomListener.onLocalParticipantReconnected`
* Added `directCall.resumeVideoCapturer`

### 1.10.1 (Jan 31, 2023 UTC)
* Added a feature to invite users to enter a room.
    * Added `room.sendInvitation(inviteeId:completionHandler:)`.
    * Added `RoomInvitation` class to manage invitation.
    * Added `roomInvitation.accept`, `roomInvitation.decline`, `roomInvitation.cancel`.
    * Added `RoomDelegate.onInvitationAccepted`, `RoomDelegate.onInvitationDeclined`, `RoomDelegate.onInvitationCanceled`.
* Added a new interface to delete a room.
    * Added `room.delete`.
* Added `directCall.resumeVideoCapturer`.

### 1.10.0 (Nov 07, 2022 UTC)
* Updated webrtc version to M106

### 1.9.3 (Nov 01, 2022 UTC)
* Fixed a bug where bluetooth headphones didn't work

### 1.9.2 (Oct 14, 2022 UTC)
* Improved stability

### 1.9.1 (Aug 03, 2022 UTC)
* Fixed a bug where the packet loss rate was negative.
* Changed the `startedAt` property of DirectCall to public
* Improved stability

### 1.9.0 (Dec 9, 2021 UTC)
For 1.9.0, a feature to delete a room in Group call is released.
* Added `onDeleted()` in `RoomListener` which is invoked when the room has been deleted by the Platform API.

* Fixed a bug that causes a crash if a user hadn't granted `BLUETOOTH_CONNECT` permission on the app targeting Android 12.

### 1.8.0 (Oct 27, 2021 UTC)

For 1.8.0, a new feature is released for both Group call and Direct call features respectively.

For the Group call feature, you can now add and manage custom items to store additional information for a room.
* Here are the details of the update:
    * Added `customItems` in `Room`.
    * Added `customItems` in `RoomParams`.
    * Added `updateCustomItems(Map<String, String>, CompletionHandler)` and `deleteCustomItems(Set<String>, CompletionHandler:)` in `Room`.
    * Added `onCustomItemsUpdated(List<String>)` and `onCustomItemsDeleted(List<String>)` in `RoomListener`.

For the Direct call feature, you can now hold and resume calls which allows you to accept an incoming call or switch between calls.
* Here are the details of the update:
    * Added `hold(CompletionHandler)` and `unhold(Boolean, CompletionHandler)` in `DirectCall`.
    * Added `isOnHold` in `DirectCall`.
    * Added `holdActiveCall` in `DialParams` and `AcceptParams`.
    * Added `onUserHoldStatusChanged(DirectCall, Boolean, Boolean)` in `DirectCallListener`.
* Added `ongoingCalls` in `SendBirdCall` to retrieve a list of ongoing Direct Calls in the Calls SDK.
* API reference is updated.
* Updated Kotlin standard library version to 1.5.31. 
* Improved stability.

### 1.7.0 (June 4, 2021 UTC)
- Added capability to query rooms.
    - Added `RoomListQuery`.
    - Added `RoomListQuery.Params`.
    - Added `createRoomListQuery(Params)` in `SendBirdCall`.
- Added `Range`.
- Improved security.
- Improved stability.

### 1.6.1 (May 14, 2021 UTC)
- Fixed an issue where `DirectCall.duration` always returns zero.
- Fixed an issue where `SendBirdCall.setCallConnectionTimeout()` doesn't work intermittently.
- Fixed an issue where Direct calls with iOS SDK were not being made.
- Fixed intermittent crashes when making Direct calls.

### 1.6.0 (Apr 22, 2021 UTC)
- Sendbird Calls now supports making group calls in a room.
    - Added `Room`.
        - Added `createRoom(room: RoomParam, handler: RoomHandler)` in `SendBirdCall`.
        - Added `fetchRoomById(roomId: String)` in `SendBirdCall`.
        - Added `getCachedRoomById(roomId: String)` in `SendBirdCall`. 
        - Added `RoomType`.
        - Added `RoomParams`.
        - Added `RoomState`.
        - Added `EnterParams`.
        - Added `RoomListener`.
    - Added `Participant`, `LocalParticipant` and `RemoteParticipant`.
        - Added `ParticipantState`.
- Removed deprecated interfaces
    - Removed `setPushToken(pushToken: String, unique: Boolean)` in `AuthenticateParams`.
    - Removed `deauthenticate(pushToken: String?, handler: CompletionHandler?)` in `SendBirdCall`.
    - Removed `isRecording` in `DirectCall`.
- Improved stability.
- As `jcenter` has been shut down, Sendbird Calls is now only available from Sendbird's maven repository: `maven { url "https://repo.sendbird.com/public/maven" }` starting from version 1.6.0.

### 1.5.3 (Mar 12, 2021 UTC)
- Added `startScreenShare(mediaProjectionPermissionResultData: Intent, handler: CompletionHandler?)` in `DirectCall`.
- Added `stopScreenShare(handler: CompletionHandler?)` in `DirectCall`.
- Added `isLocalScreenShareEnabled` in `DirectCall`.

### 1.6.0-beta (Feb 17, 2021 UTC)
Sendbird Calls SDK version 1.6.0 supports the early access program for group calling. New concepts introduced in this version center around **rooms** and **participants.**

- **New methods to existing classes**
    - Added `createRoom()` in `SendBirdCall`
    - Added `fetchRoomById()` in `SendBirdCall`
    - Added `getCachedRoomById()` in `SendBirdCall`
- **New classes**
    - Added `Room`
        - Added `EnterParams`
        - Added `RoomListener`
        - Added `RoomState`
        - Added `RoomHandler`
    - Added `Participant`
        - Added `LocalParticipant`
        - Added `RemoteParticipant`
        - Added `ParticipantState`

### 1.5.2 (Jan 8, 2021 UTC)
- Fixed crash that happens on typealias-related code.

### 1.5.1 (Dec 22, 2020 UTC)
- Fixed minor bug.

### 1.5.0 (Dec 11, 2020 UTC)
- Added support for integration with Sendbird Chat
	- Added `SendBirdChatOptions`
	- Added `setSendBirdChatOptions()` to `DialParams`
- Added support for `Call summary` on the dashboard.
- Improved backend scalability
- Enhanced security for compliance

### 1.4.2 (Nov 19, 2020 UTC)
- Added `setDirectCallDialingSoundOnWhenSilentOrVibrateMode()` in `SendBirdCall.Options`

### 1.4.1 (Nov 13, 2020 UTC)
- Fixed crash that happened on custom items.

### 1.4.0 (Nov 2, 2020 UTC)
- Added remote recording progress event
  - Added `onRemoteRecordingStatusChanged()` in `DirectCallListener`
  - Added `getLocalRecordingStatus()` and `getRemoteRecordingStatus()` in `DirectCall`
  - Added `enum class RecordingStatus { NONE, RECORDING }`
  - Deprecated `isRecording()` in `DirectCall`
- Improved stability

### 1.3.0 (Sep 22, 2020)
* Migrated SDK to Kotlin.
* Added `captureLocalVideoView` and `captureRemoteVideoView` in `DirectCall`.
* Added media recording feature.
  * Added `addRecordingListener`, `removeRecordingListener` and `removeAllRecordingListener` in `SendBirdCall`.
  * Added `startRecording`, `stopRecording` and `isRecording` in `DirectCall`
  * Added `RecordingOptions` class for the recording functions.
* Added custom sound-related feature.
    * Added `SoundType` enum class in `SendBirdCall`.
    * Added `addDirectCallSound` and `removeDirectCallSound` in `SendBirdCall.Options`.

### 1.2.0 (Jul 16, 2020)
* Support `Peer-to-peer` call.
    * The `Peer-to-peer` option can be configured on the dashboard.
* Added getting ongoing call count and ongoing status of `DirectCall`.
    * Added `getOngoingCallCount()` in `SendBirdCall`.
    * Added `isOngoing()` in `DirectCall`.
* Added `setCallConnectionTimeout()` in `SendBirdCall`.
    * The timer starts after callee accepts. And the timer will be fired to end the call after given timeout seconds.
* Improved stability.

### 1.1.5 (Jul 3, 2020)
* Improved camera management.
    * Added `onLocalVideoSettingsChanged` event in `DirectCallListener`.
        * It will be called when the local video is started or stopped.
    * If camera is disconnected by other apps or call, a `DirectCall` stops local video. And the video could be started with `startVideo()` method.

### 1.1.4 (Jun 25, 2020)
* Improved stability.

### 1.1.3 (Jun 15, 2020)
* Improved stability.

### 1.1.2 (Jun 11, 2020)
* Added `handleWebhookCommand(String payload)` in `SendBirdCall`.
* Improved stability.

### 1.1.1 (May 28, 2020)
* Added `void setPreferredAudioSource(int audioSource)` in `SendBirdCall.Options`.

### 1.1.0 (May 21, 2020)
* Deprecated `deauthenticate(String pushToken, CompletionHandler handler)` in `SendBirdCall`. Please use `deauthenticate(CompletionHandler handler)` instead.
* Added `LOGGER_WARNING` and `LOGGER_INFO` in `SendBirdCall`
* Added `getCallLog()` and `getCurrentVideoDevice()` in `DirectCall`.
* Added `isFromServer()` in `DirectCallLog`.
* Improved stability.

### 1.0.3 (May 1, 2020)
* Modified to use error codes of server if it exists instead of SDK internal error.
* Added server error codes to `SendBirdError`.
* Improved stability.

### 1.0.2 (Apr 30, 2020)
* Added video device selection features.
    * Added `VideoDevice` class which represents a video device for video stream.
    * Added `selectVideoDevice(VideoDevice videoDevice, CompletionHandler handler)` in `DirectCall`. 
    * Added `List<VideoDevice> getAvailableVideoDevices()` in `DirectCall`.
* Added `void setRingingTimeout(int timeout)` in `SendBirdCall.Options`.
* Modified to keep the `SendBirdCallListener` instances after changing app ID. 
* Deprecated `setPushToken(String pushToken, boolean unique)` in `AuthenticateParams`. Please use `registerPushToken(String pushToken, boolean unique, CompletionHandler handler)` instead.
* Optimized video call frame rate.
* Improved stability.

### 1.0.1 (Apr 1, 2020)
* Removed duplicated `Kotlin` standard library from SDK for kotlin user.

### 1.0.0 (Mar 24, 2020)
* Added `switchCamera()` in `DirectCall`.
* Improved stability.

### 0.9.0 (Mar 18, 2020)
* Changed endpoint domain.
* Improved stability.

### 0.8.1 (Mar 9, 2020)
* Improved stability.

### 0.8.0 (Mar 9, 2020)
* Added video call feature.
    * Added `SendBirdVideoView` class.
    * Added `isLocalVideoEnabled()` and `isRemoteVideoEnabled()` in `DirectCall`.
    * Added `startVideo()` and `stopVideo()` in `DirectCall`.
    * Added `setLocalVideoView(SendBirdVideoView videoView)` and `setRemoteVideoView(SendBirdVideoView videoView)` in `DirectCall`.
    * Added `setLocalVideoView(SendBirdVideoView videoView)`, `setRemoteVideoView(SendBirdVideoView videoView)` and `setVideoEnabled(boolean videoEnabled)` in `CallOptions`.
    * Added `onRemoteVideoSettingsChanged(DirectCall call)` method in `DirectCallListener`.
    * Modified `SPEAKER_PHONE` to `SPEAKERPHONE` in `AudioDevice`.
* Improved stability.

### 0.7.0 (Feb 20, 2020)
* Added custom items feature.
    * Added `class AcceptParams`, `class DialParams`, `interface CustomItemsHandler`.
    * Deprecated `dial(String calleeId, boolean isVideoCall, CallOptions callOptions, DialHandler handler)` and added `dial(DialParams params, DialHandler handler)` in `SendBirdCall`.
    * Deprecated `accept(CallOptions callOptions)` and added `accept(AcceptParams params)` in `DirectCall`.
    * Added `updateCustomItems(String callId, Map<String, String> customItems, CustomItemsHandler handler)`, `deleteCustomItems(String callId, Set<String> customItemKeys, CustomItemsHandler handler)` and `deleteAllCustomItems(String callId, CustomItemsHandler handler)` in `SendBirdCall`.
    * Added `getCustomItems()`, `updateCustomItems(Map<String, String> customItems, CustomItemsHandler handler)`, `deleteCustomItems(Set<String> customItemKeys, CustomItemsHandler handler)` and `deleteAllCustomItems(CustomItemsHandler handler)` in `DirectCall`.
    * Added `onCustomItemsUpdated(DirectCall call, List<String> updatedKeys)`, `onCustomItemsDeleted(DirectCall call, List<String> deletedKeys)` in `DirectCallListener`.
* Added `DirectCall` reconnection events.
    * Added `onReconnecting(DirectCall call)`, `onReconnected(DirectCall call)` in `DirectCallListener`.
* Added audio device selection feature.
    * Added `enum AudioDevice`.
    * Added `getCurrentAudioDevice()`, `getAvailableAudioDevices()` and `selectAudioDevice(AudioDevice audioDevice)` in `DirectCall`.
    * Added `onAudioDeviceChanged(DirectCall call, AudioDevice currentAudioDevice, Set<AudioDevice> availableAudioDevices)` in `DirectCallListener`.
* Improved stability.

### 0.6.10 (Jan 30, 2020)
* Improved stability.

### 0.6.1 (Dec 26, 2019)
* Removed unnecessary Android permissions.

### 0.6.0 (Dec 20, 2019)
* Early Access release.
