# Frigate Timelapse Generator

A web-based tool for generating high-speed timelapse videos from your Frigate NVR footage. Connect to your Frigate instance, select footage from specific time ranges or existing exports, and create accelerated timelapse videos with customizable speed and framerate.

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![Flask](https://img.shields.io/badge/Flask-3.0.0-green.svg)
![Docker](https://img.shields.io/badge/Docker-Ready-blue.svg)
![License](https://img.shields.io/badge/License-MIT-yellow.svg)

## Features

- **Dual Input Modes**: Create new exports from specific time ranges or use existing Frigate exports
- **Camera Selection**: Browse and select from available cameras in your Frigate instance
- **Customizable Speed**: Adjust timelapse speed from 1x to 5000x
- **Configurable FPS**: Set output framerate from 15 to 60 FPS
- **Real-time Progress**: Monitor export, download, and processing stages with visual feedback
- **Job Management**: Cancel running jobs at any time
- **Dockerized**: Ready-to-run Docker container with FFmpeg included

## Quick Start

### Using Docker

```bash
# Clone the repository
git clone <repository-url>
cd frigate-timelapse

# Build and run with Docker
docker build -t frigate-timelapse .
docker run -p 5000:5000 frigate-timelapse
```

Access the application at `http://localhost:5000`

### Manual Installation

```bash
# Install dependencies
pip install -r requirements.txt

# Ensure FFmpeg is installed on your system
# macOS: brew install ffmpeg
# Ubuntu: sudo apt-get install ffmpeg

# Run the application
python app.py
```

## Usage

1. **Connect to Frigate**: Enter your Frigate URL (e.g., `http://192.168.1.50:5000`)
2. **Select Input Method**:
   - **Create New**: Choose a camera and set start/end times
   - **Use Existing Export**: Select from available exports in Frigate
3. **Configure Settings**:
   - **Speed**: How fast to accelerate the video (1x - 5000x)
   - **FPS**: Output video framerate (15 - 60 FPS)
4. **Generate**: Click "Generate Timelapse" and wait for processing
5. **Download**: Once complete, download your timelapse video

## API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/` | GET | Main web interface |
| `/api/cameras` | POST | List available cameras |
| `/api/exports` | POST | List existing exports |
| `/api/create` | POST | Create new timelapse job |
| `/api/status/<job_id>` | GET | Get job status |
| `/api/cancel/<job_id>` | POST | Cancel running job |
| `/download/<job_id>` | GET | Download completed timelapse |

## Configuration

The application stores temporary files in `/app/temp`. When running in Docker, this is an internal directory. For production use with persistent storage, you may want to mount a volume:

```bash
docker run -p 5000:5000 -v /path/to/temp:/app/temp frigate-timelapse
```

## Dependencies

- **Flask 3.0.0**: Web framework
- **requests 2.31.0**: HTTP client for Frigate API
- **ffmpeg-python 0.2.0**: FFmpeg bindings for video processing
- **FFmpeg**: Video processing engine (installed in Docker image)

## Development

```bash
# Install development dependencies
pip install -r requirements.txt

# Run in debug mode
FLASK_DEBUG=1 python app.py
```

## Docker Build

```bash
# Build image
docker build -t frigate-timelapse:latest .

# Run with environment variables
docker run -p 5000:5000 \
  -e FLASK_ENV=production \
  frigate-timelapse:latest
```

## Technical Details

- **Backend**: Flask application with threading for background job processing
- **Frontend**: Vanilla HTML/CSS/JS with Tailwind CSS
- **Video Processing**: FFmpeg with setpts filter for timelapse generation
- **State Management**: In-memory job tracking (consider Redis for production)

## Limitations

- Jobs are stored in memory and will be lost if the server restarts
- Large video files may require significant disk space in `/app/temp`
- Export completion timeout is set to 10 minutes

## License

MIT License - Feel free to use and modify as needed.

## Troubleshooting

**Connection Issues**: Ensure the Frigate URL is accessible from the container/server

**Export Timeouts**: For large time ranges, exports may take longer than 10 minutes - consider using smaller ranges

**FFmpeg Errors**: Check container logs for detailed error messages during processing
