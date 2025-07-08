# OpenRouter Integration for AI Scientist-v2

This document explains how to use OpenRouter as an LLM provider in AI Scientist-v2.

## Overview

OpenRouter provides access to multiple LLM models through a unified API that's compatible with OpenAI's format. This integration allows you to use various models including Claude, GPT-4, Gemini, Llama, and many others through a single API key.

## Setup

### 1. Get an OpenRouter API Key

1. Visit [OpenRouter.ai](https://openrouter.ai)
2. Sign up for an account
3. Generate an API key from your dashboard
4. Add credits to your account

### 2. Set the Environment Variable

```bash
export OPENROUTER_API_KEY="your-api-key-here"
```

## Usage

### Available Models

You can use any model available on OpenRouter by prefixing it with `openrouter/`. Some popular examples:

- `openrouter/anthropic/claude-3.5-sonnet`
- `openrouter/anthropic/claude-3-opus`
- `openrouter/openai/gpt-4`
- `openrouter/openai/gpt-4-turbo`
- `openrouter/google/gemini-pro-1.5`
- `openrouter/meta-llama/llama-3.1-70b-instruct`
- `openrouter/meta-llama/llama-3.1-405b-instruct`

For a full list of available models, visit: https://openrouter.ai/models

### Using OpenRouter Models in AI Scientist-v2

#### 1. For Ideation Phase

```bash
python ai_scientist/perform_ideation_temp_free.py \
  --workshop-file "ai_scientist/ideas/my_research_topic.md" \
  --model openrouter/anthropic/claude-3.5-sonnet \
  --max-num-generations 20 \
  --num-reflections 5
```

#### 2. For Experiment Phase

Edit `bfts_config.yaml` and update the model fields:

```yaml
agent:
  code:
    model: openrouter/anthropic/claude-3.5-sonnet
    temp: 1.0
    max_tokens: 12000

  feedback:
    model: openrouter/openai/gpt-4-turbo
    temp: 0.5
    max_tokens: 8192
```

#### 3. For Writing and Review Phase

```bash
python launch_scientist_bfts.py \
  --load_ideas "ai_scientist/ideas/my_research_topic.json" \
  --model_writeup openrouter/anthropic/claude-3-opus \
  --model_citation openrouter/openai/gpt-4-turbo \
  --model_review openrouter/openai/gpt-4 \
  --model_agg_plots openrouter/google/gemini-pro-1.5
```

## Cost Considerations

OpenRouter charges based on the specific model used. Some notes:

- Claude models tend to be more expensive than GPT models
- Larger models (like Llama 405B) cost more than smaller ones
- You can see pricing at: https://openrouter.ai/models
- Monitor your usage at: https://openrouter.ai/activity

## Advantages of Using OpenRouter

1. **Single API Key**: Access multiple providers without managing multiple API keys
2. **Fallback Options**: If one provider is down, easily switch to another
3. **Cost Optimization**: Choose cheaper models for less critical tasks
4. **Model Comparison**: Easily test different models for the same task
5. **No Rate Limits**: OpenRouter handles rate limiting across providers

## Troubleshooting

### Common Issues

1. **"OPENROUTER_API_KEY environment variable not set"**
   - Make sure you've exported the environment variable
   - Check it's set: `echo $OPENROUTER_API_KEY`

2. **"Invalid API Key"**
   - Verify your API key is correct
   - Check you have credits in your OpenRouter account

3. **"Model not found"**
   - Ensure you're using the correct model name
   - Check the model is available at: https://openrouter.ai/models

### Debugging

To see which models are being used, run with verbose output and look for lines like:
```
Using OpenRouter API with openrouter/anthropic/claude-3.5-sonnet
```

## Integration Details

The OpenRouter integration works by:

1. Detecting models prefixed with `openrouter/`
2. Creating an OpenAI-compatible client pointing to OpenRouter's API
3. Stripping the `openrouter/` prefix before sending to the API
4. Using the same request/response format as OpenAI

This means all features that work with OpenAI models should work with OpenRouter models, including:
- Function calling
- Streaming responses
- Temperature and other parameters
- Token counting and limits