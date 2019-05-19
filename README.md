OpenFaaS Custom Templates
=============================================

The Custom templates that make use of the incubator project [of-watchdog](https://github.com/openfaas-incubator/of-watchdog).

Templates available in this repository:
- python3-sanic
- golang-mod

Notes:
- To build and deploy a function for Raspberry Pi or ARMv7 in general, use the language templates ending in *-armhf*

## Downloading the templates
```
$ faas template pull https://github.com/suryakencana007/openfaas-template
```

# Using the python3-sanic templates
Create a new function
```
$ faas new --lang python3-sanic <fn-name>
```
Build, push, and deploy
```
$ faas up -f <fn-name>.yml
```
Set your OpenFaaS gateway URL. For example:
```
$ OPENFAAS_URL=http://127.0.0.1:8080
```
Test the new function
```
$ curl -i $OPENFAAS_URL/function/<fn-name>
```

## Example usage
### Custom status codes and response bodies
Successful response status code and JSON response body
```python
async def handle(request):
    return json({
        "parsed": True,
        "url": request.url,
        "query_string": request.query_string,
        "args": request.args,
        "raw_args": request.raw_args,
        "query_args": request.query_args,
    })
```

# Using the golang-mod templates
Create a new function
```
$ faas new --lang golang-mod <fn-name>
```
Build, push, and deploy
```
$ faas up -f <fn-name>.yml
```
Set your OpenFaaS gateway URL. For example:
```
$ OPENFAAS_URL=http://127.0.0.1:8080
```
Test the new function
```
$ curl -i $OPENFAAS_URL/function/<fn-name>
```

## Example usage
### Custom status codes and response bodies
Successful response status code and JSON response body
```golang
func init() {
    log.ZapInit()
}

// Handle function from Main
func Handle(w http.ResponseWriter, r *http.Request) {
    var input []byte

    if r.Body != nil {
        defer r.Body.Close()

        body, _ := ioutil.ReadAll(r.Body)

        input = body
    }

    log.Info("Message Go~",
        log.Field("msg", fmt.Sprintf("Hello from DO! => %s", string(input))),
    )

    w.WriteHeader(http.StatusOK)
    w.Write([]byte(fmt.Sprintf("Hello world, input was: %s", string(input))))
}

```