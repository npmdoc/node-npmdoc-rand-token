# api documentation for  [rand-token (v0.3.0)](https://github.com/sehrope/node-rand-token#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-rand-token.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-rand-token) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-rand-token.svg)](https://travis-ci.org/npmdoc/node-npmdoc-rand-token)
#### Generate random tokens

[![NPM](https://nodei.co/npm/rand-token.png?downloads=true)](https://www.npmjs.com/package/rand-token)

[![apidoc](https://npmdoc.github.io/node-npmdoc-rand-token/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-rand-token_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-rand-token/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-rand-token/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-rand-token/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Sehrope Sarkuni",
        "email": "sehrope@jackdb.com"
    },
    "bugs": {
        "url": "https://github.com/sehrope/node-rand-token/issues"
    },
    "dependencies": {},
    "description": "Generate random tokens",
    "devDependencies": {
        "jshint": "1.1.0",
        "lodash": "~2.4.1",
        "mocha": "~3"
    },
    "directories": {},
    "dist": {
        "shasum": "32c0e672200867766a13601a21cef98ac67478c7",
        "tarball": "https://registry.npmjs.org/rand-token/-/rand-token-0.3.0.tgz"
    },
    "engines": {
        "node": ">= 0.8.0"
    },
    "gitHead": "438d62d910790d262b13ec019a957083f833877e",
    "homepage": "https://github.com/sehrope/node-rand-token#readme",
    "keywords": [
        "random",
        "uid",
        "token",
        "psuedorandom",
        "urandom",
        "crypto",
        "alpha-numeric"
    ],
    "license": "MIT",
    "main": "index.js",
    "maintainers": [
        {
            "name": "sehrope",
            "email": "sehrope@jackdb.com"
        }
    ],
    "name": "rand-token",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/sehrope/node-rand-token.git"
    },
    "scripts": {
        "test": "make test"
    },
    "version": "0.3.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module rand-token](#apidoc.module.rand-token)
1.  [function <span class="apidocSignatureSpan">rand-token.</span>generate (size, chars)](#apidoc.element.rand-token.generate)
1.  [function <span class="apidocSignatureSpan">rand-token.</span>generator (options)](#apidoc.element.rand-token.generator)
1.  [function <span class="apidocSignatureSpan">rand-token.</span>suid (length, epoch, prefixLength)](#apidoc.element.rand-token.suid)
1.  [function <span class="apidocSignatureSpan">rand-token.</span>uid (size, chars)](#apidoc.element.rand-token.uid)



# <a name="apidoc.module.rand-token"></a>[module rand-token](#apidoc.module.rand-token)

#### <a name="apidoc.element.rand-token.generate"></a>[function <span class="apidocSignatureSpan">rand-token.</span>generate (size, chars)](#apidoc.element.rand-token.generate)
- description and source-code
```javascript
generate = function (size, chars) {
  if( chars ) {
    validateTokenChars(chars);
  } else {
    chars = options.chars;
  }
  var max = Math.floor(256 / chars.length) * chars.length;
  var ret = "";
  while( ret.length < size ) {
    var buf = options.source(size - ret.length);
    for(var i=0;i<buf.length;i++) {
      var x = buf.readUInt8(i);
      if( x < max ) {
        ret += chars[x % chars.length];
      }
    }
  }
  return ret;
}
```
- example usage
```shell
...

# Usage

// Create a token generator with the default settings:
var randtoken = require('rand-token');

// Generate a 16 character alpha-numeric token:
var token = randtoken.generate(16);

// Use it as a replacement for uid:
var uid = require('rand-token').uid;
var token = uid(16);

// Generate mostly sequential tokens:
var suid = require('rand-token').suid;
...
```

#### <a name="apidoc.element.rand-token.generator"></a>[function <span class="apidocSignatureSpan">rand-token.</span>generator (options)](#apidoc.element.rand-token.generator)
- description and source-code
```javascript
function buildGenerator(options) {
  assert(!options || typeof(options) == 'object');
  options = options || {};
  options.chars = options.chars || defaults.chars;
  options.source = options.source || defaults.source;

  // Allowed characters
  switch( options.chars ) {
    case 'default':
      options.chars = alphaNumeric;
      break;
    case 'a-z':
    case 'alpha':
      options.chars = alphaLower;
      break;
    case 'A-Z':
    case 'ALPHA':
      options.chars = alphaUpper;
      break;
    case '0-9':
    case 'numeric':
      options.chars = numeric;
      break;
    case 'base32':
      options.chars = alphaUpper + "234567";
      break;
    default:
      // use the characters as is
  }
  validateTokenChars(options.chars);

  // Source of randomness:
  switch( options.source ) {
    case 'default':
      options.source = function(size, cb) {
        return cryptoRandomBytes(size, !cb ? null : function (buf){
          return cb(null, buf);
        });
      };
      break;
    case 'crypto':
      options.source = crypto.randomBytes;
      break;
    case 'math':
      options.source = function(size, cb) {
        var buf = new Buffer(size);
        for(var i=0;i<size;i++) {
          buf.writeUInt8(Math.floor(256 * Math.random()), i);
        }
        if( cb ) {
          cb(null, buf);
        } else {
          return buf;
        }
      };
      break;
    default:
      assert(typeof(options.source) == 'function');
  }

  return {
    "generate": function(size, chars) {
      if( chars ) {
        validateTokenChars(chars);
      } else {
        chars = options.chars;
      }
      var max = Math.floor(256 / chars.length) * chars.length;
      var ret = "";
      while( ret.length < size ) {
        var buf = options.source(size - ret.length);
        for(var i=0;i<buf.length;i++) {
          var x = buf.readUInt8(i);
          if( x < max ) {
            ret += chars[x % chars.length];
          }
        }
      }
      return ret;
    }
  };
}
```
- example usage
```shell
...

    // Generate a 24 (16 + 8) character alpha-numeric token:
    var token = suid(16)

To generate only lower case letters (a-z):

    // Create a token generator with the default settings:
    var randtoken = require('rand-token').generator({
      chars: 'a-z'
    });

    // Generate a 16 character token:
    var token = randtoken.generate(16);

Alternatively, you can create a generator with the default options and pass the characters to use as the second parameter to 'generate
':
...
```

#### <a name="apidoc.element.rand-token.suid"></a>[function <span class="apidocSignatureSpan">rand-token.</span>suid (length, epoch, prefixLength)](#apidoc.element.rand-token.suid)
- description and source-code
```javascript
suid = function (length, epoch, prefixLength) {
  epoch = epoch || defaultEpoch;
  prefixLength = prefixLength || defaultPrefixLength;
  return suidPrefix(epoch, prefixLength) + defaultGenerator.generate(length);
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.rand-token.uid"></a>[function <span class="apidocSignatureSpan">rand-token.</span>uid (size, chars)](#apidoc.element.rand-token.uid)
- description and source-code
```javascript
uid = function (size, chars) {
  if( chars ) {
    validateTokenChars(chars);
  } else {
    chars = options.chars;
  }
  var max = Math.floor(256 / chars.length) * chars.length;
  var ret = "";
  while( ret.length < size ) {
    var buf = options.source(size - ret.length);
    for(var i=0;i<buf.length;i++) {
      var x = buf.readUInt8(i);
      if( x < max ) {
        ret += chars[x % chars.length];
      }
    }
  }
  return ret;
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
