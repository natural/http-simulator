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

	$ http -vv --json GET :8000/path/to/test X-Simulator-Set:status=302 X-Location:/
	GET /path/to/test HTTP/1.1
	Accept: application/json, */*
	Accept-Encoding: gzip, deflate
	Connection: keep-alive
	Content-Type: application/json
	Host: localhost:8000
	User-Agent: HTTPie/0.9.9
	X-Location: /
	X-Simulator-Set: status=302

	HTTP/1.0 205 Reset Content
	Date: Thu, 05 Oct 2017 19:51:45 GMT
	Server: BaseHTTP/0.6 Python/3.6.2


After a URL is set, you can make requests to it (without the
`X-Simulator-Set` header):

	$ http -vv GET :8000/path/to/test


## Details

Here be baby dragons.
