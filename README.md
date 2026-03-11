# 👁️‍🗨️ LHDSR View : Cognitive Data Visualizer & Forensic AI
**Status:** Proprietary / Closed Source  
**Version:** 1.0 (Providence)  
**Domain:** Semantic Forensics, Cognitive Security, & Multi-Modal AI Orchestration
**Context:** 1st time Coding using Ai

> **Note to Verification Teams (Reddit / HF / etc.):**  
> Due to the sensitive nature of the detection algorithms, custom evasion techniques, and proprietary methodologies, the core source code is kept strictly confidential. This repository serves as an **Architecture Overview** to demonstrate the system design, technical track record, and AI orchestration behind LHDSR View.

---

## 🏗️ System Architecture Overview

LHDSR View is a hybrid Desktop/Cloud-Native investigative platform designed for the forensic analysis of media (Video, Audio, Text). It operates as a "Cognitive Exoskeleton," allowing users to extract, map, and analyze semantic anomalies, logical fallacies, and behavioral patterns from raw data streams.

The system is divided into three main micro-services:

1. **The Ingestion Bridge:** A custom Chromium-based extension that acts as a stealth scraper. It extracts DOM structures, metadata, and handles CORS-bypassing to securely transfer raw intelligence to the local or cloud station.
2. **The Command Core (Backend):** A Python-driven API server handling state, cryptographic ledgers, deterministic data processing, and dynamic AI routing.
3. **The Tactical HUD (Frontend):** A zero-dependency Vanilla JS / CSS3D / WebGL interface for real-time data visualization.

---

## 🧠 AI Orchestration & Multi-Agent Routing

Instead of relying on a single monolithic LLM call, the engine uses a **Dynamic Model Routing** architecture tailored for cost-efficiency and cognitive depth, primarily orchestrating large context multimodal models.

### 1. Swarm Processing (Map-Reduce for Large Contexts)
To handle massive transcripts (up to 2 hours) without suffering from LLM amnesia or hallucination:
*   **Context Window Management:** Large media files are handled via a custom Map-Reduce architecture using `concurrent.futures`.
*   **Temporal Anchoring:** A proprietary preprocessing pipeline programmatically anchors raw text to timecodes before inference, forcing the LLM to ground its outputs temporally.
*   **Multi-Tier Routing:** Fast, lightweight models operate in parallel to extract granular behavioral signals (Map phase), while a high-parameter reasoning model ingests the structured output to synthesize the final narrative (Reduce phase).

### 2. Multi-Modal Kinesics (Sliding Window Analysis)
For facial micro-expressions and vocal stress analysis:
*   **Continuous Stream Processing:** The system uses `ffmpeg` to dynamically slice the video into overlapping high-resolution temporal chunks. This maintains temporal continuity without overloading the multimodal API context limits.
*   **VSR & FACS Detection:** The system orchestrates vision models to perform Visual Speech Recognition (viseme analysis) and Facial Action Coding System evaluations, isolating specific physiological stress markers.

### 3. Deterministic Fallbacks (Hallucination Prevention)
To prevent AI hallucinations in data visualization:
*   Data visualization (e.g., the semantic tension UI graph) is **not** drawn or plotted by the LLM. 
*   The AI is strictly constrained to output discrete JSON events with assigned weights and probabilities.
*   A custom deterministic Python math engine applies signal-processing transformations over these timestamps to compute the final continuous topography, ensuring the UI remains 100% deterministic and free of generative rendering errors.

---

## 🛠️ Technical Stack

### Backend (Core Engine)
*   **Language:** Python 3.11+
*   **API Framework:** Custom `http.server.ThreadingHTTPServer` (Zero-dependency footprint for extreme portability, security, and local sandboxing).
*   **Database:** PostgreSQL (Cloud) / SQLite (Mnemosyne Fallback). Managed via `SQLAlchemy` ORM.
*   **Data Processing:** `ffmpeg` (media slicing), `srt` (subtitle parsing), `difflib` (Fuzzy matching for temporal realignment).

### Frontend (Tactical View)
*   **Architecture:** Vanilla JavaScript (ES6) & HTML5. Zero framework (No React/Vue) to ensure maximum performance and memory control on large DOM updates.
*   **3D Rendering:** `Three.js` (WebGL) for gyro-audio visualizers and `CSS3DRenderer` for spatial data boards.
*   **Data Visualization:** Custom Canvas API manipulations and `Chart.js` for real-time psychometric radar charts.

### Infrastructure & Security
*   **Cryptographic Ledger:** A proprietary SQLite accounting ledger ensuring atomic transactions for the tokenomics system.
*   **Anti-RCE Shields:** Custom WAF logic directly in the HTTP request handler to drop automated exploitation probes.

---

## 📂 Code Snippet: Semantic Anchoring Logic

*(Abstracted from the actual core to demonstrate the temporal realignment and fuzzy matching logic used to correct LLM timecode drift).*

```python
def align_signals_to_timecodes(self, signals: List[Dict], srt_data: str) -> List[Dict]:
    """
    Fuzzy-matching algorithm to realign LLM-generated quotes 
    back to the absolute source of truth (the original SRT file).
    Compensates for LLM paraphrasing or timecode drift.
    """
    subs = list(srt.parse(srt_data))
    aligned_signals = []
    
    for signal in signals:
        gemini_start = float(signal.get('start', 0))
        raw_quote = str(signal.get('quote', '')).lower()
        clean_quote = re.sub(r'[^\w\s]', '', raw_quote).strip()
        
        best_match_sub = None
        best_match_score = 0
        
        # Dynamic temporal window slicing
        window_start = max(0, gemini_start - self.SEARCH_WINDOW_SEC)
        window_end = gemini_start + self.SEARCH_WINDOW_SEC
        relevant_subs = [s for s in subs if s.end.total_seconds() > window_start and s.start.total_seconds() < window_end]

        for sub in relevant_subs:
            sub_content = re.sub(r'[^\w\s]', '', sub.content.lower())
            
            # Fuzzy Matching via SequenceMatcher
            ratio = difflib.SequenceMatcher(None, clean_quote, sub_content).find_longest_match(0, len(clean_quote), 0, len(sub_content))
            
            if ratio.size > len(clean_quote) * self.CONFIDENCE_THRESHOLD: 
                dist = abs(sub.start.total_seconds() - gemini_start)
                sim_score = (ratio.size / len(clean_quote)) * 100
                match_score = (500 * (sim_score/100)) - dist # Penalty for temporal distance
                
                if match_score > best_match_score:
                    best_match_score = match_score
                    best_match_sub = sub

        if best_match_sub:
            signal['start'] = best_match_sub.start.total_seconds()
            
        aligned_signals.append(signal)
        
    return aligned_signals
