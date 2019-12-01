# source
>[github](https://github.com/expressjs/session)

# versions
```
  $ node -v
  v10.15.3
  $ npm -v
  6.4.1
  express-session 1.17.0
```

# usage
## 1. init, e.g app.js
TODO use other session stores, like redis(connect-redis), mysql, ms sql, etc.
```
const session = require('express-session')
    ,MongoStore = require('connect-mongo')(session);

app.use(session({
    secret: 'anySecretYouLikeSeeDocumentForMore',
    key: 'anyKeyYouLikeSeeDocumentForMore',
    cookie: {
        maxAge: 1000 * 60 * 60 * 24 *30     // 30 days
    },
    store: new MongoStore({
        url: 'mongodb://<user>:<password>@<mongodb server>:9083/<database>',
        mongoOptions: {
            authSource: 'admin',
            w: 1
        }
    }),
    resave: false,
    saveUninitialized: true
}));
```

## 2. save session info
```
req.session.userID = 1;
req.session.displayName = 'Jeff';
req.session.roles = [0, 1, 2];
```