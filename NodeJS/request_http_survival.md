# request入门
本文介绍了request的入门使用方法，主要是form data post和raw post

>请注意：
As of Feb 11th 2020, request is fully deprecated. No new changes are expected to land. In fact, none have landed for some time. For more information about why request is deprecated and possible alternatives refer to this [issue](https://github.com/request/request/issues/3142).

# 资源
>[github](https://github.com/request/request)

# 版本
```
  $ node -v
  v12.15.0
  $ npm -v
  6.13.4
  request 2.88.0
```

# http post 使用form data形式
## client
```javascript
request.post({url: 'full url', form: body}, function (err, res, body) {});
```

## server
TODO 使用xxx方式接收参数，好久不用form data了，待验证一下

# http post 使用raw形式
## client
```javascript
const option = {
    method: 'post',
    url: 'full url',
    headers: {'cache-control': 'no-cache', 'content-type': 'application/json'},
    body: body,
    json: true
};
request.post(option, function (err, res, body) {});
```

## server
使用`req.body.xxx`接收参数
