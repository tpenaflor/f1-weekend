97% believe in it. 4% have built it. New research: State of context engineering.

[Read the report](https://redis.io/SOCE26/)

Resource Center

[Events & webinars](https://redis.io/events/) [Blog](https://redis.io/blog/) [Videos](https://redis.io/resources/videos/) [Glossary](https://redis.io/glossary/) [Resources](https://redis.io/resources/all/) [Architecture Diagrams](https://redis.io/resources/architecture-diagrams/) [Demo Center](https://redis.io/demo-center/)

Blog

[Events & webinars](https://redis.io/events/) [Videos](https://redis.io/resources/videos/) [Glossary](https://redis.io/glossary/) [Resources](https://redis.io/resources/all/) [Architecture Diagrams](https://redis.io/resources/architecture-diagrams/) [Demo Center](https://redis.io/demo-center/)

[Back to blog](https://redis.io/en/blog/)

Blog

# How to cut LLM token costs & speed up AI apps

February 19, 20269 minute read

[Jim Allen Wallace](https://redis.io/blog/author/jim-allenwallaceredis-com/)

![Image](https://cdn.sanity.io/images/sy1jschh/production/b566b185b46861c427fcba7c47672a5882774185-100x100.jpg?w=256&q=80&fit=clip&auto=format)

Jim Allen Wallace

Summarize with AI

- [Open prompt in ChatGPT](https://chatgpt.com/?q=Summarize%20this%20Redis%20blog%20post%3A%20%22How%20to%20cut%20LLM%20token%20costs%20%26%20speed%20up%20AI%20apps%22%20%E2%80%94%20https%3A%2F%2Fredis.io%2Fblog%2Fllm-token-optimization-speed-up-apps.md)
- [Open prompt in Claude](https://claude.ai/new?q=Search%20the%20web%20and%20answer%3A%20Summarize%20this%20Redis%20blog%20post%3A%20%22How%20to%20cut%20LLM%20token%20costs%20%26%20speed%20up%20AI%20apps%22%20%E2%80%94%20https%3A%2F%2Fredis.io%2Fblog%2Fllm-token-optimization-speed-up-apps.md)
- [Open prompt in Perplexity](https://www.perplexity.ai/search/new?q=Summarize%20this%20Redis%20blog%20post%3A%20%22How%20to%20cut%20LLM%20token%20costs%20%26%20speed%20up%20AI%20apps%22%20%E2%80%94%20https%3A%2F%2Fredis.io%2Fblog%2Fllm-token-optimization-speed-up-apps.md)

You've probably noticed your Large Language Model (LLM) bill creeping up faster than your user growth. Or maybe you're watching users abandon your AI app because responses take too long. Both problems often trace back to the same issue: wasted tokens.

Tokens you send cost money and add latency. Input tokens are processed in the prefill step—where the model reads your entire prompt—largely in parallel, while output tokens are generated sequentially during the decode step. Decode is often memory-bandwidth-bound, so output length usually dominates perceived latency. When you're processing millions of queries monthly, these inefficiencies compound into real costs and degraded user experience.

Token optimization isn't about squeezing every last penny from your API budget. It's about building AI apps that feel instant and scale without burning through your runway.

## **What is LLM token optimization & why optimize tokens?**

LLM token optimization minimizes token consumption in AI apps to reduce API costs and improve inference latency. LLMs process tokens, which are text chunks typically smaller than complete words.

Think of tokens as the currency of LLM interactions. One token equals roughly 4 characters of English text or about ¾ of a word. A 100-word paragraph consumes around 133 tokens. "What's on my calendar today?" costs about 8 tokens, but "Could you please provide me with a comprehensive overview of my scheduled appointments for today?" jumps to 18 tokens. That's more than double for the same intent.

The cost structure reveals why optimization matters. Flagship models charge $2–3 per million input tokens and $10–15 per million output tokens, a 4–5x multiplier. Consider a customer support chatbot handling 1 million conversations monthly with 500 input tokens and 200 output tokens per conversation. With a flagship model at $2.50/$10.00 pricing, that's $3,250/month. Switch to a budget-tier model at $0.15/$0.60 and the same workload costs $195, a 16x difference for identical token counts. Prices change frequently, so check current model pricing before estimating.

## **Why token optimization matters for app speed & latency**

Token count affects the latency metrics that shape whether your AI app feels instant or sluggish. The relationship is quantifiable.

LLM inference happens in two phases. The prefill phase processes input tokens in parallel and is relatively fast. The decoding phase generates output tokens one at a time sequentially and is slow. Depending on model size, hardware, and load, each output token can add several to tens of milliseconds of sequential processing time, so long responses quickly increase latency.

Memory bandwidth, not computational power, often limits inference speed. During token generation, the GPU must read tens to hundreds of gigabytes of model weights and key-value (KV) cache from high-bandwidth memory. Even with fast computation, insufficient memory bandwidth can constrain overall performance.

Token count also determines KV cache memory requirements, which can become a major limiting factor for system throughput at scale, especially in workloads with long contexts or many concurrent sessions. When KV cache requirements exceed available memory, system throughput can degrade significantly.

Redis addresses these latency challenges through [semantic caching](https://redis.io/blog/what-is-semantic-caching/) and sub-millisecond vector search. By storing query vector embeddings and LLM responses in memory, Redis retrieves cached answers for semantically similar queries without redundant API calls. [Redis LangCache](https://redis.io/langcache/) has achieved up to ~73% cost reduction in high-repetition workloads, with cache hits returning in milliseconds versus the seconds required for fresh LLM inference.

![Redis Iris](https://cdn.sanity.io/images/sy1jschh/production/82df1fa7f210a2b005d5ebbf3429a53a0ebccce0-64x64.svg)

## Build fast, accurate AI apps that scale

Get started with Redis for real-time AI context and retrieval.

[Try Redis for AI](https://redis.io/try-free/)

## **Where LLM token waste comes from**

Production LLM apps commonly waste a meaningful share of their token budgets across a few key areas. Understanding where waste occurs helps you fix it.

- **Verbose prompts & system instructions.** Concise instructions often achieve comparable results with far fewer tokens. "Summarize:" can work as well as a multi-sentence explanation. Repeating lengthy system prompts across every query in a session compounds the problem.
- **Inefficient conversation history.** Multi-turn conversations accumulate thousands of unnecessary tokens. A 20-turn conversation can consume 5,000-10,000 tokens when only 500-1,000 tokens of recent context would typically suffice.
- **Unoptimized function calling & few-shot examples.** Verbose function descriptions add overhead on every call, and more few-shot examples don't always mean better results. Many tasks achieve comparable quality with fewer demonstrations and leaner descriptions.
- **Excessive output generation.** Apps that don't set appropriate max\_tokens limits let models generate unnecessarily detailed responses. Since output tokens cost 4-6x more than input tokens, this waste hits particularly hard.
- **Oversized RAG context.** Retrieval-augmented generation (RAG) pulls relevant documents to include in your prompt. Retrieving more context than necessary fills the context window with low-relevance information that adds cost without improving answers.

Most of these sources are addressable with systematic optimization, often without major architectural changes.

## **A simple LLM token optimization playbook**

Start with foundation techniques that require no additional tools, then layer in compression strategies and [semantic caching](https://redis.io/blog/what-is-semantic-caching/) as your app scales.

### **Foundation techniques**

These optimizations require no additional tools and can deliver quick wins:

- **Tighten your prompts.** Lead with keywords, extract rather than generate full text, and request structured output formats. "Summarize the main points:" often works as well as verbose alternatives while using significantly fewer tokens.
- **Constrain output explicitly.** Set max\_tokens limits in API calls and include length constraints in your prompt instructions. "Answer in 50 words" gives the model clear boundaries; pair this with max\_tokens=100 (with buffer) to enforce hard limits. Without these constraints, models tend to generate unnecessarily detailed responses.
- **Deploy semantic chunking** for document-heavy apps. Semantic chunking splits text based on meaning rather than arbitrary character counts, preserving complete semantic units. This can reduce total chunks required while maintaining answer quality, because it avoids splitting concepts across chunk boundaries.
- **Implement semantic caching** for high-traffic, repetitive workloads. Redis stores query vector embeddings and LLM responses, retrieving cached answers for semantically similar queries. "What's the weather like today?" and "How's the weather right now?" can hit the same cache entry based on similarity threshold. Workloads with high query repetition see the biggest gains because every cache hit eliminates the LLM call entirely.

Start with prompt tightening and output constraints, as they're often the fastest wins. Add semantic chunking and caching as your workload grows.

### **Advanced optimization**

Once you've tackled the basics, these techniques can drive additional savings for the right workloads:

- **Integrate LLMLingua compression** for aggressive optimization. [LLMLingua](https://arxiv.org/abs/2310.05736) compresses prompts with minimal performance loss. It's particularly effective for RAG systems with long retrieved contexts where budget constraints are tight.
- **Optimize model selection by task complexity.** Use budget-tier models for simple classification and extraction. They can cost 15–50x less than flagship models. Reserve flagship models for complex reasoning or mission-critical, low-latency use cases.
- **Consider context consolidation** where appropriate. Extended context windows let you load multiple documents directly rather than chunking, though this requires careful cost-benefit analysis for your specific workload.

These optimizations compound. Prompt optimization, semantic chunking, compression, and semantic caching work together. Semantic caching in particular eliminates the LLM inference call on cache hits, which can drive meaningful cost reductions while often maintaining or improving accuracy through noise filtering.

## Make your AI apps faster and cheaper

Cut costs by up to 90% and lower latency with semantic caching powered by Redis.

[Learn more](https://redis.io/try-free/)

## **How token optimization accelerates app speed in practice**

The impact becomes clear in production RAG pipelines. Teams that systematically apply these techniques often see meaningful cost reductions while maintaining or improving system performance.

Query caching often delivers significant wins. Production workloads contain more repetition than you might expect, and caching repeated queries can significantly reduce costs for reranking and embedding generation. Once caching is in place, context assembly becomes the next lever. Limiting retrieval to a fixed token budget forces you to prioritize relevance over volume.

When optimizing for speed specifically, output tokens often matter more than input tokens. Since output tokens drive generation latency sequentially, reducing output length often provides more latency improvement than cutting input by a similar amount.

Visibility makes all of this easier. Production monitoring helps you find the wins. Many teams discover major token usage issues only after implementing granular tracking by query type, user segment, and model.

For high-volume apps, these savings compound quickly. Even modest per-query savings add up at scale, depending on your traffic patterns and model pricing.

## **Building infrastructure for token-optimized LLM apps**

Effective token optimization benefits from infrastructure that can handle caching, vector search, and session management together. One of the most impactful architectural patterns is semantic caching, which stores LLM responses alongside vector embeddings of user queries. Instead of requiring exact string matches, semantic caching retrieves cached answers for queries that are semantically similar to previous ones.

The workflow is straightforward: when a query arrives, the system converts it to a vector embedding, searches for semantically similar cached queries using vector search, and retrieves pre-generated responses if similarity exceeds a configured threshold. This approach works particularly well for workloads with natural query repetition, like customer support, FAQ-style interactions, and common user intents.

### **Vector search for fast retrieval**

Vector search is the enabling technology here. Redis supports multiple distance metrics (cosine similarity, Euclidean distance, inner product) and handles datasets with millions of vectors while maintaining low-latency retrieval. For RAG workflows, the full pipeline of document retrieval, vector lookup, and context assembly can complete in well under a second with optimized infrastructure. That's fast enough that users don't notice the retrieval step.

### **Multi-tier caching strategy**

A multi-tier caching strategy tends to work best in practice. Exact match caching handles identical queries at sub-millisecond latency. Semantic caching catches similar queries at slightly higher latency. Session context management maintains conversation state efficiently. Together, these layers can substantially reduce token consumption in chatbot deployments with high query repetition, since cache hits avoid the LLM inference call entirely.

Redis provides this infrastructure as a [real-time data platform](https://redis.io/tutorials/what-is-redis/), combining semantic caching, [vector search](https://redis.io/solutions/vector-database/), and session management in one system. Many teams end up managing three systems separately—a vector database, a cache, and an operational store. Redis combines all three with a memory-first architecture. If you're already using Redis for [caching](https://redis.io/blog/redis-smart-cache/) or session management, you can extend it to handle AI workloads without adding new infrastructure.

![Database](https://cdn.sanity.io/images/sy1jschh/production/bcfc53e71291d3d5d75ec30642114c4717c79133-64x64.svg)

## Now see how this runs in Redis

Power AI apps with real-time context, vector search, and caching.

[Get started](https://redis.io/try-free/)

## **Fast AI apps start with optimized tokens**

Token optimization matters for production LLM apps. Tokens you send cost money and add latency. The wasted tokens in verbose prompts, oversized context windows, and unoptimized conversation history compound into budget-busting API bills and frustrating response times.

If you're spending too much on LLM inference or watching users abandon slow AI features due to time-to-first-token delays, token optimization is worth exploring. Semantic caching can cut API costs by up to 73%, while prompt optimization, context engineering, and RAG tuning provide additional savings.

Redis makes this possible with sub-millisecond vector search. Your vector embeddings live alongside your operational data, so you don't need a separate vector database or complex integration. Most production systems find the trade-off worth it when they need semantic search alongside caching and operational data. The unified platform consolidates semantic caching, vector storage, and session management, letting you optimize token usage without architectural complexity.

[Try Redis free](https://redis.io/try-free/) to see how semantic caching works with your workload, or [talk to our team](https://redis.io/meeting/) about optimizing your AI infrastructure.

[View as Markdown](https://redis.io/blog/llm-token-optimization-speed-up-apps.md)

Share

## Get started with Redis today

Speak to a Redis expert and learn more about enterprise-grade Redis today.

[Try for free](https://redis.io/try-free/) [Talk to sales](https://redis.io/meeting/)

This site uses cookies and related technologies, as described in our [privacy policy](https://redis.com/legal/privacy-policy/), for purposes that may include site operation, analytics, enhanced user experience, or advertising. You may choose to consent to our use of these technologies, or manage your own preferences.

Manage SettingsAccept

Qualified

Redis Chat , AI Agent

![Redis Chat ](https://messenger-assets.qualified.com/uploads/7U9HDVYzTNS1JkJ3HJxVAG8hSXkbMDvvd7cuv/1f8b82205c464f6273be7ad0bb924b7534654e93745924163a0b8ef60edea4a7.png)

Redis Chat  AI Chatbot

Conversation

Message input

Message Redis Chat

By using this chat box, you are agreeing to [Redis' Privacy Policy](https://redis.io/legal/privacy-policy/).

![Messenger](https://messenger-assets.qualified.com/uploads/7U9HDVYzTNS1JkJ3HJxVAG8hSXkbMDvvd7cuv/32711c35059c66ca3502aa7746362ab3ea99aa4a08a1f58d960e2709eff27d7c.png)
Ask Redis Chat