# stiff

An opinionated static file web server.
Built to make serving static websites fast and easy.


## Features

- Clean URLs - no trailing slashes or `.html` extensions
- Serve precompressed files (brotli and gzip) for faster downloads
- Easy resource caching via `ETag` and `Last-Modified` headers
- Set your own headers for even better caching and security

**NOTE:**
stiff is meant to be used behind a reverse proxy, features like HTTPS, access logs, etc. are not provided.


## Usage

- Place the files to be served in a directory named `public`
- Once the server is running, files can no longer be added or modified
- For precompressed files, the filename must match the original with a `.gz` and `.br` extension
- To generate the precompressed files, you will have to use a separate tool like [prepa](https://github.com/lezgomatt/prepa)


## Configuration

You can configure stiff with a JSON file called `stiff.json`.

### Top-Level Options

| Key | Description |
| - | - |
| `headers` | Set additional headers |
| `etag` | Set the `ETag` header (default: `true`) |
| `lastmod` | Set the `Last-Modified` header (default: `false`) |
| `mimetypes` | Set custom MIME types; extensions must begin with a dot (`.`) |

### Route-Level Options

- Route-level options are placed under the `routes` key
- Routes must begin with a slash (`/`)
- Routes must end with a slash (`/`) for directories, i.e. `/assets` is separate from `/assets/`
- Only the most specific route match is applied (they don't cascade)

| Key | Description |
| - | - |
| `headers` | Same as the top-level; headers will merge with the ones specified at the top-level |
| `etag` | Same as the top-level; if not specified, the top-level value will be used |
| `lastmod` | Same as the top-level; if not specified, the top-level value will be used |
| `serve` | Always serve the specified file for all subpaths (for SPAs) |

### Example `stiff.json`

```json
{
    "headers": {
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "public, no-cache"
    },
    "lastmod": true,
    "routes": {
        "/": { "lastmod": false },
        "/app": { "serve": "webapp.html" },
        "/app/": { "serve": "webapp.html" },
        "/assets/": {
            "headers": { "Cache-Control": "public, max-age=300" }
        },
        "/assets/videos/": {
            "headers": { "Cache-Control": "public, max-age=300" },
            "etag": false
        }
    },
    "mimetypes": {
        ".atom":  "application/atom+xml"
    }
}
```
