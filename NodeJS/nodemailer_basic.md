# source
>[github](https://github.com/nodemailer/nodemailer)

# versions
```
  $ node -v
  v10.15.3
  $ npm -v
  6.4.1
  nodemailer 6.3.0
```

---

# usage
## 1. createTransport
```
{
    host: 'smtp.exmail.qq.com',
    // host: 'smtp.qq.com',
    // port: 465,       // for qq mail
    port: 587,      // for exmail
    secure: false,       // qq mail(true), exmail(false)
    // requireTLS: true,       // for exmail
    tls: {      // for exmail
        ciphers:'SSLv3',
        rejectUnauthorized: false
    },
    auth: {
        user: 'test@companydomain.com',     // or 12345678@qq.com
        pass: 'auth code. not password'
    },
    logger: true
}
```

## 2. message
```
let message = {
    to: 'customer <send2@domain.com>',
    subject: 'hello world',
    html: `
    <img src="http://127.0.0.1:6666/images/test.jpg"/>
    <p style="font-size: 12px; color: gray;">
        aaaaaaa
        <br>
        bbbbbb
    </p>`,
    attachments: [{
        filename: attachments_filename[i],
        path: 'http://files.domain.com/attachments/file-guid',       // file url is ok. save file on the disk as possible when transporting large files.
        contentType: 'application/pdf',     // important
        cid: timestamp        // unique as possible
    }]
};
```
