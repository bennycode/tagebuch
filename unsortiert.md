
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

- https://github.com/SarahRoehricht/LMw94wasHhHQTWMH/commit/d4e1ea8f750846bcf0cccc5ff750f9b4fbcee00b

Character | Unicode
:---:|:---:
Ä | \u00c4
ä | \u00e4
Ö | \u00d6
ö | \u00f6
Ü | \u00dc
ü | \u00fc
ß | \u00df
