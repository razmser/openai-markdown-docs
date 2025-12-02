# OpenAI API Reference in Markdown Format

If you're looking for the documentation, check the `docs/` folder.

## Why?

Sometimes we want to use documentation as input for coding agents. In this case, Markdown format is the best solution.
Anthropic allows you to copy their docs in Markdown format. OpenAI only has a website with a complex structure.

## Scraper

This tool scrapes OpenAI documentation pages and converts them to Markdown format.

### Setup

```bash
pip install -r requirements.txt
playwright install chromium  # Required for JavaScript-rendered pages
```

### Usage

```bash
python scrape_openai_docs.py
```

The script will process multiple documentation pages:
1. Download each page once and cache it in the `scraped/` directory (or reuse already saved ones)
2. Parse the HTML and convert to Markdown
3. Save the results to the `docs/` directory

### Output Structure

```
scraped/
  conversations.html
  chat.html
  chat-streaming.html
  responses.html
  responses-streaming.html
docs/
  conversations.md
  chat.md
  chat-streaming.md
  responses.md
  responses-streaming.md
```

On subsequent runs, the script will use cached HTML files, allowing you to iterate on the parsing logic without re-downloading.

