MetaDisk Client
===============

A Simple HTTP client for communicating with the MetaDisk API.

Quick Start
-----------

Install the MetaDisk client tools using NPM:

```
npm install metadisk-client --save
```

Optionally build a browser bundle:

```
npm run build
```

Examples
--------

Register a user account on MetaDisk:

```
// Import the client
var metadisk = require('metadisk-client');

// Create unauthenticated instance
var client = new metadisk.Client('https://api.metadisk.org');

// Register a user account
client.createUser('you@domain.tld', 'somebigsecret').then(function(result) {
  console.log(result);
}, function(err) {
  console.log(err);
});
```

Create a storage bucket for your user:

```
// Import the client
var metadisk = require('metadisk-client');

// Authenticate with credentials
var client = new metadisk.Client('https://api.metadisk.org', {
  basicauth: {
    email: 'you@domain.tld',
    password: 'somebigsecret'
  }
});

// Create a bucket and print the result
client.createBucket().then(function(result) {
  console.log(result);
}, function(err) {
  console.log(err);
});
```

Store and retrieve a file from the Storj network:

```
var fs = require('fs');

// Import the library
var metadisk = require('metadisk-client');

// Create a client authenticated with your key
var client = new metadisk.Client('https://api.metadisk.org', {
  keypair: new metadisk.KeyPair('<your_private_ecdsa_key>')
});

// Keep track of the bucket ID and file hash
var bucket = '56d6048ab3dece1959aace73';
var filehash = null;

// Create a PUSH token
client.createToken(bucket, 'PUSH').then(function(token) {
  // Stream the file upload to metadisk
  return client.storeFileInBucket(bucket, token.token, process.argv[2]);
}).then(function(filepointer) {
  // Track the file hash for later
  filehash = filepointer.hash;
  // Create a PULL token
  return client.createToken(bucket, 'PULL');
}).then(function(token) {
  // Fetch the file pointer list
  return client.getFilePointer(bucket, token.token, filehash);
}).then(function(pointers) {
  // Open download stream from network and a writable file stream
  var download = client.resolveFileFromPointers(pointers);
  var destination = fs.createWriteStream('<write_file_to_path>');
  // Write downloaded file to disk
  download.pipe(destination);
});
```

License
-------

MetaDisk Client -  Copyright (C) 2016 Storj Labs, Inc

This program is free software: you can redistribute it and/or modify it under
the terms of the GNU Lesser General Public License as published by the Free
Software Foundation, either version 3 of the License, or (at your option) any
later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE. See the GNU Lesser General Public License for more details.

You should have received a copy of the GNU Lesser General Public License along
with this program. If not, see http://www.gnu.org/licenses/.
