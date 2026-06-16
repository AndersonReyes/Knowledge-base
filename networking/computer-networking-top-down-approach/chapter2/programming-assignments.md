---

# Chapter 2 — Socket Programming Assignments

> Kurose, J. & Ross, K. (2022). *Computer Networking: A Top-Down Approach* (8th ed.), Chapter 2 Socket Programming Assignments.
> Full skeleton code available at: https://www.pearsonhighered.com/cs-resources

Six assignments total. Assignments 1–4 are Chapter 2. Assignment 5 (ICMP Ping) is Chapter 5. Assignment 6 (traceroute) is also Chapter 5.

---

## Assignment 1: Web Server

**Concepts**: TCP sockets, HTTP/1.0 request parsing, HTTP response formatting

**Task**: Build a single-threaded HTTP/1.0 web server in Python.

**Requirements**:
1. Create a TCP welcoming socket on a configurable port (not 80 if a web server is already running)
2. Accept a client connection
3. Receive and parse the HTTP GET request to extract the requested filename
4. Read the file from the local filesystem
5. Send an HTTP response with appropriate headers followed by the file content
6. If the file does not exist, return `404 Not Found`
7. Close the connection

**Key HTTP response format**:
```
HTTP/1.0 200 OK\r\n
Content-Type: text/html\r\n
\r\n
<file content>
```

**Test**: Request files from the server using a real browser or `curl`.

**Extensions** (optional):
- Support multiple concurrent clients using threading (`threading.Thread`)
- Add support for `Content-Length` header
- Support `HEAD` method

---

## Assignment 2: UDP Pinger

**Concepts**: UDP sockets, RTT measurement, packet loss handling, timeouts

**Task**: Write a UDP ping *client* (server code is provided).

**Protocol**:
- Client sends 10 ping messages: `PING <seq_num> <timestamp>\r\n`
- Server randomly drops ~30% of packets (simulating unreliable network)
- Server echoes back (uppercased) any packet it receives
- Client measures RTT for each received pong; prints "Request timed out" for lost packets
- Client waits up to 1 second (`socket.settimeout(1)`) per ping

**Requirements**:
1. Create a UDP socket
2. Send 10 sequential ping messages
3. For each: wait up to 1s for pong; compute and print RTT if received; print timeout message if not
4. Print summary: min/max/avg RTT, packet loss %

**Key socket API**:
```python
clientSocket = socket(AF_INET, SOCK_DGRAM)
clientSocket.settimeout(1)
clientSocket.sendto(message.encode(), (serverName, serverPort))
try:
    modifiedMessage, serverAddress = clientSocket.recvfrom(1024)
except timeout:
    print("Request timed out")
```

---

## Assignment 3: Mail Client (SMTP)

**Concepts**: TCP sockets, SMTP protocol, base64 encoding, TLS/SSL

**Task**: Build an SMTP mail client in Python that sends a real email.

**Requirements**:
1. Establish TCP connection to mail server (e.g., `smtp.gmail.com:587`)
2. Complete SMTP handshake: `HELO`, `EHLO`, `STARTTLS`, `AUTH LOGIN`, `MAIL FROM`, `RCPT TO`, `DATA`, `QUIT`
3. Base64-encode credentials for `AUTH LOGIN`
4. Send email body ending with `\r\n.\r\n`
5. Read and check server response codes (250 = OK, 354 = start input, 235 = auth OK)

**SMTP exchange**:
```
C: EHLO alice.org
S: 250 ...
C: STARTTLS
S: 220 ...
[TLS negotiation]
C: AUTH LOGIN
S: 334 ...
C: <base64(username)>
S: 334 ...
C: <base64(password)>
S: 235 Authentication successful
C: MAIL FROM: <alice@gmail.com>
S: 250 OK
C: RCPT TO: <bob@gmail.com>
S: 250 OK
C: DATA
S: 354 Start mail input
C: From: alice@gmail.com\r\nTo: bob@gmail.com\r\nSubject: Test\r\n\r\nHello Bob!\r\n.
S: 250 OK
C: QUIT
S: 221 Bye
```

**Note**: Gmail requires an App Password (not your regular password) when 2FA is enabled. Set it at https://myaccount.google.com/apppasswords

**Extensions** (optional):
- Add MIME support for HTML email or attachments
- Try different SMTP servers (university mail server vs Gmail)

---

## Assignment 4: Web Proxy

**Concepts**: TCP sockets, HTTP request forwarding, caching, multi-threading

**Task**: Build a web proxy server that sits between a browser and origin servers.

**Requirements**:
1. Listen for incoming browser HTTP requests on a local port (e.g., 8888)
2. Parse the HTTP request to extract the target hostname and path
3. Forward a new HTTP request to the origin server
4. Receive the response from the origin server
5. Forward the response back to the browser
6. Optionally cache responses locally; serve from cache on subsequent requests for the same URL

**Flow**:
```
Browser → Proxy (GET http://www.example.com/page.html HTTP/1.0)
Proxy  → Origin Server (GET /page.html HTTP/1.0 + Host: www.example.com)
Origin → Proxy (HTTP response)
Proxy  → Browser (HTTP response)
```

**Test**: Configure your browser to use `localhost:8888` as HTTP proxy, then browse normally.

**Key considerations**:
- Parse the full URL from the request line to extract host + path
- Use `Host:` header when forwarding to origin
- Handle binary content (images) correctly — read/write in binary mode
- Use threads to handle multiple concurrent browser connections

**Extensions** (optional):
- Implement disk-based caching (hash URL → filename)
- Add cache expiry using `Cache-Control` / `Expires` headers
- Support `CONNECT` method for HTTPS tunneling

---

## Assignment 5: ICMP Ping (Chapter 5)

**Concepts**: Raw sockets, ICMP protocol, checksum calculation

**Task**: Implement a ping client using raw ICMP sockets (requires root/admin privileges).

*Covered at end of Chapter 5.*

---

## Assignment 6: ICMP Traceroute (Chapter 5)

**Concepts**: Raw sockets, ICMP TTL manipulation, route discovery

**Task**: Implement traceroute using raw ICMP sockets with incrementing TTL.

*Covered at end of Chapter 5.*
