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
ioContext.post(handler_function);
```

```cpp
ioContext.post([](){ /* code */ });
```

#### Run posted functions
 
```cpp
// blocking, run all functions until done
ioContext.run();
```

### IO Context as worker

#### Set ioContext always wait for tasks in queue

```cpp
// TODO -> executor_work_guard
// forever blocking
ioContext.run();
```

#### Run until time

```cpp
// TODO
ioContext.run_until();
```

### Sockets
{: .-prime}

```cpp

```

### SSL
{: .-prime}

`#include <asio/ssl.hpp>`

```cpp

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

ioContext.post([=]() {
  printf("Hello World");
})

// runs all posted functions
ioContext.run();
```

#### aysnc-iocontext.cpp
{: .-file}
