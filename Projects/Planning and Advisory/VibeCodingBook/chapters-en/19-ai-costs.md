# Chapter 20 AI Costs & Rate Limits: How Not to Go Broke

AI APIs charge per token. A token is roughly three quarters of a word. If your app processes 1,000 requests a day, each with a 2,000-token prompt and a 500-token response, that's 2.5 million tokens daily. With GPT-4o, this costs about £25 per day. With GPT-4o-mini, about £1.25. With Gemini Flash on the free tier, £0. Architecture decisions determine whether your AI feature costs £0 or £750 per month.

## Key Terms

| Term                     | What it means                                                                       |
| ------------------------ | ----------------------------------------------------------------------------------- |
| **Token**                | The unit AI APIs use for billing (~0.75 words per token)                            |
| **Rate limit**           | Maximum requests per minute/hour/day an API allows                                  |
| **Input tokens**         | Tokens in your prompt (what you send)                                               |
| **Output tokens**        | Tokens in the response (what you get back)                                          |
| **Prompt caching**       | Reusing cached system prompts to reduce costs                                       |
| **Model routing**        | Sending easy tasks to cheap models and hard tasks to expensive ones                 |
| **Graceful degradation** | Continuing to work (with reduced functionality) when the AI service is unavailable  |
| **BYOK**                 | Bring Your Own Key: users supply their own API key so you don't pay for their usage |

## How AI Pricing Works

Every major AI provider charges per token, with input tokens cheaper than output tokens.

| Model              | Input (per 1M tokens) | Output (per 1M tokens) | Relative cost      |
| ------------------ | --------------------- | ---------------------- | ------------------ |
| GPT-4o             | $2.50                 | $10.00                 | Baseline           |
| GPT-4o-mini        | $0.15                 | $0.60                  | 17x cheaper        |
| Claude Sonnet 4    | $3.00                 | $15.00                 | Slightly more      |
| Claude Haiku 4     | $0.25                 | $1.25                  | 10x cheaper        |
| Gemini 2.0 Flash   | Free (1,500 req/day)  | Free                   | Free within limits |
| DeepSeek V3        | $0.27                 | $1.10                  | 9x cheaper         |
| Groq Llama 4 Scout | Free (limited)        | Free                   | Free within limits |

The difference between models is enormous. GPT-4o-mini handles most tasks (classification, summarisation, data extraction) at a fraction of GPT-4o's cost. Reserve expensive models for complex reasoning.

### What One Auto-fill Actually Costs

Let's do real maths with JobPilot. When someone clicks "Auto-fill" on a Greenhouse application, the extension sends roughly 800 input tokens (the system prompt, the list of form fields, and the user's profile) and receives about 200 output tokens (the JSON mapping).

With GPT-4o-mini at $0.15 per million input tokens and $0.60 per million output tokens:

- Input cost: 800 tokens x ($0.15 / 1,000,000) = $0.00012
- Output cost: 200 tokens x ($0.60 / 1,000,000) = $0.00012
- **Total per fill: $0.00024** (roughly one fortieth of a penny)

If a user fills 10 applications per day, that's $0.0024/day, or about $0.07 per month. A thousand active users filling 10 forms a day costs $72 per month.

With GPT-4o instead of mini? Multiply by 17. That same thousand users would cost over $1,200 per month. The quality difference for this task (matching field names to profile data) is negligible. Model choice is the single biggest cost lever.

> **Try It:** Go to openai.com/pricing. Pick the task your app does most often. Calculate the cost for 100 requests using GPT-4o, then GPT-4o-mini, then Gemini Flash. The difference will change how you think about model selection.

## Rate Limits: The Speed Limit

Every API has rate limits: maximum requests per minute, tokens per minute, or requests per day. Exceed them and you get a 429 status code. Your app stops working.

OpenAI uses a tier system:

| Tier   | Monthly spend | Requests/min | Tokens/min |
| ------ | ------------- | ------------ | ---------- |
| Free   | $0            | 3            | 40,000     |
| Tier 1 | $5+           | 500          | 200,000    |
| Tier 2 | $50+          | 5,000        | 2,000,000  |
| Tier 3 | $100+         | 5,000        | 4,000,000  |

Gemini's free tier: 1,500 requests per day, 15 requests per minute. Enough for prototyping and low-volume production.

## Five Cost-Saving Patterns

### 1. Model Routing

Send each request to the cheapest model that can handle it.

```javascript
async function classifyAndRoute(task) {
  // Simple tasks: cheap model
  if (task.type === "classify" || task.type === "extract") {
    return callModel("gpt-4o-mini", task.prompt);
  }

  // Complex tasks: capable model
  if (task.type === "reason" || task.type === "generate_code") {
    return callModel("gpt-4o", task.prompt);
  }

  // Default: cheap model
  return callModel("gpt-4o-mini", task.prompt);
}
```

### 2. Prompt Caching

If every request includes the same 2,000-token system prompt, you pay for those tokens every time. OpenAI and Anthropic support prompt caching: the system prompt is cached on the first request, and subsequent requests reference the cache at a reduced cost.

Anthropic's cache: 90% reduction on cached tokens. OpenAI's cache: 50% reduction. For high-volume applications, this adds up quickly.

### 3. Response Caching

If the same question appears repeatedly, cache the answer instead of calling the API again.

```javascript
const cache = new Map();

async function getAIResponse(prompt) {
  const cacheKey = prompt.trim().toLowerCase();

  if (cache.has(cacheKey)) {
    return cache.get(cacheKey);
  }

  const response = await callModel("gpt-4o-mini", prompt);
  cache.set(cacheKey, response);
  return response;
}
```

For production, use Redis or a database instead of an in-memory Map.

### 4. Batch API

OpenAI offers a Batch API: submit many requests at once, get results within 24 hours, at 50% off. Ideal for processing that doesn't need real-time results: analysing yesterday's reviews, classifying a backlog of support tickets, generating summaries overnight.

### 5. Spending Alerts and Hard Limits

Every provider lets you set spending limits. Set them on day one.

OpenAI: Settings → Billing → Usage limits. Set a hard limit (API stops working) and a soft limit (you get an email).

If you wake up to a £500 bill because a bug caused a loop of API calls, you'll wish you had set a £50 limit.

## Case Study: £0 Per Month AI Processing

A sales SDR tool processes 1,500 leads per day. Each lead needs: company research, personalisation, and email drafting. The architecture:

**Primary:** Gemini 2.0 Flash (free tier, 1,500 requests/day). Handles lead classification and initial research.

**Fallback:** Groq Llama 4 Scout (free tier, limited). Takes over when Gemini's daily limit is reached.

**Complex tasks only:** GPT-4o-mini (paid, but rarely invoked). Only for especially difficult personalisation.

Result: 95% of requests handled by free tiers. Monthly cost: effectively £0. The product serves paying customers with no AI expenditure.

The secret: most tasks don't need the most powerful model. Classification, extraction, and templated generation work well with smaller, cheaper (or free) models.

## BYOK: Bring Your Own Key

Instead of paying for AI calls yourself, let each user plug in their own API key. Your app stores their key (encrypted, on the backend), and every AI request uses the user's key instead of yours. Your AI bill drops to zero.

This is common in developer tools, internal dashboards, and power-user products where the audience already has an OpenAI or Anthropic account. The tradeoff: higher friction (users need to create an account, generate a key, and paste it in), but zero marginal cost per user for you.

When BYOK makes sense:

- Your users are technical enough to have their own API keys
- AI usage per user is high (so absorbing the cost would be expensive)
- You want to launch without worrying about AI bills scaling with users

When it doesn't:

- Consumer products where users have never heard of an API key
- Products where the AI is the core selling point (users expect it to "just work")
- Low usage per user (the cost of absorbing it is negligible)

Many products start BYOK and add a managed option later, once revenue justifies the AI spend.

## Graceful Degradation

AI services go down. Rate limits get hit. Free tiers run out. Your app must handle this without crashing.

```javascript
async function summariseReview(review) {
  try {
    // Try primary AI
    return await callModel("gpt-4o-mini", `Summarise: ${review}`);
  } catch (error) {
    if (error.status === 429) {
      // Rate limited: try fallback
      try {
        return await callFallbackModel(review);
      } catch {
        // Fallback also failed: return a non-AI response
        return review.substring(0, 100) + "...";
      }
    }
    // Other error: return the review as-is
    return review;
  }
}
```

The user still sees something. Less than perfect, but better than a blank screen or an error message.

## Common Beginner Mistake

**Not setting spending limits before deploying.** A bug that calls the AI in a loop, a sudden traffic spike, or a malicious user sending thousands of requests can burn through your budget in hours. Set hard spending limits on every AI provider before your first production deployment.

## Checklist

- [ ] I have spending limits set on every AI provider I use
- [ ] I use the cheapest model that handles each task type
- [ ] I cache repeated prompts and responses
- [ ] I have fallback behaviour when the AI service is unavailable
- [ ] I monitor token usage and costs regularly
- [ ] I use the Batch API for non-real-time processing

## Summary

AI APIs charge per token, with massive price differences between models. GPT-4o-mini costs 17x less than GPT-4o for tasks where quality is comparable. Five cost patterns keep expenses manageable: model routing (cheap for easy, expensive for hard), prompt caching, response caching, batch processing, and spending alerts. Rate limits exist on every service; hitting them stops your app. Graceful degradation ensures the app continues working (with reduced AI functionality) when services are unavailable or rate-limited. Setting spending limits before the first deploy is non-negotiable.

## Challenge

1. Calculate the monthly cost of your AI feature. Estimate: how many requests per day? How many tokens per request (input + output)? Multiply by the pricing for three models (GPT-4o, GPT-4o-mini, Gemini Flash). The difference might be 100x.

2. Implement response caching for a repeated AI call. Track the cache hit rate over 100 requests. If the data is static or changes rarely, the cache should serve 50% or more of requests without an API call.
