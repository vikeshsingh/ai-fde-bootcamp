# Class 5: Network I/O, HTTP, and Memory Optimization

## Objective

First class of the actual FDE syllabus (prior classes were Python recap).
Covers network I/O concepts, HTTP protocol fundamentals, the `requests`
library, and memory-optimization techniques (garbage collection,
generators, iterators, streaming) framed around real FDE use cases
(reading large PDFs, calling external APIs).

## Files

No code files for this class — notes only below (class was taught
directly in a notebook, not shared as a separate file this time).

## Class notes

### URL vs. API vs. website

- A **URL** is just an address — "a digital mailing address."
- If that address returns **HTML/CSS/images** rendered by a browser, it's
  a **website**.
- If that address returns **data** (JSON, XML, etc.), it's an **API
  endpoint**.

### Protocols — pick the right one for the job

| Need | Protocol |
|---|---|
| Transfer web pages / API data | HTTP / HTTPS |
| Real-time, two-way communication (live chat, live video) | WebSocket |
| Move large files between computers | FTP / SFTP |
| Send/receive mail | SMTP / IMAP |
| Translate a domain name to an IP address | DNS |
| Lightweight smart-home / IoT messaging | MQTT |
| High-performance peer-to-peer video streaming | WebRTC |

A protocol is just "a set of rules for communication." Picking the wrong
one for a problem (e.g. polling HTTP for something that needs instant
two-way updates) is a design mistake, not a coding one.

### HTTP vs. HTTPS

- **HTTP** sends data as plain text — readable by anyone intercepting
  the network traffic.
- **HTTPS** encrypts the data (via TLS/SSL) after an initial handshake,
  so intercepted traffic looks like gibberish until decrypted at the
  destination.
- Always prefer HTTPS for anything beyond a trivial static personal site.
- You can't use HTTPS against a server that only speaks plain HTTP — the
  server has to support the secure layer.

### HTTP/1, HTTP/2, and HTTP/3

- HTTP/1 and HTTP/2 require a separate network handshake, then a separate
  encryption handshake — multiple round trips before any data moves.
- **HTTP/3** combines the handshake and the encryption key exchange into
  a single round trip — much faster, especially at scale (saving
  milliseconds per request matters when you're making millions of
  requests).
- Write `https://` rather than hardcoding a specific HTTP version — this
  lets the client and server negotiate the best version each one
  supports, so older systems (HTTP/1 or /2) and newer ones (HTTP/3) both
  keep working through the same URL scheme.
- You can check which version a site is using in the browser's dev tools:
  Network tab → a request's response headers → look for `alt-svc`; a
  value like `h3=":443"` means HTTP/3.

### HTTP methods

| Method | Meaning |
|---|---|
| `GET` | Retrieve data. No body. |
| `POST` | Send data to create a resource (or sometimes retrieve data with parameters, though that's non-standard). |
| `PUT` | Replace a resource **entirely**. |
| `PATCH` | Update a resource **partially** (only the fields you send). |
| `DELETE` | Remove a resource. No body. |

In practice, most of what you do day to day uses `GET` and `POST`; the
others exist to communicate intent clearly to other developers/systems
consuming your API.

### Using the `requests` library

Install it inside your `uv`-managed environment (you can't use `uv add`
without a `pyproject.toml`/`uv init`, so use pip through uv instead):

```bash
uv pip install requests
```

Basic GET request:

```python
import requests

response = requests.get("https://dummyjson.com/test")
print(response.status_code)   # 200 = success
print(response.json())        # parse the JSON body
```

**Status code ranges:**
- `2xx` — success.
- `4xx` — client-side error (e.g. wrong URL/path, not found).
- `5xx` — server-side error (server reachable but something went wrong
  on its end).
- No response at all (connection error) means the server/URL isn't
  reachable — different from getting a 4xx/5xx back.

`.json()` only works if the response body is actually JSON — calling it
on a non-JSON response (e.g. a plain HTML page) raises a decode error.

**Query parameters** — pass a dict via the `params` keyword instead of
hand-building the query string:

```python
query_params = {"limit": 10, "skip": 5}
response = requests.get("https://dummyjson.com/products", params=query_params)
```

### Memory optimization

#### Garbage collection

```python
a = 10
b = a       # b points to the same object as a
del a       # only removes the name "a"; the underlying object/reference
            # isn't freed yet — Python keeps that memory reserved for reuse
```

- `del` detaches a name from its value; it does **not** immediately
  release the memory back to the operating system.
- Python keeps freed memory reserved in case it's needed again soon
  (avoids repeatedly asking the OS for more memory).
- `import gc; gc.collect()` forces Python to actively reclaim memory that
  has no remaining references.
- You rarely need this manually — only when working with very large
  objects (e.g. big embeddings/arrays) on memory-constrained servers,
  where holding onto unused reserved memory could cause problems.

#### Iterators vs. generators

- An **iterator** is the general concept/class (has `__iter__`); a
  **generator** is a simpler, special case of an iterator that you
  create yourself.
- `range()` is an iterator, not a generator — you don't call `next()` on
  it directly; it has to be converted first:

```python
r = iter(range(1, 5))
next(r)   # 1
next(r)   # 2
```

- `enumerate()` is also a lazy iterator — it doesn't hold all values at
  once, it gives you `(index, value)` pairs one at a time:

```python
for index, value in enumerate(["a", "b", "c"]):
    print(index, value)   # 0 a / 1 b / 2 c

for index, value in enumerate(["a", "b", "c"], start=1):
    print(index, value)   # 1 a / 2 b / 3 c
```

- A **generator function** uses `yield` instead of `return`. Calling it
  doesn't run the function — it returns a generator object that only
  advances (and produces the next value) when you call `next()` on it:

```python
def get_values(items):
    for item in items:
        yield item

gen = get_values([1, 2, 3])
next(gen)   # 1 — runs up to the first yield, then pauses
next(gen)   # 2 — resumes from where it paused
```

**Why this matters**: a plain function that builds and returns a full
list loads everything into memory at once. A generator produces one
value at a time, on demand — critical when processing something large
(e.g. a 10,000-page PDF) that would otherwise force you to hold the
entire thing in RAM.

```python
# Not memory-optimal — builds a full list before returning anything
def extract_all_pages(pdf_document):
    all_page_text = []
    for page in pdf_document:
        all_page_text.append(page.extract_text())
    return all_page_text

# Memory-optimal — yields one page's text at a time
def extract_pages(pdf_document):
    for page_number, page in enumerate(pdf_document, start=1):
        yield page.extract_text()

pages = extract_pages(pdf_document)
next(pages)   # process one page at a time instead of loading the whole book
```

`file.readlines()` reads everything into memory at once; iterating a
file object directly (or using `.readline()` in a loop) reads it line by
line, the same lazy-loading idea.

#### Streaming large API/network responses

By default, `requests.get(url)` waits for the **entire** response body
to be ready server-side, then hands you the whole thing — which Python
then has to store in memory (and a large JSON/dict structure often takes
noticeably more memory than the raw byte size, e.g. ~1GB of JSON can
become ~2GB in Python's internal representation).

```python
response = requests.get(url, stream=True)
```

With `stream=True`, the server sends data as it becomes available
(byte by byte / chunk by chunk) instead of waiting to assemble the full
response first, and the data is held in the **operating system's network
buffer**, not inside a Python variable, until you actually read it (e.g.
via `enumerate`-style chunked reading). This keeps memory usage close to
the actual data size instead of the inflated Python-object size, which
matters a lot when processing very large downloads on memory-constrained
servers.

**Key distinction to remember**: `yield`/generators only save memory when
the data *isn't already fully loaded into Python*. If you already called
a non-streaming `requests.get()` and have the full response in a
variable, wrapping it in `enumerate()` afterward gives you one-item-at-a-
time access, but the memory cost was already paid when the full response
was received — streaming (`stream=True`) has to be used from the start
to actually avoid that cost.

## Pending for next class

- Object-oriented programming basics (class, object, constructor,
  methods, attributes, `self`)
- Exception handling
- File I/O operations
- Event loop architecture / parallelism (if time permits)

## Assignment (due before next week, not next class)

Install a Linux environment for upcoming Linux-for-engineers topics:

- **If you already have WSL working, use that — no need to install
  anything else.**
- Otherwise, install **Multipass** (works on both Windows and Apple
  Silicon/Intel Macs, unlike WSL which is Windows-only):
  - Minimum requirements: 64-bit Intel/AMD CPU or Apple Silicon, 4GB RAM,
    5GB free disk space, Windows 10/11 or macOS 14+.
  - Suggested instance size: 2 CPU cores, 2GB RAM, 15GB disk, Ubuntu
    24.04.
  - Minimum viable instance (if resources are tight): 1 CPU core, 1GB
    RAM.
