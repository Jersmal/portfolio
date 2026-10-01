# Network Security Labs — Privacy Detection & Censorship (Zeek)

Two network-security projects built around [Zeek](https://zeek.org/). I owned the
detection/censorship side of each.

> **Write-up only.** The scripts live in a private repo — read access on request.

## 1. Privacy-Leak Detection (Zeek)

A custom Zeek script that inspects live or captured traffic and flags privacy- and
security-relevant events, going beyond a basic request logger by analyzing
individual HTTP headers. Seven event types:

- **Cleartext HTTP** — full URL/headers exposed in plaintext.
- **Trackers** — DNS lookups to known ad/analytics/telemetry domains.
- **Weak TLS** — SSLv2/SSLv3/TLS 1.0 (POODLE/BEAST risk).
- **Cookie exposure** — cookies sent over plaintext HTTP.
- **Referer leaks** — cross-origin `Referer` headers leaking browsing history.
- **OCSP leaks** — certificate-validation requests revealing site visits to the CA.
- **Fingerprinting** — identifying detail in `User-Agent` strings.

Built on four Zeek event hooks (`http_request`, `dns_request`, `ssl_established`,
`http_header`) — the per-header hook is what enables cookie, referer, OCSP, and
fingerprint detection.

## 2. Censorship Detection & Blocking (Zeek + iptables)

A system that both **detects** and **actually blocks** access to a target set of
organizations, plus detection for common circumvention methods.

- **Three detection layers** so one bypass doesn't defeat the system: DNS query
  inspection (catches lookups before a connection, incl. subdomains), HTTP
  Host-header inspection (catches hard-coded-IP / cached-DNS cases), and TLS SNI
  inspection (the real-world national-firewall technique), with an IP-range fallback.
- **Circumvention detection:** VPN (OpenVPN / WireGuard / IKEv2), Tor (known
  nodes/ports), and DNS-over-HTTPS (known resolver IPs).
- **Actual blocking:** a Docker gateway injects iptables rules to drop blocked
  traffic, with whitelist rules placed before block rules so allowed services still
  pass.
- **Policy thinking:** argues the *exceptions* question — why a small state might
  block consumer services while whitelisting shared API/CDN infrastructure that
  critical third-party apps depend on.

## Skills

Zeek scripting · DNS / HTTP / TLS analysis · network traffic monitoring · iptables /
Docker networking · VPN/Tor/DoH identification.
