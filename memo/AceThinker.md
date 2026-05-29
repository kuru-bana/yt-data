# APIレスポンス
---
## HOST_URL
### https://grab.aquapp.net
### https://vip.aquapp.net
### https://aquapp.net/downloader
### https://www.acethinker.jp/downloader
### https://www.acethinker.tw/downloader
### https://www.acethinker.ai/downloader
### https://www.acethinker.com.br/audio/ ←new!
- /api/dlapinewv2.php?url={YOUTUBE_URL}
- /api/newytdlapi/youtube_mp3_audio_video_downloader.php?url={YOUTUBE_URL} <br>
**{YOUTUBE_URL}にはhttps://www.youtube.com/watch?v={video_id} のものを入力すること**
```
fetch("https://grab.aquapp.net/api/dlapinewv2.php?url=https://www.youtube.com/watch?v={video_id]")
.then(r => r.json())
.then(console.log)
```
