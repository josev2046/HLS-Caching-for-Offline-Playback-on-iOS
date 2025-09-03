### HLS Caching for Offline Playback on iOS

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.17047096.svg)](https://doi.org/10.5281/zenodo.17047096)


<img width="929" height="494" alt="image" src="https://github.com/user-attachments/assets/0c7ff40a-dfc2-4ddb-8069-d2f928512519" />


An early morning, long long shot at reverse engineering after PTO: here's looking at implementing offline media playback within an iOS application. The methodology detailed here, while theoretical, is based on the behaviour achieved in the branded Vimeo app. Here, the process is governed by Apple's native **AVFoundation** framework and is divided into two distinct phases: the online caching of video content and the subsequent offline playback.

---

### Online Caching Process

This phase is initiated when a user, with an active internet connection, elects to download a video for later viewing.

1.  The application sends a request to **AVFoundation** to manage the download of a specified video.
2.  **AVFoundation** then communicates directly with the content servers. It requests the video using the industry-standard **HTTP Live Streaming (HLS)** protocol. This protocol segments the video into small files with a `.ts` extension and provides a playlist file (`.m3u8`) that dictates how they should be assembled.
3.  The servers respond by streaming these segments and the playlist file to the device.
4.  **AVFoundation** securely saves these files directly to the device's local storage. This operation is managed efficiently in the background, allowing the user to continue using the application.
5.  Once all segments are successfully stored, **AVFoundation** notifies the application that the download is complete. The video is now available for offline viewing.

---

### Offline Playback Process

This phase is initiated when the user, without an internet connection, wishes to watch the downloaded content.

1.  The user selects the downloaded video from within the application's library.
2.  The application instructs the internal video player, **AVPlayer**, to begin playback. Critically, instead of requesting the video from the internet, **AVPlayer** is directed to the manifest file stored locally on the device.
3.  The player reads this local playlist and begins retrieving the corresponding video segments from the device's storage in the correct sequence.
4.  The video plays seamlessly for the user, with the experience being identical to that of streaming online content.
