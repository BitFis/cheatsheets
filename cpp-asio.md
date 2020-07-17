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

#### Add Functions to the IO Context

```cpp
asio::post(ioContext, handler_function);
```

```cpp
asio::post(ioContext, [](){ /* code */ });
```

#### Add timer function

```cpp
asio::steady_timer timer(service, std::chrono::seconds(1));
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
// blocking, run until now + 1s
ioContext.run_until(/* TODO */);
```

```cpp
// blocking, run until now + 1s
ioContext.run_until(/* TODO */);
```

### Multithreading

#### Create strand

```cpp
asio::strand s1{ioContext.get_executor()};
asio::strand s2{ioContext.get_executor()};
```

#### add strand command

```cpp
asio::post(s1, [](){ /* 1: Code will not be interrupted by 2 */ });
asio::post(s1, [](){ /* 2: Code will not be interrupted by 1 */ });
asio::post(s2, [](){ /* Can interupt 1 and 2 */ });
```

### IO Context running in background

#### Run in context loop on other thread

```cpp
asio::io_context ioContext;
std::Thread t1{[](){ ioContext.run(); }};
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

### Sockets
{: .-prime}

```cpp
// TODO
```

### SSL
{: .-prime}

`#include <asio/ssl.hpp>`

```cpp
// TODO
```

## C++17
{: .-three-column}

ASIO without Boost using c++ built in coroutines.

TODO

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
std::thread t{[&ioContext](){ ioContext.run(); }};

// Code ...

// Cleanup / stop
ioContext.stop();
t.join();
```
