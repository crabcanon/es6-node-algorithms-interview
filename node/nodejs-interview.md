### 1. Asynchronous operation

- `callback` function

> In NodeJS, if a function needs a callback function as a parameter, the callback function should be set as the last parameter. Besides, the first parameter of a callback function should be the `error` object.

```javascript
// Define a callback
var callback = function (error, value) {
  if (error) {
    throw error;
  }
  console.log(value);
}

try {
  db.User.get(userId, callback);
} catch(e) {
  console.log(e);
}
```

### 2. Global objects and variables

- Global objects

>
- `global`: the global env of Node, similar as `window` object in browsers, but has different behaviours. In browsers, `var x = 1` is equal to `window.x = 1`. In NodeJS modules, `var x = 1` is not a property of the global object because modules are private and other modules cannot access to this varaible.
- `process`: represents the current process, and is allowed to interact with developers.
- `console`: built-in Node module.

- Global functions

>
- `setTimeout()`: runs callback after a specific (ms). It will return a integer which stands for the id of this new timer.
- `clearTimeout()`: stops a setTimeout timer.
- `setInterval()`: runs callback after every specific interval. It will return a integer which stands for the id of this new timer.
- `clearInterval()`: stops a setInterval timer.
- `require()`: used for loading modules.
- `Buffer()`: used for manipulating binary data.

- Global variables

>
- `__filename`: indicates the name of current running script.
- `__dirname`: indicates the directory of current running script.

### 3. Modules

- import modules

```javascript
// 1st approach
var a = require('./a.js');

// 2nd approach
var a = require('./a');

// 3rd approach
var a = require('a');
```
> First and second ways are same. Regarding the third one, Node will try to find the `main` property of `package.json` file in the module package for a pre-defined entry path.

- Core modules

>
- http: provides all the http server functionalities.
- url: used to parse URL.
- fs: used to interact with File System.
- querystring: used to parse the query string of a URL.
- child_process: creates a new child process.
- util: a set of utilities.
- path: used to manipulate file paths.
- crypto: provides encryption and decryption functionalities.

- Custom modules

```javascript
// foo.js - exports a function
module.exports = function(x) {
  console.log(x);
};

// bar.js - exports an object
var out = new Object();

function p(x) {
  console.log(x);
}

out.print = p;

module.exports = out;

// index.js
var m1 = require('./foo');
var m2 = require('./bar');

m1('This is a custom module');
m2.print('This is a custom module');
```

### 4. Error handing

- `try...catch`(only for sync codes, and cannot catch error of async codes)

```javascript
// async
try {
  process.nextTick(function () {
    throw new Error("error");
  });
} catch (err) {
  //can not catch it
  console.log(err);
}

// async
try {
  setTimeout(function(){
    throw new Error("error");
  }, 1);
} catch (err) {
  //can not catch it
  console.log(err);
}

// solution
function async(cb, err) {
  setTimeout(function() {
    try {
      if (true)
        throw new Error("woops!");
      else
        cb("done");
    } catch(e) {
      err(e);
    }
  }, 2000)
}

async(function(res) {
  console.log("received:", res);
}, function(err) {
  console.log("Error: async threw an exception:", err);
});
// Error: async threw an exception: Error: woops!
```

- `callback` function

```javascript
function async(callback) {
  setTimeout(function() {
    try {
      var res = 42;
      if (true)
        throw new Error("woops!");
      else
        callback(null, res); // pass 'null' for error
    } catch(e) {
      callback(e, null);
    }
  }, 2000);
}

async(function(err, res) {
  if (err)
    console.log("Error: (cps) failed:", err);
  else
    console.log("(cps) received:", res);
});
// Error: (cps) failed: woops!
```

- `EventEmitter` - `error` event

```javascript
var EventEmitter = require('events').EventEmitter;
var emitter = new EventEmitter();

emitter.emit('error', new Error('Error'));
// If no subscriber for the error event, the system will crash.
emitter.on('error', function(err) {
  console.log('Error: ' + err.message);
});
```

- `uncaughtException` event

```javascript
var logger = require('tracer').console();

// When callback is set to uncaughtException,
// 1. Node will not exit the process.
// 2. Cannot provide detailed error info.
// 3. Might cause memory leak.
process.on('uncaughtException', function(err) {
  console.error('Error caught in uncaughtException event:', err);

  // A good practice
  logger.log(err);
  process.exit(1);
});

try {
  setTimeout(function() {
    throw new Error("error");
  }, 1);
} catch (err) {
  //can not catch it
  console.log(err);
}
```

- `unhandledRejection` event

> This event is used to catch an uncaught reject of a `Promise`. The `unhandledRejection` event has two parameters: `error` object(first), and `promise` object(second) which causes the error.

```javascript
var http = require('http');

http.createServer(function (req, res) {
  var promise = new Promise(function(resolve, reject) {
    reject(new Error("Broken."))
  })

  promise.info = {url: req.url}
}).listen(8080)

process.on('unhandledRejection', function (err, p) {
  if (p.info && p.info.url) {
    console.log('Error in URL', p.info.url)
  }
  console.error(err.stack)
});
```

### 5. EventEmitter

- Deploy the `EventEmitter` interface to an arbitrary object

```javascript
// The first way
var EventEmitter = require('events').EventEmitter;

function Dog(name) {
  this.name = name;
}

Dog.prototype.__proto__ = EventEmitter.prototype;
// or
// Dog.prototype = Object.create(EventEmitter.prototype);

var simon = new Dog('simon');

simon.on('bark', function () {
  console.log(this.name + ' barked');
});

setInterval(function () {
  simon.emit('bark');
}, 500);

// The second way
var util = require('util');
var EventEmitter = require('events').EventEmitter;

var Radio = function (station) {
  var self = this;

  setTimeout(function() {
    self.emit('open', station);
  }, 0);
  setTimeout(function() {
    self.emit('close', station);
  }, 5000);

  this.on('newListener', function(listener) {
    console.log('Event Listener: ' + listener);
  });
};

util.inherits(Radio, EventEmitter);
module.exports = Radio;

// Usage
var Radio = require('./radio.js');

var station = {
  freq: '80.16',
  name: 'Rock N Roll Radio',
};

var radio = new Radio(station);

radio.on('open', function(station) {
  console.log('"%s" FM %s open', station.name, station.freq);
});

radio.on('close', function(station) {
  console.log('"%s" FM %s close', station.name, station.freq);
});
```

- `EventEmitter` methods

>
- emitter.emit(name, arg1, arg2...): Synchronously calls each of the listeners registered for the event named `name`, in the order they were registered, passing the supplied arguments to each
- emitter.on(name, fn): specify `fn` listener for a event `name`.
- emitter.addListener(name, fn): alias for `on` method.
- emitter.once(name, fn): only listen once.
- emitter.listeners(name): returns an array, which contains all the listener functions of a event `name`.
- emitter.removeListener(name, fn): removes listener `fn` for an event `name`.
- emitter.removeAllListeners(name): removes all the listener functions for an event `name`.
