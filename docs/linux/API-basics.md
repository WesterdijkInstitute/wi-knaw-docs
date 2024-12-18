# API and HTTP Basics

API (application programming interface) is a general term used for methods
for accessing a server/service in a programmable way (as opposed to clicking
through a web page which is an interactive user interface).

Most commonly ehat we mean by API is interacting with a server/service/website/database
using HTTP (hypertext transfer protocol) methods.

> Some notes collected from Wikipedia

In building applications, an API (application programming interface) simplifies programming by abstracting the underlying implementation and only exposing objects or actions the developer needs.

Web APIs are the defined interfaces through which interactions happen between an enterprise and applications that use its assets, which also is a Service Level Agreement (SLA) to specify the functional provider and expose the service path or URL for its API users. An API approach is an architectural approach that revolves around providing a program interface to a set of services to different applications serving different types of consumers.

## Tools for interacting with an API

You can use programs to send the requests or you can write a script or an interactive console that does the same.
For the later two options you need some programming experience. Using programs to access APIs is a good first step
both for learning how it works and also for testing when you want to incorporate the use of an API in one of your
scripts or programs.

### Common stand alone programs

- `wget`
    By default downloads to a file
- `curl`
    By default downloads and prints to STDOUT

## HTTP methods

Most of the time you will need GET which is also the default in most
cases when you do not explicitly define the method.

- GET
- POST
- DELETE
- PATCH
- PUT
- Other methods are not part of the basic usage

