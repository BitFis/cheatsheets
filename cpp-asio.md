---
title: CPP ASIO
category: cpp
updated: 2020-07-17
layout: 2017/sheet
tags: [WIP, c++, cpp, ASIO, boost]
prism_languages: [cpp,c++]
description: |
  A cheat sheet for ASIO without Boost
---

## Getting started
{: .-three-column}

### Introduction
{: .-intro}

`Cheatsheet expects the use of C++11 and later`

[ASIO](https://github.com/chriskohlhoff/asio/) Asio is a cross-platform C++ library for 
network and low-level I/O programming that provides developers with a consistent asynchronous 
model using a modern C++ approach.

- [ASIO Documentation](https://think-async.com/Asio) _think-async.com/Asio_

### Simple IO Context
{: .-prime}

```cpp
#include <asio.hpp>
```

#### Create IO Context

```cpp
asio::io_context ioContext;
```

#### Run Function

```cpp
asio::dispatch(ioContext, &handler_function)
```

#### Add Functions to the IO Context queue

```cpp
asio::post(ioContext, &handler_function);
```

```cpp
asio::post(ioContext, [](){ /* code */ });
```

#### Add object method (C++11)

```cpp
asio::post(
  ioContext,
  std::bind(&BaseClass::method, &instance)
);
```

#### Add timer function

```cpp
asio::steady_timer timer(
  ioContext,
  std::chrono::seconds(1)
);
timer.async_wait([&](auto...) {
  /* run after 1 second */
});
```

#### Run posted functions
 
```cpp
// blocking, run until all work is done
ioContext.run();
```

```cpp
// blocking until now + 1s or all work is done
ioContext.run_until(
  std::chrono::high_resolution_clock::now() + 
  std::chrono::seconds(1)
);
```

### Multithreading

#### Create strand

```cpp
asio::strand s1{ioContext.get_executor()};
asio::strand s2{ioContext.get_executor()};
```

#### Add strand command

```cpp
asio::post(s1, [](){ 
  /* 1: Code will not be interrupted by 2 */ 
});
asio::post(s1, [](){ 
  /* 2: Code will not be interrupted by 1 */ 
});
asio::post(s2, [](){ 
  /* Can interupt 1 and 2 */ 
});
```

### IO Context running in background

#### Run in context loop on other thread

```cpp
asio::io_context ioContext;
std::Thread t1{[](){ 
  ioContext.run(); 
}};
```

#### Stop ioContext 

#### Set ioContext always wait for tasks in queue

```cpp
asio::io_context::work _work{ioContext};
// Forever blocking
ioContext.run();
```

#### Blocking for 1 second

```cpp
ioContext.run_until(
  std::chrono::high_resolution_clock::now() + 
  std::chrono::seconds(1)
);
```

### Fork

// TODO

### Futures

// TODO

## Sockets
{: .-three-column}

### IO Sockets
{: .-prime}

```cpp
// TODO
```

### Buffers

#### Const Buffer

```cpp
asio::const_buffer("Hello\n", 6);
```

#### Dynamic Buffer

// TODO

#### Streams

// TODO

### IP Sockets
{: .-prime}

#### TCP Resolve

```cpp
using asio::ip::tcp;
auto endpoints = tcp::resolver{ioContext}
  .resolve("host", "port");
```

#### UDP Resolve

```cpp
using asio::ip::udp;
auto endpoints = udp::resolver{ioContext}
  .resolve("host", "port");
```

### Connect

```cpp
// TODO
```

### Bind

```cpp
// TODO
```

### Acceptors

```cpp
// TODO
```

#### create socket

```cpp
tcp::socket socket(ioContext);
```

```cpp
udp::socket socket(ioContext);
```

#### Connect

```cpp
// Todo
```

#### Connect async

```cpp
asio::async_connect(
  socket, endpoints,
  [this](
    const std::error_code& error,
    const tcp::endpoint& /*endpoint*/
  ) {
    /* Connected to endpoint */
  }
);
```

#### Write

```cpp
asio::write(sslSocket, buffer);
```

#### Write Async

```cpp
asio::async_write(sslSocket, buffer,
{}(auto..){
  // TODO
});
```

#### Read

```cpp
// TODO
```

#### Read Async

```cpp
// TODO
```

### SSL
{: .-prime}

`#include <asio/ssl.hpp>`

#### SSL Context

```cpp
// ::tlsv1 | ::tlsv1_1 | ::tlsv1_2 | ::tlsv1_3
asio::ssl::context 
  ctx(asio::ssl::context::tlsv1_2);
```

#### SSL socket

```cpp
asio::ssl::stream<tcp::socket> sslSocket{ioContext, ctx};
```

#### SSL connect

```cpp
asio::connect(sslSocket.lowest_layer(), endpoints);
```

#### SSL async connect

```cpp
asio::async_connect(
  sslSocket.lowest_layer(),
  endpoints,
  [this](
    const std::error_code& error,
    const tcp::endpoint& /*endpoint*/
  ) {
    /* connection done -> Handshake? */
  }
);
```

#### SSL handshake

#### SSL async handshake

#### Enable verify server

```cpp
ctx.load_verify_file("ca.pem");
```

## Co-routines
{: .-three-column}

Supported currently with (clang = c++17 | GNUC = c++20 | MSCV > 19.00-23506).

- [Should be integrated with C++20](https://en.cppreference.com/w/cpp/language/coroutines)

## Examples
{: .-three-column}

### IO Context
{: .-prime}

#### simple-iocontext.cpp
{: .-file}

```cpp
asio::io_context ioContext;

ioContext.post([]() {
  // Code ...
});

// Runs all posted functions
ioContext.run();
```

#### aysnc-iocontext.cpp
{: .-file}

```cpp
asio::io_context ioContext;
asio::io_context::work _work{ioContext};

// Start running context in other thread
std::thread t{[&ioContext](){ 
  ioContext.run(); 
}};

// Code ...

// Cleanup / stop
ioContext.stop();
t.join();
```

#### C++20 jthread

```cpp
asio::io_context ioContext;
asio::io_context::work _work{ioContext};

// auto join end of scope
std::jthread t{[&ioContext](){ 
  ioContext.run(); 
}};

// stop when ever (will run last task in queue)
ioContext.stop();
```