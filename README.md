# Anamnesis

Prototype of an automated psychiatric intake workflow, modelled on the referral-to-letter flow of virtual consult clinics:

1. **Clinician setup** – create an intake from a referral, with the referral letter and medication list.
2. **Patient intake** – consent screen, then a live AI interviewer that asks one question at a time across six sections.
3. **Draft consult note** – every sentence is tagged with its source (transcript turn, referral, medication list, questionnaire) and checked against it; unmatched sentences are flagged.
4. **Letters** – GP consultation letter, plain-language patient summary and a work letter, drafted only from the approved note and the psychiatrist's plan.

> **Prototype only. Use fictional patients.** No real health information. Not PIPEDA-compliant, not hosted in Canada, no authentication, no audit log.

## Interview design

- **Two-layer modules.** 11 core modules every patient gets (story, mood, safety, anxiety, sleep, substances, past care, health and medications, family, life and work, goals) plus triggered modules added when the patient raises something (highs and energy, unusual experiences, difficult experiences, focus since childhood, eating, memory).
- **Safety is core, not triggered.** Asked right after mood, or earlier if the patient mentions hopelessness or death.
- **Deterministic safety net.** Independently of the model, the app shows crisis resources (911, 9-8-8, 1-800-784-2433) and flags the intake "Needs review now" when the patient's words match a self-harm pattern, PHQ-9 item 9 is above 0, the safety topic is skipped, or the patient presses "I need help now".
- **Validated questionnaires.** PHQ-9 and GAD-7 in validated wording, scored in code, shown when the mood and anxiety modules finish.
- **Interview rules** (see `ENGINE_RULES` in `index.html`): one question per turn, no re-asking, loss of interest asked separately from low mood, clarify ambiguous answers, never attribute unsaid statements, introduce referral facts as such, log contradictions, red-flag follow-ups (e.g. choking at night → sleep apnea questions), and the mania module is gated on first establishing a distinct episode.
- **Coverage check.** The app refuses to close the interview while any core module is unfinished.
- **Patient controls.** Skip a topic (defer to the visit), pause and resume, text or dictation.

## Running it

### In a browser (GitHub Pages)

1. Open **https://brycycling.github.io/anamesis/**
2. Go to **AI setup**, paste an Anthropic API key (from console.anthropic.com) and click **Save and test**.
3. Click **Load sample case** to try the note and letters, or **New intake** to run an interview.

In this mode the page calls Anthropic's API directly from the browser with your key, stores intakes in the browser's local storage, and uses Chrome's speech recognition for dictation. The key and intakes never leave that browser except for the API calls. Don't save a key on a shared computer. Running locally works too: serve the folder (`python3 -m http.server`) and open `http://localhost:8000`.

### Inside Claude

Published as a claude.ai Artifact, the same file uses Artifact runtime capabilities instead: `sample` (AI on the viewer's Claude account), `db` (shared storage, collection `intakes`) and `dictation`. No API key is needed there.

### Production

Neither mode is suitable for real patients. A production version needs a backend that holds the API key, authenticated per-patient links, and a Canadian-hosted database with encryption and audit logging.

## Files

- `index.html` – the whole app (HTML, CSS, JS).
- `seed/demo-morgan.json` – a completed fictional intake (Morgan Tse) for trying the note and letters without running an interview.

## Not yet built

Authentication and per-patient links; clinician alert paging with a response-time commitment; Canadian data residency, encryption at rest and audit logging; EMR, MSP billing and fax integration; visit recording and scribe; sentence-level edit history; more questionnaires (ASRS, PCL-5, AUDIT; check licensing for others such as the ISI); teen version; occupational modules (functional impairment, return-to-work, disability forms) and geriatric modules (cognitive screen, collateral from family).
