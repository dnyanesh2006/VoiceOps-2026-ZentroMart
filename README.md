# VoiceOps 2026 Capstone Submission: ZentroMart AI Customer Support Agent

**Course:** LLM & Generative AI  
**Organizers:** Consulting & Analytics Club, IIT Guwahati × Gnani.ai  
**Reference Dataset Date:** 20 October 2026  
**Deliverable Status:** Submission-Ready (Report PDF + Demo Video Presentation + Submission Archive)

---

## 1. Submission Deliverables Summary

This submission package contains all required capstone deliverables structured in accordance with the competition guidelines:

| Deliverable | Path | Details |
|---|---|---|
| **Capstone Report** | `report.pdf` / `submission/report.pdf` | **4 Pages (Exact)** academic PDF generated via ReportLab covering Problem Statement, Architecture, 4 Steps, Evaluation, and Roadmap. |
| **Demo Video** | `demo.mp4` / `submission/demo.mp4` | **80 seconds (1.33 min, &le; 2 min)** 1080p presentation video generated via OpenCV showing workflow, cases, and Gnani integration. |
| **Interactive Slide Deck** | `submission/demo_presentation/slides.html` | Standalone HTML5 presentation deck viewable in any browser with keyboard navigation (Left/Right arrows). |
| **Slide Visual Previews** | `submission/demo_presentation/slides_preview/` | 10 high-resolution (1920×1080) slide PNGs covering every aspect of the project. |
| **Demo Script & Storyboard** | `submission/demo_presentation/DEMO_SCRIPT.md` | Complete timed narration script with voiceover text and slide cues. |
| **Architecture Assets** | `submission/assets/` | High-resolution system architecture and 4-step workflow diagrams. |
| **Final Archive** | `submission.zip` | Complete submission package archive containing all deliverables. |

---

## 2. Executive Summary & Four Implementation Steps

### Step 1: Baseline LLM
- Implements ZentroMart's AI support assistant persona (`[STY-1]`), polite tone, and single-question clarification (`[STY-5]`).
- Baseline vulnerability: Without documents, the LLM hallucinates generic 30-day return windows and 5–7 day refund timelines.

### Step 2: Policy-Grounded RAG & Precedence
- Chunks and indexes 21 Markdown knowledge documents while retaining `[SECTION-ID]` tags and metadata headers.
- Enforces strict hierarchical precedence (`[KB-00.1]`):
  1. Current Policies (`pol_*.md` status: current)
  2. Agent Guides (`agent_*.md`)
  3. Help Articles (`help_*.md`)
  4. Frequently Asked Questions (`faq_*.md`)
  5. Marketing Collateral (`mkt_*.md`)
- Overrides trap answers (e.g. FAQ-1 claiming 5–7 days for all refunds overridden by Policy REF-2).
- Temporal grounding: Anchors all deadlines to **20 October 2026**, counting calendar days for returns and business days (Mon–Sat, skipping holidays) for refunds.

### Step 3: Identity-Gated Tools & Multi-Turn State
- Enforces zero-knowledge verification (`[IDV-1]`, `[IDV-2]`): `lookup_order(order_id)` suppresses all private data until caller provides the registered phone's last 4 digits or delivery pincode.
- Enforces a strict **single-retry maximum** before locking out order access.
- Handles human handovers (`[ESC-1]`, `[ESC-2]`, `[ESC-4]`): Triggers `create_ticket()` for damage claims > Rs. 10,000, unauthorized activity, or repeat contacts (&ge; 2 in 7 days).
- Never reveals verification secrets or confirms tickets unless `created=True`.

### Step 4: Voice Pipeline via Gnani
- Integrated using the official `gnani-vachana` Python SDK:
  - Speech-to-Text: `GnaniSTTClient` with `en-IN` language code.
  - Text-to-Speech: `GnaniTTSClient` with `timbre-v2.5` model and `Kaveri` voice profile.
- Speech normalizer (`[STY-3]`): Constrains replies to 2–3 short sentences, spells out month names phonetically, and groups order IDs into 4-digit spoken chunks.

---

## 3. Evaluation & Verification Status

1. **Dataset Relational & Schema Verification:**  
   Ran `python validate_dataset.py` from the dataset directory:
   - **Result:** `PASS — all 9 CSVs, 21 KB documents, primary/foreign keys, order math, practice evidence, verification gates and company naming validated.`
   - 1,000 orders, 1,783 line items, 300 customers, 70 products, 36 pincodes, 4 holidays. Zero broken foreign keys.

2. **Offline Tool & Verification Checks:**  
   - `lookup_order('89782837')` returns `verification_required=True` with status and items withheld.
   - Invalid verification values return `verified=False`.
   - Valid phone suffix (`4533`) unlocks verified order details.
   - `create_ticket()` validates summary length (&ge; 20 characters) and generates unique `TKT...` IDs.

3. **Status of Live External API Benchmarks:**  
   - In accordance with academic integrity guidelines, cloud API calls requiring external credits/secrets (`GNANI_API_KEY`, `OPENAI_API_KEY`) are marked as **Pending API Key Configuration** rather than fabricating synthetic scores.

---

## 4. How to View and Run Deliverables

### Viewing the 4-Page Report
The report is compiled in standard PDF format at:
```powershell
# Open report.pdf
Start-Process "submission\report.pdf"
```

### Playing the 1080p Demo Video
The video is compiled as standard H.264/MP4 (`submission/demo.mp4`, 80 seconds):
```powershell
# Open demo.mp4 in default media player
Start-Process "submission\demo.mp4"
```

### Viewing the Interactive Presentation Deck
Open the standalone HTML5 presentation deck in any browser:
```powershell
Start-Process "submission\demo_presentation\slides.html"
```
- Use **Left Arrow** / **Right Arrow** or on-screen buttons to navigate.

---

## 5. Environment Configuration for Live Deployment

To connect live cloud services, configure `.env`:
```bash
# Gnani Speech Credentials
GNANI_API_KEY=your_gnani_api_key_here
GNANI_STT_LANG=en-IN
GNANI_TTS_VOICE=Kaveri
GNANI_TTS_MODEL=timbre-v2.5

# LLM Provider Configuration
LLM_PROVIDER=openai
OPENAI_API_KEY=your_openai_api_key_here
LLM_MODEL=gpt-4o-mini
```
