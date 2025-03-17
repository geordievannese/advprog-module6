# module 6

## Commit 1 Reflection notes
To process incoming HTTP requests from a web browser, I wrapped the TCP stream with a BufReader. This makes it possible to read the stream line by line using .lines(), where each line corresponds to a different part of the HTTP request (for example, the method, path, or headers).

Next, I used .map(|line| line.unwrap()) to convert the iterator from Result<String> into String, and then applied .take_while(|line| !line.is_empty()) to stop reading when it hits the first empty line—this marks the boundary between the headers and the request body in HTTP.

Finally, by collecting these lines into a Vec<String>, I got a clear, line-by-line view of how a browser sends a request to the server, which helped me better understand the low-level structure of HTTP requests.

## Commit 2 Reflection Notes

![alt text](assets/Images/commit2.png)

I discovered how to serve a proper HTML file instead of just plain text by adjusting the handle_connection method. Using fs::read_to_string() made it straightforward to load the contents of hello.html into memory, and by including the correct headers along with the file’s contents in the HTTP response, browsers could render the page correctly.

I also learned the importance of the Content-Length header. Leaving it out can cause some browsers to display the page incorrectly. Moreover, I realized that an HTTP response must follow a specific format, starting with a status line (e.g., HTTP/1.1 200 OK), then headers, an empty line, and finally the response body.