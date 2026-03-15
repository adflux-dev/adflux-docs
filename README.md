# adflux-docs

<div align="center">

![Adflux](https://img.shields.io/badge/Adflux-Documentation-FF4D00?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Private%20Beta-orange?style=for-the-badge)
![NVIDIA Inception](https://img.shields.io/badge/NVIDIA-Inception%20Member-76B900?style=for-the-badge&logo=nvidia)

**Official documentation for the Adflux AI Video Generation Platform.**

[Platform](https://adflux.dev) · [Core Engine](https://github.com/getadflux/adflux-core) · [Inference API](https://github.com/getadflux/adflux-inference)

</div>

---

## What is Adflux?

Adflux is an AI-powered video generation platform built exclusively for marketing agencies. It enables creative teams to generate broadcast-quality video advertisements from natural language prompts — in under 60 seconds.

Built on NVIDIA GPU infrastructure, Adflux uses a custom video diffusion model fine-tuned on advertising creative datasets to produce platform-optimized video content for Meta, Google, TikTok, and LinkedIn.

---

## Quick Start

### 1. Request Access

Adflux is currently in **private beta**. Request access at [adflux.dev](https://adflux.dev).

### 2. Get Your API Key

Once approved, your API key is available in the dashboard under **Settings → API**.

### 3. Generate Your First Video

```python
import requests

response = requests.post(
    "https://api.adflux.dev/v1/generate",
    headers={"Authorization": "Bearer YOUR_API_KEY"},
    json={
        "prompt": "A coffee brand product shot, warm tones, steam rising slowly, cinematic macro lens",
        "duration": 10,
        "resolution": "1080p",
        "aspect_ratios": ["16:9", "9:16"],
        "num_variants": 2
    }
)

job = response.json()
print(f"Job ID: {job['job_id']}")
print(f"Estimated time: {job['estimated_time_seconds']}s")
```

---

## Platform Overview

```
┌─────────────────────────────────────────────────────┐
│                  ADFLUX PLATFORM                     │
├──────────────────┬──────────────────────────────────┤
│   Agency UI      │         API Layer                 │
│   (Web App)      │   api.adflux.dev/v1               │
├──────────────────┴──────────────────────────────────┤
│              adflux-inference                        │
│         TensorRT Serving Pipeline                    │
├─────────────────────────────────────────────────────┤
│              adflux-core                             │
│       Custom Video Diffusion Model                   │
│       NVIDIA A100 / H100 GPUs                       │
├─────────────────────────────────────────────────────┤
│         NVIDIA GPU Infrastructure                    │
│   CUDA 12.x · TensorRT 9.x · cuDNN · NCCL          │
└─────────────────────────────────────────────────────┘
```

---

## Documentation Index

### Getting Started
- [Platform Overview](#platform-overview)
- [API Authentication](#api-authentication)
- [Your First Generation](#quick-start)
- [Understanding Prompts](#prompt-guide)

### Core Concepts
- [Video Generation Pipeline](#video-generation-pipeline)
- [Brand Kit Setup](#brand-kits)
- [Output Formats & Resolutions](#output-formats)
- [Generation Variants](#variants)

### API Reference
- [POST /v1/generate](#generate-video)
- [GET /v1/jobs/{id}](#job-status)
- [GET /v1/brand-kits](#brand-kits-api)
- [POST /v1/brand-kits](#upload-brand-kit)

### Agency Workflows
- [Campaign Brief → Video](#campaign-workflow)
- [Multi-Platform Export](#multi-platform-export)
- [Team Collaboration](#team-collaboration)
- [Client Approval Workflow](#client-approvals)

### Technical
- [GPU Requirements](#gpu-requirements)
- [TensorRT Integration](#tensorrt)
- [Webhook Events](#webhooks)
- [Rate Limits](#rate-limits)

---

## Prompt Guide

Writing great prompts is the key to high-quality video output. Adflux understands advertising-specific language.

### Prompt Structure

```
[Subject] + [Action/Motion] + [Scene/Environment] + [Lighting] + [Camera Style] + [Mood]
```

### Examples by Ad Category

**Product Ad:**
```
A luxury perfume bottle on a black glass surface, slow 360-degree rotation,
dramatic side lighting with soft rim light, macro lens, premium cinematic mood
```

**Fashion Ad:**
```
A model wearing a white linen dress walking through a sunlit wheat field,
golden hour lighting, slow motion, editorial fashion photography style
```

**Tech Product:**
```
A minimalist smartphone floating against a dark background, interface
animations glowing, particle effects, futuristic tech aesthetic
```

**Food & Beverage:**
```
A cold craft beer bottle with condensation droplets, golden liquid pour in
slow motion, dark bar background with bokeh lights, appetite appeal
```

### Prompt Tips

| Do | Don't |
|---|---|
| Be specific about lighting | Use vague terms like "nice" or "good" |
| Mention camera movement | Write full sentences |
| Include mood/tone | Overload with too many concepts |
| Reference visual styles | Reference copyrighted material |

---

## Brand Kits

Brand kits allow Adflux to generate videos that stay consistent with your client's visual identity.

### What You Can Upload

```
brand_kit/
├── logo.png              # PNG with transparency
├── colors.json           # Primary, secondary, accent colors
├── fonts/                # Brand typefaces (TTF/OTF)
│   ├── primary.ttf
│   └── secondary.ttf
└── style_reference.mp4   # Optional: reference video for style
```

### colors.json Format

```json
{
  "primary": "#FF4D00",
  "secondary": "#1A1A2E",
  "accent": "#FF8C42",
  "background": "#06060A",
  "text": "#F0EDE8"
}
```

---

## Output Formats

| Format | Resolution | Aspect Ratio | Best For |
|---|---|---|---|
| Stories | 1080 × 1920 | 9:16 | Instagram Stories, TikTok, YouTube Shorts |
| Feed | 1080 × 1080 | 1:1 | Instagram Feed, Facebook Feed |
| Landscape | 1920 × 1080 | 16:9 | YouTube Pre-roll, LinkedIn |
| Portrait | 1080 × 1350 | 4:5 | Instagram Feed (portrait) |
| Cinema | 3840 × 2160 | 16:9 | 4K broadcast, high-res campaigns |

All formats are generated from a single prompt in one API call.

---

## GPU Requirements

Adflux's generation pipeline requires NVIDIA GPU infrastructure:

| Use Case | Minimum GPU | Recommended GPU |
|---|---|---|
| Development / Testing | NVIDIA RTX 3090 24GB | NVIDIA A100 40GB |
| Production Inference | NVIDIA A10G 24GB | NVIDIA H100 80GB |
| Model Training | 4× NVIDIA A100 80GB | 8× NVIDIA H100 SXM |

Our hosted platform runs exclusively on NVIDIA A100 and H100 GPUs via cloud infrastructure.

---

## Rate Limits

| Plan | Generations/min | Concurrent Jobs | Monthly Videos |
|---|---|---|---|
| Starter | 2 | 1 | 50 |
| Agency | 10 | 4 | Unlimited |
| Enterprise | Custom | Custom | Unlimited |

---

## Webhooks

Adflux sends webhook events to notify your system when generation jobs complete:

```json
{
  "event": "job.completed",
  "job_id": "job_7f3a9c2d",
  "timestamp": "2025-09-14T10:23:41Z",
  "data": {
    "status": "completed",
    "generation_time_seconds": 38.2,
    "variants": [
      {
        "id": "var_001",
        "aspect_ratio": "16:9",
        "resolution": "1080p",
        "url": "https://cdn.adflux.dev/outputs/job_7f3a9c2d/var_001.mp4",
        "expires_at": "2025-10-14T10:23:41Z"
      }
    ]
  }
}
```

---

## About Adflux

Adflux Inc. is an AI video generation company founded in 2025.
We are a proud member of the **NVIDIA Inception Program**.

**Founders:**
- **Rohan Kapoor** — CEO & Co-Founder · rohan@adflux.dev
- **Dev Patel** — AI Developer & Co-Founder · dev@adflux.dev

🌐 [adflux.dev](https://adflux.dev)

---

*© 2025 Adflux Inc. All rights reserved.*
