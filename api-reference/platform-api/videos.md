# Videos

Generate videos.

## Create video

Create a new video generation job from a prompt and optional reference assets.

### Example request

```bash
curl https://api.openai.com/v1/videos \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -F "model=sora-2" \
  -F "prompt=A calico cat playing a piano on stage"
```

### Response

```json
{
  "id": "video_123",
  "object": "video",
  "model": "sora-2",
  "status": "queued",
  "progress": 0,
  "created_at": 1712697600,
  "size": "1024x1792",
  "seconds": "8",
  "quality": "standard"
}
```

## Remix video

Create a remix of a completed video using a refreshed prompt.

### Example request

```bash
curl -X POST https://api.openai.com/v1/videos/video_123/remix \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Extend the scene with the cat taking a bow to the cheering audience"
  }'
```

### Response

```json
{
  "id": "video_456",
  "object": "video",
  "model": "sora-2",
  "status": "queued",
  "progress": 0,
  "created_at": 1712698600,
  "size": "720x1280",
  "seconds": "8",
  "remixed_from_video_id": "video_123"
}
```

## List videos

List recently generated videos for the current project.

### Example request

```bash
curl https://api.openai.com/v1/videos \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "data": [
    {
      "id": "video_123",
      "object": "video",
      "model": "sora-2",
      "status": "completed"
    }
  ],
  "object": "list"
}
```

## Retrieve video

Fetch the latest metadata for a generated video.

### Example request

```javascript
import OpenAI from 'openai';

const client = new OpenAI();

const video = await client.videos.retrieve('video_123');

console.log(video.id);
```

## Delete video

Permanently delete a completed or failed video and its stored assets.

### Example request

```javascript
import OpenAI from 'openai';

const client = new OpenAI();

const video = await client.videos.delete('video_123');

console.log(video.id);
```

## Retrieve video content

Download the generated video bytes or a derived preview asset.

### Example request

```javascript
import OpenAI from 'openai';

const client = new OpenAI();

const response = await client.videos.downloadContent('video_123');

console.log(response);

const content = await response.blob();
console.log(content);
```

## Video job

Structured information describing a generated video job.

### Parameters

#### completed_at - integer
Unix timestamp (seconds) for when the job completed, if finished.

#### created_at - integer
Unix timestamp (seconds) for when the job was created.

#### error - object
Error payload that explains why generation failed, if applicable.

- **code - string**
  A machine-readable error code that was returned.

- **message - string**
  A human-readable description of the error that was returned.

#### expires_at - integer
Unix timestamp (seconds) for when the downloadable assets expire, if set.

#### id - string
Unique identifier for the video job.

#### model - string
The video generation model that produced the job.

#### object - string
The object type, which is always `video`.

#### progress - integer
Approximate completion percentage for the generation task.

#### prompt - string
The prompt that was used to generate the video.

#### remixed_from_video_id - string
Identifier of the source video if this video is a remix.

#### seconds - string
Duration of the generated clip in seconds.

#### size - string
The resolution of the generated video.

#### status - string
Current lifecycle status of the video job.