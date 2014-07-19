---
layout: post
title: "json-proxy release 0.2.0"
date: 2014-07-19 15:20:56 +0100
comments: true
categories: 
- node
- grunt
- proxy
---

Happy to announce a new release of [json-proxy](https://www.npmjs.org/package/json-proxy), a
utility for HTML5 devs to run apps locally and proxy calls like http://localhost:9000/api to
a remote server, all without CORS or JSONP.

## Grunt Plugin
This release includes better support for running as a grunt plugin.
A [change in grunt-contrib-connect@0.8.0](https://github.com/gruntjs/grunt-contrib-connect/pull/85)
simplifies life for proxy plugins inside the livereload task of `grunt serve`:

```js
livereload: {
  options: {
    middleware: function(connect, options, middlewares) {
      // inject json-proxy to the front of the default middlewares array
      // requires grunt-contrib-connect v0.8.0+
      middlewares.unshift(
        require('json-proxy').initialize({
          proxy: {
            forward: {
              '/api/': 'http://api.example.com:8080'
            },
            headers: {
              'X-Forwarded-User': 'John Doe'
            }
          }
        })
      );

      return middlewares;
    }
  }
}
```

## SSL Endpoints

This release adds support for proxying to HTTPS endpoints.   Here's a sample
config to forward http://localhost:9000/channel to https://www.youtube.com/channel .

```
{
  "proxy": {
	  "forward": {
	    "/channel": "https://www.youtube.com:443"
    }
  }
}
```

## HTTP Proxy Gateways and Basic Authentication

You can now pass your authentication credentials to a HTTP proxy gateway on
your LAN via the `proxy.gateway.auth` config setting.  The setting value
uses the `username:password` format for HTTP basic authentication
(without base64 encoding).  Here's an example config to proxying remote request
via http://proxy.example.com:8080 as `proxyuser` with password `C0mp13x_!d0rd$$@P!`

```
var config = {
  "proxy": {
    "gateway": {
      "protocol: "http:",
      "host": "proxy.example.com",
      "port": 8080,
      "auth": "proxyuser:C0mp13x_!d0rd$$@P!" /** 'user:password' **/
    },  
    "forward": {
      "/api": "http://api.example.com",
      "/foo/\\d+/bar": "http://www.example.com",
      "/secure/": "https://secure.example.com"
    }
  }
};
```

## Upgrade to NodeJitsu http-proxy v1.1

This release required heavy refactoring to use the
[latest bits of Nodejitsu's http-proxy v1.1](https://github.com/nodejitsu/node-http-proxy/blob/master/UPGRADING.md)

This was necessary since version prior to 1.0 are no longer actively supported.

## Housekeeping

There's better unit test coverage, and the code validates against a
reasonable set of jshint linting rules.
