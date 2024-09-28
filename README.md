# FLOW

## REACTIVE PROGRAMMING?
- Being notified about any changes, and doing something when its changes.

# 1. FLOW
- Is an kotlin coroutine that is able to emit multiple values over period of time.
- Single suspen function can only return a single value
  
### FlowCollector

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
- **Transformations Operators**
  - **map** => Transforms each emitted value by applying a given function.
    ```kotlin
    flowOf(1, 2, 3).map { it * 2 } // Emits 2, 4, 6
    ```
  - **filter** => Emits only those values that satisfy a given predicate.
    ```kotlin
    flowOf(1, 2, 3, 4).filter { it % 2 == 0 } // Emits 2, 4
    ```
  - **flatMapConcat**
    ```kotlin
    ```
  - **flatMapMerge**
    ```kotlin
    ```
- **Combining Oprators**
  - **combine** =>  Combines values from two flows into a single flow based on the latest values emitted.
    ```kotlin
    val flow1 = flowOf(1, 2)
    val flow2 = flowOf("A", "B")
    flow1.combine(flow2) { a, b -> "$a$b" } // Emits "1A", "2B"
    ```
  - **zip** => Combines values from two flows, emitting pairs of values as they are emitted.
    ```kotlin
    flowOf(1, 2).zip(flowOf("A", "B")) { a, b -> "$a$b" } // Emits "1A", "2B"
    ```
- **Combining Oprators**
  - **collect** => Starts the flow collection and processes each emitted value.
    ```kotlin
    flowOf(1, 2, 3).collect { println(it) }
    ```
  - **toList** => Collects all emitted values into a list.
    ```kotlin
    val list = flowOf(1, 2, 3).toList() // [1, 2, 3]
    ```
  - **first** => Returns the first value emitted by the flow.
    ```kotlin
    val firstValue = flowOf(1, 2, 3).first() // 1
    ```
- **Exception Handling**
  - **catch** => Catches exceptions thrown during flow collection and allows you to handle them.
    ```kotlin
    flow {
    emit(1)
    throw Exception("Error")
    }.catch { e -> emit(0) } // Emits 1, then 0
    ```
- **Flow Control Operators**
  - **take**
    ```kotlin
    flowOf(1, 2, 3).take(2) // Emits 1, 2
    ```
  - **drop** =>  Skips the first n values and emits the rest.
    ```kotlin
    flowOf(1, 2, 3).drop(2) // Emits 3
    ```
  - **onEach** => Allows you to perform a side effect on each emitted value without altering the flow.
    ```kotlin
    flowOf(1, 2, 3).onEach { println(it) } // Prints 1, 2, 3
    ```
- **Buffering and Conflation**
  - **buffer** => Allows the flow to emit values concurrently with the processing of the collected values.
    ```kotlin
    flowOf(1, 2, 3).buffer() // Buffers emitted values
    ```
  - **conflate** => Only keeps the latest value when the collector is slow.
    ```kotlin
    flowOf(1, 2, 3).conflate() // Emits only the latest value
    ```


  
