# module 6

## Commit 1 Reflection notes
To process incoming HTTP requests from a web browser, I wrapped the TCP stream with a BufReader. This makes it possible to read the stream line by line using .lines(), where each line corresponds to a different part of the HTTP request (for example, the method, path, or headers).

Next, I used .map(|line| line.unwrap()) to convert the iterator from Result<String> into String, and then applied .take_while(|line| !line.is_empty()) to stop reading when it hits the first empty line—this marks the boundary between the headers and the request body in HTTP.

Finally, by collecting these lines into a Vec<String>, I got a clear, line-by-line view of how a browser sends a request to the server, which helped me better understand the low-level structure of HTTP requests.