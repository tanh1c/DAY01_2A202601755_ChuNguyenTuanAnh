# Day 1 LLM API Lab Design

## Scope

Complete the nine graded functions in `template.py` and replace all nine answer placeholders in `exercises.md`. Do not create `solution/solution.py`, implement the two bonus functions, add dependencies, or refactor unrelated code.

## API Calls and Model Comparison

`call_openai` imports `OpenAI` inside the function so the existing mocks intercept client construction. It sends one user message with the provided model and sampling parameters, measures elapsed time, and returns response text with latency.

`call_openai_mini` delegates to `call_openai` with `OPENAI_MINI_MODEL`. `compare_models` invokes both functions and returns the five keys required by the tests, including a non-negative GPT-4o output-cost estimate.

## Persona, Tokens, and Cost

`chat_with_system_prompt` sends separate system and user messages and returns response text with latency. `count_tokens` uses `tiktoken.encoding_for_model`; if the model is unknown or tokenization fails, it falls back to `max(1, len(text) // 4)`.

`estimate_cost` counts input and output tokens separately, applies the existing per-1,000-token prices, and returns input, output, and total costs. Unknown model pricing falls back to GPT-4o.

## Streaming, History, and Retry

`streaming_chatbot` reads until `quit` or `exit`, calls the API with `stream=True`, prints each available chunk, and retains at most three completed user/assistant turns. Empty final chunks are ignored with `delta.content or ""`.

`retry_with_backoff` performs the initial call plus at most `max_retries` retries. Delays follow `base_delay * 2 ** attempt`; after the retry budget is exhausted, the final exception is re-raised unchanged.

## Integrated Assistant

`run_assistant` keeps the persona as a system message outside history. Before reading input, it checks whether `max_turns` has been reached. Each non-exit message is sent with the system message and up to three recent turns, using streaming and retry.

After a successful response, the function appends the user and assistant messages, trims history to six messages, and accumulates token and cost statistics. It returns `num_turns`, `total_tokens`, `total_cost`, and `history`, including when no turns run.

## Exercises

Replace all nine placeholders with concise, substantive Vietnamese answers. Include the required calculations and explain observed or expected behavior without claiming that mocked tests produced real model outputs.

## Error Handling

API exceptions propagate from the basic call helpers. Retry behavior is limited to the explicitly retry-enabled flows. Tokenization and pricing use the defined fallbacks because model identifiers can be overridden by environment variables.

## Verification

Run each checkpoint test file, the complete test suite, and `python grade.py`. The tests use mocks, so verification must not require an API key or make real network requests.
