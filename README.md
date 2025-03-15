# ppt-generator
# YouTube to PowerPoint Generator

This tool automates the process of extracting frames from YouTube videos and generating PowerPoint presentations from them. It's particularly useful for creating slide decks from educational videos, tutorials, or presentations that are available on YouTube.

## Features

- Download YouTube videos using the URL
- Extract frames at specified intervals
- Filter out duplicate or similar frames
- Store frames in a SQLite database
- Generate PowerPoint presentations with extracted frames
- Supports text extraction from frames using Tesseract OCR
- Offers both agent-based and non-agent approaches for processing

## Prerequisites

The following packages need to be installed:

```
pip install opencv-python
pip install yt-dlp
pip install python-pptx
pip install pytesseract
pip install pytube
pip install crewai
pip install crewai[tools]
pip install openai
pip install langchain-openai
pip install Pillow
```

Additionally, you'll need:

- FFmpeg (for video processing)
- Tesseract OCR (for text extraction from images)

## Project Structure

The project consists of several key components:

1. **Video Processing**
   - Download YouTube videos
   - Extract frames at specific intervals
   - Store frames in SQLite database

2. **Image Processing**
   - Detect and filter duplicate frames
   - Extract text from frames using OCR
   - Analyze frame content

3. **PowerPoint Generation**
   - Create slides from extracted frames
   - Format slides to properly display frames

4. **Agent-based Approach**
   - Uses CrewAI framework for task automation
   - Defines agents for video processing and PPT generation
   - Coordinates tasks between agents

## Usage

### Agent-based Approach

```python
# Define video processor agent
video_processor_agent = Agent(
    role='Video Processor',
    goal='Process YouTube videos to extract and manage unique frames',
    verbose=True,
    memory=True,
    backstory=(
        "You are a diligent video processing expert with a knack for extracting and managing unique frames from YouTube videos."
    ),
    tools=[youtube_video_processing_tool]
)

# Define PPT generator agent
ppt_generator_agent = Agent(
    role='PPT Generator',
    goal='Generate a PowerPoint presentation from images stored in the database',
    verbose=True,
    memory=True,
    backstory=(
        "You are an expert in creating compelling PowerPoint presentations."
    ),
    tools=[ppt_generator_tool]
)

# Process videos and generate presentation
video_processing_crew = Crew(
    agents=[video_processor_agent],
    tasks=[video_processing_task],
    process=Process.sequential
)

result = video_processing_crew.kickoff(inputs={
    'video_urls': ['https://www.youtube.com/watch?v=YOUR_VIDEO_ID'],
    'interval': 3,
    'threshold': 0.4
})

# Generate PowerPoint
ppt_generation_crew = Crew(
    agents=[ppt_generator_agent],
    tasks=[ppt_generation_task],
    process=Process.sequential
)

result = ppt_generation_crew.kickoff()
```

### Non-Agent Approach

```python
# Download and process video
video_url = 'https://www.youtube.com/watch?v=YOUR_VIDEO_ID'
video_path = download_youtube_video(video_url)
detect_unique_screenshots(video_path, interval=3)

# Generate PowerPoint
images = fetch_images_from_db()
prs = create_ppt(images)
ppt_path = 'presentation.pptx'
prs.save(ppt_path)
```

## API Reference

### Video Processing Functions

- `download_youtube_video(url)`: Downloads a YouTube video using yt-dlp
- `clear_database()`: Clears the image database
- `get_frames(video_path)`: Returns frames from a video at specified intervals
- `detect_unique_screenshots(video_path, interval)`: Captures frames and stores them in database

### Image Processing Functions

- `insert_image_into_db(filename, image)`: Inserts an image into the SQLite database
- `fetch_images_from_db()`: Retrieves all images from the database
- `extract_and_filter_images()`: Filters out duplicate images using text comparison
- `find_duplicates(embeddings, threshold)`: Finds duplicate images using feature embeddings

### PowerPoint Generation Functions

- `create_ppt(images)`: Creates PowerPoint presentation from images
- `ppt_generator_tool()`: CrewAI tool for generating PowerPoint presentations

## Configuration Parameters

- `FRAME_RATE`: Number of frames per second to process
- `INTERVAL`: Time between frame captures (in seconds)
- `THRESHOLD`: Similarity threshold for duplicate detection

## OpenAI Integration

The project uses OpenAI's API for the agent-based approach. Set your API key as follows:

```python
from google.colab import userdata
OPENAI_API_KEY = userdata.get('openai-key')
os.environ['OPENAI_API_KEY'] = OPENAI_API_KEY
```

## Limitations

- Processing very long videos may require significant time and resources
- Text extraction quality depends on the clarity of text in video frames
- Similarity detection may sometimes miss duplicate frames or remove unique ones

## Future Improvements

- Add support for batch processing multiple videos
- Improve duplicate detection algorithms
- Add slide formatting options and themes
- Implement custom slide ordering based on content
- Add support for other video sources besides YouTube
