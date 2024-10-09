# CurseCut API Documentation

Base URL: `https://api.cursecut.com/api`

Authentication: All endpoints require an API key to be provided in the `X-API-Key` header.

## Endpoints

### 1. Clean Audio

Clean and censor an .mp3 file. I would advise to wait for the response and saving the task id before sending another request.

**URL:** `/audio`

**Method:** `POST`

**Request Body:**

```ts
{
  "audio_file_url": "string",
  "approx_censor_list": ["string"],
  "exact_censor_list": ["string"],
  "callback_url": "string",
  "language": "en",
  "censor_method": {
    "method": "string",
    "freq": "integer",
    "vol": "float"
  },
  "offsets": {
    "start": "float",
    "end": "float"
  }
}
```

- `audio_file_url` (required): URL of the audio file to be cleaned.
- `approx_censor_list` (optional if exact censor list is included and non-empty): List of words to be approximately censored.
- `exact_censor_list` (optional if approx censor list is included and non-empty): List of words to be exactly censored.
- `callback_url` (optional): URL to receive job status updates.
- `language` (required): "en".
- `censor_method` (required): Method to use for censoring (currently supports "bleep" method).
  - `method`: Censoring method ("bleep").
  - `freq`: Frequency of the bleep sound (range: 0-20000).
  - `vol`: Volume of the bleep sound (range: 0-1).
- `offsets` (optional): Start and end offsets for processing in percentages of word length.
  - `start`: Start offset (range: 0-1).
  - `end`: End offset (range: 0-1).

**Response:**

```ts
{
  "message": "Job started",
  "task_id": "string",
  "original_file_name": "string",
  "audio_duration": "float",
  "cost": "float"
}
```

### 2. Get Job Status

Retrieve the status of a specific job.

**URL:** `/get-job`

In case one of the jobs has status "failed" please contact me because it won't refund usage automaticallly as I would need to investigate why it failed first so the API isn't spammed.

**Method:** `GET`

**Query Parameters:**

- `task_id` (required): ID of the task to retrieve.

**Response:**

```ts
{
  "status": "processing" | "failed" | "succeeded",
  "original_file_name": "string",
  "file_url": "string" | null,
  "removed_words": array of objects with word and start and end times.
}
```

### 3. Get All Jobs

Retrieve all jobs for the authenticated user.

**URL:** `/all-jobs`

**Method:** `GET`

**Query Parameters:**

- `include_completed` (optional): Boolean to include completed jobs (default: false).

**Response:**

```ts
[
  {
    "file_url": "string"  | null,
    "status": "processing" | "failed" | "succeeded",
    "task_id": "string",
    "original_file_name": "string",
    "removed_words": "array"
  }
]
```

### 4. Estimate Cost

Estimate the credit cost for processing an audio file.

**URL:** `/cost`

**Method:** `GET`

**Query Parameters:**

- `duration` (required): Duration of the audio file in seconds.

**Response:**

```json
{
  "credit_cost": "float"
}
```

### 5. Get Remaining Credits

Retrieve the remaining credits for the authenticated user.

**URL:** `/credits`

**Method:** `GET`

**Response:**

```json
{
  "remaining_credits": "float"
}
```

## Error Responses

In case of errors, the API will return appropriate HTTP status codes along with error details in the response body.

Example error response:

```json
{
  "detail": "Error message"
}
```

Common error status codes:
- 400: Bad Request
- 401: Unauthorized (Invalid API key)
- 404: Not Found
