# limitr

[![Build Status](https://travis-ci.org/chuckbuckethead/limitr.png?branch=master)](https://travis-ci.org/chuckbuckethead/limitr)

HTTP request rate-limiter middleware for node.js

# Install

    $ npm install limitr

# API

```javascript
var express = require('express');
var limitr = require('limitr');

var app = express();
app.use(limitr({ limit: 10, rate: 1 }));
```

## limitr(options)

limitr instances can be configured to apply rate-limits, ignore certain requests or identify clients by a given field on the `req` object. For instance, you might want to increase the rate-limit for the resources that are not subject to much traffic, and decrease it for the ones that are.

The following example sets up a limitr instance that rate-limits at ten requests per second. It uses the client's IP to identify requests, and ignores (i.e., doesn't rate-limit) requests coming from `127.0.0.1`.

```javascript
var config = {
  limit: 10,
  rate: 1,
  clientId: 'ip', //default
  ignore: ['127.0.0.1'],
  message: 'API rate limit exceeded. See https://foo-url.com/api#rate-limits for details.'
};
limitr(config);
```

### Options

The required options are `limit` and `rate`; all others are optional.

* `limit`- the rate limit ceiling.
* `rate`- the rate at which the limit window will be reset, in seconds.
* `clientId`- the name of the field in the `req` object by which to identify the client. Can be specified in a path-like fashion. For example: `headers.foo-token` (default: `'ip'`)
* `ignore`- a set of clients that will not be subject to rate-limiting (default: `[]`)
* `message`- the message to be returned to the client when the limit is exceeded (default: `'API rate limit exceeded.'`)

## HTTP Headers and Response Codes

HTTP headers are returned in order to keep the client informed about his rate limit for a given API endpoint.

* `X-RateLimit-Limit`- the maximum number of requests that the consumer is permitted to make within the `rate` window.
* `X-RateLimit-Remaining`- the number of requests remaining in the current rate limit window.
* `X-RateLimit-Reset`- the time at which the current rate limit window resets in UTC epoch milliseconds.

When a client exceeds the rate limit for a given API endpoint, limitr will interrupt the request and return an HTTP 429 [“Too Many Requests”](http://tools.ietf.org/html/rfc6585#section-4) response code. The body will contain the `message`.

# License

The MIT License (MIT)

Copyright (c) 2014 Carlos González

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
