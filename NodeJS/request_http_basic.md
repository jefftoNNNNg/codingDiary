# source
>[github](https://github.com/request/request#readme)

# versions
```
  $ node -v
  v10.15.3
  $ npm -v
  6.4.1
  request 2.88.0
```

# usage
## 1. http GET
```
exports.httpGet = function (successCB, errorCB) {
    request.get({url: 'http://api.domain.com/api/v1/user'}, function (err, httpResponse, result) {
        if (err) {
            errorCB(err);
            return;
        }
        successCB(JSON.parse(result));
    });
};
```

## 2. http POST
request body is 'form-data'
```
exports.httpPost = function (body, successCB, errorCB) {
    request.post({url: 'http://api.domain.com/api/v1/user', form: body}, function (err, httpResponse, result) {
        if (err) {
            errorCB(err);
            return;
        }
        successCB(JSON.parse(result));
    });
};
```

TODO: The body of post api(developed by typescript or nodejs) is 'raw' by default.

## 3. http PUT
request body is 'form-data'
```
exports.httpPut = function (body, successCB, errorCB) {
    request.put({url: 'http://api.domain.com/api/v1/user/12', form: body}, function (err, httpResponse, result) {
        if (err) {
            errorCB(err);
            return;
        }
        successCB(JSON.parse(result));
    });
};
```

## 4. http DELETE
```
exports.httpDelete = function (successCB, errorCB) {
    request.delete({url: 'http://api.domain.com/api/v1/user/12'}, function (err, httpResponse, result) {
        if (err) {
            errorCB(err);
            return;
        }
        successCB(JSON.parse(result));
    });
};
```