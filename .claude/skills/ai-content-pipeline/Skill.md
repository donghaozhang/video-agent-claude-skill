---
name: AI Content Pipeline
description: Generate AI content (images, videos, audio) using YAML pipelines with 28+ models. Run tests, estimate costs, and manage outputs.
dependencies: python>=3.8
---

# AI Content Pipeline Skill

This skill helps you work with the AI Content Pipeline - a unified Python package for multi-modal AI content generation.

## Quick Reference Commands

### Pipeline Execution
```bash
# Activate virtual environment first
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# Run a pipeline from YAML config
ai-content-pipeline run-chain --config input/pipelines/config.yaml

# Run with parallel execution (2-3x faster)
PIPELINE_PARALLEL_ENABLED=true ai-content-pipeline run-chain --config config.yaml

# Use short alias
aicp run-chain --config config.yaml
```

### Single Operations
```bash
# Generate a single image
ai-content-pipeline generate-image --text "A beautiful sunset" --model flux_dev

# Create video from text (text -> image -> video)
ai-content-pipeline create-video --text "A beautiful sunset"

# List all available models
ai-content-pipeline list-models
```

### Testing
```bash
# Quick smoke tests (30 seconds)
python tests/test_core.py

# Full integration tests (2-3 minutes)
python tests/test_integration.py

# Run all tests
python tests/run_all_tests.py

# Quick test mode
python tests/run_all_tests.py --quick
```

## Available AI Models (28 Total)

### Text-to-Image (4 models)
| Model | Key | Description |
|-------|-----|-------------|
| FLUX.1 Dev | `flux_dev` | Highest quality, 12B parameters |
| FLUX.1 Schnell | `flux_schnell` | Fastest inference |
| Imagen 4 | `imagen_4` | Google's photorealistic model |
| Seedream v3 | `seedream_v3` | Multilingual support |

### Image-to-Video (4 models)
| Model | Key | Description |
|-------|-----|-------------|
| Veo 3 | `veo_3` | Google's latest video model |
| Veo 2 | `veo_2` | Previous generation Veo |
| Hailuo | `hailuo` | MiniMax video generation |
| Kling | `kling` | High-quality video synthesis |

### Image-to-Image (6 models)
- Photon Flash, Photon Base, FLUX variants, Clarity Upscaler

### Image Understanding (7 models)
- Gemini variants for description, classification, OCR, Q&A

### Prompt Generation (5 models)
- OpenRouter models for video prompt optimization

## YAML Pipeline Configuration

Create a pipeline config file in `input/pipelines/`:

```yaml
name: "My Content Pipeline"
description: "Generate image and convert to video"

steps:
  - name: "generate_image"
    type: "text-to-image"
    model: "flux_dev"
    params:
      prompt: "A majestic mountain landscape at sunset"
      width: 1920
      height: 1080

  - name: "create_video"
    type: "image-to-video"
    model: "veo_3"
    params:
      image: "{{step_1.output}}"
      prompt: "Camera slowly pans across the landscape"
      duration: 5
```

### Parameter Templating
Use `{{step_N.output}}` to reference outputs from previous steps.

## Cost Estimation

Typical costs per operation:
- **Text-to-Image**: $0.001-0.004 per image
- **Image-to-Image**: $0.01-0.05 per modification
- **Image-to-Video**: $0.08-6.00 per video (model dependent)

Always estimate before large pipelines:
```bash
ai-content-pipeline estimate --config config.yaml
```

## Environment Setup

Required environment variables in `.env`:
```
FAL_KEY=your_fal_api_key
PROJECT_ID=your-gcp-project-id
OUTPUT_BUCKET_PATH=gs://your-bucket/output/
ELEVENLABS_API_KEY=your_elevenlabs_key
OPENROUTER_API_KEY=your_openrouter_key
GEMINI_API_KEY=your_gemini_key
```

## Project Structure

```
ai-content-pipeline/
├── packages/
│   ├── core/ai_content_pipeline/    # Main pipeline
│   ├── providers/                   # Google Veo, FAL AI
│   └── services/                    # TTS, video tools
├── input/                           # Pipeline configs
├── output/                          # Generated content
└── tests/                           # Test suites
```

## Common Tasks

### Creating a New Pipeline
1. Create YAML config in `input/pipelines/`
2. Define steps with model and parameters
3. Use `{{step_N.output}}` for chaining
4. Run with `aicp run-chain --config your_config.yaml`

### Adding a New Model
1. Check `packages/providers/` for the provider
2. Implement model interface
3. Register in model registry
4. Add to list-models output

### Debugging Pipeline Issues
1. Run with verbose logging
2. Check `output/` for intermediate files
3. Verify API keys in `.env`
4. Test individual steps manually
