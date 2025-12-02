# OpenAI Docs Scraper

Scrapes OpenAI documentation pages and converts them to markdown format.

## Setup

```bash
pip install -r requirements.txt
```

## Usage

```bash
python scrape_openai_docs.py
```

The script will:
1. Download the page once and cache it as `cached_page.html`
2. Parse the HTML and convert to markdown
3. Save the result to `openai_docs.md`

On subsequent runs, it will use the cached HTML file, allowing you to iterate on the parsing logic without re-downloading.

## Customization

Edit the `parse_openai_docs()` function in `scrape_openai_docs.py` to adjust how the HTML is parsed and converted to markdown.
