## Change Log

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
