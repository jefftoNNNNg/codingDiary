# source
>[github](https://github.com/exceljs/exceljs)

# versions
```
  $ node -v
  v10.15.3
  $ npm -v
  6.4.1
  exceljs 0.7.1
```

# usage
## 1. create the workbook
```
let workbook = new Excel.Workbook();
let sheetName = 'my sheet';
let worksheet = workbook.addWorksheet(sheetName);
```

## 2. render data
basic
```
worksheet.mergeCells('A1:B2');
worksheet.getCell('A1').value = 'cell value';
worksheet.getCell('A1').alignment = {vertical: 'middle', horizontal: 'right'};
worksheet.getCell('A1').border = {top: {style: 'thin'}, left: {style: 'thin'}, bottom: {style: 'thin'}, right: {style: 'thin'}};
```

render image
```
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

## 3. return the file
```
workbook.xlsx.writeBuffer().then(function (buffer) {
    const fileName = encodeURIComponent('my excel.xlsx');

    res.setHeader('Content-Disposition', 'attachment; filename='+fileName+'; filename*=utf-8\'\''+fileName);
    res.setHeader('content-type', 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet');
    res.status(200).send(buffer);
});
```
