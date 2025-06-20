# Kev's HLS.js implementation notes

Frame accuracy, known when PTS is known otherwise needs a new monge in DTS/PTSdule to detect cha

### `audio-track-controller.ts`

managing the audio tracks that are available in the HLS manifest. It allows the user to select a specific audio track and ensures that the correct audio data is loaded and played. It works in conjunction with the `audio-stream-controller.ts` to handle the actual streaming of the audio data.

### `base-playlist-controller.ts`

base class that provides common functionality for controllers that deal with playlists, such as the `level-controller.ts` and `audio-track-controller.ts`. It handles the loading and parsing of playlist files and provides a foundation for more specialized playlist controllers.

### `base-stream-controller.ts`

base class for stream controllers, such as `stream-controller.ts` and `audio-stream-controller.ts`. It provides common functionality for loading and managing media fragments, handling retries, and managing the buffer.

### `buffer-operation-queue.ts`

implements a queue for managing buffer operations. It ensures that buffer operations, such as appending or removing data, are executed in the correct order and that the `SourceBuffer` is not in an updating state when an operation is performed.

### `cap-level-controller.ts`

capping the quality level of the stream based on the dimensions of the media player. It prevents the player from loading a higher quality level than can be displayed, which can save bandwidth and improve performance.

### `cmcd-controller.ts`

implements the Common Media Client Data (CMCD) specification. It gathers and sends data about the client's media playback experience to the server, which can be used for analytics and to improve the quality of the stream.

### `content-steering-controller.ts`

implements the HLS Content Steering specification. It allows the server to steer the client to different CDN paths for the same content, which can be used for load balancing and to improve the reliability of the stream.

### `eme-controller.ts`

handles Encrypted Media Extensions (EME), which are used for playing DRM-protected content. It manages the key system, license requests, and the decryption of media data.

### `error-controller.ts`

handling errors that occur during the playback of the HLS stream. It determines the appropriate action to take based on the type of error, such as retrying a request, switching to a different quality level, or displaying an error message to the user.

### `fps-controller.ts`

monitors the frame rate of the video and can cap the quality level if the frame rate drops below a certain threshold. This can help to ensure a smooth playback experience on devices with limited processing power.

### `fragment-finders.ts`

contains utility functions for finding specific fragments in a playlist, such as finding a fragment by its Program Date Time (PDT) or by its PTS (Presentation Timestamp).

### `fragment-tracker.ts`

keeps track of the state of each fragment in the playlist, such as whether it has been loaded, appended to the buffer, or is currently being downloaded. This information is used by other controllers to make decisions about what to do next.

### `gap-controller.ts`

detecting and handling gaps in the buffer. It can try to skip over small gaps or nudge the playhead to get it unstuck.

### `id3-track-controller.ts`

handles the parsing and display of ID3 metadata, which can be embedded in the HLS stream. This metadata can include information such as the song title, artist, and album.

### `interstitial-player.ts`

logic for playing interstitial ads. It manages the loading and playback of the ad content and ensures a seamless transition back to the main content.

### `interstitials-controller.ts`

manages the scheduling and playback of interstitial ads. It determines when to play an ad based on the HLS manifest and the user's playback position.

### `interstitials-schedule.ts`

logic for parsing the interstitial schedule from the HLS manifest. It creates a timeline of when ads should be played and provides this information to the `interstitials-controller.ts`.

### `latency-controller.ts`

managing the latency of the live stream. It tries to keep the latency as low as possible while still ensuring a smooth playback experience.

### `subtitle-stream-controller.ts`

loading and parsing subtitle data. It works in conjunction with the `subtitle-track-controller.ts` to display the subtitles to the user.

### `subtitle-track-controller.ts`

manages the available subtitle tracks and allows the user to select a specific track. It also handles the display of the subtitles on the screen.

### `timeline-controller.ts`

managing the timeline of the HLS stream. It keeps track of the current playback position and provides this information to other controllers. It also handles the parsing of CEA-608/708 captions.

#### MediaSource Creation and Management

1. **MediaSource Detection and Selection**:

   - In `mediasource-helper.ts`, hls.js first checks for `ManagedMediaSource` availability (a newer API that improves MSE), falling back to standard `MediaSource`, and finally to `WebKitMediaSource` for older browsers.
   - The config option `preferManagedMediaSource` (default: true) controls whether to prioritize the newer API.

2. **MediaSource Lifecycle**:

   - When media is attached (`onMediaAttaching` in `buffer-controller.ts`), hls.js creates a new MediaSource instance.
   - It creates an object URL from this MediaSource and assigns it to the media element's `src` attribute.
   - The controller attaches event listeners for `sourceopen`, `sourceended`, and `sourceclose` events.
   - For `ManagedMediaSource`, additional `startstreaming` and `endstreaming` events are handled.

3. **MediaSource State Management**:
   - hls.js carefully tracks the state of the MediaSource through its "open", "ended", and "closed" states.
   - When the MediaSource is open, it creates appropriate SourceBuffers.
   - When detaching media, it properly revokes object URLs and removes event listeners.

#### SourceBuffer Handling

1. **SourceBuffer Creation**:

   - Based on the media codecs detected in the manifest, hls.js creates the appropriate SourceBuffers.
   - It typically creates separate SourceBuffers for audio and video tracks, but may use a single SourceBuffer for both in case of "muxed" content.
   - Creation happens in the `createSourceBuffers` method in `buffer-controller.ts` after the MediaSource is open.

2. **Codec Management**:

   - The system checks codec compatibility through `areCodecsMediaSourceSupported` to ensure the browser supports the provided codecs.
   - For video, it may attempt codec replacement (via `replaceVideoCodec`) to handle edge cases with some browsers.
   - It handles codec switching through the `changeType` method when available (for example, when switching between different video qualities).

3. **Buffer Operation Queue**:
   - Since MSE operations require SourceBuffer not to be in an updating state, hls.js implements a queueing system in `buffer-operation-queue.ts`.
   - Operations like `append`, `remove`, `changeType`, and `endOfStream` are queued and executed when the SourceBuffer is ready.
   - The system handles errors and retries for buffer operations.

#### Media Segment Processing

1. **Appending Media Segments**:

   - Parsed media segments from the HLS stream are queued for appending to the appropriate SourceBuffer.
   - The `onBufferAppending` method handles the actual appending process.
   - Special handling exists for MPEG audio to ensure proper timestamp alignment.

2. **Buffer Management**:

   - hls.js implements sophisticated buffer management to ensure smooth playback.
   - It flushes parts of the buffer when necessary (like during seeking or level switches).
   - The system tracks buffer fill levels to make ABR (adaptive bitrate) decisions.
   - It handles discontinuities in the stream by aligning PTS (Presentation Timestamp) values.

3. **End of Stream Handling**:
   - When a stream is complete, hls.js calls `mediaSource.endOfStream()`.
   - It manages special cases like live streams where the end is periodically extended.

#### Advanced Features

1. **Media Transfer**:

   - hls.js supports transferring MediaSource between instances through `transferMedia`.
   - This allows seamless transitions between different hls.js instances or configurations.

2. **Error Recovery**:

   - The system implements sophisticated error recovery for MSE operations.
   - It tracks append errors by type (audio/video/audiovideo) and can switch strategies when errors persist.
   - SourceBuffer removal and recreation may be attempted in some error scenarios.

3. **ManagedMediaSource API Support**:

   - When available, hls.js leverages the newer ManagedMediaSource API.
   - This includes special handling for appending source elements and managing disableRemotePlayback.

4. **Codec Switching**:
   - For platforms that support it, hls.js can switch codecs without recreating SourceBuffers using `changeType`.
   - This enables more efficient quality switching and handling of streams with mixed codecs.
