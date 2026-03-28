<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>RAG Evaluation Pipeline — RAGAS & Observability</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #080b10;
    --surface: #0d1117;
    --surface2: #131a24;
    --border: #1e2d3d;
    --accent: #00d9ff;
    --accent2: #7c3aed;
    --accent3: #10b981;
    --warn: #f59e0b;
    --text: #e2e8f0;
    --muted: #64748b;
    --code-bg: #0a0f16;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Syne', sans-serif;
    line-height: 1.7;
    overflow-x: hidden;
  }

  /* Grid overlay */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,217,255,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,217,255,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .container {
    max-width: 900px;
    margin: 0 auto;
    padding: 0 2rem;
    position: relative;
    z-index: 1;
  }

  /* ── HERO ── */
  .hero {
    padding: 5rem 0 3rem;
    position: relative;
  }

  .hero-eyebrow {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.7rem;
    color: var(--accent);
    letter-spacing: 0.2em;
    text-transform: uppercase;
    margin-bottom: 1.5rem;
    border: 1px solid rgba(0,217,255,0.25);
    padding: 0.35rem 0.8rem;
    border-radius: 2px;
  }

  .hero-eyebrow::before {
    content: '▶';
    font-size: 0.55rem;
  }

  h1 {
    font-size: clamp(2rem, 5vw, 3.4rem);
    font-weight: 800;
    line-height: 1.1;
    margin-bottom: 1.5rem;
    letter-spacing: -0.02em;
  }

  h1 .line1 { display: block; color: var(--text); }
  h1 .line2 { display: block; color: var(--accent); }

  .hero-desc {
    font-size: 1.05rem;
    color: var(--muted);
    max-width: 600px;
    margin-bottom: 2.5rem;
  }

  .badge-row {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    margin-bottom: 3rem;
  }

  .badge {
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    padding: 0.3rem 0.7rem;
    border-radius: 2px;
    letter-spacing: 0.05em;
    font-weight: 700;
  }

  .badge-blue  { background: rgba(0,217,255,0.12); color: var(--accent); border: 1px solid rgba(0,217,255,0.25); }
  .badge-purple{ background: rgba(124,58,237,0.15); color: #a78bfa; border: 1px solid rgba(124,58,237,0.3); }
  .badge-green { background: rgba(16,185,129,0.12); color: var(--accent3); border: 1px solid rgba(16,185,129,0.25); }
  .badge-amber { background: rgba(245,158,11,0.12); color: var(--warn); border: 1px solid rgba(245,158,11,0.25); }

  /* ── DIVIDER ── */
  .divider {
    height: 1px;
    background: linear-gradient(90deg, transparent, var(--border), transparent);
    margin: 2.5rem 0;
  }

  /* ── SECTION HEADERS ── */
  .section {
    margin-bottom: 3rem;
  }

  .section-label {
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    color: var(--accent);
    letter-spacing: 0.2em;
    text-transform: uppercase;
    margin-bottom: 0.75rem;
    display: flex;
    align-items: center;
    gap: 0.6rem;
  }

  .section-label::after {
    content: '';
    flex: 1;
    height: 1px;
    background: var(--border);
  }

  h2 {
    font-size: 1.5rem;
    font-weight: 700;
    margin-bottom: 1.25rem;
    color: var(--text);
    letter-spacing: -0.01em;
  }

  h3 {
    font-size: 1.05rem;
    font-weight: 700;
    margin-bottom: 0.75rem;
    color: var(--accent);
    font-family: 'Space Mono', monospace;
    font-size: 0.85rem;
    letter-spacing: 0.02em;
  }

  p { color: #94a3b8; margin-bottom: 1rem; font-size: 0.97rem; }

  /* ── ARCHITECTURE ── */
  .arch-block {
    background: var(--code-bg);
    border: 1px solid var(--border);
    border-left: 3px solid var(--accent);
    border-radius: 4px;
    padding: 1.5rem 1.75rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.78rem;
    color: #94a3b8;
    line-height: 2;
    overflow-x: auto;
  }

  .arch-block .hl { color: var(--accent); }
  .arch-block .hl2 { color: #a78bfa; }
  .arch-block .hl3 { color: var(--accent3); }
  .arch-block .arrow { color: var(--muted); }

  /* ── PIPELINE STEPS ── */
  .steps {
    display: flex;
    flex-direction: column;
    gap: 1px;
    margin-bottom: 2rem;
  }

  .step {
    display: grid;
    grid-template-columns: 3rem 1fr;
    gap: 1.25rem;
    background: var(--surface);
    border: 1px solid var(--border);
    padding: 1.25rem 1.5rem;
    position: relative;
    transition: border-color 0.2s;
  }

  .step:hover { border-color: rgba(0,217,255,0.3); }

  .step:first-child { border-radius: 4px 4px 0 0; }
  .step:last-child  { border-radius: 0 0 4px 4px; }

  .step-num {
    font-family: 'Space Mono', monospace;
    font-size: 1.3rem;
    font-weight: 700;
    color: var(--accent);
    line-height: 1;
    padding-top: 0.1rem;
    opacity: 0.5;
  }

  .step-content h3 {
    color: var(--text);
    font-family: 'Syne', sans-serif;
    font-size: 0.95rem;
    font-weight: 700;
    letter-spacing: 0;
    margin-bottom: 0.4rem;
  }

  .step-content p { margin: 0; font-size: 0.88rem; }

  .step-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 0.35rem;
    margin-top: 0.6rem;
  }

  .tag {
    font-family: 'Space Mono', monospace;
    font-size: 0.6rem;
    padding: 0.15rem 0.5rem;
    background: rgba(0,217,255,0.06);
    border: 1px solid rgba(0,217,255,0.15);
    color: var(--accent);
    border-radius: 2px;
  }

  /* ── METRICS TABLE ── */
  .metrics-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
    gap: 1px;
    background: var(--border);
    border: 1px solid var(--border);
    border-radius: 4px;
    overflow: hidden;
    margin-bottom: 2rem;
  }

  .metric-card {
    background: var(--surface);
    padding: 1.25rem;
    transition: background 0.2s;
  }

  .metric-card:hover { background: var(--surface2); }

  .metric-icon {
    font-size: 1.4rem;
    margin-bottom: 0.5rem;
  }

  .metric-name {
    font-family: 'Space Mono', monospace;
    font-size: 0.68rem;
    color: var(--accent);
    letter-spacing: 0.05em;
    margin-bottom: 0.35rem;
    font-weight: 700;
  }

  .metric-desc {
    font-size: 0.8rem;
    color: var(--muted);
    margin: 0;
    line-height: 1.4;
  }

  /* ── TECH STACK ── */
  .tech-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 1rem;
    margin-bottom: 2rem;
  }

  .tech-item {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 1rem 1.25rem;
    display: flex;
    align-items: center;
    gap: 0.75rem;
    transition: border-color 0.2s, transform 0.2s;
  }

  .tech-item:hover {
    border-color: rgba(124,58,237,0.4);
    transform: translateY(-1px);
  }

  .tech-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  .dot-blue   { background: var(--accent); box-shadow: 0 0 8px var(--accent); }
  .dot-purple { background: #a78bfa; box-shadow: 0 0 8px #a78bfa; }
  .dot-green  { background: var(--accent3); box-shadow: 0 0 8px var(--accent3); }
  .dot-amber  { background: var(--warn); box-shadow: 0 0 8px var(--warn); }

  .tech-name {
    font-weight: 700;
    font-size: 0.88rem;
    color: var(--text);
  }

  .tech-role {
    font-size: 0.75rem;
    color: var(--muted);
    font-family: 'Space Mono', monospace;
  }

  /* ── CODE BLOCKS ── */
  .code-block {
    background: var(--code-bg);
    border: 1px solid var(--border);
    border-radius: 4px;
    overflow: hidden;
    margin-bottom: 1.5rem;
  }

  .code-header {
    background: var(--surface2);
    padding: 0.5rem 1rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    color: var(--muted);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: center;
    gap: 0.5rem;
  }

  .code-header::before {
    content: '●';
    color: var(--accent);
    font-size: 0.5rem;
  }

  .code-body {
    padding: 1.25rem 1.5rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.78rem;
    color: #94a3b8;
    line-height: 2;
  }

  .code-body .cmd { color: var(--accent3); }
  .code-body .str { color: #f9a8d4; }
  .code-body .kw  { color: #a78bfa; }

  /* ── FEATURES ── */
  .features-list {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 0.75rem;
    margin-bottom: 2rem;
  }

  @media (max-width: 560px) { .features-list { grid-template-columns: 1fr; } }

  .feature-item {
    display: flex;
    align-items: flex-start;
    gap: 0.75rem;
    background: var(--surface);
    border: 1px solid var(--border);
    padding: 0.9rem 1.1rem;
    border-radius: 4px;
    font-size: 0.87rem;
    color: #94a3b8;
    transition: border-color 0.2s;
  }

  .feature-item:hover { border-color: rgba(16,185,129,0.3); }

  .feature-check {
    color: var(--accent3);
    font-size: 0.9rem;
    flex-shrink: 0;
    margin-top: 0.05rem;
    font-family: 'Space Mono', monospace;
  }

  /* ── LIMITATIONS ── */
  .limits-list {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    margin-bottom: 2rem;
  }

  .limit-item {
    display: flex;
    align-items: flex-start;
    gap: 0.75rem;
    font-size: 0.87rem;
    color: #94a3b8;
    padding: 0.6rem 0;
    border-bottom: 1px solid var(--border);
  }

  .limit-item:last-child { border-bottom: none; }

  .limit-warn {
    color: var(--warn);
    font-family: 'Space Mono', monospace;
    flex-shrink: 0;
  }

  /* ── FUTURE / USE CASES ── */
  .two-col {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
    margin-bottom: 2rem;
  }

  @media (max-width: 600px) { .two-col { grid-template-columns: 1fr; } }

  .list-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 1.25rem 1.5rem;
  }

  .list-card h3 {
    color: var(--text);
    font-family: 'Syne', sans-serif;
    font-size: 0.9rem;
    font-weight: 700;
    letter-spacing: 0;
    margin-bottom: 1rem;
    padding-bottom: 0.6rem;
    border-bottom: 1px solid var(--border);
  }

  .list-card ul {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .list-card li {
    font-size: 0.83rem;
    color: #94a3b8;
    padding-left: 1.1rem;
    position: relative;
  }

  .list-card li::before {
    content: '→';
    position: absolute;
    left: 0;
    color: var(--accent2);
    font-family: 'Space Mono', monospace;
    font-size: 0.7rem;
  }

  /* ── PROBLEM CALLOUT ── */
  .callout {
    background: linear-gradient(135deg, rgba(124,58,237,0.08), rgba(0,217,255,0.05));
    border: 1px solid rgba(124,58,237,0.25);
    border-radius: 4px;
    padding: 1.5rem 1.75rem;
    margin-bottom: 2rem;
  }

  .callout p { margin: 0; font-size: 0.92rem; }
  .callout strong { color: var(--text); }

  /* ── FOOTER ── */
  .footer {
    padding: 3rem 0 4rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: gap;
    gap: 1rem;
  }

  .footer-left {
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    color: var(--muted);
    letter-spacing: 0.1em;
  }

  .license-badge {
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    padding: 0.35rem 0.8rem;
    border: 1px solid var(--border);
    color: var(--muted);
    border-radius: 2px;
    letter-spacing: 0.08em;
  }

  /* ── ANIMATIONS ── */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(16px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  .hero { animation: fadeUp 0.5s ease both; }
  .section { animation: fadeUp 0.5s ease both; }

  .section:nth-child(2)  { animation-delay: 0.05s; }
  .section:nth-child(3)  { animation-delay: 0.1s; }
  .section:nth-child(4)  { animation-delay: 0.15s; }
  .section:nth-child(5)  { animation-delay: 0.2s; }
  .section:nth-child(6)  { animation-delay: 0.25s; }
  .section:nth-child(7)  { animation-delay: 0.3s; }
  .section:nth-child(8)  { animation-delay: 0.35s; }

  /* Glow pulse on accent elements */
  @keyframes pulse-glow {
    0%, 100% { opacity: 0.5; }
    50% { opacity: 1; }
  }

  .dot-blue { animation: pulse-glow 2.5s ease infinite; }
</style>
</head>
<body>
<div class="container">

  <!-- HERO -->
  <header class="hero">
    <div class="hero-eyebrow">Open Source · MIT License</div>
    <h1>
      <span class="line1">RAG Evaluation Pipeline</span>
      <span class="line2">RAGAS + Observability</span>
    </h1>
    <p class="hero-desc">
      End-to-end evaluation of Retrieval-Augmented Generation systems with synthetic dataset generation, reference-free metrics, and production-grade monitoring.
    </p>
    <div class="badge-row">
      <span class="badge badge-blue">Python</span>
      <span class="badge badge-purple">LangChain</span>
      <span class="badge badge-green">RAGAS</span>
      <span class="badge badge-blue">ChromaDB</span>
      <span class="badge badge-amber">OpenAI</span>
      <span class="badge badge-purple">Langfuse</span>
    </div>
  </header>

  <div class="divider"></div>

  <!-- PROBLEM -->
  <section class="section">
    <div class="section-label">Problem Statement</div>
    <h2>Why evaluating RAG is hard</h2>
    <div class="callout">
      <p>Evaluating LLM-based RAG systems is fundamentally difficult: outputs are <strong>non-deterministic</strong>, ground truth is <strong>often unavailable</strong>, hallucinations are <strong>hard to detect</strong>, and retrieval quality <strong>directly impacts</strong> downstream performance. This project solves these by using reference-free evaluation metrics and synthetic dataset generation.</p>
    </div>
  </section>

  <!-- ARCHITECTURE -->
  <section class="section">
    <div class="section-label">Architecture</div>
    <h2>System Design</h2>
    <div class="arch-block">
      <span class="hl">Documents</span>
      <span class="arrow"> ──▶ </span>
      <span class="hl2">Chunking</span>
      <span class="arrow"> ──▶ </span>
      <span class="hl2">Embeddings</span>
      <span class="arrow"> ──▶ </span>
      <span class="hl">Vector DB</span>
      <span class="arrow"> ──▶ </span>
      <span class="hl">Retriever</span><br>
      <span class="arrow">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓</span><br>
      <span class="hl2">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;RAG Pipeline</span>
      <span class="arrow"> ──▶ </span>
      <span class="hl">Generated Answers</span><br>
      <span class="arrow">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓</span><br>
      <span class="hl3">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;RAGAS Evaluation</span>
      <span class="arrow"> ──▶ </span>
      <span class="hl3">Metrics</span>
      <span class="arrow"> ──▶ </span>
      <span class="hl">Langfuse</span>
    </div>
  </section>

  <!-- TECH STACK -->
  <section class="section">
    <div class="section-label">Tech Stack</div>
    <h2>Components</h2>
    <div class="tech-grid">
      <div class="tech-item">
        <div class="tech-dot dot-blue"></div>
        <div>
          <div class="tech-name">LangChain</div>
          <div class="tech-role">RAG orchestration</div>
        </div>
      </div>
      <div class="tech-item">
        <div class="tech-dot dot-green"></div>
        <div>
          <div class="tech-name">RAGAS</div>
          <div class="tech-role">Evaluation framework</div>
        </div>
      </div>
      <div class="tech-item">
        <div class="tech-dot dot-blue"></div>
        <div>
          <div class="tech-name">ChromaDB</div>
          <div class="tech-role">Vector storage</div>
        </div>
      </div>
      <div class="tech-item">
        <div class="tech-dot dot-amber"></div>
        <div>
          <div class="tech-name">OpenAI</div>
          <div class="tech-role">GPT + Embeddings</div>
        </div>
      </div>
      <div class="tech-item">
        <div class="tech-dot dot-purple"></div>
        <div>
          <div class="tech-name">Langfuse</div>
          <div class="tech-role">Observability</div>
        </div>
      </div>
      <div class="tech-item">
        <div class="tech-dot dot-green"></div>
        <div>
          <div class="tech-name">Pandas + Seaborn</div>
          <div class="tech-role">Visualization</div>
        </div>
      </div>
    </div>
  </section>

  <!-- PIPELINE -->
  <section class="section">
    <div class="section-label">Pipeline</div>
    <h2>Steps</h2>
    <div class="steps">
      <div class="step">
        <div class="step-num">01</div>
        <div class="step-content">
          <h3>Document Processing</h3>
          <p>Load raw text, split into overlapping chunks, and attach metadata for full traceability throughout the pipeline.</p>
          <div class="step-tags">
            <span class="tag">text-splitter</span>
            <span class="tag">chunk-overlap</span>
            <span class="tag">metadata</span>
          </div>
        </div>
      </div>
      <div class="step">
        <div class="step-num">02</div>
        <div class="step-content">
          <h3>Test Dataset Generation</h3>
          <p>Automatically generate evaluation questions via LLM — simple, reasoning-based, and multi-context question types supported.</p>
          <div class="step-tags">
            <span class="tag">synthetic-qa</span>
            <span class="tag">multi-context</span>
            <span class="tag">reasoning</span>
          </div>
        </div>
      </div>
      <div class="step">
        <div class="step-num">03</div>
        <div class="step-content">
          <h3>RAG System</h3>
          <p>Embed chunks into ChromaDB, retrieve relevant documents per query, and generate answers using the configured LLM.</p>
          <div class="step-tags">
            <span class="tag">embeddings</span>
            <span class="tag">chroma</span>
            <span class="tag">retrieval</span>
          </div>
        </div>
      </div>
      <div class="step">
        <div class="step-num">04</div>
        <div class="step-content">
          <h3>Evaluation via RAGAS</h3>
          <p>Score pipeline quality across five reference-free metrics covering retrieval and generation quality dimensions.</p>
          <div class="step-tags">
            <span class="tag">context-relevancy</span>
            <span class="tag">faithfulness</span>
            <span class="tag">answer-relevancy</span>
          </div>
        </div>
      </div>
      <div class="step">
        <div class="step-num">05</div>
        <div class="step-content">
          <h3>Visualization</h3>
          <p>Heatmaps and charts to pinpoint weak components. See exactly where your pipeline loses performance.</p>
          <div class="step-tags">
            <span class="tag">seaborn</span>
            <span class="tag">heatmaps</span>
          </div>
        </div>
      </div>
      <div class="step">
        <div class="step-num">06</div>
        <div class="step-content">
          <h3>Observability with Langfuse</h3>
          <p>Every evaluation run is traced. Metric scores are logged for production monitoring and regression detection.</p>
          <div class="step-tags">
            <span class="tag">traces</span>
            <span class="tag">scores</span>
            <span class="tag">monitoring</span>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- METRICS -->
  <section class="section">
    <div class="section-label">Evaluation</div>
    <h2>RAGAS Metrics</h2>
    <div class="metrics-grid">
      <div class="metric-card">
        <div class="metric-icon">🎯</div>
        <div class="metric-name">Context Relevancy</div>
        <p class="metric-desc">Are retrieved documents relevant to the query?</p>
      </div>
      <div class="metric-card">
        <div class="metric-icon">🔬</div>
        <div class="metric-name">Context Precision</div>
        <p class="metric-desc">How much retrieved context is actually useful?</p>
      </div>
      <div class="metric-card">
        <div class="metric-icon">📡</div>
        <div class="metric-name">Context Recall</div>
        <p class="metric-desc">Was all necessary information retrieved?</p>
      </div>
      <div class="metric-card">
        <div class="metric-icon">⚖️</div>
        <div class="metric-name">Faithfulness</div>
        <p class="metric-desc">Is the answer grounded in retrieved context?</p>
      </div>
      <div class="metric-card">
        <div class="metric-icon">💬</div>
        <div class="metric-name">Answer Relevancy</div>
        <p class="metric-desc">Does the answer address the actual question?</p>
      </div>
    </div>
  </section>

  <!-- HOW TO RUN -->
  <section class="section">
    <div class="section-label">Setup</div>
    <h2>How to Run</h2>
    <div class="code-block">
      <div class="code-header">install dependencies</div>
      <div class="code-body">
        <span class="cmd">pip install</span> <span class="str">langchain ragas chromadb openai langfuse seaborn pandas</span>
      </div>
    </div>
    <div class="code-block">
      <div class="code-header">environment variables</div>
      <div class="code-body">
        <span class="kw">export</span> OPENAI_API_KEY=<span class="str">your_key</span><br>
        <span class="kw">export</span> LANGFUSE_PUBLIC_KEY=<span class="str">your_key</span><br>
        <span class="kw">export</span> LANGFUSE_SECRET_KEY=<span class="str">your_key</span>
      </div>
    </div>
    <div class="code-block">
      <div class="code-header">run</div>
      <div class="code-body">
        <span class="cmd">jupyter notebook</span> <span class="str">ragas.ipynb</span>
      </div>
    </div>
  </section>

  <!-- FEATURES -->
  <section class="section">
    <div class="section-label">Features</div>
    <h2>Key Capabilities</h2>
    <div class="features-list">
      <div class="feature-item"><span class="feature-check">✓</span> Synthetic test dataset generation</div>
      <div class="feature-item"><span class="feature-check">✓</span> End-to-end RAG evaluation</div>
      <div class="feature-item"><span class="feature-check">✓</span> Reference-free metrics — no labels needed</div>
      <div class="feature-item"><span class="feature-check">✓</span> Production-grade observability</div>
      <div class="feature-item"><span class="feature-check">✓</span> Modular and extensible pipeline</div>
      <div class="feature-item"><span class="feature-check">✓</span> Hallucination detection via Faithfulness</div>
    </div>
  </section>

  <!-- LIMITATIONS + FUTURE -->
  <section class="section">
    <div class="two-col">
      <div class="list-card">
        <h3>⚠ Limitations</h3>
        <div class="limits-list">
          <div class="limit-item"><span class="limit-warn">!</span> Small default dataset (10 samples)</div>
          <div class="limit-item"><span class="limit-warn">!</span> No advanced error analysis</div>
          <div class="limit-item"><span class="limit-warn">!</span> Requires OpenAI API access</div>
          <div class="limit-item"><span class="limit-warn">!</span> Metrics are LLM-based</div>
        </div>
      </div>
      <div class="list-card">
        <h3>🛠 Future Improvements</h3>
        <ul>
          <li>Add DeepEval for hybrid evaluation</li>
          <li>Scale dataset for robustness</li>
          <li>Failure case analysis</li>
          <li>Compare retrievers & embeddings</li>
          <li>Latency and cost tracking</li>
        </ul>
      </div>
    </div>
  </section>

  <!-- USE CASES + LEARNING -->
  <section class="section">
    <div class="two-col">
      <div class="list-card">
        <h3>📌 Use Cases</h3>
        <ul>
          <li>Evaluating RAG-based chatbots</li>
          <li>Debugging hallucinations</li>
          <li>Benchmarking retrieval systems</li>
          <li>Monitoring LLM apps in production</li>
        </ul>
      </div>
      <div class="list-card">
        <h3>🎯 Learning Outcomes</h3>
        <ul>
          <li>Evaluate beyond accuracy</li>
          <li>Design evaluation pipelines</li>
          <li>Integrate observability into AI</li>
          <li>Debug RAG failures with metrics</li>
        </ul>
      </div>
    </div>
  </section>

  <div class="divider"></div>

  <!-- FOOTER -->
  <footer class="footer">
    <div class="footer-left">RAG EVALUATION PIPELINE · MIT LICENSE · OPEN SOURCE</div>
    <div class="license-badge">MIT LICENSE</div>
  </footer>

</div>
</body>
</html>
