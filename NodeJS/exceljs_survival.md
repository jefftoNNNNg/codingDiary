# exceljs入门

# 资源
>[github](https://github.com/exceljs/exceljs)
>[github中文api](https://github.com/exceljs/exceljs/blob/master/README_zh.md)

# 版本
```shell
  $ node -v
  v10.15.3
  $ npm -v
  6.4.1
  exceljs 0.7.1
```

# 创建一个excel文件
## 1. 创建workbook
```javascript
const Excel = require('exceljs');

let workbook = new Excel.Workbook();
let sheetName = 'my sheet';
let worksheet = workbook.addWorksheet(sheetName);
```

## 2. 数据
### 基础用法
```javascript
worksheet.mergeCells('A1:B2');
worksheet.getCell('A1').value = 'cell value';
worksheet.getCell('A1').alignment = {vertical: 'middle', horizontal: 'right'};
worksheet.getCell('A1').border = {top: {style: 'thin'}, left: {style: 'thin'}, bottom: {style: 'thin'}, right: {style: 'thin'}};

const test = worksheet.getCell('A1').value;     // 从单元格取值
```

### 插入图片图片
```javascript
const image = {
    buffer: fs.readFileSync('../public/images/Oval.png'),
    extension: 'png'
};
const imageId = workbook.addImage(image);
worksheet.addImage(imageId, {
    tl: {col: 1, row: 2},        // top left
    br: {col: 2, row: 4}     // bottom right
});
```

## 3. 返回文件，浏览器自动下载
```javascript
workbook.xlsx.writeBuffer().then(function (buffer) {
    const fileName = encodeURIComponent('my excel.xlsx');

    res.setHeader('Content-Disposition', 'attachment; filename='+fileName+'; filename*=utf-8\'\''+fileName);
    res.setHeader('content-type', 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet');
    res.status(200).send(buffer);
});
```

# 读取一个excel文件，一般应用在数据导入，或读取报表的模版并完善数据后再进行输出
## 1. 读取一个excel
通常我们使用`<input type="file" />`进行文件上传（本地路径问题可结合busboy，本人另一篇文章有简短介绍，请自行查阅）。在获取到文件的buffer后：
```javascript
const workbook = new Excel.Workbook();
```

## 1. 从excel中取图片
通常当我们导入excel文件时，需要读取excel中的图片并保存数据，本例同时介绍了，读取图片时获取其所在的单元格的位置，以此判断其应该对应的行数据
```javascript
const Excel = require('exceljs');
const buffers = [];
const buffer = Buffer.concat(buffers);       // TODO 说明一下buffer的格式

const workbook = new Excel.Workbook();
await workbook.xlsx.load(buffer);

// 此时有两个关键的值要从workbook中获取
const media = workbook.model.media;        // 获取workbook中所有的media信息，包含了所有的sheet页
const media_inWorksheet = workbook.model.worksheets[0].media;      // 获取某个worksheet中所有的media信息，注意此处worksheet的index从0开始
```
`media`和`media_inWorksheet`均为数组类型
`media`的结构（仅列出关键字段）：
```json
{
    "index": 1,     // index可以作为image的id
    "buffer": "xxx"       // TODO 说明一下buffer的格式
}
```
`media_inWorksheet`的结构（仅列出关键字段）：
```json
{
    "imageId": 1,       // 可以去对应media数组中的index，以此获取image的buffer
    "range": {
        "tl": {     // top left。本人比较倾向使用image的左上角进行判断所处单元格的位置
            "nativeCol": 1,     // 所在列
            "nativeRow": 1      // 所在行
        }
    }
}
```
至此我们便可以获取到image的buffer了

## 2. 保存图片
至此，我们需要将上一步获取的buffer转换为nodejs的readstream以便进行保存
```javascript
const stream = require('stream');

let readStream = new stream.PassThrough();
readStream.end(buffer);     // TODO 这里的buffer不需要转换了，直接可以拿来转换，后续说明一下buffer的格式

// 保存至gfs等...
```
