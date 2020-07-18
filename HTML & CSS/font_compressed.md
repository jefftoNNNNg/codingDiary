# 中文字体压缩
使用`font-spider`

## 资源
>[font-spider](https://www.font-spider.org)

## 运行
一般nodejs项目的路径是不匹配font-spider脚本的，所以需要重新建立项目

1. 把所有页面拷贝过来

2. 建立css, ttf是必须的，其他格式会自动生成
```css
@font-face {
    font-family: 'alibabapuhuiti_h'; 
    src: url('../fonts/Alibaba-PuHuiTi-Heavy.eot');
    src: url('../fonts/Alibaba-PuHuiTi-Heavy.eot#font-spider') format('embedded-opentype'), /* IE9*/
            url('../fonts/Alibaba-PuHuiTi-Heavy.ttf') format('truetype'),
            url('../fonts/Alibaba-PuHuiTi-Heavy.woff') format('woff'), /* chrome、firefox */
            url('../fonts/Alibaba-PuHuiTi-Heavy.svg') format('svg');
    font-weight: normal;
}
```

3. 运行脚本
```shell
font-spider --debug ./views/*.ejs
```
