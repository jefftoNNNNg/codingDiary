# busboy入门
本文主要解决了浏览器在使用`<input type="file">`时，后端无法通过本地路径获取文件流的问题

# 资源
>[github](https://github.com/mscdex/busboy)

# 版本
```shell
  $ node -v
  v12.15.0
  $ npm -v
  6.13.4
  busboy 0.3.1
```

# 获取文件stream
```javascript
const Busboy = require('busboy');

const busboy = new Busboy({headers: req.headers});
    busboy.on('file', async function(fieldname, file, filename, encoding, mimetype) {
        // 这里返回的file对象即是readstream
        // 可以创建writestream后直接调用pie()进行保存，比如保存至gfs等...
        
        // your services ...

        // res.end, res.send, res.redirect等...
    });

    req.pipe(busboy);
```

# 获取文件buffer
```javascript
const Busboy = require('busboy');

const busboy = new Busboy({headers: req.headers});
    let buffers = [];

    busboy.on('file', async function(fieldname, file, filename, encoding, mimetype) {
        // 如果你需要buffer，那么我们需要监听busboy的finish事件
        file.on('data', function (data) {
            buffers.push(data);
        });
    });

    busboy.on('finish', async function() {
        // 这里buffers就是我们需要的数据，后续可以自行转换格式

        // 比如buffer可以组成excel文件内容，可详见本人的exceljs入门文章，请自行查找
        let exceljs_buffer = Buffer.concat(buffers);        // 比如转换成exceljs需要的buffer形式

        // do your services...
    });

    req.pipe(busboy);
```
