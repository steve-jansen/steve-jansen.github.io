---
layout: post
title: "Wiring together Express, Passport OAuth, and Swagger in NodeJS"
date: 2013-08-22 22:07
comments: true
categories: 
- NodeJS
- passport-github
- oauth
- swagger
- swagger-node-express
---

I spent a couple of hours tonight sorting out how to wire a NodeJS Express app to use both 
the [Passport authentication strategy for GitHub OAuth](http://passportjs.org/guide/) with
[Swagger for Express](https://github.com/wordnik/swagger-node-express).  Normally, this 
would be fairly straightforward.  A wrinkle with other examples is I needed to disallow
all anonymous access for an intranet app.

I'm a big fan of Swagger UI - it's a great, DRY way to auto-document your REST APIs. I expected Swagger
to be a natural fit with NodeJS.  Suprisingly, I found the Swagger.Net implmentation for .Net's WebAPI
to be considerably easier to use compared to the NodeJS implementation. 
Swagger.Net easily drops into an existing WebAPI project.  Not so with NodeJS.

In Node, you really have to write your REST APIs from the ground up using the Swagger packge.  The other
wacky part for me was you need two Express apps side-by-side to handle requests for both REST APIs and
static content. 

	var express = require('express'),
	    app     = express(),
	    subapp  = express(),;
	    swagger = require('./lib/swagger/swagger');

As we'll see below, the `subapp` sandboxes the Swagger package to the `/api/*` route.

_(I use a local copy of the swagger-node-express package downloaded to my source tree because I found that 
the npm package was considerably outdated compared to the master branch of the swagger-node-express repo.)_


Next, we configure PassportJS to use GitHub OAuth.  GitHub will POST back to the route
`/auth/github/callback` upon successful authentication.

```js
var passport       = require('passport'),
    GitHubStrategy = require('passport-github').Strategy;

passport.use(new GitHubStrategy({
    clientID: config['OAUTH_ID'],
    clientSecret: config['OAUTH_TOKEN'],
    callbackURL: '/auth/github/callback'
  },
  function(accessToken, refreshToken, profile, done) {
    var user = {
      id: profile.username,
      email: (profile.emails.length) ? profile.emails[0].value : null,
      gravatar: profile._json.gravatar_id
    };
    return done(null, user);
  }
));
```

For my needs, I got away with serializing the 3 user properties I need to a session cookie:

```js
passport.serializeUser(function(user, done) {
  done(null, user);
});

passport.deserializeUser(function(user, done) {
  done(null, user);
});
```

A critical piece of wiring is a reusable function to verify authentication:

```js
// middleware to ensure user is authenticated
function authenticate(req, res, next) {
  if (req.isAuthenticated()) { 
    return next(); 
  }
  if (req.headers['x-requested-with'] === 'XMLHttpRequest') {
    res.send("Authentication required", 401);
  } else {
    res.redirect('/login');
  }
}
```

The next key wiring is to configure both Express apps to use Passport as well as our `authenticate` function.
This approach prevents infinite OAuth HTTP 302 redirects:

```js
// configure express to use swagger for the `/api` route
subpath.configure(function() {
  subpath.use(express.cookieParser());
  subpath.use(express.bodyParser());
  subpath.use(express.methodOverride());
  subpath.use(express.session({ secret: config['SESSION_TOKEN'] }));
  subpath.use(passport.initialize());
  subpath.use(passport.session());
  subpath.use(authenticate);
  swagger.setAppHandler(subpath);
});

// configure express for the static content on the `/` route
app.configure(function() {
  app.use(express.cookieParser());
  app.use(express.bodyParser());
  app.use(express.methodOverride());
  app.use(express.session({ secret: config['SESSION_TOKEN'] }));
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(authenticate);
  app.use('/api', subpath); /* mount `/api` using the subapp */
  // default document middleware for swagger/index.html
  app.use('/swagger', function(req, res, next) {
    if (req.url === '/swagger') {
      res.redirect('/swagger/');
    }
    next();
  });
  app.use('/swagger', express.static(path.join(__dirname, 'public/swagger')));
  app.use(express.static(path.join(__dirname, 'public/client')));
});
```


Finally, we define routes to handle OAuth logins and callbacks, then configure Swagger using the
route `/api/doc`, and finally start the server.


``` js
// GitHub OAuth routes
app.get('/login', passport.authenticate('github'));
app.get('/auth/github/callback',  passport.authenticate('github', { successReturnToOrRedirect: '/', failureRedirect: '/login' }));

// Swagger configuration
swagger.addModels(require('./models'));
swagger.addGet(require('./controllers/user').get);
swagger.configureSwaggerPaths('', '/doc', '');
swagger.configure('', require('../package.json').version);

app.listen(8080);
```
