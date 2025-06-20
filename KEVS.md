# Kev's notes

abr-controller.ts
The abr-controller.ts file is responsible for Adaptive Bitrate (ABR) switching. It monitors network bandwidth and selects the most appropriate quality level to ensure a smooth playback experience without buffering. This controller listens to events such as fragment loading and buffering to dynamically adjust the bitrate. It uses an Exponentially Weighted Moving Average (EWMA) to estimate bandwidth and make decisions about when to switch to a higher or lower quality level.
stream-controller.ts
The stream-controller.ts is the primary controller for managing the media stream. It's responsible for loading media fragments, managing the buffer, and handling the playback state. It works in conjunction with the abr-controller.ts to switch between different quality levels. This controller also manages audio track switching and buffer flushing operations. It's the central component that orchestrates the entire streaming process.
audio-stream-controller.ts
This controller is dedicated to managing the audio stream, particularly when alternate audio tracks are available. It handles the loading of audio fragments and the seamless switching between different audio tracks. A key responsibility of this controller is to ensure that the audio and video streams remain synchronized, which is crucial for a good user experience.
buffer-controller.ts
The buffer-controller.ts file is responsible for managing the SourceBuffer objects for both audio and video. It handles the creation of SourceBuffers, appending and removing data from them, and managing buffer-related errors. For example, it handles the QuotaExceededError which occurs when the buffer is full, and it needs to evict older data to make space for new data.
level-controller.ts
This controller manages the various quality levels (or "levels") that are defined in the HLS manifest. Its responsibilities include parsing the manifest, filtering out any unsupported levels, and sorting the available levels, typically by bitrate. The level-controller.ts also handles the logic for switching between these levels, which can be done either manually by the user or automatically based on the decisions made by the ABR controller.
