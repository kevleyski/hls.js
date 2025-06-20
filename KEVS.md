# Kev's notes

### `audio-track-controller.ts`

This controller is responsible for managing the audio tracks that are available in the HLS manifest. It allows the user to select a specific audio track and ensures that the correct audio data is loaded and played. It works in conjunction with the `audio-stream-controller.ts` to handle the actual streaming of the audio data.

### `base-playlist-controller.ts`

This is a base class that provides common functionality for controllers that deal with playlists, such as the `level-controller.ts` and `audio-track-controller.ts`. It handles the loading and parsing of playlist files and provides a foundation for more specialized playlist controllers.

### `base-stream-controller.ts`

This is a base class for stream controllers, such as `stream-controller.ts` and `audio-stream-controller.ts`. It provides common functionality for loading and managing media fragments, handling retries, and managing the buffer.

### `buffer-operation-queue.ts`

This file implements a queue for managing buffer operations. It ensures that buffer operations, such as appending or removing data, are executed in the correct order and that the `SourceBuffer` is not in an updating state when an operation is performed.

### `cap-level-controller.ts`

This controller is responsible for capping the quality level of the stream based on the dimensions of the media player. It prevents the player from loading a higher quality level than can be displayed, which can save bandwidth and improve performance.

### `cmcd-controller.ts`

This controller implements the Common Media Client Data (CMCD) specification. It gathers and sends data about the client's media playback experience to the server, which can be used for analytics and to improve the quality of the stream.

### `content-steering-controller.ts`

This controller implements the HLS Content Steering specification. It allows the server to steer the client to different CDN paths for the same content, which can be used for load balancing and to improve the reliability of the stream.

### `eme-controller.ts`

This controller handles Encrypted Media Extensions (EME), which are used for playing DRM-protected content. It manages the key system, license requests, and the decryption of media data.

### `error-controller.ts`

This controller is responsible for handling errors that occur during the playback of the HLS stream. It determines the appropriate action to take based on the type of error, such as retrying a request, switching to a different quality level, or displaying an error message to the user.

### `fps-controller.ts`

This controller monitors the frame rate of the video and can cap the quality level if the frame rate drops below a certain threshold. This can help to ensure a smooth playback experience on devices with limited processing power.

### `fragment-finders.ts`

This file contains utility functions for finding specific fragments in a playlist, such as finding a fragment by its Program Date Time (PDT) or by its PTS (Presentation Timestamp).

### `fragment-tracker.ts`

This controller keeps track of the state of each fragment in the playlist, such as whether it has been loaded, appended to the buffer, or is currently being downloaded. This information is used by other controllers to make decisions about what to do next.

### `gap-controller.ts`

This controller is responsible for detecting and handling gaps in the buffer. It can try to skip over small gaps or nudge the playhead to get it unstuck.

### `id3-track-controller.ts`

This controller handles the parsing and display of ID3 metadata, which can be embedded in the HLS stream. This metadata can include information such as the song title, artist, and album.

### `interstitial-player.ts`

This file contains the logic for playing interstitial ads. It manages the loading and playback of the ad content and ensures a seamless transition back to the main content.

### `interstitials-controller.ts`

This controller manages the scheduling and playback of interstitial ads. It determines when to play an ad based on the HLS manifest and the user's playback position.

### `interstitials-schedule.ts`

This file contains the logic for parsing the interstitial schedule from the HLS manifest. It creates a timeline of when ads should be played and provides this information to the `interstitials-controller.ts`.

### `latency-controller.ts`

This controller is responsible for managing the latency of the live stream. It tries to keep the latency as low as possible while still ensuring a smooth playback experience.

### `subtitle-stream-controller.ts`

This controller is responsible for loading and parsing subtitle data. It works in conjunction with the `subtitle-track-controller.ts` to display the subtitles to the user.

### `subtitle-track-controller.ts`

This controller manages the available subtitle tracks and allows the user to select a specific track. It also handles the display of the subtitles on the screen.

### `timeline-controller.ts`

This controller is responsible for managing the timeline of the HLS stream. It keeps track of the current playback position and provides this information to other controllers. It also handles the parsing of CEA-608/708 captions.
