
---

**Web Cryptography API**

Code:

```javascript
// Generate random bytes with JavaScript
var crypto = window.crypto || window.msCrypto;
var arrayBuffer = new ArrayBuffer(32); // 2 bytes for each char
var arrayBufferView = new Uint16Array(arrayBuffer);
var randomBytes = crypto.getRandomValues(arrayBufferView);
```

Read more:

- http://blog.tompawlak.org/how-to-generate-random-values-nodejs-javascript
- https://developers.google.com/web/updates/2012/06/How-to-convert-ArrayBuffer-to-and-from-String
- https://msdn.microsoft.com/en-us/library/dn302324(v=vs.85).aspx
- https://developer.mozilla.org/de/docs/Web/API/RandomSource/getRandomValues
- Compatibility: https://developer.mozilla.org/en-US/docs/Web/API/window.crypto.getRandomValues

Common errors:

- Uncaught DOMException: Failed to execute 'getRandomValues' on 'Crypto': The provided ArrayBufferView is null.
- Uncaught RangeError: byte length of Uint16Array should be a multiple of 2

---

**@typedef tag**

JSDoc, typedef

```javascript
* @type {{identity_key_pair: (Proteus.keys.IdentityKeyPair|IdentityKeyPair|*), pre_key_bundle: (Proteus.keys.PreKeyBundle|PreKeyBundle|*)
```

Reference:

- http://wiki.netbeans.org/NewAndNoteworthyNB81#.40typedef

---

- [How to convert ArrayBuffer to and from String](https://developers.google.com/web/updates/2012/06/How-to-convert-ArrayBuffer-to-and-from-String)

---

Byte array to hexadecimal string conversion

```javascript
byteToHex(123) -> 7B
Crypto.util.bytesToHex([123])
 -> "7b"
```

---

**Rust println for Newbies**

```rust
Code:
println!("{}", master_key);

Error:
error: the trait `core::fmt::Display` is not implemented for the type `collections::vec::Vec<_>`

Ursache:
Ein Vektor hat keine Display-Methode, daher muss man den Debug Trait (core::fmt::Debug) verwenden.

Beispiel:
println!("{:?}", master_key);

Printen mit Hilfsfunktion:
println!("Key: {}\n", fmt_hex(&master_key));
```

---

**Working with Typed Arrays**

```javascript
var byteArray = [5, 30, 208, 218, 140, 173, 89, 133, 238, 120, 243, 172, 56, 0, 84, 80, 225, 83, 110, 68, 59, 136, 105, 202, 200, 243, 73, 174, 28, 38, 66, 246];
var arrayBuffer = new ArrayBuffer(byteArray.length);

// Typed Array, also the so called "Buffer view":
// Array of 8-bit unsigned integers with 32 byte length
var typedArray = new Uint8Array(arrayBuffer, byteArray);

// Copy array into buffer view
for (var i = 0, length = byteArray.length; i < length; i++) {
  typedArray[i] = byteArray[i];
}

console.log('Array length: ' + byteArray.length);
console.log('Byte length: ' + arrayBuffer.byteLength);
console.log('Typed Array content', typedArray);
```

---

**WebAudio**

When setting `gainNode.gain.value`

Fehler:
Value being assigned to AudioParam.value is not a finite floating-point value

Lösung:
anstatt 0 mach ne 0.0

---

**JS Refactoring**

z.util.ArrayUtil.move_element self_index, center, participants_sorted
	
Moves an element from one place to another by it's index.
This change happens in place which means that the array is modified immediately.

todo: nice idea, DSL with "move_element.from(...).to(...).in(...)"

---


**jasmine.Ajax**

```coffeescript
backend.respondWith 'GET', "http://localhost/endpoint", [200, {"Content-Type": "application/json"}, '{"B":"B"}']
```

```coffeescript
beforeEach ->
server = sinon.fakeServer.create()

it 'A', ->
  url = 'http://localhost/test'
  server.respondWith 'GET', url, [200, {"Content-Type": "application/json"}, '{"A":"A"}']

  $.ajax
    dataType: 'json'
    url: url
    success: (data, textStatus, jqXHR) ->
      console.log "Response: #{JSON.stringify(data)}"

  server.respond()

  server.respondWith 'GET', url, [200, {"Content-Type": "application/json"}, '{"B":"B"}']

  $.ajax
    dataType: 'json'
    url: url
    success: (data, textStatus, jqXHR) ->
      console.log "Response: #{JSON.stringify(data)}"

  server.respond()

  expect('A').toBe 'A'
```

```coffeescript
it 'can handle an unlimited amount of requests and respond to each one individually after all requests have been made', ->
  jasmine.Ajax.install()
  url = 'http://localhost/test'

  $.ajax
    dataType: 'json'
    url: url
    success: (data, textStatus, jqXHR) ->
      # Receives {"A":"A"}
      console.log "Response: #{JSON.stringify(data)}"

  $.ajax
    dataType: 'json'
    url: url
    success: (data, textStatus, jqXHR) ->
      # Receives {"B":"B"}
      console.log "Response: #{JSON.stringify(data)}"

  request = jasmine.Ajax.requests.first()
  request.respondWith {
    contentType: 'application/json'
    responseText: '{"A":"A"}'
    status: 200
  }

  request = jasmine.Ajax.requests.mostRecent()
  request.respondWith {
    contentType: 'application/json'
    responseText: '{"B":"B"}'
    status: 200
  }

  expect(jasmine.Ajax.requests.count()).toBe 2
  jasmine.Ajax.uninstall()
```

```coffeescript
it 'can handle an unlimited amount of requests and respond to each one individually after all requests have been made', ->
  jasmine.Ajax.install()
  url = 'http://localhost/test'

  $.ajax
    dataType: 'json'
    url: url
    success: (data, textStatus, jqXHR) ->
      # Receives {"A":"A"}
      console.log "Response: #{JSON.stringify(data)}"

  $.ajax
    dataType: 'json'
    url: url
    success: (data, textStatus, jqXHR) ->
      # Receives {"B":"B"}
      console.log "Response: #{JSON.stringify(data)}"

  responses = [
    {
      contentType: 'application/json'
      responseText: '{"A":"A"}'
      status: 200
    },
    {
      contentType: 'application/json'
      responseText: '{"B":"B"}'
      status: 200
    }
  ]

  for i in [0...jasmine.Ajax.requests.count()]
    request = jasmine.Ajax.requests.at i
    request.respondWith responses[i]

  expect(jasmine.Ajax.requests.count()).toBe 2
  jasmine.Ajax.uninstall()
```

```coffeescript
var preFilters = $.Callbacks();
$.ajaxPrefilter(preFilters.fire);
```

```coffeescript
it 'can handle an unlimited amount of requests and respond to each one individually after all requests have been made', ->
  jasmine.Ajax.install()

  $.ajax
    contentType: 'application/json; charset=utf-8'
    data: JSON.stringify {"PUT": "Request"}
    type: 'PUT'
    url: 'http://localhost/test'
    success: (data, textStatus, jqXHR) ->
      # Receives: {"PUT":"Successful"}
      console.log "Response: #{JSON.stringify(data)}"

  $.ajax
    dataType: 'json'
    url: 'http://localhost/abc'
    success: (data, textStatus, jqXHR) ->
      # Receives nothing
      console.log "Response: #{JSON.stringify(data)}"

  $.ajax
    contentType: 'application/json; charset=utf-8'
    data: JSON.stringify {"POST": "Request"}
    type: 'POST'
    url: 'http://localhost/test'
    success: (data, textStatus, jqXHR) ->
      # Receives: {"POST":"Successful"}
      console.log "Response: #{JSON.stringify(data)}"

  $.ajax
    dataType: 'json'
    url: 'http://localhost/test'
    success: (data, textStatus, jqXHR) ->
      # Receives: {"GET":"Successful"}
      console.log "Response: #{JSON.stringify(data)}"

  responses = {
    'http://localhost/test': {
      'PUT': [
        {
          data: {"PUT": "Request"},
          response: {
            contentType: 'application/json'
            responseText: '{"PUT":"Successful"}'
            status: 200
          }
        }
      ],
      'GET': [
        {
          data: {},
          response: {
            contentType: 'application/json'
            responseText: '{"GET":"Successful"}'
            status: 200
          }
        },
        {
          data: {},
          response: {
            contentType: 'application/json'
            responseText: '{"SECOND-GET":"Successful"}'
            status: 200
          }
        }
      ],
      'POST': [
        {
          data: {"POST":"Request"},
          response: {
            contentType: 'application/json'
            responseText: '{"POST":"Successful"}'
            status: 200
          }
        }
      ]
    }
  }

  for i in [0...jasmine.Ajax.requests.count()]
    request = jasmine.Ajax.requests.at i
    response_candidates = responses[request.url]?[request.method]

    if response_candidates
      for response_candidate in response_candidates
        request_data = JSON.stringify request.data()
        expected_request = JSON.stringify response_candidate.data

        if request_data is expected_request
          # Find the index of the response in our mocked data
          response_index = response_candidates.indexOf response_candidate
          # Send the response
          request.respondWith response_candidate.response
          # Remove the response from our mocked data
          response_candidates.splice response_index, 1
          if Object.keys(responses[request.url][request.method]).length is 0
            delete responses[request.url][request.method]
          break

  expect(jasmine.Ajax.requests.count()).toBe 4
  jasmine.Ajax.uninstall()
```

---

**Node.js Buffers in Plain JavaScript**

```javascript
// Node.js
var buffer = new Buffer(8); // uninitialized buffer with 8 bytes capacity
buffer.fill(0); // fill buffer with Zeros (00 00 00 00 00 00 00 00)

// JavaScript
var buffer = new ArrayBuffer(8);
console.log(buffer.byteLength); // 8 bytes long buffer (all bytes are pre-initialized to 0)

// Read data into buffer
var arrayBuffer = new ArrayBuffer(8);
var array = [0, 0, 0, 0, 0, 0, 0, 0];
var arrayBufferView = new Uint8Array(buffer);
for (var i = 0; i < arrayBufferView.length; i++) {
  arrayBufferView[i] = array[i];
}
```

**Bad JavaScript comments**

```javascript
// Bad: https://github.com/benadida/node-hkdf/blob/master/lib/hkdf.js#L17 

// imk is initial keying material
function HKDF(hashAlg, salt, ikm) {
  ...
}

// good


```

**Cool JavaScript loops**

```javascript
var len = 10, i = 0;
for (; i < len; i++) {
  console.log('Loop');
}

for (var len = 10, i = 0; i < len; i++) {
  console.log('Loop');
}
```

---

**Enums in CoffeeScript** 

Definition:

```coffeescript
window.Proteus ?= {}
window.Proteus.errors ?= {}

class Proteus.errors.DecodeError
  INVALID_ARRAY_LEN: 'Array length mismatch'
  INVALID_TYPE: 'Invalid type'
  MISSING_FIELD: 'Missing field'

  constructor: (@message) ->
    return @
```

Anwendung:

```coffeescript
Proteus.errors.DecodeError::INVALID_TYPE
```

In JavaScript:

```coffeescript
Proteus.errors.DecodeError.prototype.INVALID_TYPE;
```

---

**IIFE**

- http://benalman.com/news/2010/11/immediately-invoked-function-expression/#iife

- Immediately-Invoked Function Expression
- Self-executing anonymous function
- Safety Wrapper
- Self-invoked anonymous function

```javascript
(function(){ 
  // Placeholder
})();
```

**Node-style callbacks**

Callbacks in localForage are Node-style (error argument first).

Example:

```javascript
localforage.getItem('key', function(err, value) {
    if (err) {
        console.error('Oh noes!');
    } else {
        alert(value);
    }
});
```

**Java Unicode German Umlaut Escape Sequences**

Character | Unicode
:---:|:---:
Ä | \u00c4
ä | \u00e4
Ö | \u00d6
ö | \u00f6
Ü | \u00dc
ü | \u00fc
ß | \u00df

**+function**

- The word function looks like the beginning of a **function declaration** rather than a **function expression** and so the `()` following it (the ones at the end of the line above) would be a syntax error (as would the absense of a name, in that example)
- You can use parentheses (this is more common) like: `(function() { console.log("Foo!"); }());`
- Other variation: `(function() {})(); // dog balls`

- https://raw.githubusercontent.com/twbs/bootstrap/master/dist/js/bootstrap.js
- http://stackoverflow.com/a/13341710/451634

**Regex: Ranges & Capturing Groups**

```javascript
"Windows 10".match(/Windows/); // ["Windows"]
"Windows 10".match(/[0-9]/); // ["1"], Range
"Windows 10".match(/\d/); // ["1"], digit (also matches Eastern Arabic numerals)
"Windows 10".match(/\d{1,2}/); // ["10"]
"Windows 10".match(/\d+/g); // ["10"], d+ one or more digits
"Windows 10".match(/\d+\./); // null
"Windows 10.1".match(/\d+\./); // ["10."]
"Windows 10.1".match(/\d+\.+\d/); // ["10.1"]
"Windows 10.10".match(/\d+\.+\d/); // ["10.1"]
"Windows 10.10".match(/\d+\.+\d+/); // ["10.10"]
"Windows 10.10.10".match(/\d+\.+\d/); // ["10.1"]
"Windows 10.10.10".match(/\d+\.+\d+/); // ["10.10"]
"Windows 10.10.10".match(/(\d+\.)/); // ["10.", "10."]
"Windows 10.10.10".match(/(\d+(\.)?){3}/); // ["10.10.10", "10", undefined], Repeating match group + one or less
"Windows 10.10.10".match(/(\d+)\.(\d+)\.(\d+)/); // Match groups. They will output the whole result + the result of the matched groups, so ["10.10.10", "10", "10", "10"]
// More advanced
"Windows 10.10.10".match(/([A-Za-z ]+) (\d+)\.(\d+)\.(\d+)/); // ["Windows 10.10.10", "Windows", "10", "10", "10"]
"Windows 1\r\n0".match(/\d{1,2}/); ["1"]
"Windows 1\r\n0".match(/\d{1,2}/g); ["1", "0"]
```

Display

```
^(\d+\.){3}(\d+)$

^			Beginning of line
  (\d+\.)	Any number 1 or more followed by a .
  {3}		The above should happen 3 times
  (\d+)		A number 1 or more.
$			End of the line/data
```

TODO: - Capturing & Non-Capturing groups (http://www.bennyn.de/regex)

**Bower**

Reference SVN repositories:

```json
"cryptojs": "svn+http://crypto-js.googlecode.com/svn/#3.1.2"
```

On Windows you need an SVN client for that like "CollabNet Subversion 1.9.2 (Windows 64-bit)":
- http://www.collab.net/downloads/subversion

Registered libraries:

```json
"jquery": "2.1.4"
````

Warning, because of Bower downtime.

The pro way:

```json
"jquery": "git@github.com:jquery/jquery.git#2.1.4",
```

Local files on Windows:

```json
"example": "file://C:/Projects/my-project/.git#9203e6166b343d7d8b3bb638775b41fe5de3524c"
```

**CoffeeScript**

```coffeescript
if @is_answer() is true and @media_stream() isnt undefined and @has_added_remote_sdp() is true
  return true
``` 

->

```coffeescript
return @is_answer() and @media_stream()? and @has_added_remote_sdp()
```

OK, but...

```coffeescript
if z.util.Environment.browser.firefox
  signaling_states = [A, B]
```

->

```coffeescript
signaling_states = [A, B] if z.util.Environment.browser.firefox
```

isn't cool because you read from left to right and cannot skip right away.

**Content Security Policy**

```python
response.headers['Strict-Transport-Security'] = 'max-age=26280000'
response.headers['X-Content-Type-Options'] = 'nosniff'
response.headers['X-Frame-Options'] = 'deny'
response.headers['X-XSS-Protection'] = '1; mode=block'

csp_values = ';'.join([
  # Firefox <35 need this data: attribute for the grayscale filter based on SVG to work https://bugzilla.mozilla.org/show_bug.cgi?id=878608; in newer FF versions we use the new grayscale filter
  "default-src data:",
  "connect-src 'self' https://*.giphy.com https://api.raygun.io https://www.google.com https://prod-nginz-https.wire.com wss://prod-nginz-ssl.wire.com https://staging-nginz-https.zinfra.io wss://staging-nginz-ssl.zinfra.io https://edge-nginz-https.zinfra.io wss://edge-nginz-ssl.zinfra.io",
  "font-src 'self' data:",
  "frame-src 'self' https://accounts.google.com https://*.youtube.com https://*.soundcloud.com https://*.vimeo.com https://*.spotify.com",
  "img-src 'self' blob: data: https://*.giphy.com https://1-ps.googleusercontent.com https://*.localytics.com https://prod-nginz-https.wire.com https://*.cloudfront.net https://*.zinfra.io https://www.google-analytics.com https://csi.gstatic.com",
  # Firefox 36 does not handle "media-src" correctly, so we have to use a wildcard to make calling work.
  # Firefox 37 does not have this issue anymore, so we can change our CSP values for "media-src" in the future.
  "media-src *",
  "object-src 'self' https://*.youtube.com 1-ps.googleusercontent.com",
  "script-src 'self' 'unsafe-inline' 'unsafe-eval' https://apis.google.com https://*.localytics.com https://www.google-analytics.com https://api.raygun.io https://prod-nginz-https.wire.com https://staging-nginz-https.zinfra.io https://edge-nginz-https.zinfra.io",
  "style-src 'self' 'unsafe-inline' https://*.wire.com https://*.googleusercontent.com"
])
response.headers['Content-Security-Policy'] = csp_values
response.headers['X-Content-Security-Policy'] = csp_values
```

----------

**Travis CI: Encrypt environment variables**

```bash
# Given that Ruby is installed...
gem install travis
travis login
cd C:\dev\projects\welovecoding\webapp
travis encrypt PASSWORD=secret123 --add`
```

----------

## Web Worker

### Dedicated Worker

**index.html**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Title</title>
    <meta charset="UTF-8" />
  </head>
  <body>
    <script>
      // Worker Initialization
      var workerUrl = 'js/ReverseTextWorker.js';
      var worker = new Worker(workerUrl);

      worker.addEventListener('message', function (output) {
        console.log('Result from dedicated Worker: ' + output.data);
      }, false);

      worker.postMessage('Hello World');
    </script>
  </body>
</html>
```

**js/ReverseTextWorker.js**

```javascript
/* global self */

// Worker Definition
self.addEventListener('message', function (input) {
  var ouput = input.data.split('').reverse().join('');
  self.postMessage(ouput);
}, false);
```

### Dedicated Inline-Worker

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Title</title>
    <meta charset="UTF-8" />
  </head>
  <body>
    <script>
      // Worker Definition
      var reverseTextWorker = new Blob(["self.addEventListener('message', function (input) { var ouput = input.data.split('').reverse().join(''); self.postMessage(ouput); }, false);"], {type: 'text/javascript'});

      // Worker Initialization
      var workerUrl = window.URL.createObjectURL(reverseTextWorker);
      var worker = new Worker(workerUrl);

      worker.addEventListener('message', function (output) {
        console.log('Result from dedicated Worker: ' + output.data);
      }, false);

      worker.postMessage('Hello World');
    </script>
  </body>
</html>
```

----------
