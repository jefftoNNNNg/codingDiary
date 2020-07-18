# gridfs-stream
使用gfs向mongodb保存文件

# 资源
>[gridfs-stream](https://github.com/aheckmann/gridfs-stream)

# 版本
```shell
  $ node -v
  v12.15.0
  $ npm -v
  6.13.4
  gridfs-stream 1.1.1
```

# 保存
```javascript
const Grid = require('gridfs-stream')
    ,mongo = require('mongodb');

const gfs = Grid(db, mongo);        // db请参考gridfs-stream官网guide

const options = {
    mode: 'w', 
    chunkSize: 261120, 
    content_type: req.headers['content-type'],      // For content_type to work properly, set "mode"-option to "w" too!
    filename: 'your filename',
    aliases: {
        a: 1,
        b: '2',
        c: [1, 2, 3]
    }
};
let writeStream = gfs.createWriteStream(options);
writeStream.on('error', function (err) {
    // handler err
}).on('close', function () {
    // 至此保存成功

    // do your services...
});

readStream.pipe(writeStream);
```

# 取文件
```javascript    
let readstream = gfs.createReadStream({
    _id: imgId
});

readstream.on('error', function (err) {
    // handler err
}).on('close', function () {
    // 至此浏览器可以自动下载    
});

res.setHeader('content-type', 'image/jpg');
readstream.pipe(res);
```

