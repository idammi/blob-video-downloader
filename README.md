# Blob Video Downloader

A simple JavaScript tool to download blob videos directly from web pages using the browser console.

## 🚀 Features

- Download blob videos from any webpage
- Easy to use via browser console
- No additional software or extensions required
- Works with most modern browsers

## 🛠️ How to Use

1. Open the webpage containing the blob video you want to download
2. Open your browser's developer tools (usually F12 or right-click > Inspect)
3. Navigate to the "Console" tab
4. Copy and paste the following code into the console:

```javascript
(function() {
    const videoElement = document.querySelector('video');
    if (!videoElement) {
        console.error('No video element found on this page.');
        return;
    }

    const videoSource = videoElement.src;
    if (!videoSource.startsWith('blob:')) {
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

Press Enter to run the code
The video will be downloaded as "video.mp4" in your default downloads folder

## ⚠️ Disclaimer
This tool is for educational purposes only. Always ensure you have the right to download and use the video content. Respect copyright laws and the terms of service of the websites you're using.
## 🤝 Contributing
Contributions, issues, and feature requests are welcome! Feel free to check the issues page.
## 📝 License
This project is MIT licensed.
## 🔍 Keywords
blob video download, JavaScript video downloader, web video download, browser console, blob URL, media downloader, web scraping, HTML5 video
