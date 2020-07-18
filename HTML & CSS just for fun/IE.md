# IE兼容性
本文简单汇总了IE浏览器不兼容的问题，只照顾IE11，IE9随缘

# object-fit
## 资源
>[object-fit-images](https://github.com/fregante/object-fit-images)

## 使用
css
```css
.your-favorite-image {
	object-fit: contain;
	font-family: 'object-fit: contain;';
}
```

javascript
```html
<script src="/javascripts/ofi.min.js" charset="UTF-8"></script>
<script>
    $(document).ready(function () {
        objectFitImages();
    });
</script>
```

# url
url中或url参数含有中文，请使用`encodeURI`或`encodeURIComponent()`
