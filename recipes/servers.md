# Servers

Overview of `http-proxy-middleware` implementation in different servers.

Missing a server? Feel free to extend this list of examples.

<!-- TOC depthfrom:2 insertanchor:false -->

- [Browser-Sync](#browser-sync)
- [Express](#express)
- [Connect](#connect)
- [fastify](#fastify)
- [lite-server](#lite-server)
- [Polka](#polka)
- [grunt-contrib-connect](#grunt-contrib-connect)
- [grunt-browser-sync](#grunt-browser-sync)
- [gulp-connect](#gulp-connect)
- [gulp-webserver](#gulp-webserver)

<!-- /TOC -->

## Browser-Sync

https://github.com/BrowserSync/browser-sync
[![GitHub stars](https://img.shields.io/github/stars/BrowserSync/browser-sync.svg?style=social&label=Star)](https://github.com/BrowserSync/browser-sync)

```javascript
const browserSync = require('browser-sync').create();
const { createProxyMiddleware } = require('http-proxy-middleware');

const apiProxy = createProxyMiddleware('/api', {
  target: 'http://www.example.org',
  changeOrigin: true, // for vhosted sites
});

browserSync.init({
  server: {
    baseDir: './',
    port: 3000,
    middleware: [apiProxy],
  },
  startPath: '/api',
});
```

## Express

https://github.com/expressjs/express
[![GitHub stars](https://img.shields.io/github/stars/expressjs/express.svg?style=social&label=Star)](https://github.com/expressjs/express)

```javascript
const express = require('express');
const { createProxyMiddleware } = require('http-proxy-middleware');

const apiProxy = createProxyMiddleware('/api', {
  target: 'http://www.example.org',
  changeOrigin: true, // for vhosted sites
});

const app = express();

app.use(apiProxy);
app.listen(3000);
```

## Connect

https://github.com/senchalabs/connect
[![GitHub stars](https://img.shields.io/github/stars/senchalabs/connect.svg?style=social&label=Star)](https://github.com/senchalabs/connect)

```javascript
const http = require('http');
const connect = require('connect');
const { createProxyMiddleware } = require('http-proxy-middleware');

const apiProxy = createProxyMiddleware('/api', {
  target: 'http://www.example.org',
  changeOrigin: true, // for vhosted sites
});

const app = connect();
app.use(apiProxy);

http.createServer(app).listen(3000);
```

## fastify

https://github.com/fastify/fastify [![GitHub stars](https://img.shields.io/github/stars/fastify/fastify.svg?style=social&label=Star)](https://github.com/fastify/fastify)

```javascript
const fastify = require('fastify')({ logger: true });
const { createProxyMiddleware } = require('http-proxy-middleware');

(async () => {
  await fastify.register(require('fastify-express'));

  const proxy = createProxyMiddleware({
    target: 'http://jsonplaceholder.typicode.com',
    changeOrigin: true,
  });

  fastify.use(proxy);

  fastify.listen(3000, (err, address) => {
    if (err) throw err;
    fastify.log.info(`server listening on ${address}`);
  });
})();

// curl http://localhost:3000/users
```

## lite-server

https://github.com/johnpapa/lite-server
[![GitHub stars](https://img.shields.io/github/stars/johnpapa/lite-server.svg?style=social&label=Star)](https://github.com/johnpapa/lite-server)

File: `bs-config.js`

```javascript
const { createProxyMiddleware } = require('http-proxy-middleware');

const apiProxy = createProxyMiddleware('/api', {
  target: 'http://www.example.org',
  changeOrigin: true, // for vhosted sites
});

module.exports = {
  server: {
    // Start from key `10` in order to NOT overwrite the default 2 middleware provided
    // by `lite-server` or any future ones that might be added.
    // Reference: https://github.com/johnpapa/lite-server/blob/master/lib/config-defaults.js#L16
    middleware: {
      10: apiProxy,
    },
  },
};
```

## Polka

https://github.com/lukeed/polka
[![GitHub stars](https://img.shields.io/github/stars/lukeed/polka.svg?style=social&label=Star)](https://github.com/lukeed/polka)

```javascript
const polka = require('polka');
const { createProxyMiddleware } = require('http-proxy-middleware');

const app = polka();

app.use(
  createProxyMiddleware({
    target: 'http://www.example.org',
    changeOrigin: true,
  })
);

app.listen(3000);
```

## grunt-contrib-connect

https://github.com/gruntjs/grunt-contrib-connect
[![GitHub stars](https://img.shields.io/github/stars/gruntjs/grunt-contrib-connect.svg?style=social&label=Star)](https://github.com/gruntjs/grunt-contrib-connect)

As an `Array`:

```javascript
const { createProxyMiddleware } = require('http-proxy-middleware');

const apiProxy = createProxyMiddleware('/api', {
  target: 'http://www.example.org',
  changeOrigin: true, // for vhosted sites
});

grunt.initConfig({
  connect: {
    server: {
      options: {
        middleware: [apiProxy],
      },
    },
  },
});
```

As a `function`:

```javascript
const { createProxyMiddleware } = require('http-proxy-middleware');

const apiProxy = createProxyMiddleware('/api', {
  target: 'http://www.example.org',
  changeOrigin: true, // for vhosted sites
});

grunt.initConfig({
  connect: {
    server: {
      options: {
        middleware: function (connect, options, middlewares) {
          // inject a custom middleware into the array of default middlewares
          middlewares.unshift(apiProxy);

          return middlewares;
        },
      },
    },
  },
});
```

## grunt-browser-sync

https://github.com/BrowserSync/grunt-browser-sync
[![GitHub stars](https://img.shields.io/github/stars/BrowserSync/grunt-browser-sync.svg?style=social&label=Star)](https://github.com/BrowserSync/grunt-browser-sync)

```javascript
const { createProxyMiddleware } = require('http-proxy-middleware');

const apiProxy = createProxyMiddleware('/api', {
  target: 'http://www.example.org',
  changeOrigin: true, // for vhosted sites
});

grunt.initConfig({
  // BrowserSync Task
  browserSync: {
    default_options: {
      options: {
        files: ['css/*.css', '*.html'],
        port: 9000,
        server: {
          baseDir: ['app'],
          middleware: apiProxy,
        },
      },
    },
  },
});
```

## gulp-connect

https://github.com/avevlad/gulp-connect
[![GitHub stars](https://img.shields.io/github/stars/avevlad/gulp-connect.svg?style=social&label=Star)](https://github.com/avevlad/gulp-connect)

```javascript
const gulp = require('gulp');
const connect = require('gulp-connect');
const { createProxyMiddleware } = require('http-proxy-middleware');

gulp.task('connect', function () {
  connect.server({
    root: ['./app'],
    middleware: function (connect, opt) {
      const apiProxy = createProxyMiddleware('/api', {
        target: 'http://www.example.org',
        changeOrigin: true, // for vhosted sites
      });

      return [apiProxy];
    },
  });
});

gulp.task('default', ['connect']);
```

## gulp-webserver

https://github.com/schickling/gulp-webserver
[![GitHub stars](https://img.shields.io/github/stars/schickling/gulp-webserver.svg?style=social&label=Star)](https://github.com/schickling/gulp-webserver)

```javascript
const gulp = require('gulp');
const webserver = require('gulp-webserver');
const { createProxyMiddleware } = require('http-proxy-middleware');

gulp.task('webserver', function () {
  const apiProxy = createProxyMiddleware('/api', {
    target: 'http://www.example.org',
    changeOrigin: true, // for vhosted sites
  });

  gulp.src('app').pipe(
    webserver({
      livereload: true,
      directoryListing: true,
      open: true,
      middleware: [apiProxy],
    })
  );
});

gulp.task('default', ['webserver']);
```
