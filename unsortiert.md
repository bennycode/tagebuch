
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
- http://www.javascripture.com/ArrayBuffer

**ArrayBuffer to String (Base64-encoded)**

```coffeescript
array_buffer_to_base64_string = (array_buffer) ->
  binary = ''
  bytes = new Uint8Array array_buffer
  len = bytes.byteLength
  i = 0

  while i < len
    binary += String.fromCharCode bytes[i]
    i++

  return btoa binary
```

Alternative:

```coffeescript
array_buffer_to_base64_string = (array_buffer) ->
  string = String.fromCharCode.apply(null, new Uint8Array(array_buffer)); 
  return window.btoa string
```

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

- http://www.html5rocks.com/en/tutorials/webgl/typed_arrays/

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

**The Power of CoffeeScript**

```coffeescript
return (device for device in @encryption_repository.devices() when device.id isnt @current_client()?.id)
```

Generates

```javascript
var device;

return (function() {
  var i, len, ref, ref1, results;
  ref = this.encryption_repository.devices();
  results = [];
  for (i = 0, len = ref.length; i < len; i++) {
    device = ref[i];
    if (device.id !== ((ref1 = this.current_client()) != null ? ref1.id : void 0)) {
      results.push(device);
    }
  }
  return results;
}).call(this);
```

**Shorthands**

```coffeescript
for client in @clients()
  if client.id is client_id
   @clients.remove client
   break
```

```coffeescript
for client in @clients() when client.id is client_id
  @clients.remove client
  break
```

**Filter "undefined" values from array**

```coffeescript
array_values = array_values.filter (x) -> x isnt undefined
```

**Array Shortcuts**

```coffeescript
for client in all_clients
  client_et = @client_mapper.map_client client
  client_ets.push client_et
```

->

```coffeescript
clients_ets = (@client_mapper.map_client client for client in all_clients)
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

→

```coffeescript
return @is_answer() and @media_stream()? and @has_added_remote_sdp()
```

OK, but...

```coffeescript
if z.util.Environment.browser.firefox
  signaling_states = [A, B]
```

→

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

**Important Note!** :construction:

To not mess up with message events from other domains you should make use of a `targetOrigin`:

```javascript
/* global self */

// Worker Definition
self.addEventListener('message', function (event) {
  // Note: A dedicated worker does not have access to `window` so we take it from the event
  var window = window || event.currentTarget;
  var targetOrigin = window.location.protocol + "//" + window.location.hostname + ":" + window.location.port;

  if (event.origin === targetOrigin) {
    var ouput = event.data.split('').reverse().join('');
    self.postMessage(ouput, targetOrigin);
  }
}, false);
```

```javascript
/* global self */

// Worker Definition
self.addEventListener('message', function (event) {
  // Don't talk with "https://accounts.google.com" & Co.
  if (event.origin === '') {
    var ouput = event.data.split('').reverse().join('');
    self.postMessage(ouput);
  }
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

## Working with Promises & localForage

```javascript
var users = [];

var data = {
  'User@1': 'Homer',
  'User@2': 'Marge',
  'User@3': 'Lisa',
  'User@4': 'Bart',
  'User@5': 'Maggie',
  'Location@1': 'London',
  'Location@2': 'Paris',
  'Location@3': 'Rome'
};

// Clear database (delete items)
localforage.clear()
  // Fill database (save items)
  .then(function () {
    var keys = Object.keys(data);
    var promises = keys.map(function (key) {
      return localforage.setItem(key, data[key]);
    });
    return Promise.all(promises);
  })
  .then(function (values) {
    console.log('Saved items: ' + values.length, values);
  })
  .then(function () {
    // Filter database (query items)
    localforage.keys()
      .then(function (keys) {
        var pattern = 'User';
        return filtered_key = keys.filter(function (key) {
          return key.indexOf(pattern) > -1
        })
      })
      .then(function (filtered_key) {
        var promises = filtered_key.map(function (item) {
          return localforage.getItem(item);
        });
        return Promise.all(promises);
      })
      .then(function (values) {
        users = values;
        console.log('Filtered items: ' + users.length, users);
      });
  });
```

----------

## Callbacks

**Iteration 1**

```javascript
    function queryEncoding(onSuccess, onError) {
    var method = 'GET';
    var url = 'https://spreadsheets.google.com/feeds/list/o13394135408524254648.240766968415752635/od6/public/values?alt=json';
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status === 200) {
          var json = JSON.parse(xhr.responseText);
          var encoding = json.encoding;
          if (typeof onSuccess === 'function') {
            onSuccess(encoding);
          }
        } else {
          var error = new Error('Could not get encoding.');
          if (typeof onError === 'function') {
            onError(error);
          }
        }
      }
    };
    xhr.open(method, url, true);
    xhr.send(null);
  }

  queryEncoding(function (encoding) {
    console.log('Received encoding: ' + encoding);
  }, function (error) {
    console.log('Error: ' + error.message, error);
  });
```

**Iteration 2**

```javascript
  function queryEncoding(onSuccess, onError) {
    var method = 'GET';
    var url = 'https://spreadsheets.google.com/feeds/list/o13394135408524254648.240766968415752635/od6/public/values?alt=json';

    var xhr = new XMLHttpRequest();

    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status === 200) {
          var json = JSON.parse(xhr.responseText);
          var encoding = json.encoding;
          if (typeof onSuccess === 'function') {
            onSuccess(encoding);
          }
        } else {
          var error = new Error('Could not get encoding.');
          if (typeof onError === 'function') {
            onError(error);
          }
        }
      }
    };

    xhr.open(method, url, true);
    xhr.send(null);
  }

  var callbacks = {
    onSuccess: function (encoding) {
      console.log('Received encoding: ' + encoding);
    },
    onError: function (error) {
      console.log('Error: ' + error.message, error);
    }
  };

  queryEncoding(callbacks.onSuccess, callbacks.onError);
```

**Iteration 3: with Promises**

```javascript
  var queryEncoding = function () {
    var promise = new Promise(function (resolve, reject) {
      var method = 'GET';
      var url = 'https://spreadsheets.google.com/feeds/list/o13394135408524254648.240766968415752635/od6/public/values?alt=json';
      var xhr = new XMLHttpRequest();
      xhr.onreadystatechange = function () {
        if (xhr.readyState === 4) {
          if (xhr.status === 200) {
            var json = JSON.parse(xhr.responseText);
            var encoding = json.encoding;
            resolve(encoding);
          } else {
            var error = new Error('Could not get encoding.');
            reject(error);
          }
        }
      };
      xhr.open(method, url, true);
      xhr.send(null);
    });

    return promise;
  };

  var callbacks = {
    onSuccess: function (encoding) {
      console.log('Received encoding: ' + encoding);
    },
    onError: function (error) {
      console.log('Error: ' + error.message, error);
    }
  };

  queryEncoding().then(callbacks.onSuccess).catch(callbacks.onError);
```

**Iteration 4: with Promises & Fetch API**

```javascript
  var queryEncoding = function () {
    var method = 'GET';
    var url = 'https://sspreadsheets.google.com/feeds/list/o13394135408524254648.240766968415752635/od6/public/values?alt=json';

    var promise = new Promise(function (resolve, reject) {
      var request = new Request(url);
      var options = {
        method: method
      };

      window.fetch(request, options).then(function (response) {
        response.json().then(function (json) {
          var encoding = json.encoding;
          resolve(encoding);
        });
      }).catch(function (error) {
        var error = new Error('Could not get encoding.');
        reject(error);
      });
    });

    return promise;
  };

  var callbacks = {
    onSuccess: function (encoding) {
      console.log('Received encoding: ' + encoding);
    },
    onError: function (error) {
      console.log('Error: ' + error.message, error);
    }
  };

  queryEncoding().then(callbacks.onSuccess).catch(callbacks.onError);
```

----------

## CoffeeScript's Existential Operator

**CoffeeScript**

```coffeescript
targetOrigin =  event.data?.targetOrigin?;
```

**Transpiled JavaScript**

```javascript
var ref, targetOrigin;
targetOrigin = ((ref = event.data) != null ? ref.targetOrigin : void 0) != null;
```

----------

## Promise examples with setTimeout

```javascript

var async = new Promise(function (resolve, reject) {
  window.setTimeout(function () {
    resolve(72);
  }, 2000);
});

async().then(function(result){
  console.log(result);
});
```

```javascript
function collectionOfPromises() {
  var promises = [];
  var promise = new Promise(function (resolve, reject) {
    window.setTimeout(function () {
      resolve(72);
    }, 2000);

    return promise;
  });

  for (var i = 0; i < 5; i++) {
    var promiseInstance = promise;
    promises.push(promiseInstance);
  }

  return Promise.all(promises);
}

collectionOfPromises().then(function (results) {
  // Will print 5 results after 2 seconds
  console.log(results);
});
```

----------

## Install WildFly AS

- `add-user.bat` (`admin`/`admin`)
- `standalone.bat`
- Admin console listening on http://127.0.0.1:9990
- Standard: http://localhost:8080/

**Configs:**

```
%WILDFLY_HOME%\standalone\configuration\standalone-full.xml
%WILDFLY_HOME%\standalone\configuration\mgmt-users.properties
```

----------

## RegEx from String

**Match everything until the first dot**

```javascript
// With classic regex
var resultsArray = 'welovecoding.com'.match(/[^.]*/i);
// With a String regex
var resultsArray = 'welovecoding.com'.match(new RegExp('[^.]*', 'i'));
```

----------

## MV-Something in HTML


**NO seperation between view and logic**

```html
<form id="form-contact" onsubmit="return validate(this);">
  ...
</form>
```

**Seperation between view and logic**

```html
<form id="form-contact">
  ...
</form>
<script>
  var formContact = document.getElementById('form-contact');
  formContact.addEventListener('submit', function(submitEvent){
    submitEvent.preventDefault();
  });
</script>
```

----------

## Modern JavaScript

Functional programming, String Substitution (Template Strings) & convenient methods

**Before**

```javascript
var names = ["Sarah", "Benny", "Felina"];

for (var i = 0, length = names.length; i < length; i++) { 
  console.log('Hello, ' + names[i] + '!');
}
```

**With ES6**

```javascript
var names = ["Sarah", "Benny", "Felina"];

names.forEach((name) => console.log(`Hello, ${name}!`));
```

----------

## CoffeeScript patterns

Private functions (declared with an underscore) never use fat arrows (because they are not made to be called from "outside").

**Example**

```coffeescript
_setup_view_models: ->
```

----------

## When to use Promises

- [JavaScript Promises in Detail](http://dailyjs.com/2014/02/20/promises-in-detail/)

```coffeescript
promised_access_token = new Promise((resolve, reject) ->
 if true
   resolve 'Result'
 else
   reject 'Unknown error'
)
```

Promises are a good thing to introduce when one condition of your code is synchronous and the other one asnychronous. With a Promise around this decision you can easily bundle these to worls without the need of a callback. You will also preserve a direct return value which is very nice to have.

If you make use of the `reject` function of a Promise, then you also need to declare a `catch` call when executing your promise.

**Example**

```javascript
promised_access_token.then(onSuccess).catch(onError);
```

**Before using a Promise**

```coffeescript
@_load_access_token (access_token) =>
  if access_token
    @init_app()
  else
    @redirect_to_login()
```

**With using a Promise**

```coffeescript
access_token_promise = @_load_access_token()
access_token_promise.then(@init_app).catch @redirect_to_login
```

## Smart CoffeeScript

```coffeescript
# 1. Check timestamp
filtered = records.filter((record) ->
  record.meta.timestamp < timestamp
)

# 2. Sort by timestamp
filtered.sort (a, b) ->
  a.meta.timestamp < b.meta.timestamp

# 3. Limit result set to 1
sliced = filtered.reverse().slice(Math.max(filtered.length - 1, 1))
```

**Filter items which are undefined**

```coffeescript
session_ids = session_ids.filter (x) -> x isnt undefined
```

→

```coffeescript
records = (record for record in records when record.meta.timestamp < offset) if offset
sliced = records.slice(0, limit)
```

## Keep things short

```coffeescript
# https://github.com/dcodeIO/ProtoBuf.js/wiki/Builder
generic_message = new z.proto.GenericMessage z.util.create_random_uuid(), new z.proto.Text message
generic_message_encoded = generic_message.encode()
generic_message_array_buffer = generic_message_encoded.toArrayBuffer()
generic_message_string = z.util.array_buffer_to_string generic_message_array_buffer
generic_message_encrypted = cryptobox_session.encrypt generic_message_string
generic_message_encrypted_base64 = z.util.array_to_base64 generic_message_encrypted
```

→

```coffeescript
generic_message = new z.proto.GenericMessage z.util.create_random_uuid(), new z.proto.Text message
generic_message_encrypted = cryptobox_session.encrypt generic_message.toArrayBuffer()
generic_message_encrypted_base64 = z.util.array_to_base64 generic_message_encrypted
```

## Working with Dexie.js

- [Dexie.js: A Minimalistic Wrapper for IndexedDB](http://dexie.org/)
- Note: Good example to show uselessness of automatic CoffeeScript generation

```javascript
// Drop database before creating a new one
var dbName = 'MyFriends';
var dbSchema = {friends: 'name, age'};
var db = new Dexie(dbName);
db.version(1).stores(dbSchema);
db['open']().then(function () {
  db['delete']().then(function () {
    db = new Dexie(dbName);
    db.version(1).stores(dbSchema);
    db['open']().then(function () {
      console.log('Hello database!');
    }).catch(function (error) {
    }).finally(function () {
    });
  }).catch(function (error) {
  }).finally(function () {
  });
}).catch(function (error) {
}).finally(function () {
});
```

```javascript
function operate(db, operation, onSuccess, onError, always) {
  db[operation]().then(function () {
    if (typeof onSuccess === "function") {
      onSuccess();
    }
  }).catch(function (error) {
    if (typeof onError === "function") {
      onError(error);
    }
  }).finally(function () {
    if (typeof always === "function") {
      always();
    }
  });
}

function createNewDatabase(dbName, dbSchema) {
  var db = new Dexie(dbName);
  db.version(1).stores(dbSchema);
  return db;
}

function generalErrorHandling(error) {
  console.error('There was an error: ' + error.message);
}

// Drop database before creating a new one
var dbName = 'MyFriends';
var dbSchema = {friends: 'name, age'};
var db = createNewDatabase(dbName, dbSchema);
operate(db, 'open', function () {
  operate(db, 'delete', function () {
    var db = createNewDatabase(dbName, dbSchema);
    operate(db, 'open', function () {
      console.error('Hello database!');
    }, generalErrorHandling);
  }, generalErrorHandling);
}, generalErrorHandling);
```

```javascript
function createNewDatabase(dbName, dbSchema) {
  var db = new Dexie(dbName);
  db.version(1).stores(dbSchema);
  return db;
}

var dbName = 'MyFriend';
var dbSchema = {friends: 'name, age'};
var db = createNewDatabase(dbName, dbSchema);
db.open()
  .then(function () {
    return db.delete();
  })
  .then(function () {
    var db = createNewDatabase(dbName, dbSchema);
    return db.open();
  })
  .then(function () {
    console.log('Hello database');
  })
  .catch(function (error) {
    console.warn('There was an error: ' + error.message);
  });
;
```

**Complex schema**

```javascript
function createNewDatabase(dbName, dbSchema) {
  var db = new Dexie(dbName);
  db.version(1).stores(dbSchema);
  return db;
}

var events = [
  {"raw": {"conversation": "d4ff3d06-771e-461b-b65a-1213a8a4faff", "time": "2016-02-05T16:50:55.574Z", "from": "39b7f597-dfd1-4dff-86f5-fe1b79cb70a0", "type": "conversation.otr-message-add"}, "meta": {"timestamp": 1454691055574, "version": 1}, "mapped": {"data": {"nonce": "d4ff3d06-771e-461b-b65a-1213a8a4faff@39b7f597-dfd1-4dff-86f5-fe1b79cb70a0@1454691055574"}, "from": "39b7f597-dfd1-4dff-86f5-fe1b79cb70a0", "time": "2016-02-05T16:50:55.574Z", "id": "419788d2-9be3-450b-8371-a73cdfe05ede", "conversation": "d4ff3d06-771e-461b-b65a-1213a8a4faff", "type": "conversation.knock"}},
  {"raw": {"conversation": "d4ff3d06-771e-461b-b65a-1213a8a4faff", "time": "2016-02-05T16:50:48.332Z", "from": "9b47476f-974d-481c-af64-13f82ed98a5f", "type": "conversation.otr-message-add"}, "meta": {"timestamp": 1454691048332, "version": 1}, "mapped": {"data": {"nonce": "d4ff3d06-771e-461b-b65a-1213a8a4faff@9b47476f-974d-481c-af64-13f82ed98a5f@1454691048332", "content": "Test 🔒"}, "from": "9b47476f-974d-481c-af64-13f82ed98a5f", "time": "2016-02-05T16:50:48.332Z", "id": "f0b31281-1e83-4d12-bde7-03e02ddfe103", "type": "conversation.message-add", "conversation": "d4ff3d06-771e-461b-b65a-1213a8a4faff"}}
];

var dbName = 'Wire';
var dbSchema = {
  conversation_events: ', raw.conversation, raw.time, meta.timestamp'
};

var db = createNewDatabase(dbName, dbSchema);
db.open()
  .then(function () {
    return db.delete();
  })
  .then(function () {
    db = createNewDatabase(dbName, dbSchema);
    return db.open();
  })
  .then(function () {
    var promises = [];
    for (var i = 0; i < events.length; i++) {
      var promise = db['conversation_events'].put(events[i], 'a' + i);
      promises.push(promise);
    }
    return Promise.all(promises);
  })
  .then(function () {
    console.log('Saved all events.');
  })
  .catch(function (error) {
    console.warn('There was an error: ' + error.message);
  });
```

## Catching Errors in Promises

Notice the different on how the error message is printed:

```javascript
// reject
new Promise(function(resolve, reject) {
  reject('Stack Overflow');
})
.catch(function(error) {
  console.log('Catched error: ' + error); // Catched error: Stack Overflow
});

// throw new Error
new Promise(function(resolve, reject) {
  throw new Error('Stack Overflow');
})
.catch(function(error) {
  console.log('Catched error: ' + error.message); // Catched error: Stack Overflow
});
```

So if you make use of `reject`, then use an `Error` object:

```javascript
new Promise(function(resolve, reject) {
  reject(new Error('Stack Overflow'));
})
.catch(function(error) {
  console.log('Catched error: ' + error.message); // Catched error: Stack Overflow
});
```

Another approach:

```javascript
new Promise(function(resolve, reject) {
  throw new Error('Stack Overflow');
})
.then(function(result) {
  console.log('Everything is cool.');
}, function(error) {
  console.log('Catched error: ' + error.message); // Catched error: Stack Overflow
});
```

Which compiles down to:

```javascript
var onSuccess = function(result) {
  console.log('Everything is cool.');
};

var onError = function(error) {
  console.log('Catched error: ' + error.message); // Catched error: Stack Overflow
};

new Promise(function(resolve, reject) {
  throw new Error('Stack Overflow');
})
.then(onSuccess, onError);
```

### Catching all rejections in a Promise.all

`Promise.all` rejects as soon as one `Promise` rejects, so we have to use `Promise.settle`.

**Example**

## Promise terminology

- http://www.html5rocks.com/en/tutorials/es6/promises/

## Promise Playground

### Find odd numbers with Promises

*Idea: Give people code comments and they have to assign them to code blocks*

```javascript
function isOdd(number) {
  return !!(number % 2);
}

var promises = [];

for (var i = 1; i < 11; i++) {
  var promise = new Promise(function(resolve, reject) {
    if (isOdd(i)) {
      resolve(i);
    } else {
      resolve(undefined);
    }
  });

  promises.push(promise);
}

Promise.all(promises).then(function(results) {
  // Filter 'undefined' results
  var oddNumbers = results.filter(function(number) {
    return number !== undefined;
  });

  console.log('Odd numbers', oddNumbers);
});
```

## Cannot be compiled in CoffeeScript

```javascript
	switch (remainder) {
		case 3: k1 ^= (key.charCodeAt(i + 2) & 0xff) << 16;
		case 2: k1 ^= (key.charCodeAt(i + 1) & 0xff) << 8;
		case 1: k1 ^= (key.charCodeAt(i) & 0xff);
		
		k1 = (((k1 & 0xffff) * c1) + ((((k1 >>> 16) * c1) & 0xffff) << 16)) & 0xffffffff;
		k1 = (k1 << 15) | (k1 >>> 17);
		k1 = (((k1 & 0xffff) * c2) + ((((k1 >>> 16) * c2) & 0xffff) << 16)) & 0xffffffff;
		h1 ^= k1;
	}
```

- Uses fallthrough-behaviour which cannot be compiled by CoffeeScript

## CoffeeScript Bad Practices

```coffeescript
      return {} =
        context: {}
        events: []
```

->

```coffeescript
return {
  context: {}
  events: []
}
```

## Why operators are cool

**Without operator**

```javascript
42.toString(); // Uncaught SyntaxError: Unexpected token ILLEGAL

var number = 42;
number.toString(); // "42"
```

**With operator**

```javascript
42+''; // "42"
```

---

## Testing

### Behavior Driven Development

#### Test Runner

```coffeescript
describe 'Conversation Repository', ->
  conversation_repository = new z.conversation.ConversationRepository()

  describe 'handles member join correctly', ->
    beforeEach -> spyOn(conversation_repository, 'member_join').and.callThrough()
    member_join_event = id: 1421, status: 'cancelled'
      
    it 'evaluates a member join event if joining a group conversation', ->
      conversation_repository.on_conversation_event member_join_event
      expect(conversation_repository.member_join).toHaveBeenCalled()

    it 'ignores a member join event if joining a one-to-one conversation', ->
      connection = new z.connection.Connection()
      connection.conversation_id = conversation_repository.get_current().id
      connection.status z.connection.ConnectionStatus.PENDING
      conversation_repository.on_conversation_event member_join_event
      expect(conversation_repository.member_join).not.toHaveBeenCalled()

###
  Execution by Karma generates the following sentences:
  Chromium 37.0.2062 (Ubuntu 0.0.0) Conversation Repository handles member join correctly evaluates a member join event if joining a group conversation
  Chromium 37.0.2062 (Ubuntu 0.0.0) Conversation Repository handles member join correctly ignores a member join event if joining a one-to-one conversation
###
```

---

## Checklists

### Methodologies

- http://12factor.net/

### Tücken beim Laden von Daten aus einer Datenbank

- [ ] Datenzugriff wird verwehrt
- [ ] Daten sind nicht verfügbar
- [ ] Daten sind korrupt
- [ ] Daten sind veraltet
- [ ] Daten sind veraltet und entsprechen nicht dem aktuellen Datenbank-Schema
- [ ] Daten sind aktuelll aber entsprechen nicht dem aktuellen Datenbank-Schema

## Unicodes

### Emojis

- http://graphemica.com/%E2%98%BB
- 

Character: ✒
Chracter Name: Black Nib
Number (Decimal): 10002
Number (UTF-8): E29C92
Number (UTF-16): 2712
Unicode Code Point: U+2712 (U+2712)
HTML Entity (Decimal): &#10002;
HTML Entity (Hexadecimal): &#x2712;
URL Escape Code: %E2%9C%92
JavaScript String: "\u2712"
Emoji Shortcode: :black_nib: 

Character: 🤖
Chracter Name: Robot Face
Number (Decimal): 129302
Number (UTF-8): F09FA496
Number (UTF-16): D83EDD16
Unicode Code Point: U+1F916 (U+1F916)
HTML Entity (Decimal): &#129302;
HTML Entity (Hexadecimal): &#x1F916;
URL Escape Code: %F0%9F%A4%9
JavaScript String: -
Emoji Shortcode: - 

---

## Keys & Values

```coffee
window.z ?= {}
z.client ?= {}

z.client.ClientError =
  MISSING_ON_BACKEND: 'missing_on_backend'
  NO_LOCAL_CLIENT: 'no_local_client'
```

->

```coffee
window.z ?= {}
z.client ?= {}

z.client.ClientError =
  MISSING_ON_BACKEND: 'MISSING_ON_BACKEND'
  NO_LOCAL_CLIENT: 'NO_LOCAL_CLIENT'
```

->

```coffee
window.z ?= {}
z.client ?= {}

z.client.ClientError =
  MISSING_ON_BACKEND: 'z.client.ClientError.MISSING_ON_BACKEND'
  NO_LOCAL_CLIENT: 'z.client.ClientError.NO_LOCAL_CLIENT
```

---

## How to interrupt Promise chains

### Alternative 1: Create an unresolved Promsise

```coffee
      .then (local_identity) =>
        return {} if not local_identity
        if skip_sessions
          @logger.log 'Initialized repository and skipped loading of sessions and pre-keys'
          return new Promise => resolve @
        else
          deserialise_sessions = (buffer) -> Proteus.session.Session.deserialise local_identity, buffer
          return @_load_store @storage_service.OBJECT_STORE_SESSIONS, deserialise_sessions
      .then (sessions) =>
        @sessions = sessions
        @logger.log @logger.levels.INFO, "Loaded '#{Object.keys(@sessions).length}' sessions from database", @sessions
        return @_load_store @storage_service.OBJECT_STORE_PREKEYS, Proteus.keys.PreKey.deserialise
```

### Alternative 2: Throw a defined error

```coffee
```

### Alternative 3: reject

```coffee
```

### Alternative 3: Nested Promises

```coffee
```

## Functional operators in Java

**Without**

```java
for (Node node : nodes) {
  if (node instanceof HeaderNode) {
    HeaderNode headerNode = (HeaderNode) node;
    String text = getTextContent(node);
  }
}
```

**With Functional Operation**

```java
nodes.stream().filter((node) -> (node instanceof HeaderNode)).map((node) -> {
  HeaderNode headerNode = (HeaderNode) node;
  return node;
}).forEach((node) -> {
  String text = getTextContent(node);
});
```

### Best practices wisdoms

Always return Objects if possible (instead of Arrays). Later it will save you lots of time if you already have associative arrays.

### Testing Promises

```coffee
  beforeEach ->
    spyOn(client_service, 'get_clients_by_user_id').and.returnValue Promise.resolve(['a'])
```

```javascript`
spyOn(StoreService, 'listStores').and.callThrough();

spyOn(Contact, 'retrieveContactInfo').and.callFake(function() {
  return {
	then: function(callback) { return callback(user); }
  };
});

expect(StoreService.listStores).toHaveBeenCalled();
```

**Faking a Promise object**

```javascript
return {
  then: function(callback) { return callback(user); }
};
```

**IMPORTANT:** 

- `done` is needed to make the test async

```coffeescript
  describe '_get_clients_by_user_id', ->
    it 'does something', (done) ->
      payload_for_clients = [{
        "id": "1c6ecb6ab34d2f52",
        "class": "desktop",
        "meta": {"is_verified": false, "primary_key": "c31e8831-d6b7-4c8d-adb0-c169a6e0e625@1c6ecb6ab34d2f52"}
      }, {
        "id": "7cc2cd6b6a8a01ea",
        "class": "phone",
        "meta": {"is_verified": false, "primary_key": "c31e8831-d6b7-4c8d-adb0-c169a6e0e625@7cc2cd6b6a8a01ea"}
      }, {
        "id": "c11cbef4f403ac25",
        "class": "desktop",
        "meta": {"is_verified": false, "primary_key": "c31e8831-d6b7-4c8d-adb0-c169a6e0e625@c11cbef4f403ac25"}
      }]


      spyOn(client_service, 'get_clients_by_user_id').and.returnValue Promise.resolve(['a'])

      client_repository.get_clients_by_user_id entities.user.john_doe.id
      .then ->
        expect('A').toBe 'A'
        done()
      .catch (error) ->
        console.log 'ERROR'
        fail error
```

- Jasmine Spy Cheatsheet: http://tobyho.com/2011/12/15/jasmine-spy-cheatsheet/

### Fake server responses

```coffeescript
# Fake server
server = sinon.fakeServer.create()
server.autoRespond = true

url = "https://localhost:8888/some/url"
server.respondWith 'GET', url, [
  200
  'Content-Type': 'application/json'
  JSON.stringify ['faked', 'data']
]

afterEach -> server.restore()

describe '_get_clients_by_user_id', ->
  it 'does something', (done) ->
    client_repository.get_clients_by_user_id 42
    .then ->
      expect('A').toBe 'A'
      done()
    .catch (error) ->
      console.log 'ERROR'
      fail error
```

## CoffeeScript shortcuts

```coffeescript
client_ets = []

for client_payload in client_payloads
  client_ets.push @map_client client_payload
```

```coffeescript
return (@map_client client_payload for client_payload in client_payloads)
```

----------

## Object.freeze

- Schutz vor Überschreibung von Methoden

Build example with:

- https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor

----------

```coffeescript
current_client = ko.observable(new z.client.Client({id: z.util.create_random_uuid()}));
```

->

```coffeescript
client = new z.client.Client()
client.id = z.util.create_random_uuid()
current_client = ko.observable client
```

```coffescript
    for user_et in user_ets
      user_client_map[user_et.id] = [] if user_et.devices()[0]?
      for client_et in user_et.devices()
        user_client_map[user_et.id].push client_et.id
```

->

```coffeescript
    for user_et in user_ets when user_et.devices()[0]
      user_client_map[user_et.id] = (client_et.id for client_et in user_et.devices())
```

----------

## TypeScript Type Definitions

**typings.json**

```json
{
  "globalDependencies": {
    "jasmine": "registry:dt/jasmine#2.2.0+20160505161446"
  }
}
```

**Type Definitions Repository**
- https://github.com/DefinitelyTyped/DefinitelyTyped
- http://definitelytyped.org/
- https://github.com/typings/registry

**TypeScript Definition Manager**
- https://github.com/typings/typings

**Install Typings**
```bash
typings search --name react
typings install dt~mocha --global --save
// dt - typings from DefinitelyTyped
npm typings -- install dt~jasmine --save --global
typings install jquery --save --ambient
typings install github:DefinitelyTyped/DefinitelyTyped/angularjs/angular.d.ts#17ef40452039d19e06dc2a3815ea898c505860fa --ambient
```

## How to install Type Definitions

There are `globalDependencies` and `dependencies`.

Example: `typings install dt~dexie --save` or `typings install dt~dexie --save --global` (since Typings 1.0 `ambient` means also `global`).

```
typings install github:dfahlander/Dexie.js/blob/master/src/Dexie.d.ts
// ERR! caused by https://raw.githubusercontent.com/dfahlander/Dexie.js/master/blob/master/src/Dexie.d.ts responded with 404, expected it to equal 200
->
typings install github:dfahlander/Dexie.js/src/Dexie.d.ts
// badlocation "github:dfahlander/Dexie.js/src/Dexie.d.ts" is mutable and may change, consider specifying a commit hash
->
typings install github:dfahlander/Dexie.js/src/Dexie.d.ts#d271a952f2fed858bd6a25bfb2c4f5e8a55eaba3 --save --global
// Attempted to compile "Dexie" as a global module, but it looks like an external module.
->
typings install github:dfahlander/Dexie.js/src/Dexie.d.ts#d271a952f2fed858bd6a25bfb2c4f5e8a55eaba3 --save
// Woohooo! 
```

Generates:

**typings.json**
```json
{
  "dependencies": {
    "Dexie": "github:dfahlander/Dexie.js/src/Dexie.d.ts#d271a952f2fed858bd6a25bfb2c4f5e8a55eaba3"
  }
}
```

Reference Typing:

```typescript
/// <reference path="../../typings/modules/Dexie/index.d.ts" />
import {Dexie} from "Dexie";
```
 