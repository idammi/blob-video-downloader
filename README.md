# Blob Video Downloader

A simple JavaScript tool to download blob videos directly from web pages using the browser console.

## 🚀 Features

- Download blob videos from any webpage
- Easy to use via browser console
- No additional software or extensions required
- Works with most modern browsers

## 🛠️ How to Use
1. Open the webpage that has the video you want to download
2. Open your browser's developer tools (usually F12 or right-click > Inspect)
3. Navigate to the video link
4. Open it in a new tab
5. Navigate to the “Console” tab
6. Copy and paste the following code into the console:


```javascript
(function() {
    const videoElement = document.querySelector('video');
    if (!videoElement) {
        console.error('No video element found on this page.');
        return;
    }

    const sourceElement = videoElement.querySelector('source');
    const videoSource = (sourceElement ? sourceElement.src : null) || videoElement.src;
    if (!videoSource || !videoSource.startsWith('blob:')) {
        console.error('This video does not use a blob URL.');
        return;
    }

    fetch(videoSource)
        .then(response => response.blob())
        .then(blob => {
            const url = window.URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.style.display = 'none';
            a.href = url;
            a.download = 'video.mp4';
            document.body.appendChild(a);
            a.click();
            window.URL.revokeObjectURL(url);
            console.log('Download started. Check your downloads folder.');
        })
        .catch(error => console.error('Error downloading video:', error));
})();
```

Press Enter to run the code.
The video will be downloaded as "video.mp4" in your default downloads folder.

## ⁉️ Getting an error about failure to fetch the video?

This can happen if the blob URL points to a `MediaSource` object (MSE streaming) and not a fetchable blob. In that case you need to record the video as it plays using `MediaRecorder`. Follow the same steps 1-5 above and paste the following code into the console for step 6 instead:

```javascript
(async function() {
    const videoElement = document.querySelector('video');
    if (!videoElement) {
        console.error('No video element found on this page.');
        return;
    }

    // Load StreamSaver.js to stream chunks to disk without holding in RAM
    await new Promise((resolve, reject) => {
        const script = document.createElement('script');
        script.src = 'https://cdn.jsdelivr.net/npm/streamsaver@2.0.6/StreamSaver.min.js';
        script.onload = resolve;
        script.onerror = reject;
        document.head.appendChild(script);
    });

    const fileStream = streamSaver.createWriteStream('video.webm');
    const writer = fileStream.getWriter();

    const stream = videoElement.captureStream();
    const recorder = new MediaRecorder(stream, { mimeType: 'video/webm' });

    let writeQueue = Promise.resolve();

    recorder.ondataavailable = e => {
        if (e.data.size > 0) {
            writeQueue = writeQueue.then(async () => {
                const buffer = await e.data.arrayBuffer();
                await writer.write(new Uint8Array(buffer));
            });
        }
    };
    recorder.onstop = () => {
        writeQueue.then(async () => {
            await writer.close();
            console.log('File saved successfully.');
        });
    };

    // flush chunk to disk every 10 seconds instead of buffering all in RAM
    recorder.start(10000);
    console.log('Recording started. Play the video now. File will save automatically when video ends.');
    videoElement.addEventListener('ended', () => {
        recorder.stop();
        console.log('Video ended. Finishing save...');
    }, { once: true });
})();

```

Paste the code into the browser console and press Enter. A save dialog will appear — choose where to save the file before recording begins. Then press play on the video. You can mute it, switch away from the window/tab, or minimize it. The `MediaRecorder` captures the video stream in the background and streams it to disk every 10 seconds, so it won't run out of memory even for long videos. Recording stops and the file is saved automatically when the video ends. As long as the tab stays open (not closed or navigated away from), recording will continue fine while minimized or while you're on another tab.

The video saves as .webm since that's what `MediaRecorder` produces in browsers. If you need .mp4, you'd have to convert it after (e.g. with ffmpeg).

## ⚠️ Disclaimer
This tool is for educational purposes only. Always ensure you have the right to download and use the video content. Respect copyright laws and the terms of service of the websites you're using.
## 🤝 Contributing
Contributions, issues, and feature requests are welcome! Feel free to check the issues page.
## 📝 License
This project is MIT licensed.
## 🔍 Keywords
blob video download, JavaScript video downloader, web video download, browser console, blob URL, media downloader, web scraping, HTML5 video
