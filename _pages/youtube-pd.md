---
title: "YouTube PD"
permalink: /youtube-pd
layout: single
toc: true
toc_sticky: true
---

# YouTube PD

A personal automation tool that converts a daily economics blog post and related news data into a YouTube video and publishes it to the author's own YouTube channel.

This is a **personal project** built and operated by one individual for their own YouTube channel. It is not a public service and does not onboard other users.

## What it does

Every morning before market open, the pipeline:

1. Fetches the author's own blog post and related news articles from a private API
2. Rewrites the content into an English YouTube script (~1,500 words, ~10 minutes)
3. Generates narration audio with a text-to-speech model
4. Assembles a video (images + captions + audio) with FFmpeg
5. Uploads the finished video to the author's YouTube channel via the YouTube Data API

## Who uses it

Only the developer (Young-Won Kim). The tool runs unattended on a personal machine and uploads exclusively to a single YouTube channel owned by the developer.

## Why OAuth is required

The YouTube Data API requires OAuth 2.0 authorization for any video upload. Because the pipeline runs unattended every day, the app needs verified status so that refresh tokens do not expire after 7 days. The app requests a single sensitive scope:

- `https://www.googleapis.com/auth/youtube.upload` — to upload videos to the developer's own YouTube channel.

No other users authenticate with this app. No third-party data is collected or processed.

## Developer

- **Name**: Young-Won Kim
- **Email**: [gousekid@gmail.com](mailto:gousekid@gmail.com)

## Links

- [Privacy Policy](/youtube-pd/privacy)
- [Terms of Service](/youtube-pd/terms)
