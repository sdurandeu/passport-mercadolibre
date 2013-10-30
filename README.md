# Passport-Mercadolibre

[Passport](https://github.com/jaredhanson/passport) strategy for authenticating
with [Mercadolibre](http://wordpress.com) using the OAuth 2.0 API.

## Install

    $ npm install passport-mercadolibre

## Usage

#### Configure Strategy

The Mercadolibre authentication strategy authenticates users using a Mercadolibre
account and OAuth 2.0 tokens.  The strategy requires a `verify` callback, which
accepts these credentials and calls `done` providing a user, as well as
`options` specifying a client ID, client secret, and callback URL.

You can obtain the client ID and secret [here](http://applications.mercadolibre.com.ar/list).
    
    var MercadoLibreStrategy = require('passport-mercadolibre').Strategy;

    passport.use(new MercadoLibreStrategy({
        clientID: CLIENT_ID,
        clientSecret: CLIENT_SECRET
      },
      function(accessToken, refreshToken, profile, done) {
        User.findOrCreate({ WordpressId: profile.id }, function (err, user) {
          return done(err, user);
        });
      }
    ));

#### WARNING

The OAuth2 Module (0.9.5), which is a dependency, automatically adds a 'type=web_server' parameter to the query portion of the URL. This is a bug and that line should be commented out for the module to work. 

To do this, you need to comment the following line in the *node_modules\passport-oauth\node_modules\oauth\lib\auth2.js* file:

  exports.OAuth2.prototype.getOAuthAccessToken= function(code, params, callback) {
    var params= params || {};
    params['client_id'] = this._clientId;
    params['client_secret'] = this._clientSecret;

    // THIS ONE
    //params['type']= 'web_server';
    //

    var codeParam = (params.grant_type === 'refresh_token') ? 'refresh_token' : 'code';
    params[codeParam]= code;

    ...
  }


#### Authenticate Requests

Use `passport.authorize()`, specifying the `'mercadolibre'` strategy, to
authenticate requests.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

    app.get('/auth/mercadolibre',
      passport.authorize('mercadolibre'));

    app.get('/auth/mercadolibre/callback', 
      passport.authorize('mercadolibre', { failureRedirect: '/login' }),
      function(req, res) {
        // Successful authentication, redirect home.
        res.redirect('/');
      });

## License

[The MIT License](http://opensource.org/licenses/MIT)

## Thanks

Thanks to https://github.com/mjpearson/passport-wordpress for the README and file structure.
