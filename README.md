# Meridian-proposal-ai-agent
An agent that:  1. Takes typed adviser notes as input (plain English ‚Äî see `sample-inputs/`). 2. Uses an LLM to extract the details and produce a proposal object that    conforms to `proposal-schema.md`. 3. Loads it into the tool via `window.loadProposal(...)` so the proposal renders.
