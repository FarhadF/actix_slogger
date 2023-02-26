# SlogLogger Middleware

The `SlogLogger` middleware is a logging middleware for the Actix web framework that logs incoming HTTP requests with the Slog logging framework.

## How to use

To use the `SlogLogger` middleware in your Actix web application, you can add it to the middleware stack in your server configuration as follows:


```rust

use actix_web::{App, HttpServer};
use slog::{o, Drain};
use slog_async::Async;

let logger = slog_scope::logger().new(o!("version" => env!("CARGO_PKG_VERSION")));
let drain = slog_json::Json::default(std::io::stdout());
let async_drain = Async::new(drain).build().fuse();
let middleware = SlogLogger::new(logger.clone());

HttpServer::new(|| {
    App::new()
        .wrap(middleware)
        .service(...)
})
.bind("127.0.0.1:8080")?
.run()?;
```

This will log incoming HTTP requests using the slog-json formatter to format the log entries as JSON objects, and output them to stdout. You can customize the logger and formatter as desired by modifying the logger and drain variables.
Implementation details

The SlogLogger middleware is implemented as a Transform and Service in the Actix web framework. The Transform is responsible for creating an instance of the middleware, while the Service is responsible for processing incoming HTTP requests and generating log entries.

When the middleware is created, it creates a new SlogLoggerMiddleware instance that wraps the original service and the logger. When an incoming HTTP request is processed, the middleware records the start time of the request and passes the request to the original service. When the response is returned, the middleware calculates the elapsed time of the request and generates a log entry using the Slog logging framework.

The log entry includes the following fields:

    latency: the elapsed time of the request, in milliseconds.
    method: the HTTP method of the request.
    path: the path of the request.
    status: the HTTP status code of the response.
    any HTTP headers included in the request.

##Dependencies

The SlogLogger middleware depends on the following libraries:

    actix-web: a powerful, pragmatic, and extremely fast web framework for Rust.
    futures-util: a collection of utilities for working with futures in Rust.
    slog: a structured logging framework for Rust. dynamic-keys feature must be enabled.
