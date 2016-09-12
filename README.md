# Overview

This is README.

Here is an absolute link to API: [API Here](https://github.com/jhuckaby/mdtest/blob/master/docs/API.md)

Here is a relative link to API: [API rel](docs/API.md)

Here is an alternate relative link to API: [API rel alt](blob/master/docs/API.md)

# Usage

Use [npm](https://www.npmjs.com/) to install the module:

```
npm install pixl-server pixl-server-storage
```

Here is a simple usage example.  Note that the component's official name is `Storage`, so that is what you should use for the configuration key, and for gaining access to the component via your server object.

```javascript
var PixlServer = require('pixl-server');
var server = new PixlServer({
	
	__name: 'MyServer',
	__version: "1.0",
	
	config: {
		"log_dir": "/var/log",
		"debug_level": 9,
		
		"Storage": {
			"engine": "Filesystem",
			"Filesystem": {
				"base_dir": "/var/data/myserver",
			}
		}
	},
	
	components: [
		require('pixl-server-storage')
	]
	
});

server.startup( function() {
	// server startup complete
	var storage = server.Storage;
	
	// store key
	storage.put( 'test-key', { foo:"hello", bar:42 }, function(err) {
		if (err) throw err;
		
		// fetch key
		storage.get( 'test-key', function(err, data) {
			if (err) throw err;
			console.log(data);
		} );
	} );
} );
```

Notice how we are loading the [pixl-server](https://www.npmjs.com/package/pixl-server) parent module, and then specifying [pixl-server-storage](https://www.npmjs.com/package/pixl-server-storage) as a component:

```javascript
components: [
	require('pixl-server-storage')
]
```

This example is a very simple server configuration, which will start a local filesystem storage instance pointed at `/var/data/myserver` as a base directory.  It then simply stores a test key, then fetches it back.

# Section 8

Hallo, this is section 8.

# Amazon S3

If you want to use [Amazon S3](http://aws.amazon.com/s3/) as a backing store, here is how to do so.  First, you need to manually install the [aws-sdk](https://www.npmjs.com/package/aws-sdk) module into your app:

```
npm install aws-sdk
```

Then configure your storage thusly:

```javascript
{
	"engine": "S3",
	"AWS": {
		"accessKeyId": "YOUR_AMAZON_ACCESS_KEY", 
		"secretAccessKey": "YOUR_AMAZON_SECRET_KEY", 
		"region": "us-west-1" 
	},
	"S3": {
		"params": {
			"Bucket": "MY_S3_BUCKET_ID"
		}
	}
}
```

Replace `YOUR_AMAZON_ACCESS_KEY` and `YOUR_AMAZON_SECRET_KEY` with your Amazon Access Key and Secret Key, respectively.  These can be generated on the Security Credentials page.  Replace `MY_S3_BUCKET_ID` with the ID if your own S3 bucket.  Make sure you match up the region too.

The `AWS` object is passed directly to the `config.update()` function from the [aws-sdk](https://www.npmjs.com/package/aws-sdk) module, so you can also include any properties supported there.  See the [docs](http://docs.aws.amazon.com/AWSJavaScriptSDK/guide/node-configuring.html) for more details.

The `S3` object is passed directly to the S3 class constructor, so check the [docs](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#constructor-property) to see what other properties are supported.

If you plan on using Amazon AWS in other parts of you application, you can actually move the `AWS` config object into your outer server configuration.  The storage module will look for it there.
