# http-simulator

Simulate HTTP responses with HTTP requests.

## Usage

For one-shot execution, e.g., in a container:

	$ curl https://raw.githubusercontent.com/natural/http-simulator/master/http-simulator | python3


For local installation:

	$ curl -Lo http-simulator https://raw.githubusercontent.com/natural/http-simulator/master/http-simulator && chmod +x http-simulator && sudo mv http-simulator /usr/local/bin/
	$ http-simulator

Once the server is running, you configure the URLs it serves by
sending it requests with the `X-Simulator-Set` header.  For example:

    $ http GET :8000/path/to/test X-Simulator-Set:status=302 Location:/some/other/path/to/check
    HTTP/1.0 205 Reset Content
    Date: Thu, 05 Oct 2017 20:55:28 GMT
    Server: BaseHTTP/0.6 Python/3.6.2

After a URL is set, you can make requests to it (without the x-simulator headers):

    $ http GET :8000/path/to/test
    HTTP/1.0 302 Found
    Date: Thu, 05 Oct 2017 20:55:39 GMT
    Location: /some/other/path/to/check
    Server: BaseHTTP/0.6 Python/3.6.2	


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

* `headers`: value one of `all`, `default` or `xs`; default is `default`
  * `all`: all request headers are saved
  * `default`: only `Content-Type`, `Content-Length`, and `Location` headers are saved
  * `xs`: only extension headers (`X-*`) are saved 


### Future Work

* match by query string isn't yet supported
* match by header isn't, either
* save individual header, e.g., `X-Simulator-Set: status=401,header=Date`
