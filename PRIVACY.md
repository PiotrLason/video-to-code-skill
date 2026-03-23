# Privacy Policy

## Data Processing

Video processing (keyframe extraction and audio transcription) runs **locally on your machine** using OpenCV and MLX-Whisper/OpenAI-Whisper. No external APIs are called during this step.

However, the extracted keyframe images, transcripts, and summaries are then **read into the Claude Code conversation context** and sent to **Anthropic's API** for analysis. This means content from your video — including screenshots and spoken words — is transmitted to Anthropic's servers and subject to [Anthropic's privacy policy](https://www.anthropic.com/privacy) and [terms of service](https://www.anthropic.com/terms).

## Data Storage

Processed outputs (keyframes, transcripts, summaries) are stored locally in `~/video-to-code-skill-storage/`. You have full control over this data and can delete it at any time.

## What This Plugin Does NOT Do

- Does not collect, store, or transmit data to any service other than Anthropic (via Claude Code)
- Does not make external API calls during video processing
- Does not add any data collection beyond what Claude Code already does

## Contact

For questions about this policy, open an issue at https://github.com/PiotrLason/video-to-code-skill/issues.
