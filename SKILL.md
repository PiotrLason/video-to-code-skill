---
name: video-input-code-skill
description: Analyzes video feedback by extracting key frames and audio transcription. Use when the user mentions video feedback, screen recordings, user recordings, or wants to analyze a video file from ~/video-input-code-skill-storage folder.
license: MIT
compatibility: Requires Python 3, opencv-python, numpy, and mlx-whisper (or openai-whisper). macOS recommended for Metal acceleration.
disable-model-invocation: true
metadata:
  author: Piotr Lason & Claudia
  version: "1.0"
---

# Your video recording will be added as a multimodal data bundle to the context window and tokenised as a part of your prompt

Analyze video feedback from `~/video-input-code-skill-storage` folder by extracting key frames and generating audio transcription.

## When to Use

- User mentions "video feedback" or "screen recording"
- User wants to analyze a recorded demo or bug report
- User asks about feedback in `~/video-input-code-skill-storage`

## Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `-d_t`, `-detection_threshold` | Keyframe detection threshold (0-100). Lower values capture more keyframes. | `1` |

Example: `/video-input-code-skill -d_t 5`

## Instructions

Important: You can only modify files in `~/video-input-code-skill-storage` folder. If other files are to be modified outside of this folder, always ask the user for permission.

1. **Notify the user**: Tell them "Analyzing user feedback video from ~/video-input-code-skill-storage - extracting key frames and narration transcript..."

2. **Ensure storage folder exists**:
   ```bash
   mkdir -p ~/video-input-code-skill-storage
   ```

3. **Check and install dependencies** (first run only):
   ```bash
   python3 -c "import cv2" 2>/dev/null || pip install opencv-python numpy
   python3 -c "import mlx_whisper" 2>/dev/null || pip install mlx-whisper
   ```

4. **Find the latest video file** (by modification time):
   ```bash
   find ~/video-input-code-skill-storage -maxdepth 1 -type f \( -name "*.mov" -o -name "*.mp4" -o -name "*.webm" \) -exec ls -t {} + | head -1
   ```

5. **If no video file found**, fall back to the most recent archived analysis:
   ```bash
   ls -dt ~/video-input-code-skill-storage/archive/*/ 2>/dev/null | head -1
   ```
   - If an archived folder exists, read its `analysis.json` and keyframe images — skip to step 8.
   - If no archived analysis exists either, tell the user: **"No current or archived videos to input into the context"** and stop.

6. **Run the analysis script** (use the `-d_t` or `-detection_threshold` parameter value if provided, otherwise default to `1`):
   ```bash
   python3 scripts/video-input-code-skill-processor.py <video_path> -o ~/video-input-code-skill-storage/analysis/<video_name> -t <detection_threshold>
   ```

7. **Read the results**:
   - Read `~/video-input-code-skill-storage/analysis/<video_name>/analysis.json`
   - Read each keyframe image listed in the analysis

8. **Summarize** what the user is demonstrating or reporting

9. **Archive** the analyzed video (skip if using archived analysis from step 5):
   - Create timestamp folder: `~/video-input-code-skill-storage/archive/YYYY-MM-DD_HH-MM-SS/`
   - Move video file and analysis folder to archive

10. **Ask the user** what they would like help with based on the feedback

## Output Format

After analyzing, provide:

- **Video**: filename and duration
- **Summary**: What the user is showing/describing
- **Key moments**: Important timestamps with what's happening
- **User's intent**: What they seem to want help with
- **Questions and requests in the recording**: List all questions and requests from the presenter in the recording. Try to answer the questions.

Then ask: "What would you like me to help you with based on this feedback?"

## Script Reference

The `scripts/video-input-code-skill-processor.py` script accepts these arguments:

| Argument | Description | Default |
|----------|-------------|---------|
| `<video_path>` | Path to video file | Required |
| `-o, --output-dir` | Output directory | `./video_analysis` |
| `-t, --threshold` | Keyframe detection threshold (0-100) | `5` |
| `-m, --model` | Whisper model (tiny, base, small, medium, large) | `base` or `large-v3-turbo` |

## Dependencies

| Package | Purpose |
|---------|---------|
| `opencv-python` | Frame extraction and scene change detection |
| `numpy` | Array operations |
| `mlx-whisper` | Audio transcription (Metal-accelerated, macOS) |
| `openai-whisper` | Audio transcription (fallback) |
