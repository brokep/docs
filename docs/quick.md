---
id: quick
title: Quick Start
sidebar_label: Quick Start
---

## Prerequisites

For the quick start the only thing you need is [Docker](https://docs.docker.com/install/).

## Try it now!

Create a directory for experiments with Vaulty. In the directory, let's create simple routes.json file with transformation rule:

```json
{
  "routes":[
    {
      "name":"in",
      "method":"POST",
      "url":"/post",
      "upstream":"https://postman-echo.com",
      "request_transformations":[
        {
          "type":"json",
          "expression":"card.number",
          "action":{
            "type":"encrypt"
          }
        }
      ]
    }
  ]
}
```

In short, Vaulty will encrypt `card.number` element of JSON body of all POST requests with /post path and then send it [http://postman-echo.com](http://postman-echo.com) (postman-echo is echo server; it will return all data it receives).

Now, let's run Vaulty as a proxy:

```bash
docker run -p 8080:8080 -v ${PWD}:/vaulty/.vaulty/ vaulty/vaulty
```

You should see something like this:

```
==> Vaulty proxy server started on port 8080!
```

Let's make a request with card number to Vaulty:

```bash
curl http://127.0.0.1:8080/post \
  -d '{ "card": { "number": "4242424242424242", "exp": "10/22" } }' \
  -H "Content-Type: application/json"
```

In postman-echo response you can see that it received an encrypted card.number instead of the plain value of our original request.

```
{"args":{},"data":{"card":{"number":"NDI0MjQyNDI0MjQyNDI0Mg(demo encryption)","exp":"10/22"}},"files":{},"form":{},"headers":{"x-forwarded-proto":"https","x-forwarded-port":"443","host":"127.0.0.1","x-amzn-trace-id":"Root=1-5ec1412f-6ab8d3f28110822b8a425e81","content-length":"83","user-agent":"curl/7.64.1","accept":"*/*","content-type":"application/json","accept-encoding":"gzip"},"json":{"card":{"number":"NDI0MjQyNDI0MjQyNDI0Mg(demo encryption)","exp":"10/22"}},"url":"https://127.0.0.1/post"}%
```

That's it for the quick start!
