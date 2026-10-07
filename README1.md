# Meridian Proposal Agent

A small Python app that turns fictional adviser notes into a draft in the supplied Meridian proposal generator. The original challenge files in `challenge/` are unchanged.

## Quick start

Requires Python 3.10+ and a browser. Open a terminal in this folder.

```sh
python -m venv .venv
```

Activate the virtual environment:

- Windows PowerShell: `.venv\Scripts\Activate.ps1`
- macOS/Linux: `source .venv/bin/activate`

Install the one dependency:

```sh
python -m pip install -r requirements.txt
```

Configure your OpenAI API key in that terminal (never put the real key in the code or commit it).

Windows PowerShell:

```powershell
$env:OPENAI_API_KEY="your-api-key"
```

macOS/Linux:

```sh
export OPENAI_API_KEY="your-api-key"
```

Start:

```sh
python app.py
```

Open **http://127.0.0.1:8000**. Select example notes or paste fictional notes, click **Generate with AI**, review warnings and JSON, then **Open draft in generator**. The generator opens its preview; use its editor controls to refine the proposal. **Download JSON** exports the exact payload.

For a no-key UI check, run `python app.py`, select a supplied example and click **Preview supplied answer**. This replays the pack's expected JSON. It is explicitly labelled and is NOT AI generation. Editing the notes disables this sample replay until another sample is selected.

## Model and design

Default model: `gpt-4o-mini`, using OpenAI's Responses API with strict JSON Schema output. Change `OPENAI_MODEL` to another model available to your account that supports structured output. There is no agent framework or database. Python's standard-library HTTP server and HTTPS client keep this prototype small; `jsonschema` validates output.

1. The browser reads the actual model catalog from `window.challenge.models()` in the embedded original generator.
2. The Python backend sends notes, the catalog and extraction rules to the LLM. The API key remains server-side. Requests use `store: false`.
3. A small extraction schema represents missing information with `null`.
4. Python checks types, numeric string format, model IDs, currency compatibility and exact supporting source quotes. It maps fields into the provided contract and validates the final proposal with `challenge/schema.json`.
5. The browser displays warnings and source quotes, then calls `iframe.contentWindow.loadProposal(proposal, {preview: true})` when the user opens the draft.

All proposals remain drafts. Vague amounts stay blank. Explicit no-income requirements become `"0"`. Approximate wording and income frequency remain in the needs summary. Missing client horizons are explicitly blanked in both objective and goal assessment to avoid adopting a model preset. Amounts are written to both documented key-figure locations for reliable rendering. Model allocations/composition/management fees come from the supplied generator; adviser fees and client targets must come from the notes.

This baseline supports the supplied shared-model use cases. It does not build bespoke allocation schedules or transcribe audio. Pasted transcripts work as text. It does not send WhatsApp messages.

## Files

- `app.py`: local server, sample preview and generation endpoints.
- `agent.py`: prompt, extraction schema, API call, mapping and validation.
- `web/index.html`: notes, review and embedded generator interface.
- `challenge/`: original challenge pack.
- `tests/test_agent.py`: automated validation, API-mock and HTTP checks.
- `docs/`: demo instructions and sample-preview screenshots where available.

## Tests and validation status

```sh
python -m unittest discover -s tests -v
```

12 tests cover sparse notes, explicit zero income, unsupported evidence, unknown portfolios, currency mismatch, invalid types, missing API key, mocked API responses/refusal, sample labelling, file exposure and cross-origin requests.

A browser integration check also passed: the live model catalog was read and the supplied retiree fixture rendered in the original generator with no JavaScript errors. Screenshots in `docs/` are labelled sample previews.

The tests mock the LLM response; they do not measure real-model extraction accuracy. A live API call was not performed during preparation because no API key was configured. Before submitting, run all four supplied notes through **Generate with AI** with your own key and compare key facts against the notes and expected JSON. Do not present fixture previews as evidence of live generation.

## Limitations and next steps

The source-quote check proves the quote appears in the notes; it does not prove that the model interpreted or normalized it correctly. The PM must verify figures, periods and model choice. Model-provided defaults also require review. Conflicting notes may need a follow-up question. Structured output enforces shape, not truth.

With more time, I would add field-level evaluation against the four cases plus contradictory/adversarial notes, deterministic normalization of monetary expressions with stronger evidence checks, and a clarification round for missing values. Then I would add audio transcription and optional WhatsApp integration. I would keep extraction and validation separate so each could be evaluated independently.

The server binds to loopback only and serves an explicit file allowlist. It is a local challenge prototype, not a production deployment. The generator can save drafts in browser local storage; use only synthetic data and clear its drafts/browser site data when finished.
<img width="468" height="642" alt="image" src="https://github.com/user-attachments/assets/77258147-f3ed-4f1f-a0db-0e527cbfe0b6" />
