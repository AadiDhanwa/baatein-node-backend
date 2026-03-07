<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Baatein — DevOps Architecture</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;600;700;800&display=swap');

  :root {
    --bg: #0a0e17;
    --surface: #111827;
    --border: #1e2d45;
    --accent: #00d4ff;
    --accent2: #7c3aed;
    --accent3: #10b981;
    --accent4: #f59e0b;
    --text: #e2e8f0;
    --muted: #64748b;
    --danger: #ef4444;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Syne', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Grid background */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,212,255,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,212,255,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .wrapper {
    position: relative;
    z-index: 1;
    max-width: 960px;
    margin: 0 auto;
    padding: 48px 24px 80px;
  }

  /* ── HEADER ── */
  .header {
    text-align: center;
    margin-bottom: 56px;
    animation: fadeDown 0.7s ease both;
  }

  .badge {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background: rgba(0,212,255,0.08);
    border: 1px solid rgba(0,212,255,0.25);
    border-radius: 999px;
    padding: 6px 16px;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--accent);
    letter-spacing: 1px;
    text-transform: uppercase;
    margin-bottom: 20px;
  }

  .badge .dot {
    width: 6px; height: 6px;
    background: var(--accent3);
    border-radius: 50%;
    animation: pulse 1.5s infinite;
  }

  h1 {
    font-size: clamp(36px, 6vw, 64px);
    font-weight: 800;
    line-height: 1.05;
    letter-spacing: -2px;
    background: linear-gradient(135deg, #ffffff 0%, #00d4ff 50%, #7c3aed 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    margin-bottom: 16px;
  }

  .subtitle {
    font-size: 16px;
    color: var(--muted);
    max-width: 560px;
    margin: 0 auto;
    line-height: 1.6;
  }

  .subtitle span { color: var(--accent); font-weight: 600; }

  /* ── PIPELINE FLOW ── */
  .section-title {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 2px;
    color: var(--muted);
    margin-bottom: 20px;
    display: flex;
    align-items: center;
    gap: 12px;
  }
  .section-title::after {
    content: '';
    flex: 1;
    height: 1px;
    background: var(--border);
  }

  .pipeline {
    display: flex;
    align-items: stretch;
    gap: 0;
    margin-bottom: 48px;
    overflow-x: auto;
    padding-bottom: 8px;
    animation: fadeUp 0.7s 0.2s ease both;
  }

  .pipe-step {
    flex: 1;
    min-width: 110px;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 16px 12px;
    text-align: center;
    position: relative;
    transition: transform 0.2s, border-color 0.2s;
  }
  .pipe-step:hover {
    transform: translateY(-4px);
    border-color: var(--accent);
  }

  .pipe-step + .pipe-step {
    margin-left: -1px;
    border-radius: 0 12px 12px 0;
  }
  .pipe-step:first-child { border-radius: 12px 0 0 12px; }

  .pipe-arrow {
    display: flex;
    align-items: center;
    padding: 0 4px;
    color: var(--muted);
    font-size: 18px;
    flex-shrink: 0;
  }

  .pipe-icon {
    font-size: 24px;
    margin-bottom: 8px;
  }

  .pipe-name {
    font-size: 11px;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    color: var(--text);
    margin-bottom: 4px;
  }

  .pipe-tool {
    font-family: 'Space Mono', monospace;
    font-size: 9px;
    color: var(--muted);
  }

  .pipe-step.green { border-color: rgba(16,185,129,0.4); }
  .pipe-step.green .pipe-icon { filter: drop-shadow(0 0 8px #10b981); }
  .pipe-step.blue { border-color: rgba(0,212,255,0.4); }
  .pipe-step.blue .pipe-icon { filter: drop-shadow(0 0 8px #00d4ff); }
  .pipe-step.purple { border-color: rgba(124,58,237,0.4); }
  .pipe-step.purple .pipe-icon { filter: drop-shadow(0 0 8px #7c3aed); }
  .pipe-step.orange { border-color: rgba(245,158,11,0.4); }
  .pipe-step.orange .pipe-icon { filter: drop-shadow(0 0 8px #f59e0b); }

  /* ── ARCH GRID ── */
  .arch-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-bottom: 48px;
    animation: fadeUp 0.7s 0.35s ease both;
  }

  .arch-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 24px;
    position: relative;
    overflow: hidden;
    transition: transform 0.2s, border-color 0.2s;
  }
  .arch-card:hover {
    transform: translateY(-3px);
  }
  .arch-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
  }
  .arch-card.cyan::before { background: linear-gradient(90deg, transparent, var(--accent), transparent); }
  .arch-card.violet::before { background: linear-gradient(90deg, transparent, var(--accent2), transparent); }
  .arch-card.green::before { background: linear-gradient(90deg, transparent, var(--accent3), transparent); }
  .arch-card.amber::before { background: linear-gradient(90deg, transparent, var(--accent4), transparent); }

  .arch-card.cyan:hover { border-color: rgba(0,212,255,0.4); }
  .arch-card.violet:hover { border-color: rgba(124,58,237,0.4); }
  .arch-card.green:hover { border-color: rgba(16,185,129,0.4); }
  .arch-card.amber:hover { border-color: rgba(245,158,11,0.4); }

  .card-header {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 16px;
  }

  .card-icon {
    font-size: 28px;
    line-height: 1;
  }

  .card-title {
    font-size: 15px;
    font-weight: 700;
    color: var(--text);
  }

  .card-sub {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    margin-top: 2px;
  }

  .tag-list {
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
  }

  .tag {
    display: inline-block;
    padding: 4px 10px;
    border-radius: 6px;
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    font-weight: 700;
  }

  .tag.cyan { background: rgba(0,212,255,0.1); color: var(--accent); border: 1px solid rgba(0,212,255,0.2); }
  .tag.violet { background: rgba(124,58,237,0.1); color: #a78bfa; border: 1px solid rgba(124,58,237,0.2); }
  .tag.green { background: rgba(16,185,129,0.1); color: var(--accent3); border: 1px solid rgba(16,185,129,0.2); }
  .tag.amber { background: rgba(245,158,11,0.1); color: var(--accent4); border: 1px solid rgba(245,158,11,0.2); }
  .tag.red { background: rgba(239,68,68,0.1); color: #f87171; border: 1px solid rgba(239,68,68,0.2); }

  /* ── SECURITY ROW ── */
  .security-row {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 12px;
    margin-bottom: 48px;
    animation: fadeUp 0.7s 0.5s ease both;
  }

  .sec-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 16px;
    text-align: center;
    transition: transform 0.2s;
  }
  .sec-card:hover { transform: translateY(-3px); }

  .sec-icon { font-size: 28px; margin-bottom: 8px; }
  .sec-name { font-size: 12px; font-weight: 700; color: var(--text); margin-bottom: 4px; }
  .sec-desc { font-family: 'Space Mono', monospace; font-size: 9px; color: var(--muted); line-height: 1.5; }

  /* ── ISSUES SECTION ── */
  .issues {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 28px;
    margin-bottom: 48px;
    animation: fadeUp 0.7s 0.6s ease both;
  }

  .issue-list {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-top: 16px;
  }

  .issue-item {
    display: flex;
    align-items: flex-start;
    gap: 12px;
    padding: 12px 16px;
    background: rgba(0,0,0,0.3);
    border-radius: 10px;
    border-left: 3px solid;
  }
  .issue-item.red { border-color: var(--danger); }
  .issue-item.green { border-color: var(--accent3); }
  .issue-item.amber { border-color: var(--accent4); }

  .issue-icon { font-size: 16px; flex-shrink: 0; margin-top: 1px; }

  .issue-text { font-size: 13px; line-height: 1.5; }
  .issue-text strong { color: var(--text); display: block; font-size: 12px; margin-bottom: 2px; }
  .issue-text span { color: var(--muted); font-family: 'Space Mono', monospace; font-size: 11px; }

  /* ── NUMBERS ── */
  .metrics {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 48px;
    animation: fadeUp 0.7s 0.7s ease both;
  }

  .metric {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 24px 16px;
    text-align: center;
  }

  .metric-num {
    font-size: 36px;
    font-weight: 800;
    letter-spacing: -2px;
    line-height: 1;
    margin-bottom: 6px;
  }

  .metric-label {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 1px;
  }

  /* ── LINKEDIN POST TEXT ── */
  .post-box {
    background: linear-gradient(135deg, rgba(0,212,255,0.05), rgba(124,58,237,0.05));
    border: 1px solid rgba(0,212,255,0.2);
    border-radius: 20px;
    padding: 32px;
    animation: fadeUp 0.7s 0.8s ease both;
  }

  .post-box h2 {
    font-size: 18px;
    font-weight: 700;
    margin-bottom: 20px;
    color: var(--accent);
    font-family: 'Space Mono', monospace;
    letter-spacing: 1px;
    text-transform: uppercase;
    font-size: 12px;
  }

  .post-text {
    font-size: 14px;
    line-height: 1.8;
    color: #cbd5e1;
    white-space: pre-line;
  }

  .post-text .emoji { font-style: normal; }
  .post-text .highlight { color: var(--accent); font-weight: 600; }
  .post-text .hl2 { color: #a78bfa; font-weight: 600; }
  .post-text .hl3 { color: var(--accent3); font-weight: 600; }

  /* ── ANIMATIONS ── */
  @keyframes fadeDown {
    from { opacity: 0; transform: translateY(-20px); }
    to { opacity: 1; transform: translateY(0); }
  }
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
  }
  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.3; }
  }

  /* Scrollbar */
  ::-webkit-scrollbar { height: 4px; }
  ::-webkit-scrollbar-track { background: var(--bg); }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 4px; }

  @media (max-width: 640px) {
    .arch-grid { grid-template-columns: 1fr; }
    .security-row { grid-template-columns: 1fr 1fr; }
    .metrics { grid-template-columns: 1fr 1fr; }
    h1 { letter-spacing: -1px; }
  }
</style>
</head>
<body>
<div class="wrapper">

  <!-- HEADER -->
  <div class="header">
    <div class="badge">
      <span class="dot"></span>
      Production UAT — AWS ap-south-1
    </div>
    <h1>Baatein Backend<br>DevOps Pipeline</h1>
    <p class="subtitle">
      Full CI/CD infrastructure built on <span>AWS ECS Fargate</span> — from code push to live deployment, automated end-to-end.
    </p>
  </div>

  <!-- PIPELINE FLOW -->
  <div class="section-title">CI/CD Pipeline — 9 Stages</div>
  <div class="pipeline">
    <div class="pipe-step green">
      <div class="pipe-icon">📥</div>
      <div class="pipe-name">Checkout</div>
      <div class="pipe-tool">GitHub</div>
    </div>
    <div class="pipe-arrow">→</div>
    <div class="pipe-step green">
      <div class="pipe-icon">📦</div>
      <div class="pipe-name">Install</div>
      <div class="pipe-tool">npm ci</div>
    </div>
    <div class="pipe-arrow">→</div>
    <div class="pipe-step blue">
      <div class="pipe-icon">🔍</div>
      <div class="pipe-name">SonarQube</div>
      <div class="pipe-tool">SAST Scan</div>
    </div>
    <div class="pipe-arrow">→</div>
    <div class="pipe-step blue">
      <div class="pipe-icon">✅</div>
      <div class="pipe-name">Quality Gate</div>
      <div class="pipe-tool">Webhook</div>
    </div>
    <div class="pipe-arrow">→</div>
    <div class="pipe-step purple">
      <div class="pipe-icon">🐳</div>
      <div class="pipe-name">Docker Build</div>
      <div class="pipe-tool">node:20-alpine</div>
    </div>
    <div class="pipe-arrow">→</div>
    <div class="pipe-step orange">
      <div class="pipe-icon">🔒</div>
      <div class="pipe-name">Trivy Scan</div>
      <div class="pipe-tool">CVE Check</div>
    </div>
    <div class="pipe-arrow">→</div>
    <div class="pipe-step blue">
      <div class="pipe-icon">📤</div>
      <div class="pipe-name">Push ECR</div>
      <div class="pipe-tool">AWS ECR</div>
    </div>
    <div class="pipe-arrow">→</div>
    <div class="pipe-step green">
      <div class="pipe-icon">🚀</div>
      <div class="pipe-name">Deploy ECS</div>
      <div class="pipe-tool">Fargate</div>
    </div>
    <div class="pipe-arrow">→</div>
    <div class="pipe-step green">
      <div class="pipe-icon">❤️</div>
      <div class="pipe-name">Health Check</div>
      <div class="pipe-tool">ALB /health</div>
    </div>
  </div>

  <!-- ARCHITECTURE CARDS -->
  <div class="section-title">Infrastructure Components</div>
  <div class="arch-grid">

    <div class="arch-card cyan">
      <div class="card-header">
        <div class="card-icon">🌐</div>
        <div>
          <div class="card-title">Network — VPC</div>
          <div class="card-sub">10.10.0.0/16 · ap-south-1</div>
        </div>
      </div>
      <div class="tag-list">
        <span class="tag cyan">Public Subnet 1a/1b</span>
        <span class="tag cyan">Private Subnet 1a/1b</span>
        <span class="tag cyan">NAT Gateway</span>
        <span class="tag cyan">Internet Gateway</span>
        <span class="tag cyan">Security Groups</span>
      </div>
    </div>

    <div class="arch-card violet">
      <div class="card-header">
        <div class="card-icon">⚖️</div>
        <div>
          <div class="card-title">Load Balancer — ALB</div>
          <div class="card-sub">Public · Port 80 → ECS :3000</div>
        </div>
      </div>
      <div class="tag-list">
        <span class="tag violet">Multi-AZ (1a + 1b)</span>
        <span class="tag violet">Target Group</span>
        <span class="tag violet">Health Check /health</span>
        <span class="tag violet">Listener Rules</span>
      </div>
    </div>

    <div class="arch-card green">
      <div class="card-header">
        <div class="card-icon">🐳</div>
        <div>
          <div class="card-title">ECS Fargate</div>
          <div class="card-sub">2 Tasks · Private Subnets</div>
        </div>
      </div>
      <div class="tag-list">
        <span class="tag green">Serverless Containers</span>
        <span class="tag green">Rolling Deploy</span>
        <span class="tag green">Task Definition v1</span>
        <span class="tag green">Port 3000</span>
        <span class="tag green">Secrets Injection</span>
      </div>
    </div>

    <div class="arch-card amber">
      <div class="card-header">
        <div class="card-icon">☁️</div>
        <div>
          <div class="card-title">Supporting Services</div>
          <div class="card-sub">AWS Managed</div>
        </div>
      </div>
      <div class="tag-list">
        <span class="tag amber">ECR — Image Registry</span>
        <span class="tag amber">Secrets Manager</span>
        <span class="tag amber">CloudWatch Logs</span>
        <span class="tag amber">IAM Roles</span>
        <span class="tag amber">S3 — TF State</span>
      </div>
    </div>

  </div>

  <!-- SECURITY ROW -->
  <div class="section-title">Security Layers</div>
  <div class="security-row">
    <div class="sec-card">
      <div class="sec-icon">🔍</div>
      <div class="sec-name">SonarQube</div>
      <div class="sec-desc">SAST · Code Quality<br>Quality Gate enforced</div>
    </div>
    <div class="sec-card">
      <div class="sec-icon">🛡️</div>
      <div class="sec-name">Trivy</div>
      <div class="sec-desc">Container CVE scan<br>CRITICAL blocks deploy</div>
    </div>
    <div class="sec-card">
      <div class="sec-icon">🔐</div>
      <div class="sec-name">Secrets Manager</div>
      <div class="sec-desc">No env vars in image<br>Runtime injection</div>
    </div>
    <div class="sec-card">
      <div class="sec-icon">🔒</div>
      <div class="sec-name">IAM + SG</div>
      <div class="sec-desc">Least privilege roles<br>Private subnet tasks</div>
    </div>
  </div>

  <!-- ISSUES RESOLVED -->
  <div class="issues">
    <div class="section-title" style="margin-bottom:0">Real Challenges Solved 🧠</div>
    <div class="issue-list">
      <div class="issue-item amber">
        <div class="issue-icon">⚠️</div>
        <div class="issue-text">
          <strong>Quality Gate Timeout</strong>
          <span>SonarQube webhook not configured → waitForQualityGate hung forever. Fix: added webhook + 10min timeout.</span>
        </div>
      </div>
      <div class="issue-item red">
        <div class="issue-icon">🚨</div>
        <div class="issue-text">
          <strong>CannotPullContainerError — 62 failed tasks</strong>
          <span>ECS tried to pull image before first pipeline run pushed it to ECR. Fix: image now always present before deploy.</span>
        </div>
      </div>
      <div class="issue-item red">
        <div class="issue-icon">🔴</div>
        <div class="issue-text">
          <strong>zlib CRITICAL CVE-2026-22184</strong>
          <span>node:20-alpine base image had vulnerable zlib. Fix: RUN apk upgrade --no-cache in Dockerfile.</span>
        </div>
      </div>
      <div class="issue-item amber">
        <div class="issue-icon">⚡</div>
        <div class="issue-text">
          <strong>Trivy blocking on npm internals</strong>
          <span>tar/minimatch HIGH CVEs inside npm tool itself (not app deps). Fix: two-pass scan — CRITICAL blocks, HIGH reports only.</span>
        </div>
      </div>
      <div class="issue-item green">
        <div class="issue-icon">✅</div>
        <div class="issue-text">
          <strong>ECS services-stable waiter exceeded</strong>
          <span>App crashed on DB connection. Root cause: wrong DB driver in code (pg vs mongoose). Diagnosed via CloudWatch logs.</span>
        </div>
      </div>
    </div>
  </div>

  <!-- METRICS -->
  <div class="section-title">By The Numbers</div>
  <div class="metrics">
    <div class="metric">
      <div class="metric-num" style="background: linear-gradient(135deg,#00d4ff,#7c3aed); -webkit-background-clip:text; -webkit-text-fill-color:transparent;">9</div>
      <div class="metric-label">Pipeline Stages</div>
    </div>
    <div class="metric">
      <div class="metric-num" style="color: #10b981;">2</div>
      <div class="metric-label">ECS Tasks / AZs</div>
    </div>
    <div class="metric">
      <div class="metric-num" style="color: #f59e0b;">11</div>
      <div class="metric-label">Builds to Production</div>
    </div>
    <div class="metric">
      <div class="metric-num" style="color: #00d4ff;">0</div>
      <div class="metric-label">CRITICAL CVEs Live</div>
    </div>
  </div>

  <!-- LINKEDIN POST TEXT -->
  <div class="post-box">
    <h2>📋 Ready-to-Post — LinkedIn Caption</h2>
    <div class="post-text">🚀 <strong style="color:#e2e8f0">Just shipped a complete AWS CI/CD pipeline for Baatein — a real-time Node.js backend — from scratch.</strong>

Here's the full DevOps stack I built:

<span class="highlight">🏗️ Infrastructure (Terraform)</span>
→ VPC with public + private subnets across 2 AZs
→ ECS Fargate — serverless containers, no EC2 to manage
→ ALB with health checks routing to port 3000
→ ECR for Docker image registry (lifecycle: last 5 images)
→ Secrets Manager for runtime secret injection (zero secrets in image)
→ S3 remote Terraform state

<span class="hl2">🔄 CI/CD Pipeline — 9 Stages (Jenkins)</span>
→ GitHub → npm install → SonarQube SAST → Quality Gate
→ Docker build (node:20-alpine) → Trivy CVE scan
→ Push to ECR → ECS rolling deploy → ALB health check

<span class="hl3">🛡️ Security-first approach</span>
→ SonarQube Quality Gate must pass before any build
→ Trivy 2-pass scan: CRITICAL OS CVEs block pipeline, HIGH npm internals report-only
→ Fixed zlib CVE-2026-22184 via apk upgrade in Dockerfile
→ All secrets via AWS Secrets Manager — never in env or image
→ ECS tasks in private subnets, only ALB is public

<span style="color:#f59e0b">🧠 Real problems I debugged:</span>
→ 62 failed ECS tasks → CannotPullContainerError (image not in ECR yet)
→ SonarQube Quality Gate hanging → missing webhook config
→ Trivy blocking on npm's own internal packages (not app deps)
→ App crash diagnosed via CloudWatch → wrong DB driver in code

11 builds. Every failure taught something.
The pipeline is only as good as the problems it surfaces. 💡

<span style="color:#64748b">#DevOps #AWS #Terraform #Jenkins #Docker #ECS #Fargate #CICD #CloudEngineering #SonarQube #Trivy #Security #NodeJS #InfrastructureAsCode</span></div>
  </div>

</div>
</body>
</html>
