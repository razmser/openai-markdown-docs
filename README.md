# OpenAI Docs Scraper

Scrapes OpenAI documentation pages and converts them to markdown format.

## Setup

```bash
pip install -r requirements.txt
playwright install chromium  # Required for JavaScript-rendered pages
```

## Usage

```bash
python scrape_openai_docs.py
```

The script will process multiple documentation pages:
1. Download each page once and cache it in `scraped/` directory
2. Parse the HTML and convert to markdown
3. Save the results to `docs/` directory

### Output Structure

```
scraped/
  responses.html
  responses-streaming.html
docs/
  responses.md
  responses-streaming.md
```

On subsequent runs, it will use cached HTML files, allowing you to iterate on the parsing logic without re-downloading.

## Configured Pages

The script currently processes:
- `responses` - Main Responses API reference
- `responses-streaming` - Streaming events documentation

To add more pages, edit the `PAGES_TO_SCRAPE` list in `scrape_openai_docs.py`.

## Customization

The script handles two different page structures:
- **Endpoint pages** - Standard API reference pages with `param-section` structure
- **Streaming pages** - Event-based pages with `div.section` + `div.endpoint` structure

Edit `parse_streaming_page()` or `parse_endpoint_page()` functions to adjust parsing for specific page types.
