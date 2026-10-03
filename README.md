# myproxy: Multithreaded HTTP-to-HTTPS Proxy Server (C)

A proxy server written in C that accepts plain HTTP requests from clients, upgrades
each one to HTTPS before forwarding it to the destination server, and relays the
response back. Built for CSE 156/L (Computer Networks) at UC Santa Cruz.

> **Note on the code.** Source and full documentation live in a **private**
> repository to keep coursework solutions off the public internet. Happy to share
> read access on request.

---

## What it does

- **HTTP in, HTTPS out.** Clients speak plain HTTP to the proxy; the proxy opens a
  TLS connection (OpenSSL, TLS 1.1 to 1.2) to the real server and returns the response.
- **Concurrent clients.** One listener thread; each connection gets its own detached
  pthread, supporting up to 50 simultaneous clients.
- **Persistent connections.** HTTP/1.1 keep-alive loop per client until
  `Connection: close`.
- **Access control.** Loads a forbidden-sites list at startup and returns
  `403 Forbidden` for blocked hosts while keeping the client connection alive.
- **Structured logging.** Thread-safe access log with RFC 3339 millisecond
  timestamps, client IP, request line, status, and bytes.
- **Header handling.** Rewrites the request to a path-only URI, strips
  `Proxy-Connection`, and adds `X-Forwarded-For`.

## Design highlights

- **SNI** is set before the TLS handshake so virtual-hosted servers present the
  right certificate.
- **Dual-stack outbound connections.** `getaddrinfo()` with `AF_UNSPEC` tries IPv6
  and IPv4 addresses in order, so the proxy reaches servers over either.
- **Clean failure handling.** `400` malformed request, `501` unsupported method
  (including `CONNECT`), `502` DNS failure, `504` connect or handshake failure.
  `SIGPIPE` is ignored and mid-transfer drops on either side clean up all sockets
  and SSL state without leaking connections.
- **Streaming relay** in 64 KB chunks, so large responses never need to fit in memory.

## Known limitations (by design for the assignment)

- Certificate verification is disabled per the project spec, so this is not
  MITM-safe for real deployment.
- ACL is exact-match only (subdomains must be listed individually).
- TLS 1.3 is not enabled.

## Tech

`C` · `POSIX sockets` · `pthreads` · `OpenSSL` · `TLS / SNI` · `HTTP/1.1` ·
`IPv4 / IPv6` · `Make` · `Ubuntu Linux`
