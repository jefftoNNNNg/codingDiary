# 中文字体压缩
使用`font-spider`

请注意，目前不支持动态。我也没时间研究，也不想研究，这个文章就是为了甲方的需求随意搞了一下

如果网页是动态网页，请在导入期初数据的时候，保存字库

中文字库可以选择在网页开屏loading的时候进行下载，有类库是可以判断字体是否加载完成的，请自行在互联网中搜索

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
