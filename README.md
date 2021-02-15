# login-with-twitter (without sessions)

### the login with twitter api doesn't actually use the secret token during the exchange, making the use of sessions totally redundant. to avoid complicating our existing auth setup, we're going sans sessions.


## Features

This module is designed to be the lightest possible wrapper on Twitter OAuth.

All this in < 100 lines of code.

## Install

```
npm install login-with-twitter
```

## Usage

Set up two routes on your web sever. We'll call them `/twitter` and
`/twitter/callback`, but they can be named anything.

### Initialization
Initialize this module with the consumer key and secret for your Twitter App you created with an Twitter Developer account.

```js
const LoginWithTwitter = require('login-with-twitter')

const tw = new LoginWithTwitter({
  consumerKey: '<your consumer key>',
  consumerSecret: '<your consumer secret>',
  callbackUrl: 'https://example.com/twitter/callback'
})
```

### Login

Call `login` from your `/twitter` route.

```js 
app.get('/twitter', (req, res) => {
  tw.login((err, tokenSecret, url) => {
    if (err) {
      // Handle the error your way
    }
    
    // Redirect to the /twitter/callback route, with the OAuth responses as query params
    res.redirect(url)
  })
})
```

### Callback

Then, call `callback` from your `/twitter/callback` route. The request will include `oauth_token` and `oauth_verifier` in the URL, accessible with `req.query`. Pass those into `callback` and a callback that handles a `user` object that this module will return.

```js
app.get('/twitter/callback', (req, res) => {
  tw.callback({
    oauth_token: req.query.oauth_token,
    oauth_verifier: req.query.oauth_verifier
  }, (err, user) => {
    if (err) {
      // Handle the error your way
    }
    
    // The user object contains 4 key/value pairs, which
    // you should store and use as you need, e.g. with your
    // own calls to Twitter's API, or a Twitter API module
    // like `twitter` or `twit`.
    // user = {
    //   userId,
    //   userName,
    //   userToken,
    //   userTokenSecret
    // }
    req.user = user
    
    // Redirect to whatever route that can handle your new Twitter login user details!
    res.redirect('/')
  });
});
```

---

For more information, check out the implementation in [index.js](index.js).

## license

MIT. Copyright (c) [Feross Aboukhadijeh](http://feross.org).
