## Change Log

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
