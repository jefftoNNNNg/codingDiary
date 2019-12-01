# source
>[mongodb](https://github.com/mongodb/node-mongodb-native)

>[gridfs-stream](https://github.com/aheckmann/gridfs-stream)

>[connect-busboy](https://github.com/mscdex/connect-busboy)

# versions
```
  $ node -v
  v10.15.3
  $ npm -v
  6.4.1
  mongodb: 2.2.31
  gridfs-stream 1.1.1
  connect-busboy: 0.0.2
```
>notice! mongodb: 2.2.31

# usage
## 1. init, e.g app.js
```
const mongoDB = require('mongodb')
    ,MongoClient = mongoDB.MongoClient;

let db = '';

exports.init = function () {
    MongoClient.connect("mongodb://<user>:<password>@<mongodb server>:9083/<database>", {
        authSource: 'admin',
        w: 1
    }, function (err, db1) {
        db = db1;
    });
};

exports.db = function () {
    return db;
};
```

## 2. read
db object was created when 'init' step
```
const mongo = require('mongodb');

db.collection('myCollection').find({}).toArray(function(err, docs) {
    console.log(docs);
});

db.collection('myCollection').findOne({_id: mongo.ObjectId('xxx-xxx-xxx')}, function (err, doc) {
    console.log(err, doc);
});
```

## 3. create
```
db.collection('myCollection').insert({
        a: 'value1',
        b: 'value2',
        c: 'value3'
    }, function (err, result) {
        console.log(err, result);
    });
```

## 4. update
```
const mongo = require('mongodb');

mdbClient.db().collection('myCollection').updateOne(
        {_id: mongo.ObjectId('xxx-xxx-xxx')},
        {$set: {
            a: 'value1',
            b: 'value2'
        }},
        function (err, result) {
            console.log(err, result);
        });
```

## 5. delete
```
const mongo = require('mongodb');

db.collection('myCollection').deleteOne({_id: mongo.ObjectId('xxx-xxx-xxx')}, function (err, result) {
    console.log(err, result);
});
```

## 6. gridfs-stream & busboy
>notice! mongodb: 2.2.31

### read records
```
const Grid = require('gridfs-stream');
const gfs = Grid(db, mongo);           // db object was created when 'init' step

// Set any aliases you like. eg: 'aliases.type', 'aliases.parentID', etc.
gfs.files.find({'aliases.type': 'a', 'aliases.parentID': 12}).toArray(function (err, files) {
    // your service
});
```

### read stream. eg: rendering an image
```
const gfs = Grid(db, mongo);           // db object was created when 'init' step

let readstream = gfs.createReadStream({
    _id: 'your-image-mongo-id'
});

readstream.on('error', function (err) {
    console.log('An error occurred!', err);
    throw err;
}).on('close', function () {
    console.log('download close');
});

readstream.pipe(res);
```

### write
> use 'busboy' when there's 'input[type=file]' in your page

> notice! Your page should be on your PC(desktop client). iPad or others device have not been tested.

```
const busboy = new Busboy({ headers: req.headers })
    ,gfs = Grid(mdbClient.db(), mongo);

busboy.on('file', function (fieldname, file, filename) {
    const options = {
        mode: 'w',      // default value: w+, possible options: w, w+ or r, see [GridStore](http://mongodb.github.com/node-mongodb-native/api-generated/gridstore.html)
        chunkSize: 261120,
        content_type: req.headers['content-type'],      // For content_type to work properly, set "mode"-option to "w" too!
        filename: filename,
        aliases: {
            type: 'typeA',
            a: 1,
            anything: 'ifYouLike',
            TODO: 'limitOfNumber?'
        }
    };
    let writeStream = gfs.createWriteStream(options);

    writeStream.on('error', function (err) {
        // console.log('write stream error', err);
    }).on('close', function (fileResult) {
        // your service
    });

    // 2. write file into db
    file.pipe(writeStream);
});

// 1. read file from page
req.pipe(busboy);
```
