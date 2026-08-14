# Image edit streaming events

> For the complete documentation index, see [llms.txt](https://developers.openai.com/llms.txt). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Stream image generation and editing in real time with server-sent events.
[Learn more about image streaming](https://developers.openai.com/docs/guides/image-generation).

## image_edit.partial_image

Emitted when a partial image is available during image editing streaming.

### Schema

Schema name: `ImageEditPartialImageEvent`

### Example

```json
{
  "type": "image_edit.partial_image",
  "b64_json": "...",
  "created_at": 1620000000,
  "size": "1024x1024",
  "quality": "high",
  "background": "transparent",
  "output_format": "png",
  "partial_image_index": 0
}
```

## image_edit.completed

Emitted when image editing has completed and the final image is available.

### Schema

Schema name: `ImageEditCompletedEvent`

### Example

```json
{
  "type": "image_edit.completed",
  "b64_json": "...",
  "created_at": 1620000000,
  "size": "1024x1024",
  "quality": "high",
  "background": "transparent",
  "output_format": "png",
  "usage": {
    "total_tokens": 100,
    "input_tokens": 50,
    "output_tokens": 50,
    "input_tokens_details": {
      "text_tokens": 10,
      "image_tokens": 40
    }
  }
}
```
