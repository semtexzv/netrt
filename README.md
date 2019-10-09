# netrt
Generic runtime for creating high performace distributed services in rust.


Goal of this crate is to provide a high-performance runtime for futures-based applications. This mainly includes servers, 
bit might include something like a database engine. 

## Goals
The designed library should aim to 
 - Minimize latency for services that aren't under load
 - Minimize overhead of managing services that are under load (easy work-stealing)
 - Provide good locality for dependant task ( more on this later )
 
## Approach 
The approach we are taking is as following:
- Utilize a fixed number of threads
- Each thread has its local Executor, Reactor, Timer and all the runtime resources it needs
- Each thread has local work queue, which is used to spawn futures onto this thread.
- All of the processing necessary for resolving a request is performed on local thread.
- If there is a bigger unit of work, a thread can spawn a `task` onto a global queue, from which free threads pick work, and 
assign it to themselves.

### Design inspirations
- Actix-rt
- Seastar

## Underlying runtime
For now we utilize a tokio `current-thread` executor, with a reactor and a timer on each thread. There might be a utility for 
implementing a thread-pool like runtime for large work units, to ensure complex requests do not compromise latency.
