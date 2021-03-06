# Cookie client

This library emulates client cookie behaviour, allowing appropriate cookie behaviour when making outgoing requests from Node.

This library is **not particularly efficient** for large numbers of cookies.  The cookies are stored in a big list, and this entire list is searched for every request.  For small numbers of cookies, or cookies on a single domain, this should not be an issue - so if you're just automatically logging into and exploring a single API, this should be fine.

If you're looking to create a full-blown multi-domain scraper or something, then raise a GitHub issue or email the author about making it more efficient.

## Usage

### Create a cookie store

```javascript
var cookieClient = require('cookie-client');

var cookieStore = cookieClient();  // use of "new" is optional
```

### Adding cookies from incoming response headers

```javascript
cookieStore.addFromHeaders(response.headers); // full headers object
cookieStore.addFromHeaders(response.headers['set-cookie']); // just the cookie headers
```

### Cookie string for outgoing request

```javascript
request.headers['cookie'] = cookieStore.cookieStringForRequest(domain, path, isSecure);
```

### Inspect cookie objects for outgoing request

```javascript
request.headers['cookie'] = cookieStore.cookiesForRequest(domain, path, isSecure);
```

## Public Suffix List

To prevent "super-cookies" assigning themselves domains like `.com` (which is dangerous), this module attempts to download a Public Suffix List (see [publicsuffix.org](http://publicsuffix.org/)).  Any response other than a 200 will log an error to the console.

This is downloaded and stored in the module directory, so it happens once per installation.

Even if it is cached locally, it is loaded asynchronously.  You can query whether the PSL has loaded yet, or request a callback:

```javascript
cookieClient.pslLoaded;  // boolean flag

cookieClient,whenPslLoaded(function (error) {
	...
});
```

## License

The code is licensed as [Public Domain](http://geraintluff.github.io/tv4/LICENSE.txt) or [MIT](http://jsonary.com/LICENSE.txt) (your choice).

However, the file `public-suffix-list.txt` is taken from [publicsuffix.org](http://publicsuffix.org/), and has separate licensing terms (Mozilla Public License).  This package *used* to fetch the file from the web on first run, but that caused Node to crash if the connection was dropped by the remote server.  It is therefore included in the package, but is license separately from the rest of the code.