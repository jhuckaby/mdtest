# Hashes

Hello.

# Key Normalization

In order to maintain compatibility with all the various engines, keys are "normalized" on all entry points.  Specifically, they undergo the following transformations before being passed along to the engine:

* They are converted to lower-case.
* Only the following characters are allowed (everything else is stripped):
	* Alphanumerics
	* Dashes (hyphens)
	* Dots (periods)
	* Forward-slashes
* Duplicate adjacent slashes (i.e. "//") are converted to a single slash.
* Leading and trailing slashes are stripped.

So for example, this key:

```
" / / / // HELLO-KEY @*#&^$*@/#&^$(*@#&^$   test   / "
```

...is normalized to this:

```
"hello-key/test"
```

The same key normalization filter is applied when both storing and fetching records.

# Basic Functions

The storage module supports the following basic methods for typical operations.  Upon error, all callback methods are passed an `Error` object as the first argument.  If not, the first argument will be falsey (i.e. `false`, `0`, `null` or `undefined`), and the second argument will contain any requested data, if applicable.

The code examples all assume you have your preloaded `Storage` component instance in a local variable named `storage`.  The component instance can be retrieved from a running server like this:

```javascript
var storage = server.Storage;
```

# Section 7

Hello, this is section 7.

# Fetching Records

To fetch a record, call the [get()](#get) method.  Pass in a key, and a callback.  The data returned will be parsed back into an Object if JSON, or a raw `Buffer` object will be returned for binary records.

```javascript
storage.get( 'test1', function(err, data) {
	if (err) throw err;
} );
```

Some engines also allow you to "head" (i.e. ping) an object to retrieve some metadata about it, without fetching the value.  To do this, call the [head()](#head) method, and pass in the key.  The metadata usually consists of the size (`len`) and last modification date (`mod`).  Example:

```javascript
storage.head( 'test1', function(err, data) {
	if (err) throw err;
	// data.mod
	// data.len
} );
```

Note that the [Couchbase](#couchbase) engine does not support `head`, but the [Amazon S3](#amazon-s3) and [Local Filesystem](#local-filesystem) engines both do.

You can fetch multiple records at once by calling `getMulti()` and passing in array of keys.  Example:

```javascript
storage.getMulti( ['test1', 'test2', 'test3'], function(err, values) {
	if (err) throw err;
	// values[0] will be the test1 record.
	// values[1] will be the test2 record.
	// values[2] will be the test3 record.
} );
```
