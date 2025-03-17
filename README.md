# module 6

## Commit 1 Reflection notes
To process incoming HTTP requests from a web browser, I wrapped the TCP stream with a BufReader. This makes it possible to read the stream line by line using .lines(), where each line corresponds to a different part of the HTTP request (for example, the method, path, or headers).

Next, I used .map(|line| line.unwrap()) to convert the iterator from Result<String> into String, and then applied .take_while(|line| !line.is_empty()) to stop reading when it hits the first empty line—this marks the boundary between the headers and the request body in HTTP.

Finally, by collecting these lines into a Vec<String>, I got a clear, line-by-line view of how a browser sends a request to the server, which helped me better understand the low-level structure of HTTP requests.

## Commit 2 Reflection Notes

![alt text](assets/Images/commit2.png)

I discovered how to serve a proper HTML file instead of just plain text by adjusting the handle_connection method. Using fs::read_to_string() made it straightforward to load the contents of hello.html into memory, and by including the correct headers along with the file’s contents in the HTTP response, browsers could render the page correctly.

I also learned the importance of the Content-Length header. Leaving it out can cause some browsers to display the page incorrectly. Moreover, I realized that an HTTP response must follow a specific format, starting with a status line (e.g., HTTP/1.1 200 OK), then headers, an empty line, and finally the response body.

## Commit 3 Reflection Notes
![alt text](assets/Images/commit3.png)

I implemented logic in the handle_connection method that conditionally returns different responses based on the request path. The idea is to inspect the request line from the browser and serve the appropriate page: if the request is for /, the server responds with a 200 OK status and displays hello.html; for any other path (like /bad), it returns a 404 NOT FOUND status along with a custom 404.html file.

This approach treats the URI / as the only valid request. If the request line matches GET / HTTP/1.1, the server provides the usual hello.html with a success code; otherwise, it returns an error status and an error page.

Additionally, the tutorial suggests refactoring the code to improve clarity and maintainability. By breaking the logic into smaller, reusable components rather than keeping everything in one function, the code becomes easier to understand and scales better in the future.


## commit 4 Reflection Notes
I simulated a performance bottleneck by adding a /sleep route to the web server. When this route is accessed, the server pauses for 10 seconds using thread::sleep() before sending a response. This experiment demonstrated how a single-threaded server behaves under delay or heavy processing load: if /sleep is opened in one browser tab, other requests—even those to the normal / route in another tab—are stalled until the delay finishes.

This experience highlighted that single-threaded architectures are not scalable. In practical web servers, multithreading or asynchronous handling is essential to prevent slow or delayed requests from blocking others. It was a valuable lesson in understanding how concurrency and performance are intertwined in web server design.

## commit 5 Reflection Notes
n this section of the project, we boosted our server's performance by implementing a ThreadPool to manage multiple connections concurrently. Instead of spawning a new thread for every request, we created a fixed number of threads—four in our case—using ThreadPool::new(4). These threads remain active and are reused to efficiently handle incoming tasks.

Within the main loop, we assigned tasks (closures) to the thread pool with pool.execute(). This functions similarly to Rust’s thread::spawn(), where a closure is executed; however, by reusing existing worker threads instead of creating new ones for each task, we avoid the overhead associated with thread creation.

We chose the usize type for the thread count parameter in ThreadPool::new() because it represents non-negative whole numbers, which is appropriate since the number of threads can’t be negative. Moreover, usize works well for managing our worker threads stored in a vector.

In the execute method, we specified the closure type as FnOnce(), indicating that the closure will only be executed a single time. This suits our scenario where each task handles one connection. The empty parentheses following FnOnce denote that the closure takes no arguments and returns nothing, mirroring a standard function without parameters.