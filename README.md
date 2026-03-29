# peasy-video-go

[![Go Reference](https://pkg.go.dev/badge/github.com/peasytools/peasy-video-go.svg)](https://pkg.go.dev/github.com/peasytools/peasy-video-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/peasytools/peasy-video-go)](https://goreportcard.com/report/github.com/peasytools/peasy-video-go)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![GitHub stars](https://agentgif.com/badge/github/peasytools/peasy-video-go/stars.svg)](https://github.com/peasytools/peasy-video-go)

Go client for the [PeasyVideo](https://peasyvideo.com) API — calculate resolution, estimate bitrate, and analyze frame rates for video files. Built with `net/http`, `encoding/json`, and zero external dependencies.

Built from [PeasyVideo](https://peasyvideo.com), a comprehensive video processing toolkit offering free online tools for trimming, resizing, generating thumbnails, and creating GIFs from video files. The site includes in-depth guides on video codec selection, compression strategies for web delivery, and a glossary covering concepts from H.264 encoding and frame rates to container formats and color grading.

> **Try the interactive tools at [peasyvideo.com](https://peasyvideo.com)** — [Video Resolution Calculator](https://peasyvideo.com/video/video-resolution/), [Video Bitrate Calculator](https://peasyvideo.com/video/video-bitrate/), [Video Framerate Converter](https://peasyvideo.com/video/video-framerate/), and more.

<p align="center">
  <a href="https://agentgif.com/C9jQVzN3"><img src="https://media.agentgif.com/C9jQVzN3.gif" alt="peasy-video-go demo — video resolution calculation and codec analysis tools in Go terminal" width="800"></a>
</p>

## Table of Contents

- [Install](#install)
- [Quick Start](#quick-start)
- [What You Can Do](#what-you-can-do)
  - [Video Analysis Tools](#video-analysis-tools)
  - [Browse Reference Content](#browse-reference-content)
  - [Search and Discovery](#search-and-discovery)
- [API Client](#api-client)
  - [Available Methods](#available-methods)
- [Learn More About Video Tools](#learn-more-about-video-tools)
- [Also Available](#also-available)
- [Peasy Developer Tools](#peasy-developer-tools)
- [License](#license)

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

## What You Can Do

### Video Analysis Tools

Digital video combines spatial resolution (the number of pixels in each frame) with temporal resolution (the number of frames displayed per second) to create the illusion of motion. A 1080p video at 30 fps produces 30 full 1920x1080 frames every second — over 62 million pixels per second of raw data. Codecs like H.264 (AVC) and H.265 (HEVC) use inter-frame prediction, motion compensation, and transform coding to compress this data by 100-1000x, while newer codecs like AV1 push efficiency even further at the cost of encoding time. PeasyVideo provides calculators and analysis tools for understanding these encoding parameters.

| Tool | Slug | Description |
|------|------|-------------|
| Resolution Calculator | `video-resolution` | Calculate pixel counts, aspect ratios, and display dimensions |
| Bitrate Calculator | `video-bitrate` | Estimate file sizes for different bitrate and duration combinations |
| Framerate Converter | `video-framerate` | Analyze frame rate conversions and motion smoothness trade-offs |

```go
// Retrieve video analysis tools and explore their capabilities
client := peasyvideo.New()
ctx := context.Background()

// Get the resolution calculator for pixel and aspect ratio analysis
tool, err := client.GetTool(ctx, "video-resolution")
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Tool: %s\n", tool.Name)         // Video resolution calculator name
fmt.Printf("Description: %s\n", tool.Description) // How resolution calculation works

// List all available video tools with pagination
tools, err := client.ListTools(ctx, &peasyvideo.ListOptions{Page: 1, Limit: 20})
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Total video tools available: %d\n", tools.Count)
```

Learn more: [Video Resolution Calculator](https://peasyvideo.com/video/video-resolution/) · [Video Codecs Explained](https://peasyvideo.com/guides/video-codecs-explained/) · [Video Compression for Web Delivery](https://peasyvideo.com/guides/video-compression-web-delivery/)

### Browse Reference Content

PeasyVideo includes a comprehensive glossary of video engineering terminology and practical guides for common workflows. The glossary covers foundational concepts like H.264 (the most widely deployed video codec, used in everything from Blu-ray discs to web streaming), frame rate (the number of individual frames displayed per second, typically 24 fps for cinema, 30 fps for broadcast TV, and 60 fps for gaming), container formats (MP4 and WebM wrap encoded video and audio streams into a single file), and color grading (the process of altering the color and tone of footage for creative or corrective purposes).

| Term | Description |
|------|-------------|
| [AV1](https://peasyvideo.com/glossary/av1/) | AOMedia Video 1 — royalty-free codec with 30% better compression than H.265 |
| [Frame Rate](https://peasyvideo.com/glossary/frame-rate/) | Frames per second — 24 fps (cinema), 30 fps (broadcast), 60 fps (gaming) |
| [Color Grading](https://peasyvideo.com/glossary/color-grading/) | Creative color adjustment for tone, mood, and visual consistency |

```go
// Browse the video glossary for codec and encoding terminology
glossary, err := client.ListGlossary(ctx, &peasyvideo.ListOptions{
	Search: str("codec"), // Search for video encoding concepts
})
if err != nil {
	log.Fatal(err)
}
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition)
}

// Read a guide explaining video codec selection and trade-offs
guide, err := client.GetGuide(ctx, "video-codecs-explained")
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Guide: %s (Level: %s)\n", guide.Title, guide.AudienceLevel)
```

Learn more: [Video Glossary](https://peasyvideo.com/glossary/) · [Video Codecs Explained](https://peasyvideo.com/guides/video-codecs-explained/) · [Video Compression for Web Delivery](https://peasyvideo.com/guides/video-compression-web-delivery/)

### Search and Discovery

The API supports full-text search across all content types — tools, glossary terms, guides, use cases, and format documentation. Search results are grouped by content type, making it easy to find the right tool or reference for any video workflow.

```go
// Search across all video content — tools, glossary, guides, and formats
results, err := client.Search(ctx, "convert mp4", nil)
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Found %d tools, %d glossary terms, %d guides\n",
	len(results.Results.Tools),
	len(results.Results.Glossary),
	len(results.Results.Guides),
)

// Discover format conversion paths — what can WebM convert to?
conversions, err := client.ListConversions(ctx, &peasyvideo.ListConversionsOptions{
	Source: str("webm"), // Find all formats WebM can be converted to
})
if err != nil {
	log.Fatal(err)
}
for _, c := range conversions.Results {
	fmt.Printf("%s → %s\n", c.SourceFormat, c.TargetFormat)
}
```

Learn more: [REST API Docs](https://peasyvideo.com/developers/) · [All Video Tools](https://peasyvideo.com/)

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

## Learn More About Video Tools

- **Tools**: [Video Resolution Calculator](https://peasyvideo.com/video/video-resolution/) · [Video Bitrate Calculator](https://peasyvideo.com/video/video-bitrate/) · [Video Framerate Converter](https://peasyvideo.com/video/video-framerate/) · [All Tools](https://peasyvideo.com/)
- **Guides**: [Video Codecs Explained](https://peasyvideo.com/guides/video-codecs-explained/) · [Video Compression for Web Delivery](https://peasyvideo.com/guides/video-compression-web-delivery/) · [All Guides](https://peasyvideo.com/guides/)
- **Glossary**: [AV1](https://peasyvideo.com/glossary/av1/) · [Frame Rate](https://peasyvideo.com/glossary/frame-rate/) · [Color Grading](https://peasyvideo.com/glossary/color-grading/) · [All Terms](https://peasyvideo.com/glossary/)
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

## Embed Widget

Embed [PeasyVideo](https://peasyvideo.com) widgets on any website with [peasy-video-embed](https://widget.peasyvideo.com):

```html
<script src="https://cdn.jsdelivr.net/npm/peasy-video-embed@1/dist/embed.min.js"></script>
<div data-peasyvideo="entity" data-slug="example"></div>
```

Zero dependencies · Shadow DOM · 4 themes (light/dark/sepia/auto) · [Widget docs](https://widget.peasyvideo.com)

## License

MIT
