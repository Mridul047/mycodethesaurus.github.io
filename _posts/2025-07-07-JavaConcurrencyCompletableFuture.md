---
title: "Java Concurrency"
date: 2025-07-07 18:00
categories: [java, core library, concurrency]
tags: [java, core library, concurrency]
---

# Mastering Java Concurrency using CompletableFuture & ExecutorService in Java 21

## Introduction

Modern applications demand efficient handling of concurrent operations to maximize performance and responsiveness. Java has continuously evolved its concurrency APIs since their introduction in Java 5, with significant enhancements in Java 8 (CompletableFuture), Java 9 (reactive streams), and further refinements in subsequent releases including Java 21.

This article provides a comprehensive exploration of two cornerstone components of Java's concurrency framework: `ExecutorService` and `CompletableFuture`. We'll examine their capabilities, interactions, and best practices, with a particular focus on features available in Java 21.

## ExecutorService: The Foundation of Java Concurrency

### Understanding ExecutorService

`ExecutorService` is an interface that extends the `Executor` interface, providing methods to manage termination and methods that can produce a `Future` for tracking progress of one or more asynchronous tasks.

```java
public interface ExecutorService extends Executor {
    void shutdown();
    List<Runnable> shutdownNow();
    boolean isShutdown();
    boolean isTerminated();
    boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;
    <T> Future<T> submit(Callable<T> task);
    <T> Future<T> submit(Runnable task, T result);
    Future<?> submit(Runnable task);
    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException;
    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException;
    <T> T invokeAny(Collection<? extends Callable<T>> tasks) throws InterruptedException, ExecutionException;
    <T> T invokeAny(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException;
}
```

### Types of ExecutorService Implementations

Java provides several implementations of `ExecutorService` through the `Executors` factory class:

#### 1. Fixed Thread Pool

```java
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(4);
```

A fixed thread pool creates a specified number of threads that remain in the pool throughout its lifetime. If all threads are busy and additional tasks are submitted, they wait in a queue until a thread becomes available.

#### 2. Cached Thread Pool

```java
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
```

A cached thread pool creates new threads as needed but reuses previously constructed threads when available. Threads that remain idle for 60 seconds are terminated and removed from the pool.

#### 3. Single Thread Executor

```java
ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
```

A single thread executor uses a single worker thread to execute tasks from an unbounded queue. This guarantees that tasks are executed sequentially in the order they were submitted.

#### 4. Scheduled Thread Pool

```java
ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(4);
```

A scheduled thread pool can schedule tasks to run after a given delay or to execute periodically.

#### 5. Work Stealing Pool (ForkJoinPool)

```java
ExecutorService workStealingPool = Executors.newWorkStealingPool();
```

Introduced in Java 8, this creates a work-stealing thread pool using all available processors as its target parallelism level. It's particularly effective for tasks that spawn subtasks.

#### 6. Virtual Thread Per Task Executor (Java 21)

```java
ExecutorService virtualThreadExecutor = Executors.newVirtualThreadPerTaskExecutor();
```

Introduced in Java 21 as part of Project Loom, this executor creates a new virtual thread for each task. Virtual threads are lightweight threads that are managed by the JVM rather than the operating system, allowing for a much larger number of concurrent tasks.

### Basic Usage of ExecutorService

Here's a simple example demonstrating how to use an `ExecutorService`:

```java
import java.util.concurrent.*;

public class ExecutorServiceExample {
    public static void main(String[] args) {
        // Create a fixed thread pool with 5 threads
        ExecutorService executor = Executors.newFixedThreadPool(5);
        
        try {
            // Submit 10 tasks for execution
            for (int i = 0; i < 10; i++) {
                final int taskId = i;
                executor.submit(() -> {
                    System.out.println("Task " + taskId + " is running on thread " + 
                                      Thread.currentThread().getName());
                    try {
                        // Simulate some work
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                    }
                    System.out.println("Task " + taskId + " completed");
                    return taskId;
                });
            }
        } finally {
            // Initiate an orderly shutdown
            executor.shutdown();
            
            try {
                // Wait for all tasks to complete or timeout after 60 seconds
                if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                    executor.shutdownNow(); // Force shutdown if tasks don't complete in time
                }
            } catch (InterruptedException e) {
                executor.shutdownNow();
                Thread.currentThread().interrupt();
            }
        }
        
        System.out.println("All tasks submitted");
    }
}
```

### Advanced ExecutorService Features

#### Custom Thread Factory

You can customize thread creation by providing a `ThreadFactory`:

```java
ThreadFactory threadFactory = r -> {
    Thread t = new Thread(r);
    t.setDaemon(true);
    t.setPriority(Thread.MIN_PRIORITY);
    t.setName("CustomWorker-" + t.getId());
    return t;
};

ExecutorService executor = Executors.newFixedThreadPool(5, threadFactory);
```

#### Custom ThreadPoolExecutor

For more control, you can directly create a `ThreadPoolExecutor`:

```java
int corePoolSize = 5;
int maxPoolSize = 10;
long keepAliveTime = 60L;
TimeUnit unit = TimeUnit.SECONDS;
BlockingQueue<Runnable> workQueue = new LinkedBlockingQueue<>(100);
ThreadFactory threadFactory = Executors.defaultThreadFactory();
RejectedExecutionHandler handler = new ThreadPoolExecutor.CallerRunsPolicy();

ThreadPoolExecutor executor = new ThreadPoolExecutor(
    corePoolSize, maxPoolSize, keepAliveTime, unit, 
    workQueue, threadFactory, handler);
```

This gives you fine-grained control over:
- Core and maximum pool sizes
- Keep-alive times for excess idle threads
- Work queue type and capacity
- Thread factory
- Rejected execution handler policy

#### Rejection Policies

When a thread pool is saturated, it needs a strategy to handle new task submissions:

1. **AbortPolicy** (default): Throws `RejectedExecutionException`
2. **CallerRunsPolicy**: Executes the task in the caller's thread
3. **DiscardPolicy**: Silently discards the task
4. **DiscardOldestPolicy**: Discards the oldest unhandled task and tries again

```java
// Example with custom rejection policy
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    2, 2, 0L, TimeUnit.MILLISECONDS,
    new LinkedBlockingQueue<>(1),
    new ThreadPoolExecutor.CallerRunsPolicy());
```

## CompletableFuture: Asynchronous Programming Made Easy

### Understanding CompletableFuture

`CompletableFuture` was introduced in Java 8 as an extension of `Future` with support for functional programming and composition. It represents a future result of an asynchronous computation that can be combined, chained, or have dependencies.

```java
public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {
    // Many methods for composition, combination, and exception handling
}
```

### Creating CompletableFuture Instances

There are several ways to create a `CompletableFuture`:

#### 1. Already Completed

```java
CompletableFuture<String> future = CompletableFuture.completedFuture("Result");
```

#### 2. Running Asynchronously

```java
CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
    // Some operation that doesn't return a value
    System.out.println("Running asynchronously");
});
```

#### 3. Supplying Asynchronously

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    // Some operation that returns a value
    return "Result of async computation";
});
```

#### 4. With Explicit Executor

```java
ExecutorService executor = Executors.newFixedThreadPool(5);
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "Result using custom executor";
}, executor);
```

### Transforming and Combining Results

One of the most powerful features of `CompletableFuture` is its ability to transform and combine results:

#### Transformation

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")  // Transforms the result
    .thenApply(String::toUpperCase);  // Further transformation
```

#### Consuming Results

```java
CompletableFuture<Void> future = CompletableFuture.supplyAsync(() -> "Hello")
    .thenAccept(s -> System.out.println("Received: " + s));  // Consumes the result
```

#### Chaining Asynchronous Operations

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Hello")
    .thenCompose(s -> CompletableFuture.supplyAsync(() -> s + " World"));  // Chain another CompletableFuture
```

#### Combining Results

```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");

CompletableFuture<String> combined = future1.thenCombine(future2, (s1, s2) -> s1 + " " + s2);
```

### Error Handling

`CompletableFuture` provides several methods for handling exceptions:

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    if (Math.random() > 0.5) {
        throw new RuntimeException("Something went wrong");
    }
    return "Success";
})
.exceptionally(ex -> {
    System.out.println("Error: " + ex.getMessage());
    return "Default value after error";
})
.handle((result, ex) -> {
    if (ex != null) {
        return "Recovered from: " + ex.getMessage();
    }
    return result;
});
```

### Timeouts

In Java 9, `CompletableFuture` gained timeout capabilities:

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    try {
        Thread.sleep(2000);  // Simulate long-running task
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
    return "Result";
})
.orTimeout(1, TimeUnit.SECONDS)  // Complete exceptionally if timeout occurs
.exceptionally(ex -> "Timeout occurred");
```

### Waiting for Multiple Futures

#### Wait for All

```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "First");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "Second");
CompletableFuture<String> future3 = CompletableFuture.supplyAsync(() -> "Third");

CompletableFuture<Void> allFutures = CompletableFuture.allOf(future1, future2, future3);

// Wait for all futures to complete
allFutures.join();

// Now all futures are complete, collect results
List<String> results = Stream.of(future1, future2, future3)
    .map(CompletableFuture::join)
    .collect(Collectors.toList());
```

#### Wait for Any

```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> {
    try {
        Thread.sleep(2000);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
    return "First";
});

CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
    return "Second";
});

CompletableFuture<Object> anyFuture = CompletableFuture.anyOf(future1, future2);
Object result = anyFuture.join();  // Will be "Second" since it completes first
```

## Java 21 Enhancements: Virtual Threads and Structured Concurrency

### Virtual Threads

Java 21 introduced virtual threads as part of Project Loom. Virtual threads are lightweight threads that are managed by the JVM rather than the operating system, allowing for a much larger number of concurrent tasks.

```java
// Creating a single virtual thread
Thread vThread = Thread.startVirtualThread(() -> {
    System.out.println("Running in a virtual thread");
});

// Using ExecutorService with virtual threads
try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
    IntStream.range(0, 10_000).forEach(i -> {
        executor.submit(() -> {
            // Even with 10,000 tasks, this is efficient with virtual threads
            try {
                Thread.sleep(100);
                System.out.println("Task " + i + " completed");
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            return i;
        });
    });
    // No need for explicit shutdown with try-with-resources
}
```

### Structured Concurrency (Preview in Java 21)

Structured Concurrency is a preview feature in Java 21 that aims to simplify multithreaded programming by treating multiple related tasks running in different threads as a single unit of work.

```java
import jdk.incubator.concurrent.StructuredTaskScope;

// Enable preview features with: --enable-preview
public class StructuredConcurrencyExample {
    public static void main(String[] args) {
        try {
            String result = fetchUserAndRecommendations("user123");
            System.out.println(result);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    static record UserData(String name, int age) {}
    static record Recommendations(List<String> items) {}
    
    static String fetchUserAndRecommendations(String userId) throws Exception {
        try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
            // Fork two subtasks
            StructuredTaskScope.Subtask<UserData> userTask = 
                scope.fork(() -> fetchUserData(userId));
                
            StructuredTaskScope.Subtask<Recommendations> recTask = 
                scope.fork(() -> fetchRecommendations(userId));
            
            // Wait for both subtasks to complete or one to fail
            scope.join();
            // Propagate any exceptions
            scope.throwIfFailed();
            
            // Both tasks completed successfully, process results
            UserData user = userTask.get();
            Recommendations recs = recTask.get();
            
            return "User: " + user.name() + ", Age: " + user.age() + 
                   ", Recommendations: " + recs.items();
        }
    }
    
    static UserData fetchUserData(String userId) throws Exception {
        // Simulate API call
        Thread.sleep(1000);
        return new UserData("John Doe", 30);
    }
    
    static Recommendations fetchRecommendations(String userId) throws Exception {
        // Simulate API call
        Thread.sleep(1500);
        return new Recommendations(List.of("Book", "Movie", "Game"));
    }
}
```

## Real-World Examples

### Example 1: Parallel API Requests

This example demonstrates how to make multiple API requests in parallel and combine their results:

```java
public class ParallelApiRequests {
    private final HttpClient httpClient = HttpClient.newHttpClient();
    
    public static void main(String[] args) throws Exception {
        ParallelApiRequests client = new ParallelApiRequests();
        client.fetchDataFromMultipleEndpoints();
    }
    
    void fetchDataFromMultipleEndpoints() throws Exception {
        // Define our API endpoints
        List<String> endpoints = List.of(
            "https://api.example.com/users",
            "https://api.example.com/products",
            "https://api.example.com/orders"
        );
        
        // Create a CompletableFuture for each request
        List<CompletableFuture<String>> futures = endpoints.stream()
            .map(this::fetchDataAsync)
            .collect(Collectors.toList());
        
        // Combine all futures into one
        CompletableFuture<Void> allFutures = CompletableFuture.allOf(
            futures.toArray(new CompletableFuture[0])
        );
        
        // When all futures complete, collect and process results
        CompletableFuture<List<String>> allResults = allFutures.thenApply(v -> 
            futures.stream()
                .map(CompletableFuture::join)
                .collect(Collectors.toList())
        );
        
        // Process the combined results
        List<String> results = allResults.get(10, TimeUnit.SECONDS);
        System.out.println("All API responses received:");
        results.forEach(System.out::println);
    }
    
    CompletableFuture<String> fetchDataAsync(String endpoint) {
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(endpoint))
            .GET()
            .build();
            
        return CompletableFuture.supplyAsync(() -> {
            try {
                HttpResponse<String> response = httpClient.send(
                    request, HttpResponse.BodyHandlers.ofString());
                return response.body();
            } catch (Exception e) {
                throw new CompletionException(e);
            }
        });
    }
}
```

### Example 2: Asynchronous Task Pipeline

This example demonstrates a pipeline of asynchronous tasks for processing data:

```java
public class AsyncDataProcessingPipeline {
    public static void main(String[] args) throws Exception {
        // Create an executor with virtual threads (Java 21)
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            // Start the pipeline
            CompletableFuture<List<String>> pipeline = fetchData(executor)
                .thenApplyAsync(AsyncDataProcessingPipeline::parseData, executor)
                .thenApplyAsync(AsyncDataProcessingPipeline::filterData, executor)
                .thenApplyAsync(AsyncDataProcessingPipeline::transformData, executor);
                
            // Wait for the pipeline to complete and get the result
            List<String> result = pipeline.get(30, TimeUnit.SECONDS);
            System.out.println("Pipeline completed with " + result.size() + " items");
            result.forEach(System.out::println);
        }
    }
    
    static CompletableFuture<String> fetchData(Executor executor) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println("Fetching data from source...");
            try {
                Thread.sleep(2000); // Simulate network delay
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            return """
                   {"items": [
                     {"id": 1, "name": "Item 1", "active": true},
                     {"id": 2, "name": "Item 2", "active": false},
                     {"id": 3, "name": "Item 3", "active": true},
                     {"id": 4, "name": "Item 4", "active": true}
                   ]}
                   """;
        }, executor);
    }
    
    static List<Map<String, Object>> parseData(String jsonData) {
        System.out.println("Parsing JSON data...");
        try {
            Thread.sleep(1000); // Simulate processing time
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        // In a real application, use a proper JSON parser
        // This is simplified for the example
        List<Map<String, Object>> items = new ArrayList<>();
        
        // Simulate parsing JSON
        items.add(Map.of("id", 1, "name", "Item 1", "active", true));
        items.add(Map.of("id", 2, "name", "Item 2", "active", false));
        items.add(Map.of("id", 3, "name", "Item 3", "active", true));
        items.add(Map.of("id", 4, "name", "Item 4", "active", true));
        
        return items;
    }
    
    static List<Map<String, Object>> filterData(List<Map<String, Object>> items) {
        System.out.println("Filtering data...");
        try {
            Thread.sleep(1000); // Simulate processing time
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        return items.stream()
            .filter(item -> (boolean) item.get("active"))
            .collect(Collectors.toList());
    }
    
    static List<String> transformData(List<Map<String, Object>> items) {
        System.out.println("Transforming data...");
        try {
            Thread.sleep(1000); // Simulate processing time
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        return items.stream()
            .map(item -> "Processed: " + item.get("name"))
            .collect(Collectors.toList());
    }
}
```

### Example 3: Resilient Service Calls with Retry Logic

This example demonstrates how to implement resilient service calls with retry logic:

```java
public class ResilientServiceCaller {
    private final HttpClient httpClient = HttpClient.newHttpClient();
    private final ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor();
    
    public static void main(String[] args) throws Exception {
        ResilientServiceCaller caller = new ResilientServiceCaller();
        try {
            String result = caller.callServiceWithRetry("https://api.example.com/data", 3);
            System.out.println("Service call succeeded: " + result);
        } finally {
            caller.executor.shutdown();
        }
    }
    
    String callServiceWithRetry(String url, int maxRetries) throws Exception {
        CompletableFuture<String> future = retryAsync(
            () -> callService(url),
            maxRetries,
            1000, // Initial delay in ms
            executor
        );
        
        return future.get(30, TimeUnit.SECONDS);
    }
    
    CompletableFuture<String> callService(String url) {
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(url))
            .GET()
            .build();
            
        return CompletableFuture.supplyAsync(() -> {
            try {
                System.out.println("Calling service: " + url);
                
                // Simulate random failures
                if (Math.random() < 0.7) {
                    throw new IOException("Service temporarily unavailable");
                }
                
                HttpResponse<String> response = httpClient.send(
                    request, HttpResponse.BodyHandlers.ofString());
                    
                if (response.statusCode() != 200) {
                    throw new IOException("Service returned status: " + response.statusCode());
                }
                
                return response.body();
            } catch (Exception e) {
                throw new CompletionException(e);
            }
        }, executor);
    }
    
    <T> CompletableFuture<T> retryAsync(
            Supplier<CompletableFuture<T>> supplier,
            int maxRetries,
            long delayMs,
            Executor executor) {
        
        CompletableFuture<T> future = new CompletableFuture<>();
        
        retryAsyncInternal(supplier, future, maxRetries, delayMs, executor);
        
        return future;
    }
    
    private <T> void retryAsyncInternal(
            Supplier<CompletableFuture<T>> supplier,
            CompletableFuture<T> resultFuture,
            int retriesLeft,
            long delayMs,
            Executor executor) {
        
        supplier.get().whenComplete((result, error) -> {
            if (error == null) {
                // Success
                resultFuture.complete(result);
            } else if (retriesLeft > 0) {
                // Retry after delay
                System.out.println("Operation failed. Retries left: " + retriesLeft + 
                                  ", Error: " + error.getMessage());
                
                CompletableFuture.delayedExecutor(delayMs, TimeUnit.MILLISECONDS, executor)
                    .execute(() -> retryAsyncInternal(
                        supplier, resultFuture, retriesLeft - 1, delayMs * 2, executor));
            } else {
                // No more retries
                System.out.println("Operation failed after all retries: " + error.getMessage());
                resultFuture.completeExceptionally(error);
            }
        });
    }
}
```

## Best Practices

### 1. Always Shut Down ExecutorService

Always ensure that your `ExecutorService` is properly shut down to prevent resource leaks:

```java
try (ExecutorService executor = Executors.newFixedThreadPool(4)) {
    // Use executor
} // Auto-closes with try-with-resources (Java 9+)

// Or manually:
executor.shutdown();
try {
    if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
        executor.shutdownNow();
    }
} catch (InterruptedException e) {
    executor.shutdownNow();
    Thread.currentThread().interrupt();
}
```

### 2. Choose the Right Thread Pool Size

The optimal thread pool size depends on the nature of your tasks:

- **CPU-bound tasks**: Use `Runtime.getRuntime().availableProcessors()` threads
- **I/O-bound tasks**: Use more threads than CPU cores
- **Virtual threads**: Consider using `newVirtualThreadPerTaskExecutor()` for I/O-bound tasks in Java 21

### 3. Handle Exceptions Properly

Always handle exceptions in asynchronous tasks:

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    // Risky operation
    return "result";
})
.exceptionally(ex -> {
    logger.error("Operation failed", ex);
    return "fallback";
})
.whenComplete((result, ex) -> {
    if (ex != null) {
        logger.error("Operation failed in whenComplete", ex);
    }
});
```

### 4. Avoid Blocking Operations in CompletableFuture Chains

Avoid blocking operations like `get()` or `join()` in the middle of `CompletableFuture` chains:

```java
// Bad practice
CompletableFuture.supplyAsync(() -> "first")
    .thenApply(s -> {
        String result = anotherFuture.join(); // Blocks the thread!
        return s + result;
    });

// Good practice
CompletableFuture.supplyAsync(() -> "first")
    .thenCompose(s -> anotherFuture.thenApply(result -> s + result));
```

### 5. Use Timeouts

Always use timeouts to prevent indefinite waiting:

```java
try {
    String result = future.get(5, TimeUnit.SECONDS);
} catch (TimeoutException e) {
    // Handle timeout
}

// Or with Java 9+
future = future.orTimeout(5, TimeUnit.SECONDS);
```

### 6. Consider Using Virtual Threads for I/O-Bound Tasks (Java 21)

Virtual threads are particularly well-suited for I/O-bound tasks:

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    // Submit thousands of I/O-bound tasks without worrying about thread pool size
    for (int i = 0; i < 10_000; i++) {
        executor.submit(() -> {
            // I/O operation like database query or HTTP request
            return performIoOperation();
        });
    }
}
```

### 7. Use the Right CompletableFuture Composition Method

Choose the appropriate composition method based on your needs:

- `thenApply`: Transform a result synchronously
- `thenApplyAsync`: Transform a result asynchronously
- `thenCompose`: Chain another `CompletableFuture`
- `thenCombine`: Combine with another `CompletableFuture`
- `thenAccept`: Consume a result without returning a value

### 8. Avoid Thread Pool Saturation

Prevent thread pool saturation by using appropriate rejection policies and monitoring:

```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    corePoolSize, maxPoolSize, keepAliveTime, TimeUnit.SECONDS,
    new LinkedBlockingQueue<>(queueCapacity),
    new ThreadPoolExecutor.CallerRunsPolicy());
    
// Monitor the executor
ScheduledExecutorService monitor = Executors.newSingleThreadScheduledExecutor();
monitor.scheduleAtFixedRate(() -> {
    System.out.println("Active threads: " + executor.getActiveCount());
    System.out.println("Queue size: " + executor.getQueue().size());
}, 0, 10, TimeUnit.SECONDS);
```

## Conclusion

Java's concurrency framework has evolved significantly over the years, with `ExecutorService` providing a solid foundation for thread management and `CompletableFuture` offering powerful tools for asynchronous programming. Java 21's introduction of virtual threads and structured concurrency further enhances these capabilities, making it easier to write efficient, scalable concurrent applications.

By understanding these tools and following best practices, you can build robust concurrent applications that effectively utilize system resources while maintaining code readability and maintainability.

Remember that concurrency is a complex topic, and the right approach depends on your specific use case. Always consider factors like task nature (CPU-bound vs. I/O-bound), expected load, and error handling requirements when designing concurrent systems.

With the knowledge and examples provided in this article, you should be well-equipped to leverage Java's concurrency capabilities effectively in your applications, taking full advantage of the latest features in Java 21.