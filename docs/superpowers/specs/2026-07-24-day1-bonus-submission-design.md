# Day 1 Bonus and Submission Design

## Scope

Implement the two optional functions in `template.py`, verify them without real API calls, create the required `solution/` copies, run the full tests through the solution-first loader, scan the submission for embedded API credentials, and produce `solution.zip`.

## Batch Comparison

`batch_compare(prompts)` calls the existing `compare_models(prompt)` once for each prompt. It returns results in input order. Each item is a new dictionary containing all comparison fields plus the original value under `prompt`, so it does not mutate a dictionary returned by `compare_models`.

An empty input returns an empty list naturally.

## Text Table

`format_comparison_table(results)` performs no API calls. A local `shorten` helper converts values to strings and truncates text longer than 40 characters to the first 40 characters followed by `...`.

The returned table contains a header with `Prompt`, `GPT-4o Response`, `Mini Response`, `GPT-4o Latency`, and `Mini Latency`, followed by one row per result. Response and prompt columns use `shorten`; both latency values use two decimal places and an `s` suffix. Rows are joined with newline characters and columns use `|` separators.

For an empty result list, the function still returns the header and separator so the output remains readable.

## Verification

Use an inline assert-based self-check that patches `compare_models`, proving that:

- `batch_compare` preserves input order and adds `prompt`.
- The returned list length equals the prompt count.
- The table is a string containing all headers.
- Every result produces a row.
- Long text is truncated and latency is formatted as seconds.

Then run the complete pytest suite and `grade.py` before copying the final files.

## Submission Packaging

Create `solution/`, copy the final `template.py` to `solution/solution.py`, and copy `exercises.md` to `solution/exercises.md`. Run the complete pytest suite and grader again after copying because the loader then tests `solution/solution.py` instead of `template.py`.

Scan the submission source for likely embedded OpenAI API keys. Environment-variable names such as `OPENAI_API_KEY` are allowed; a literal credential value is not.

Create `solution.zip` containing the `solution/` directory. Inspect the archive listing to confirm it contains exactly `solution/solution.py` and `solution/exercises.md` as submission files.
