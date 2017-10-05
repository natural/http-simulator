# http-simulator

Simulate HTTP responses with HTTP requests.


## Synopsis

```sh
$ # start the simulator
$ http-simulator &

# tell it what to serve
$ http --json GET :8000/breakfast X-Simulator-Set:status=415 eggs=green ham=spam
HTTP/1.0 205 Reset Content
...

# make http requests
$ http GET :8000/breakfast
HTTP/1.0 415 Unsupported Media Type
Content-Type: application/json
...
{
	"eggs": "green",
	"ham": "spam"
}
```

## Usage

```
usage: http-simulator [-h] [-b BIND] [-d DB] [-q] [-r] [-w]

Serve HTTP responses simulated with HTTP requests.

optional arguments:
  -h, --help            show this help message and exit
  -b BIND, --bind BIND  bind to host:port (default: *:8000)
  -d DB, --database DB  sqlite3 database filename (default: :memory:)
  -q, --quiet           quiet output (default: False)
  -r, --read-only       do not write to the database (default: False)
  -w, --watch           watch the script for changes and restart (default: False)
```

## Install

For one-shot execution, e.g., in a container:

```sh
$ curl https://raw.githubusercontent.com/natural/http-simulator/master/http-simulator | python3
```

For local installation:

```sh
$ curl -Lo http-simulator https://raw.githubusercontent.com/natural/http-simulator/master/http-simulator && chmod +x http-simulator && sudo mv http-simulator /usr/local/bin/
$ http-simulator
```

## Operation

Once the server is running, you configure the URLs it serves by
sending it requests with the `X-Simulator-Set` header.  For example:

```sh
$ http GET :8000/path/to/test X-Simulator-Set:status=302 Location:/some/other/path/to/check
HTTP/1.0 205 Reset Content
Date: Thu, 05 Oct 2017 20:55:28 GMT
Server: BaseHTTP/0.6 Python/3.6.2
```

After a URL is set, you can make requests to it (without the x-simulator headers):

```sh
$ http GET :8000/path/to/test
HTTP/1.0 302 Found
Date: Thu, 05 Oct 2017 20:55:39 GMT
Location: /some/other/path/to/check
Server: BaseHTTP/0.6 Python/3.6.2
```

## Details

The server responds to requests as follows:

1.  If the request contains a `X-Simulator-Set` header, update the
	routing table with the contents of the header, and then respond with `205 Reset Content`.

2.  Otherwise query the routing table with the HTTP request method,
	path (and in the future, query string).  Respond with the status,
	headers, and body from the routing table.

3.  Otherwise respond with `404 Not Found`

### The `X-Simulator-Set` Header

When the `X-Simulator-Set` should contain key-value pairs, separated via `=`.  Recognized keys:

* `status`: value used as the response status code; default `200`

* `headers`: name of header to copy, or name of known preset; default
  is preset `default`.  May be repeated, and if so, results are
  cumulative.  Presets:

  * `all`: all request headers are saved
  * `all-nox`: all non-extension (`X-*`) headers are saved
  * `default`: only `Content-Type`, `Content-Length`, and `Location` headers are saved
  * `xs`: only extension headers (`X-*`) are saved


### Future Work

* needs a test
* match by query string isn't yet supported
* map method ANY to create routes for GET, PUT, POST, etc.
