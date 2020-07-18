# html5 video标签读取网络视频
区别于本地视频，网络视频需要我们的api支持断点续传

## client
使用`video`标签

TODO 文件下载的断点续传

## server
```javascript
/*
    计算 range, start, end
*/
var range = req.headers.range;
if (!range) {
    // 416 Wrong range
    return res.sendStatus(416);
}
var positions = range.replace(/bytes=/, "").split("-");
var start = parseInt(positions[0], 10);
var total = doc.length;
var end = positions[1] ? parseInt(positions[1], 10) : total - 1;
var chunksize = (end - start) + 1;

res.writeHead(206, {
    "Content-Range": "bytes " + start + "-" + end + "/" + total,
    "Accept-Ranges": "bytes",
    "Content-Length": chunksize,
    "Content-Type": "video/mp4"
});

/*
    返回，使用你习惯的方法，这里用nodejs gfs举例
    关于gfs更多入门内容，请自行查阅本人文章
*/
let readstream = gfs.createReadStream({
    _id: 'xxxxxx, your video id'
}, {
    range: {
        startPos: start, 
        endPos: end
    }
});

readstream.pipe(res);

```
