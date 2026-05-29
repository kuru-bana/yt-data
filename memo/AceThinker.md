# APIレスポンス
**通信しているapiを抜くコード**
```
(function() {
    console.log('%c[API Monitor] 監視を開始しました (Fetch & XHR)', 'color: #fff; background: #222; padding: 4px; font-weight: bold;');

    // ==========================================
    // 1. Fetch API の監視設定
    // ==========================================
    const originalFetch = window.fetch;
    window.fetch = async function(...args) {
        const url = args[0];
        const options = args[1] || {};
        
        console.log(`%c[Fetch API] 🚀 ${options.method || 'GET'}: ${url}`, 'color: #00ff00; font-weight: bold;');
        if (options.body) {
            console.log('  └ 送信(Payload):', options.body);
        }

        try {
            const response = await originalFetch.apply(this, args);
            const clone = response.clone();
            const contentType = clone.headers.get('content-type');
            
            if (contentType && contentType.includes('application/json')) {
                const data = await clone.json();
                console.log('  └ 受信(JSON):', data);
            } else {
                const text = await clone.text();
                console.log('  └ 受信(Text):', text.substring(0, 200));
            }
            return response;
        } catch (error) {
            console.error(`  └ [Fetch Error]:`, error);
            throw error;
        }
    };

    // ==========================================
    // 2. XMLHttpRequest (XHR) の監視設定
    // ==========================================
    const originalOpen = XMLHttpRequest.prototype.open;
    const originalSend = XMLHttpRequest.prototype.send;

    XMLHttpRequest.prototype.open = function(method, url, ...args) {
        this._url = url;
        this._method = method;
        return originalOpen.apply(this, [method, url, ...args]);
    };

    XMLHttpRequest.prototype.send = function(body, ...args) {
        console.log(`%c[XHR] 🚀 ${this._method}: ${this._url}`, 'color: #ff00ff; font-weight: bold;');
        if (body) {
            console.log('  └ 送信(Payload):', body);
        }

        this.addEventListener('load', function() {
            try {
                const data = JSON.parse(this.responseText);
                console.log('  └ 受信(JSON):', data);
            } catch (e) {
                console.log('  └ 受信(Text):', this.responseText.substring(0, 200)); 
            }
        });

        return originalSend.apply(this, [body, ...args]);
    };
})();

```
---
## HOST_URL
### https://grab.aquapp.net
### https://vip.aquapp.net
### https://aquapp.net/downloader
### https://www.acethinker.jp/downloader
### https://www.acethinker.tw/downloader
### https://www.acethinker.ai/downloader
- /api/dlapinewv2.php?url={YOUTUBE_URL}
- /api/newytdlapi/youtube_mp3_audio_video_downloader.php?url={YOUTUBE_URL} <br>
**{YOUTUBE_URL}にはhttps://www.youtube.com/watch?v={video_id} のものを入力すること**
```
fetch("https://grab.aquapp.net/api/dlapinewv2.php?url=https://www.youtube.com/watch?v={video_id]")
.then(r => r.json())
.then(console.log)
```
