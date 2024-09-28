# FLOW

## REACTIVE PROGRAMMING?
- Being notified about any changes, and doing something when its changes.

# 1. FLOW
- Is an kotlin coroutine that is able to emit multiple values over period of time.
- Single suspen function can only return a single value. So flow comes into play to be opposed to susfunct.
- A flow is conceptually a stream of data that can be computed asynchronously.
- The emitted values must be of the same type. For example, a Flow<Int> is a flow that emits integer values.

- A flow is very similar to an Iterator that produces a sequence of values, but it uses suspend functions to produce and consume values asynchronously. This means, for example, that the flow can safely make a network request to produce the next value without blocking the main thread.

# 2. EMIT
- Is a function used to send values from a flow to its collector.
- When you create a flow using the flow {} builder, you can use emit(value) to produce a value that can be collected later.
- Key values :
  - Sending Values. Push values inti the flow.
  - Suspending Function. Emit is suspend function.
  - Flow Control. Using emit, you can control when and how often values are sent to the collectors. This is particularly useful in scenarios involving delays, network calls, or complex data transformations.
  - Multiple emits in a single flow is valid.
    ```kotlin
    import kotlinx.coroutines.*
    import kotlinx.coroutines.flow.*
    
    fun main() = runBlocking {
        // Define a flow that emits multiple values
        val multiEmitFlow = flow {
            emit("First Value")  // Emit the first value
            delay(100)           // Simulate some processing time
            emit("Second Value") // Emit the second value
            delay(100)
            emit("Third Value")  // Emit the third value
        }
    
        // Collect the emitted values
        multiEmitFlow.collect { value ->
            println("Received: $value")
        }
    }
    ```
    Result : 
    ```
    Received: First Value
    Received: Second Value
    Received: Third Value
    ```
    
# 3. COLLECT
-  Is a terminal operator used to gather the emitted values from a flow.
-  When you call collect on a flow, it starts the flow's collection process, allowing you to react to each emitted item.
-  Key points :
  - Collection Emissions. When you collect a flow, youre subscribing to it
  - Collect is suspending function, meaning it **should be called form a coroutine** or **another suspending function**. This allows it to be non-blocking.
  - Lambda param to define what to do with each emitted value
- Types :
  - ``collect``
  - ``collectLatest``
  - ``collectIndexed``
  - ``onEach``
  - ``collectWhile`` dan ``collectUtil``
  
# 4. FLOW OPERATORS
- Ada banyak bgt, mls gw. Collect itu salah satunya. 


  
