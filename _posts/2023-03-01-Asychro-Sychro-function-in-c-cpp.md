---
layout: post
title: Asychro-Sychro function in c/c++
categories: [IoT, C/C++]
description: different approache to implement asychro function in c/c++
keywords: coroutine, thread, callback
---

## General Summary

In this discussion, we explored different approaches for handling synchronous and asynchronous functions in C/C++. We discussed three main approaches: using multiple threads, setting timeouts, and using coroutines. We compared the efficiency and complexity of these approaches, and discussed the advantages and disadvantages of each.

We also talked about the difference between threads and processes in hardware level, and how they interact with the operating system to manage system resources.

## Asynchronous function and synchronous function

### Synchronous Functions: 
These functions immediately return the result when called and do not block the execution of the program. Examples of synchronous functions include basic arithmetic operations or simple logic statements.
code example:
```c
int add(int a, int b) {
    return a + b;
}
```

### Asynchronous Functions:
These functions take some time to complete and do not immediately return a result. These functions may perform I/O operations, network operations, or other tasks that require time to complete. Asynchronous functions are typically executed in a separate thread to avoid blocking the execution of the program. Examples of asynchronous functions include searching for WiFi or Bluetooth devices or making HTTP requests.
code example:
Searching for WiFi devices asynchronously:
```c
void searchWiFi() {
    // This function will be executed asynchronously in a separate thread
    while (true) {
        // Search for WiFi devices
        // ...
        // Sleep for 1 second
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }
}

// To call the searchWiFi function asynchronously:
std::thread t1(searchWiFi);
```

Making an HTTP request asynchronously using libcurl library:
```c
void httpCallback(char *data, size_t size, size_t nmemb, void *userdata) {
    // This function will be called when the HTTP response is received
    // ...
}

void makeHttpRequestAsync() {
    CURL *curl;
    CURLcode res;

    // Initialize curl
    curl = curl_easy_init();
    if (curl) {
        // Set the URL for the HTTP request
        curl_easy_setopt(curl, CURLOPT_URL, "https://example.com");

        // Set the callback function to handle the HTTP response
        curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, httpCallback);

        // Perform the HTTP request asynchronously
        curl_easy_setopt(curl, CURLOPT_NOSIGNAL, 1L);
        res = curl_easy_perform(curl);

        // Cleanup
        curl_easy_cleanup(curl);
    }
}

// To call the makeHttpRequestAsync function asynchronously:
std::thread t2(makeHttpRequestAsync);
```

## Using callback or coroutine to implement asynchronous function

### Callback
Callback functions are commonly used with asynchronous functions to handle the result when it becomes available. Here is an example of using a callback function with an asynchronous function:
```c
#include <iostream>
#include <functional>
#include <chrono>
#include <thread>

void asyncFunction(std::function<void(int)> callback) {
    // Simulate an asynchronous operation that takes some time to complete
    std::this_thread::sleep_for(std::chrono::seconds(1));
    
    // Call the callback function with the result
    callback(42);
}

void handleResult(int result) {
    std::cout << "Result: " << result << std::endl;
}

int main() {
    // Call the asynchronous function and pass the handleResult function as the callback
    asyncFunction(handleResult);

    // Wait for the asynchronous operation to complete
    std::this_thread::sleep_for(std::chrono::seconds(2));

    return 0;
}
```
Note that the callback function is called in the same thread as the asynchronous function. This means that the callback function will block the execution of the program until it is finished. If the callback function takes a long time to complete, it may block the execution of the program for a long time. This is why it is important to keep the callback function as simple as possible.

### Coroutine
Coroutines are a special type of function that can be paused and resumed. This allows the function to be executed in multiple steps. Here is an example of a coroutine function:
```c
#include <iostream>
#include <coroutine>
#include <chrono>
#include <thread>

struct asyncOperation {
    struct promise_type {
        int result;

        asyncOperation get_return_object() {
            return asyncOperation{std::coroutine_handle<promise_type>::from_promise(*this)};
        }

        std::suspend_always initial_suspend() noexcept {
            return {};
        }

        std::suspend_always final_suspend() noexcept {
            return {};
        }

        void return_value(int value) noexcept {
            result = value;
        }

        void unhandled_exception() noexcept {
            std::terminate();
        }
    };

    std::coroutine_handle<promise_type> coroutine;

    asyncOperation(std::coroutine_handle<promise_type> coroutine) : coroutine(coroutine) {}

    ~asyncOperation() {
        if (coroutine) coroutine.destroy();
    }

    int getResult() {
        return coroutine.promise().result;
    }

    bool await_ready() noexcept {
        return false;
    }

    void await_suspend(std::coroutine_handle<> handle) noexcept {
        std::thread([this, handle]() mutable {
            coroutine.resume();
            handle.resume();
        }).detach();
    }

    int await_resume() noexcept {
        return getResult();
    }
};

asyncOperation asyncFunction() {
    co_await std::suspend_always{};
    
    // Simulate an asynchronous operation that takes some time to complete
    std::this_thread::sleep_for(std::chrono::seconds(1));
    
    // Return the result
    co_return 42;
}

int main() {
    // Call the asynchronous function using a coroutine
    asyncOperation operation = asyncFunction();

    // Wait for the asynchronous operation to complete
    while (!operation.await_ready()) {}

    // Get the result
    int result = operation.await_resume();

    // Print the result
    std::cout << "Result: " << result << std::endl;

    return 0;
}
```
In this example, we define an asyncOperation struct that represents an asynchronous operation. This struct has a nested promise_type struct that provides the implementation for the coroutine. The promise_type struct has methods that allow us to create and destroy coroutine objects, as well as suspend and resume the coroutine.

The asyncOperation struct has methods for starting and resuming the coroutine, as well as waiting for the coroutine to complete and getting the result.

We define an asyncFunction function that performs an asynchronous operation and returns a result using a coroutine. The coroutine is suspended initially using co_await std::suspend_always{}. We then simulate an asynchronous operation using std::this_thread::sleep_for and return the result using co_return.

In the main function, we call the asyncFunction using a coroutine and get an asyncOperation object. We then wait for the coroutine to complete using a busy wait loop while (!operation.await_ready()) {}. Finally, we get the result of the coroutine using int result = operation.await_resume(); and print it to the console.

## Compare coroutine and multithread

The efficiency of coroutines depends on the specific use case and the performance characteristics of the code being executed. In general, coroutines are most efficient when:
1. There are many small tasks to be executed that can be completed quickly. In this case, the overhead of creating new threads and managing thread synchronization can outweigh the benefits of multi-threading.
2. The tasks are mostly I/O-bound, meaning that they spend most of their time waiting for I/O operations to complete. In this case, multi-threading may not provide much benefit, as the threads will spend most of their time waiting rather than executing code.

On the other hand, multi-threading can be more efficient when:
1. There are a few large tasks to be executed that require significant computational resources. In this case, multi-threading can allow the tasks to be executed in parallel, taking advantage of multiple CPU cores and reducing the overall execution time.
2. The tasks are mostly CPU-bound, meaning that they spend most of their time executing code rather than waiting for I/O operations to complete. In this case, multi-threading can allow the tasks to be executed in parallel, taking advantage of multiple CPU cores and reducing the overall execution time.







