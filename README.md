# waPC JavaScript Host (Node + Browser)

This is the JavaScript implementation of the **waPC** standard for WebAssembly host runtimes. It allows any WebAssembly module to be loaded as a guest and receive requests for invocation as well as to make its own function requests of the host.

This package defines the protocol for RPC exchange between guest (WebAssembly) modules and the host runtime.

## Who is this for?

Library developers who want to load waPC-compliant WebAssembly binaries in nodejs or browser environments. The waPC protocol allows a host to execute arbitrary wasm functions without knowing the exported bindings beforehand.

While waPC does not prescribe a serialization/deserialization format, MessagePack is common recommended unless you need otherwise.

## Installation

```sh
$ npm install @wapc/host
```

## Usage

**Node**

```js
import { instantiate } from '@wapc/host';
import { encode, decode } from '@msgpack/msgpack';

// Read wasm off the local disk as Uint8Array
buffer = fs.readFileSync(path.join(__dirname, 'fixtures', 'rust_echo_string.wasm'));

// Instantiate a WapcHost with the Uint8Array
const host = await instantiate(buffer);

// Encode the arguments to send to the wasm function (Example using msgpack)
const payload = encode({ msg: 'hello world' });

// Invoke the "echo" function in the wasm guest
const result = await host.invoke('echo', payload);

// Decode the results (using msgpack in this example)
const decoded = decode(result);
```

**Browser**

Browser environments can instantiate from a FetchResponse with `waPC.instantiateStreaming()`.

```html
<script src="https://unpkg.com/@msgpack/msgpack@2.5.1/dist.es5+umd/msgpack.min.js"></script>
<script src="dist/index.bundle.js"></script>
<script>
  (async () => {
    // Fetch wasm bytes
    response = await fetch('/test/fixtures/rust_echo_string.wasm');

    // Instantiate a WapcHost with fetch response object
    const host = await waPC.instantiateStreaming(response);

    // Encode the arguments to send to the wasm function (Example using msgpack)
    const payload = MessagePack.encode({ msg: 'hello world' });

    // Invoke the "echo" function in the wasm guest
    const result = await host.invoke('echo', payload);

    // Decode the results (using msgpack in this example)
    const decoded = MessagePack.decode(result);
  })();
</script>
```

## Contributing

### Running tests

**Node unit tests**

```sh
$ npm run test:unit
```

**Browser unit tests**

```sh
$ npm run test:browser
```

### Building

```sh
$ npm run build
```
