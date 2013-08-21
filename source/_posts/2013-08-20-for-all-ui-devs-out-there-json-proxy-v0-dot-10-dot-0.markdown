---
layout: post
title: "For all UI devs out there...json-proxy v0.10.0"
date: 2013-08-20 21:44
comments: true
categories: 
- node
- grunt
- proxy
---


To all my UI devs out there, I finally had some time to rework json-proxy into a lean and meaner machine. 

If you haven't seen the proxy before, it enables a UI dev to proxy localhost URLs to a remote server.

Why do this?   Say you are doing an AngularJS front end, and want to wire some data to a REST API call like `$http.get('/api/foo/1')`.
Before, you had to mock out the JSON result, run the entire server stack locally, or just cross your fingers and hope it works on the integration server.  

Well, the proxy lets you transparently reroute `http://localhost/api/foo/1` from your local laptop to an integration server
like `http://integration-server/api/foo/1`.  No CORS.  No JSONP.  No nonsense.  

### Grunt

With v0.1.0, the big change is the proxy now works right inside the Grunt server.   Here's an example config from the scaffold project:

    connect: {
      options: {
        port: 9000,
        // Change this to '0.0.0.0' to access the server from outside.
        hostname: 'localhost'
      },
      livereload: {
        options: {
          middleware: function (connect) {
            return [
              require('json-proxy').initialize({
                proxy: {
                  forward: {
                    '/api/': 'http://integration-server:4040',
                    '/swagger/': 'http://integration-server:4040',
                  },
                  headers: {
                    'X-Forwarded-User': 'John Doe',
                    'X-Forwarded-User-Email': 'john.doe@example.com'
                  }
                }
              }),
              lrSnippet,
              mountFolder(connect, '.tmp'),
              mountFolder(connect, yeomanConfig.app)
            ];
          }
        }
      },



### Auth Headers

Even better, you can inject headers into proxied request.  So if you remote server requires an Authorization header with an OAuth-style token, or sits behind an Enterprise SSO appliance, you can add the headers to deal with bypassing the remote server authentication.

### CLI

The CLI utility is also improved.  The CLI properly supports global installation. 
If you install using `sudo npm install -g json-proxy`, you can invoke the proxy simply as `json-proxy` with whatever args you want.



### CLI usage info:

     json-proxy [-c configFile] [-p port] [-f proxy forwarding rule]
                 [-h header rule] [-html5mode [defaultFile]] [directory]

    Examples:
       json-proxy -p 8080 -f "/api=http://server" -f "/=http://localhost:9000" .
       json-proxy -h "X-Forwarded-User=johndoe" /tmp/folder
       json-proxy -c "/tmp/config.json"

    By default, looks for a config file at ./json-proxy.json

    Environmental variables:
      JSON_PROXY_PORT         see --port
      JSON_PROXY_WEBROOT      directory
      JSON_PROXY_GATEWAY      --gateway
      JSON_PROXY_GATEWAY_AUTH "username:password" credentials for --gateway)

    Options:
      -p, --port     The TCP port for the proxy server                      
      -f, --forward  a forwarding rule (ex. /foo=server/foo)                
      -h, --header   a custom request header (ex. iv-user=johndoel)         
      -c, --config   a config file                                          
      -g, --gateway  URL for a LAN HTTP proxy to use for forwarding requests
      --html5mode    support AngularJS HTML5 mode by catching 404s          
      -?, --help     about this utility                                     
      --version      version info            


More info @ [https://github.com/steve-jansen/json-proxy](https://github.com/steve-jansen/json-proxy) and 
[https://npmjs.org/package/json-proxy](https://npmjs.org/package/json-proxy)

Enjoy!