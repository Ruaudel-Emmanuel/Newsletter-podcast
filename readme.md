# Newsletter to Audio Template (n8n Workflow)

This project is an n8n workflow that converts written newsletters from your inbox into a conversational audio podcast between two AI voices.[^1]

## Overview

The workflow automatically:[^1]

- Fetches unread newsletter emails from Gmail (or another source such as a webhook).[^1]
- Sends the content to an LLM (GPT‑4o Mini) to generate a long, natural dialogue script between two personas.[^1]
- Splits the script into speaker turns and generates audio files with a TTS service (e.g. ElevenLabs or Google TTS).[^1]
- Merges all audio chunks into a single MP3 using FFmpeg.[^1]
- Emails the final audio file back to you as an attachment.[^1]

This template is inspired by Google’s NotebookLM style of transforming dense content into conversational, easy‑to‑listen summaries.[^1]

## Features

- **Automated newsletter intake**: Polls Gmail for unread messages from specific senders using search filters such as `from:demandcurve.com`.[^1]
- **Dialogue generation**: Uses an OpenAI Chat node (GPT‑4o Mini) to turn the newsletter body into a human‑sounding conversation between `voice1` (curious, expressive) and `voice2` (analytical, calm).[^1]
- **Long‑form content**: Prompt enforces at least 10,000 characters of output for ~10–15 minutes of audio.[^1]
- **Speaker‑aware splitting**: A code node splits the script into segments keyed by `voice1:` and `voice2:` so each part can be voiced separately.[^1]
- **Audio merging**: Generated audio chunks are concatenated via FFmpeg using a `concat_list.txt` file and cleaned up after merging.[^1]
- **Delivery by email**: The final `final_merged.mp3` is attached and sent back to the original recipient via Gmail.[^1]


## Prerequisites

- n8n instance (self‑hosted or cloud).[^1]
- Gmail account and Gmail OAuth2 credentials configured in n8n.[^1]
- OpenAI API key configured in n8n (for the `@n8n/n8n-nodes-langchain.openAi` node).[^1]
- TTS provider credentials (e.g. ElevenLabs, Google Cloud TTS) configured in the corresponding nodes.[^1]
- FFmpeg installed and available in the environment where n8n runs.[^1]


## Installation

1. Export and copy the `newsletter-podcast.json` file into your project or import it directly in the n8n UI as a workflow.[^1]
2. Open the workflow in n8n.[^1]
3. Configure the following credentials:
    - Gmail OAuth2 credential on the “Get Newsletter” and “Send audio” nodes.[^1]
    - OpenAI credential on the “Generate Dialogue Script” node.[^1]
    - TTS provider credentials on the TTS nodes for both voices.[^1]
4. Ensure the file paths used by the “Save Audio Chucks”, “Generate `concat_list.txt`”, “Join audio chucks and delete all files”, and “read final_merged” nodes exist and are writable (for example `/newsletter2podcast/tmp/`).[^1]
5. Activate the workflow when you are ready to start processing newsletters.[^1]

## Configuration

You can customize several aspects of the workflow:[^1]

- **Email filter**
    - In the “Get Newsletter” Gmail trigger, change the `filters.q` value (e.g. `from:substack.com`, by subject, by label, etc.).[^1]
- **Content source**
    - Replace the Gmail trigger with a Webhook node if you want to send HTML, text, or URLs from your own app.[^1]
- **Prompt \& personas**
    - Edit the system prompt in “Generate Dialogue Script” to adjust tone, length, structure, or speaker personalities.[^1]
- **Voices**
    - Map `voice1` and `voice2` segments to different voices or languages in your TTS nodes.[^1]
- **Output \& delivery**
    - Change the output file name or path (`final_merged.mp3`).[^1]
    - Modify the “Send audio” node to send via a different email, or replace it with Slack, Telegram, Drive, etc.[^1]


## How It Works (High Level)

1. **Get Newsletter**
    - Gmail Trigger node fetches new emails that match the configured filter.[^1]
    - The node exposes the newsletter text under `json.text`.[^1]
2. **Generate Dialogue Script**
    - The OpenAI node uses GPT‑4o Mini with a detailed prompt to produce a long, continuous dialogue with tags `voice1:` and `voice2:`.[^1]
3. **Split Script**
    - A Code node normalizes line breaks and splits the script into an array of segments, one per speaker turn.[^1]
4. **Prepare and Generate TTS**
    - Segments are cleaned and routed through an `If` node to decide which voice to use.[^1]
    - Separate Function nodes prepare the final TTS input for each voice.[^1]
    - TTS nodes generate one audio file per segment.[^1]
5. **Merge Audio and Clean Up**
    - A Function node generates `concat_list.txt` listing all partial MP3 files.[^1]
    - A Command/Exec node runs FFmpeg to merge them into `final_merged.mp3` and optionally delete intermediate chunks.[^1]
6. **Read and Send Audio**
    - A “Read Binary File” node loads the merged file into n8n.[^1]
    - The “Send audio” Gmail node sends the MP3 as an attachment to the original email recipient with a dynamic subject line.[^1]

## Notes and Tips

- You can easily adapt this workflow to process other long‑form content such as blog posts, documentation, or reports.[^1]
- To debug issues, start by testing each section independently: newsletter fetch, script generation, TTS, then FFmpeg merge.[^1]
- For production use, consider adding logging, error handling, and rate‑limit controls around the LLM and TTS calls.[^1]


## Support and Contact

This template was originally documented with a support contact at `Luis.acosta@news2podcast.com` for more advanced audio and AI setups (e.g. publishing automatically to podcast platforms).[^1]

Would you like a shorter “marketing-style” README version for your public GitHub repository, or is this more for internal documentation?

<div align="center">⁂</div>

[^1]: newsletter-podcast.json

