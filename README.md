# otp

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).


npm install --save speakeasy
npm install --save qrcode

In "node_modules/speakeasy/index.js" replace from line 39 to 41 with this code

if (!Buffer.isBuffer(secret)) {
    secret = encoding === 'base32' ? new Buffer(base32.decode(secret)) : new Buffer(secret, encoding);
  }