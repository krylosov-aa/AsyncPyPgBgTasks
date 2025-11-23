# AsyncPyPgBgTasks

The service and library is currently under development.

A service and library that enable your application to run background tasks written in asynchronous Python. PostgreSQL is used as the task queue, providing very high write performance and guaranteeing reliable, one-time execution of each task once it has been added to the queue.

The service supports concurrent processing of any number of tasks.
It can be launched multiple times and connected to the same task queue.

## How It Works

### Library

The library helps you queue tasks — queuing a task simply means inserting a record into a table.
Inserting a record is very fast thanks to the optimized table configuration.

Tasks must be asynchronous Python functions.
A Pythonic model is passed to them as input, and you write the function yourself.
The only limitation is the function’s interface.

### Service

The service acts as a supervisor that launches N workers.
Each worker retrieves M asynchronous tasks from the queue and executes them concurrently.

#### Supervisor

The supervisor’s job is to start the workers and make sure they stay alive.
If any of them fail, it restarts them automatically.

#### Worker

A worker’s job is to process tasks from the queue.
The worker imports the function specified in the queue entry and passes it the associated data.
Each task has a limited execution time. If the function completes successfully, the task is removed from the queue.
If an error occurs, the task remains in the queue, and after a short delay, any worker can retry it.

#### Parameters for configuration

1. You can configure the number of supervisors, allowing you to deploy this service multiple times.
The service operates reliably in a competitive environment with any number of concurrent instances.

2. You can configure how many workers the supervisor will start.

3. You can configure how many tasks each worker will handle concurrently.

As a result, you can calculate how many tasks will be processed concurrently:
number of supervisors (i.e., how many times you’ve launched the service) × number of workers × number of tasks each worker handles.

4. You can also configure several delay-related parameters:

- Task execution timeout — how much time is allotted to execute a single task.
- Retry delay — how long to wait between a failed attempt and the next attempt to execute the task.
- Supervisor check interval — how often the supervisor checks the status of workers.
- Worker idle sleep duration — how long a worker sleeps when the queue is empty.

