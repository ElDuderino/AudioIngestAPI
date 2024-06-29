# Audio Ingest Service

This application framework is designed to receive PCM encoded audio data from remote sensors and integrate with RabbitMQ for further processing (persistence, AI classification, etc.)

## Project Structure

```
audio_ingest/
├── html/
│   └── index.html
├── routers/
│   ├── __init__.py
│   ├── audio.py
├── config.cfg
├── dependencies.py
├── main.py
└── AudioIngest.log
```

## Setup Instructions

### Prerequisites

Before you begin, ensure you have the following installed:

- Python 3.7+
- RabbitMQ
- pip (Python package installer)

### Installation

1. **Clone the repository:**

    ```sh
    git clone https://your-repo-url.git
    cd audio_ingest
    ```

2. **Install the required Python packages:**

    ```sh
    pip install fastapi uvicorn pika apscheduler
    ```

3. **Set up RabbitMQ:**

    Make sure RabbitMQ is running on your local machine or server.

### Configuration

Create a configuration file `config.cfg` in the project root:

```ini
[DEFAULT]
background_sync_interval_mins = 30
```

### Running the Application

To start the FastAPI application, run:

```sh
uvicorn main:app --reload
```

The service will be available at `http://localhost:8000`.

### Endpoints

#### Upload Audio Data

**URL:** `/upload-audio/{device_id}`

**Method:** `POST`

**Description:** Receives PCM encoded audio data and metadata, and queues the data for processing.

**Request Headers:**

- `Authorization: Bearer <YOUR_TOKEN_HERE>`

**Request Body:**

```json
{
  "audio_payload": {
    "payload": [1, -1, 0, 255, -255], 
    "bit_depth": 16, 
    "channels": 1, 
    "samplerate": 32000
  },
  "timestamp": 12324555224,
  "location": {
    "lat": 2.2232,
    "lng": 2.4422
  }
}
```

**Response:**

```json
{
  "message": "Audio data received and queued for processing"
}
```

### Static Files

Static HTML files are served from the `/html` path. For example, you can access the main page at `http://localhost:8000/html`.

### Logging

Logs are written to `AudioIngest.log` with a rotating file handler to manage log file size and backups.

## Developing

### Adding New Endpoints

To add new endpoints, create a new router file in the `routers` directory and include it in `main.py`.

Example router file (`routers/example.py`):

```python
from fastapi import APIRouter

router = APIRouter()

@router.get("/example")
async def example_endpoint():
    return {"message": "This is an example endpoint"}
```

Include the new router in `main.py`:

```python
from routers.example import router as example_router

app.include_router(example_router)
```

### Scheduled Tasks

Scheduled tasks are managed using APScheduler. Define your tasks in the `scheduled_task` function in `main.py` and configure the interval in `config.cfg`.

### Authentication

The application currently accepts any token provided in the `Authorization` header. Implement actual authorization logic in `dependencies.py`.

## Contributing

Feel free to submit issues or pull requests if you have any improvements or fixes.
