# peasy-video-go

[![Go Reference](https://pkg.go.dev/badge/github.com/peasytools/peasy-video-go.svg)](https://pkg.go.dev/github.com/peasytools/peasy-video-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/peasytools/peasy-video-go)](https://goreportcard.com/report/github.com/peasytools/peasy-video-go)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Go client for the [PeasyVideo](https://peasyvideo.com) API — video trim, resize, thumbnails, and GIF creation. Zero dependencies beyond the Go standard library.

Built from [PeasyVideo](https://peasyvideo.com), a comprehensive video processing toolkit offering free online tools for trimming, resizing, generating thumbnails, and creating GIFs from video files with detailed format guides and glossary.

> **Try the interactive tools at [peasyvideo.com](https://peasyvideo.com)** — [Video Tools](https://peasyvideo.com/), [Video Glossary](https://peasyvideo.com/glossary/), [Video Guides](https://peasyvideo.com/guides/)

## Install

```bash
go get github.com/peasytools/peasy-video-go
```

Requires Go 1.21+.

## Quick Start

```go
package main

import (
	"context"
	"fmt"
	"log"

	peasyvideo "github.com/peasytools/peasy-video-go"
)

func main() {
	client := peasyvideo.New()
	ctx := context.Background()

	// List available video tools
	tools, err := client.ListTools(ctx, nil)
	if err != nil {
		log.Fatal(err)
	}
	for _, t := range tools.Results {
		fmt.Printf("%s: %s\n", t.Name, t.Description)
	}
}
```

## API Client

The client wraps the [PeasyVideo REST API](https://peasyvideo.com/developers/) with typed Go structs and zero external dependencies.

```go
client := peasyvideo.New()
// Or with a custom base URL:
// client := peasyvideo.New(peasyvideo.WithBaseURL("https://custom.example.com"))
ctx := context.Background()

// List tools with pagination
tools, _ := client.ListTools(ctx, &peasyvideo.ListOptions{Page: 1, Limit: 10})

// Get a specific tool by slug
tool, _ := client.GetTool(ctx, "video-trim")
fmt.Println(tool.Name, tool.Description)

// Search across all content
results, _ := client.Search(ctx, "resize mp4", nil)
fmt.Printf("Found %d tools\n", len(results.Results.Tools))

// Browse the glossary
glossary, _ := client.ListGlossary(ctx, &peasyvideo.ListOptions{Search: str("codec")})
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition)
}

// Discover guides
guides, _ := client.ListGuides(ctx, &peasyvideo.ListGuidesOptions{Category: str("encoding")})
for _, g := range guides.Results {
	fmt.Printf("%s (%s)\n", g.Title, g.AudienceLevel)
}

// List file format conversions
conversions, _ := client.ListConversions(ctx, &peasyvideo.ListConversionsOptions{Source: str("mp4")})

// Get format details
format, _ := client.GetFormat(ctx, "webm")
fmt.Printf("%s (%s): %s\n", format.Name, format.Extension, format.MimeType)
```

Helper for optional string parameters:

```go
func str(s string) *string { return &s }
```

### Available Methods

| Method | Description |
|--------|-------------|
| `ListTools(ctx, opts)` | List tools (paginated, filterable) |
| `GetTool(ctx, slug)` | Get tool by slug |
| `ListCategories(ctx, opts)` | List tool categories |
| `ListFormats(ctx, opts)` | List file formats |
| `GetFormat(ctx, slug)` | Get format by slug |
| `ListConversions(ctx, opts)` | List format conversions |
| `ListGlossary(ctx, opts)` | List glossary terms |
| `GetGlossaryTerm(ctx, slug)` | Get glossary term |
| `ListGuides(ctx, opts)` | List guides |
| `GetGuide(ctx, slug)` | Get guide by slug |
| `ListUseCases(ctx, opts)` | List use cases |
| `Search(ctx, query, limit)` | Search across all content |
| `ListSites(ctx)` | List Peasy sites |
| `OpenAPISpec(ctx)` | Get OpenAPI specification |

Full API documentation at [peasyvideo.com/developers/](https://peasyvideo.com/developers/).
OpenAPI 3.1.0 spec: [peasyvideo.com/api/openapi.json](https://peasyvideo.com/api/openapi.json).

## Learn More

- **Tools**: [Video Trim](https://peasyvideo.com/tools/video-trim/) · [Video Resize](https://peasyvideo.com/tools/video-resize/) · [Video to GIF](https://peasyvideo.com/tools/video-to-gif/) · [All Tools](https://peasyvideo.com/)
- **Guides**: [Video Encoding Guide](https://peasyvideo.com/guides/video-encoding/) · [All Guides](https://peasyvideo.com/guides/)
- **Glossary**: [MP4](https://peasyvideo.com/glossary/mp4/) · [WebM](https://peasyvideo.com/glossary/webm/) · [All Terms](https://peasyvideo.com/glossary/)
- **Formats**: [MP4](https://peasyvideo.com/formats/mp4/) · [WebM](https://peasyvideo.com/formats/webm/) · [All Formats](https://peasyvideo.com/formats/)
- **API**: [REST API Docs](https://peasyvideo.com/developers/) · [OpenAPI Spec](https://peasyvideo.com/api/openapi.json)

## Also Available

| Language | Package | Install |
|----------|---------|---------|
| **Python** | [peasy-video](https://pypi.org/project/peasy-video/) | `pip install "peasy-video[all]"` |
| **TypeScript** | [peasy-video](https://www.npmjs.com/package/peasy-video) | `npm install peasy-video` |
| **Rust** | [peasy-video](https://crates.io/crates/peasy-video) | `cargo add peasy-video` |
| **Ruby** | [peasy-video](https://rubygems.org/gems/peasy-video) | `gem install peasy-video` |

## Peasy Developer Tools

Part of the [Peasy Tools](https://peasytools.com) open-source developer ecosystem.

| Package | PyPI | npm | Go | Description |
|---------|------|-----|----|-------------|
| peasy-pdf | [PyPI](https://pypi.org/project/peasy-pdf/) | [npm](https://www.npmjs.com/package/peasy-pdf) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-pdf-go) | PDF merge, split, rotate, compress — [peasypdf.com](https://peasypdf.com) |
| peasy-image | [PyPI](https://pypi.org/project/peasy-image/) | [npm](https://www.npmjs.com/package/peasy-image) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-image-go) | Image resize, crop, convert, compress — [peasyimage.com](https://peasyimage.com) |
| peasy-audio | [PyPI](https://pypi.org/project/peasy-audio/) | [npm](https://www.npmjs.com/package/peasy-audio) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-audio-go) | Audio trim, merge, convert, normalize — [peasyaudio.com](https://peasyaudio.com) |
| **peasy-video** | [PyPI](https://pypi.org/project/peasy-video/) | [npm](https://www.npmjs.com/package/peasy-video) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-video-go) | **Video trim, resize, thumbnails, GIF — [peasyvideo.com](https://peasyvideo.com)** |
| peasy-css | [PyPI](https://pypi.org/project/peasy-css/) | [npm](https://www.npmjs.com/package/peasy-css) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-css-go) | CSS minify, format, analyze — [peasycss.com](https://peasycss.com) |
| peasy-compress | [PyPI](https://pypi.org/project/peasy-compress/) | [npm](https://www.npmjs.com/package/peasy-compress) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-compress-go) | ZIP, TAR, gzip compression — [peasytools.com](https://peasytools.com) |
| peasy-document | [PyPI](https://pypi.org/project/peasy-document/) | [npm](https://www.npmjs.com/package/peasy-document) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-document-go) | Markdown, HTML, CSV, JSON conversion — [peasyformats.com](https://peasyformats.com) |
| peasytext | [PyPI](https://pypi.org/project/peasytext/) | [npm](https://www.npmjs.com/package/peasytext) | [Go](https://pkg.go.dev/github.com/peasytools/peasytext-go) | Text case conversion, slugify, word count — [peasytext.com](https://peasytext.com) |

## License

MIT
