<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
  <title>DailyFlow — Smart Task Checklist</title>
  <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&family=Outfit:wght@200;400;600;800;900&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
  <style>
    :root {
      --bg: #0b0b0e;
      --bg-secondary: #131318;
      --card: #18181f;
      --card-hover: #1f1f28;
      --fg: #eeeae2;
      --fg-secondary: #c8c4ba;
      --fg-muted: #706d63;
      --accent: #e8a838;
      --accent-dark: #c4882a;
      --accent-glow: rgba(232,168,56,0.25);
      --accent-soft: rgba(232,168,56,0.07);
      --danger: #e85454;
      --danger-glow: rgba(232,84,84,0.15);
      --success: #3ecf7a;
      --success-glow: rgba(62,207,122,0.2);
      --priority-high: #e85454;
      --priority-medium: #e8a838;
      --priority-low: #3ecf7a;
      --border: #252530;
      --border-light: #2e2e3a;
      --radius: 16px;
      --radius-sm: 10px;
      --radius-xs: 6px;
      --safe-bottom: env(safe-area-inset-bottom, 0px);
      --shadow-sm: 0 2px 8px rgba(0,0,0,0.3);
      --shadow-md: 0 8px 32px rgba(0,0,0,0.4);
      --shadow-lg: 0 16px 48px rgba(0,0,0,0.5);
      --transition: 0.3s cubic-bezier(0.16, 1, 0.3, 1);
    }

    * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

    html { scroll-behavior: smooth; }

    body {
      font-family: 'Space Grotesk', sans-serif;
      background: var(--bg);
      color: var(--fg);
      min-height: 100vh;
      min-height: 100dvh;
      overflow-x: hidden;
      position: relative;
      -webkit-font-smoothing: antialiased;
    }

    /* === Animated Background === */
    .bg-layer {
      position: fixed;
      inset: 0;
      z-index: 0;
      overflow: hidden;
      pointer-events: none;
    }

    .bg-blob {
      position: absolute;
      border-radius: 50%;
      filter: blur(130px);
      opacity: 0.35;
      animation: blobFloat 22s ease-in-out infinite alternate;
    }

    .bg-blob:nth-child(1) {
      width: 550px; height: 550px;
      background: radial-gradient(circle, var(--accent-glow), transparent 70%);
      top: -200px; left: -150px;
      animation-duration: 20s;
    }
    .bg-blob:nth-child(2) {
      width: 450px; height: 450px;
      background: radial-gradient(circle, rgba(232,84,84,0.1), transparent 70%);
      bottom: -120px; right: -100px;
      animation-duration: 24s;
      animation-delay: -6s;
    }
    .bg-blob:nth-child(3) {
      width: 350px; height: 350px;
      background: radial-gradient(circle, rgba(62,207,122,0.08), transparent 70%);
      top: 40%; left: 60%;
      animation-duration: 28s;
      animation-delay: -12s;
    }

    @keyframes blobFloat {
      0% { transform: translate(0, 0) scale(1); }
      33% { transform: translate(50px, -30px) scale(1.08); }
      66% { transform: translate(-25px, 40px) scale(0.94); }
      100% { transform: translate(35px, 15px) scale(1.03); }
    }

    .bg-grid {
      position: fixed;
      inset: 0;
      z-index: 0;
      pointer-events: none;
      background-image:
        linear-gradient(rgba(255,255,255,0.012) 1px, transparent 1px),
        linear-gradient(90deg, rgba(255,255,255,0.012) 1px, transparent 1px);
      background-size: 50px 50px;
    }

    /* === Noise Texture === */
    .bg-noise {
      position: fixed;
      inset: 0;
      z-index: 0;
      pointer-events: none;
      opacity: 0.03;
      background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
      background-repeat: repeat;
      background-size: 200px 200px;
    }

    /* === Scrollbar === */
    ::-webkit-scrollbar { width: 6px; }
    ::-webkit-scrollbar-track { background: transparent; }
    ::-webkit-scrollbar-thumb { background: var(--border-light); border-radius: 100px; }
    ::-webkit-scrollbar-thumb:hover { background: var(--fg-muted); }

    /* === App Shell === */
    .app-shell {
      position: relative;
      z-index: 1;
      max-width: 720px;
      margin: 0 auto;
      padding: 0 20px;
      padding-bottom: calc(100px + var(--safe-bottom));
      min-height: 100vh;
      min-height: 100dvh;
    }

    /* === Top Bar === */
    .top-bar {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 20px 0 8px;
      animation: slideDown 0.7s cubic-bezier(0.16, 1, 0.3, 1);
      position: sticky;
      top: 0;
      z-index: 50;
      background: linear-gradient(to bottom, var(--bg) 60%, transparent);
      padding-bottom: 24px;
    }

    @keyframes slideDown {
      from { opacity: 0; transform: translateY(-30px); }
      to { opacity: 1; transform: translateY(0); }
    }

    .top-bar-left {
      display: flex;
      align-items: center;
      gap: 12px;
    }

    .logo-icon {
      width: 42px; height: 42px;
      background: linear-gradient(135deg, var(--accent), var(--accent-dark));
      border-radius: 12px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 18px;
      color: #0f0f0f;
      box-shadow: 0 4px 20px var(--accent-glow);
      flex-shrink: 0;
    }

    .logo-info h1 {
      font-family: 'Outfit', sans-serif;
      font-size: 22px;
      font-weight: 800;
      letter-spacing: -0.5px;
      line-height: 1.1;
      background: linear-gradient(135deg, var(--fg), var(--accent));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .logo-info p {
      font-size: 12px;
      color: var(--fg-muted);
      font-weight: 400;
    }

    .top-bar-right {
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .icon-btn {
      width: 40px; height: 40px;
      border-radius: var(--radius-sm);
      border: 1px solid var(--border);
      background: var(--card);
      color: var(--fg-muted);
      font-size: 15px;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all var(--transition);
      position: relative;
    }

    .icon-btn:hover {
      border-color: var(--accent);
      color: var(--accent);
      background: var(--accent-soft);
    }

    .icon-btn:active { transform: scale(0.92); }

    .icon-btn .badge {
      position: absolute;
      top: -4px; right: -4px;
      width: 18px; height: 18px;
      border-radius: 50%;
      background: var(--danger);
      color: #fff;
      font-size: 10px;
      font-weight: 700;
      display: flex;
      align-items: center;
      justify-content: center;
      opacity: 0;
      transform: scale(0);
      transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
    }

    .icon-btn .badge.visible {
      opacity: 1;
      transform: scale(1);
    }

    /* === Greeting Section === */
    .greeting-section {
      padding: 24px 0 20px;
      animation: slideUp 0.7s cubic-bezier(0.16, 1, 0.3, 1) 0.1s both;
    }

    @keyframes slideUp {
      from { opacity: 0; transform: translateY(25px); }
      to { opacity: 1; transform: translateY(0); }
    }

    .greeting-text {
      font-family: 'Outfit', sans-serif;
      font-size: 32px;
      font-weight: 800;
      line-height: 1.15;
      margin-bottom: 6px;
    }

    .greeting-text .wave {
      display: inline-block;
      animation: wave 2s ease-in-out infinite;
      transform-origin: 70% 70%;
    }

    @keyframes wave {
      0%, 100% { transform: rotate(0deg); }
      10% { transform: rotate(14deg); }
      20% { transform: rotate(-8deg); }
      30% { transform: rotate(14deg); }
      40% { transform: rotate(-4deg); }
      50% { transform: rotate(10deg); }
      60% { transform: rotate(0deg); }
    }

    .greeting-sub {
      font-size: 15px;
      color: var(--fg-muted);
      font-weight: 300;
    }

    .greeting-sub .date-highlight {
      color: var(--accent);
      font-weight: 500;
    }

    /* === Stats Cards === */
    .stats-row {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 10px;
      margin-bottom: 24px;
      animation: slideUp 0.7s cubic-bezier(0.16, 1, 0.3, 1) 0.2s both;
    }

    .stat-card {
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      padding: 18px 14px;
      text-align: center;
      transition: all var(--transition);
      position: relative;
      overflow: hidden;
    }

    .stat-card::after {
      content: '';
      position: absolute;
      bottom: 0; left: 0; right: 0;
      height: 3px;
      border-radius: 0 0 var(--radius) var(--radius);
      opacity: 0;
      transition: opacity var(--transition);
    }

    .stat-card:nth-child(1)::after { background: var(--fg-muted); }
    .stat-card:nth-child(2)::after { background: var(--accent); }
    .stat-card:nth-child(3)::after { background: var(--success); }

    .stat-card:hover {
      transform: translateY(-3px);
      border-color: var(--border-light);
      box-shadow: var(--shadow-sm);
    }

    .stat-card:hover::after { opacity: 1; }

    .stat-icon {
      width: 36px; height: 36px;
      border-radius: var(--radius-xs);
      display: inline-flex;
      align-items: center;
      justify-content: center;
      font-size: 14px;
      margin-bottom: 10px;
    }

    .stat-card:nth-child(1) .stat-icon { background: rgba(238,234,226,0.08); color: var(--fg-secondary); }
    .stat-card:nth-child(2) .stat-icon { background: var(--accent-soft); color: var(--accent); }
    .stat-card:nth-child(3) .stat-icon { background: rgba(62,207,122,0.08); color: var(--success); }

    .stat-number {
      font-family: 'Outfit', sans-serif;
      font-size: 30px;
      font-weight: 800;
      line-height: 1;
      margin-bottom: 4px;
    }

    .stat-card:nth-child(1) .stat-number { color: var(--fg); }
    .stat-card:nth-child(2) .stat-number { color: var(--accent); }
    .stat-card:nth-child(3) .stat-number { color: var(--success); }

    .stat-label {
      font-size: 11px;
      color: var(--fg-muted);
      text-transform: uppercase;
      letter-spacing: 1.5px;
      font-weight: 600;
    }

    /* === Progress Ring === */
    .progress-section {
      margin-bottom: 28px;
      animation: slideUp 0.7s cubic-bezier(0.16, 1, 0.3, 1) 0.3s both;
    }

    .progress-card {
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      padding: 24px;
      display: flex;
      align-items: center;
      gap: 24px;
    }

    .progress-ring-wrapper {
      position: relative;
      width: 80px;
      height: 80px;
      flex-shrink: 0;
    }

    .progress-ring-svg {
      width: 80px;
      height: 80px;
      transform: rotate(-90deg);
    }

    .progress-ring-bg {
      fill: none;
      stroke: var(--border);
      stroke-width: 6;
    }

    .progress-ring-fill {
      fill: none;
      stroke: url(#progressGradient);
      stroke-width: 6;
      stroke-linecap: round;
      stroke-dasharray: 213.628;
      stroke-dashoffset: 213.628;
      transition: stroke-dashoffset 0.8s cubic-bezier(0.16, 1, 0.3, 1);
    }

    .progress-ring-text {
      position: absolute;
      inset: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }

    .progress-ring-percent {
      font-family: 'Outfit', sans-serif;
      font-size: 20px;
      font-weight: 800;
      color: var(--fg);
      line-height: 1;
    }

    .progress-ring-label {
      font-size: 8px;
      color: var(--fg-muted);
      text-transform: uppercase;
      letter-spacing: 1px;
      font-weight: 600;
    }

    .progress-info { flex: 1; }

    .progress-info h3 {
      font-size: 16px;
      font-weight: 600;
      margin-bottom: 4px;
      color: var(--fg);
    }

    .progress-info p {
      font-size: 13px;
      color: var(--fg-muted);
      font-weight: 300;
      line-height: 1.5;
    }

    .progress-bar-track {
      width: 100%;
      height: 5px;
      background: var(--border);
      border-radius: 100px;
      margin-top: 14px;
      overflow: hidden;
    }

    .progress-bar-fill {
      height: 100%;
      border-radius: 100px;
      background: linear-gradient(90deg, var(--accent-dark), var(--accent), #f0c060);
      background-size: 200% 100%;
      animation: shimmer 2.5s ease-in-out infinite;
      transition: width 0.6s cubic-bezier(0.16, 1, 0.3, 1);
    }

    @keyframes shimmer {
      0% { background-position: 200% 0; }
      100% { background-position: -200% 0; }
    }

    /* === Filter Tabs === */
    .filter-row {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 16px;
      animation: slideUp 0.7s cubic-bezier(0.16, 1, 0.3, 1) 0.4s both;
    }

    .filter-tabs {
      display: flex;
      gap: 4px;
      background: var(--bg-secondary);
      border-radius: var(--radius-sm);
      padding: 3px;
      border: 1px solid var(--border);
    }

    .filter-tab {
      padding: 8px 16px;
      font-size: 13px;
      font-weight: 500;
      font-family: 'Space Grotesk', sans-serif;
      color: var(--fg-muted);
      background: none;
      border: none;
      border-radius: var(--radius-xs);
      cursor: pointer;
      transition: all 0.25s ease;
      position: relative;
    }

    .filter-tab.active {
      background: var(--card);
      color: var(--accent);
      box-shadow: var(--shadow-sm);
    }

    .filter-tab:hover:not(.active) { color: var(--fg-secondary); }

    .filter-tab .count-badge {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      min-width: 18px;
      height: 18px;
      border-radius: 9px;
      background: var(--border);
      font-size: 10px;
      font-weight: 700;
      padding: 0 5px;
      margin-left: 6px;
      color: var(--fg-muted);
      transition: all 0.25s ease;
    }

    .filter-tab.active .count-badge {
      background: var(--accent-soft);
      color: var(--accent);
    }

    .clear-done-btn {
      padding: 8px 14px;
      font-size: 12px;
      font-weight: 600;
      font-family: 'Space Grotesk', sans-serif;
      color: var(--danger);
      background: var(--danger-glow);
      border: 1px solid rgba(232,84,84,0.12);
      border-radius: var(--radius-xs);
      cursor: pointer;
      transition: all 0.25s ease;
      display: flex;
      align-items: center;
      gap: 6px;
    }

    .clear-done-btn:hover {
      background: rgba(232,84,84,0.12);
      border-color: rgba(232,84,84,0.25);
    }

    .clear-done-btn:active { transform: scale(0.95); }

    /* === Task List === */
    .task-list {
      display: flex;
      flex-direction: column;
      gap: 8px;
      min-height: 80px;
      animation: slideUp 0.7s cubic-bezier(0.16, 1, 0.3, 1) 0.5s both;
    }

    /* === Task Item === */
    .task-item {
      display: flex;
      align-items: center;
      gap: 14px;
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      padding: 16px 16px;
      transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
      animation: taskSlideIn 0.5s cubic-bezier(0.16, 1, 0.3, 1) both;
      position: relative;
      overflow: hidden;
      touch-action: pan-y;
      user-select: none;
    }

    .task-item::before {
      content: '';
      position: absolute;
      left: 0; top: 0; bottom: 0;
      width: 3px;
      border-radius: 0 3px 3px 0;
      transition: all var(--transition);
    }

    .task-item[data-priority="high"]::before { background: var(--priority-high); }
    .task-item[data-priority="medium"]::before { background: var(--priority-medium); }
    .task-item[data-priority="low"]::before { background: var(--priority-low); }

    .task-item:hover {
      border-color: var(--border-light);
      background: var(--card-hover);
    }

    .task-item.completed { opacity: 0.5; }
    .task-item.completed:hover { opacity: 0.7; }

    .task-item.removing {
      animation: taskRemove 0.4s cubic-bezier(0.55, 0, 1, 0.45) forwards;
    }

    @keyframes taskSlideIn {
      from {
        opacity: 0;
        transform: translateY(15px) scale(0.97);
      }
      to {
        opacity: 1;
        transform: translateY(0) scale(1);
      }
    }

    @keyframes taskRemove {
      to {
        opacity: 0;
        transform: translateX(80px) scale(0.92);
        max-height: 0;
        padding: 0 16px;
        margin-bottom: -8px;
        border-width: 0;
      }
    }

    /* Swipe hint */
    .task-item .swipe-delete-bg {
      position: absolute;
      right: 0; top: 0; bottom: 0;
      width: 80px;
      background: var(--danger);
      display: flex;
      align-items: center;
      justify-content: center;
      color: #fff;
      font-size: 18px;
      border-radius: 0 var(--radius) var(--radius) 0;
      opacity: 0;
      transition: opacity 0.2s ease;
    }

    /* Custom Checkbox */
    .check-wrapper {
      flex-shrink: 0;
      position: relative;
      width: 24px;
      height: 24px;
    }

    .check-wrapper input {
      position: absolute;
      opacity: 0;
      width: 100%;
      height: 100%;
      cursor: pointer;
      z-index: 2;
      margin: 0;
    }

    .check-visual {
      width: 24px;
      height: 24px;
      border: 2px solid var(--fg-muted);
      border-radius: 7px;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
    }

    .check-visual i {
      font-size: 11px;
      color: #0f0f0f;
      opacity: 0;
      transform: scale(0) rotate(-45deg);
      transition: all 0.25s cubic-bezier(0.34, 1.56, 0.64, 1);
    }

    .check-wrapper input:checked + .check-visual {
      background: var(--success);
      border-color: var(--success);
      box-shadow: 0 0 14px var(--success-glow);
    }

    .check-wrapper input:checked + .check-visual i {
      opacity: 1;
      transform: scale(1) rotate(0deg);
    }

    .check-wrapper input:focus-visible + .check-visual {
      outline: 2px solid var(--accent);
      outline-offset: 2px;
    }

    /* Task Content */
    .task-content { flex: 1; min-width: 0; }

    .task-text {
      font-size: 14.5px;
      font-weight: 450;
      color: var(--fg);
      line-height: 1.4;
      transition: all 0.3s ease;
      word-break: break-word;
    }

    .task-item.completed .task-text {
      color: var(--fg-muted);
      text-decoration: line-through;
      text-decoration-color: var(--fg-muted);
      text-decoration-thickness: 1.5px;
    }

    .task-meta {
      display: flex;
      align-items: center;
      gap: 8px;
      margin-top: 5px;
    }

    .task-time {
      font-size: 11px;
      color: var(--fg-muted);
      font-weight: 400;
      opacity: 0.7;
      display: flex;
      align-items: center;
      gap: 4px;
    }

    .priority-dot {
      width: 7px;
      height: 7px;
      border-radius: 50%;
      flex-shrink: 0;
    }

    .priority-dot.high { background: var(--priority-high); box-shadow: 0 0 6px rgba(232,84,84,0.4); }
    .priority-dot.medium { background: var(--priority-medium); box-shadow: 0 0 6px rgba(232,168,56,0.4); }
    .priority-dot.low { background: var(--priority-low); box-shadow: 0 0 6px rgba(62,207,122,0.4); }

    .priority-label {
      font-size: 10px;
      font-weight: 600;
      text-transform: uppercase;
      letter-spacing: 0.8px;
    }

    .priority-label.high { color: var(--priority-high); }
    .priority-label.medium { color: var(--priority-medium); }
    .priority-label.low { color: var(--priority-low); }

    /* Delete Button */
    .task-delete {
      flex-shrink: 0;
      width: 34px;
      height: 34px;
      background: none;
      border: 1px solid transparent;
      border-radius: var(--radius-xs);
      color: var(--fg-muted);
      font-size: 13px;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.25s ease;
      opacity: 0;
    }

    .task-item:hover .task-delete { opacity: 1; }

    .task-delete:hover {
      background: var(--danger-glow);
      border-color: rgba(232,84,84,0.2);
      color: var(--danger);
    }

    .task-delete:active { transform: scale(0.88); }

    /* === Empty State === */
    .empty-state {
      text-align: center;
      padding: 50px 20px;
      animation: fadeIn 0.5s ease;
    }

    @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

    .empty-visual {
      width: 90px;
      height: 90px;
      border-radius: 50%;
      background: var(--accent-soft);
      display: inline-flex;
      align-items: center;
      justify-content: center;
      font-size: 36px;
      color: var(--accent);
      margin-bottom: 20px;
      animation: emptyPulse 3s ease-in-out infinite;
    }

    @keyframes emptyPulse {
      0%, 100% { transform: scale(1); box-shadow: 0 0 0 0 var(--accent-glow); }
      50% { transform: scale(1.05); box-shadow: 0 0 0 14px transparent; }
    }

    .empty-title {
      font-family: 'Outfit', sans-serif;
      font-size: 20px;
      font-weight: 700;
      color: var(--fg);
      margin-bottom: 6px;
      opacity: 0.5;
    }

    .empty-desc {
      font-size: 14px;
      color: var(--fg-muted);
      opacity: 0.5;
      line-height: 1.5;
    }

    /* === Floating Add Button (Mobile) === */
    .fab {
      position: fixed;
      bottom: calc(28px + var(--safe-bottom));
      right: 24px;
      width: 58px;
      height: 58px;
      border-radius: 18px;
      background: linear-gradient(135deg, var(--accent), var(--accent-dark));
      border: none;
      color: #0f0f0f;
      font-size: 24px;
      cursor: pointer;
      display: none;
      align-items: center;
      justify-content: center;
      box-shadow: 0 6px 28px var(--accent-glow), 0 2px 8px rgba(0,0,0,0.3);
      z-index: 100;
      transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
    }

    .fab:hover { transform: scale(1.08) rotate(90deg); }
    .fab:active { transform: scale(0.94) rotate(90deg); }

    .fab.visible { transform: scale(1); }
    .fab.hidden { transform: scale(0) rotate(180deg); }

    /* === Modal Overlay === */
    .modal-overlay {
      position: fixed;
      inset: 0;
      background: rgba(0,0,0,0.6);
      backdrop-filter: blur(8px);
      -webkit-backdrop-filter: blur(8px);
      z-index: 200;
      display: none;
      align-items: flex-end;
      justify-content: center;
      animation: overlayIn 0.3s ease;
    }

    .modal-overlay.active { display: flex; }

    @keyframes overlayIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }

    .modal-sheet {
      width: 100%;
      max-width: 520px;
      background: var(--bg-secondary);
      border: 1px solid var(--border);
      border-radius: var(--radius) var(--radius) 0 0;
      padding: 24px 24px calc(24px + var(--safe-bottom));
      animation: sheetUp 0.4s cubic-bezier(0.16, 1, 0.3, 1);
    }

    @keyframes sheetUp {
      from { transform: translateY(100%); }
      to { transform: translateY(0); }
    }

    .modal-handle {
      width: 40px;
      height: 4px;
      border-radius: 100px;
      background: var(--border-light);
      margin: 0 auto 20px;
    }

    .modal-title {
      font-family: 'Outfit', sans-serif;
      font-size: 20px;
      font-weight: 700;
      margin-bottom: 20px;
      color: var(--fg);
    }

    .form-group {
      margin-bottom: 16px;
    }

    .form-label {
      display: block;
      font-size: 12px;
      font-weight: 600;
      color: var(--fg-muted);
      text-transform: uppercase;
      letter-spacing: 1px;
      margin-bottom: 8px;
    }

    .form-input {
      width: 100%;
      padding: 14px 16px;
      background: var(--card);
      border: 2px solid var(--border);
      border-radius: var(--radius-sm);
      color: var(--fg);
      font-size: 15px;
      font-family: 'Space Grotesk', sans-serif;
      outline: none;
      transition: all var(--transition);
    }

    .form-input:focus {
      border-color: var(--accent);
      box-shadow: 0 0 0 4px var(--accent-glow);
    }

    .form-input::placeholder {
      color: var(--fg-muted);
      font-weight: 300;
    }

    /* Priority Selector */
    .priority-selector {
      display: flex;
      gap: 8px;
    }

    .priority-option {
      flex: 1;
      padding: 12px 10px;
      border: 2px solid var(--border);
      border-radius: var(--radius-sm);
      background: var(--card);
      cursor: pointer;
      text-align: center;
      font-size: 13px;
      font-weight: 600;
      font-family: 'Space Grotesk', sans-serif;
      transition: all 0.25s ease;
      color: var(--fg-muted);
    }

    .priority-option i { margin-right: 4px; }

    .priority-option.high.selected {
      border-color: var(--priority-high);
      background: rgba(232,84,84,0.08);
      color: var(--priority-high);
    }

    .priority-option.medium.selected {
      border-color: var(--priority-medium);
      background: var(--accent-soft);
      color: var(--priority-medium);
    }

    .priority-option.low.selected {
      border-color: var(--priority-low);
      background: rgba(62,207,122,0.08);
      color: var(--priority-low);
    }

    .modal-actions {
      display: flex;
      gap: 10px;
      margin-top: 24px;
    }

    .btn {
      flex: 1;
      padding: 14px 20px;
      border-radius: var(--radius-sm);
      font-size: 14px;
      font-weight: 700;
      font-family: 'Space Grotesk', sans-serif;
      cursor: pointer;
      transition: all 0.25s ease;
      border: none;
    }

    .btn:active { transform: scale(0.97); }

    .btn-primary {
      background: linear-gradient(135deg, var(--accent), var(--accent-dark));
      color: #0f0f0f;
      box-shadow: 0 4px 16px var(--accent-glow);
    }

    .btn-primary:hover { box-shadow: 0 6px 24px var(--accent-glow); }

    .btn-secondary {
      background: var(--card);
      border: 1px solid var(--border);
      color: var(--fg-muted);
    }

    .btn-secondary:hover { border-color: var(--border-light); color: var(--fg); }

    /* Desktop inline form */
    .inline-form {
      display: block;
      margin-bottom: 20px;
      animation: slideUp 0.7s cubic-bezier(0.16, 1, 0.3, 1) 0.35s both;
    }

    .inline-form-row {
      display: flex;
      gap: 8px;
      background: var(--card);
      border: 2px solid var(--border);
      border-radius: var(--radius);
      padding: 5px;
      transition: all var(--transition);
    }

    .inline-form-row:focus-within {
      border-color: var(--accent);
      box-shadow: 0 0 0 4px var(--accent-glow);
    }

    .inline-form-row input {
      flex: 1;
      min-width: 0;
      background: none;
      border: none;
      outline: none;
      color: var(--fg);
      font-size: 14.5px;
      font-family: 'Space Grotesk', sans-serif;
      padding: 11px 14px;
      font-weight: 400;
    }

    .inline-form-row input::placeholder {
      color: var(--fg-muted);
      font-weight: 300;
    }

    .inline-priority-row {
      display: flex;
      gap: 6px;
      margin-top: 8px;
    }

    .inline-priority-btn {
      padding: 6px 14px;
      border: 1px solid var(--border);
      border-radius: 100px;
      background: var(--card);
      cursor: pointer;
      font-size: 12px;
      font-weight: 600;
      font-family: 'Space Grotesk', sans-serif;
      color: var(--fg-muted);
      transition: all 0.25s ease;
      display: flex;
      align-items: center;
      gap: 5px;
    }

    .inline-priority-btn .dot {
      width: 6px; height: 6px;
      border-radius: 50%;
    }

    .inline-priority-btn.high .dot { background: var(--priority-high); }
    .inline-priority-btn.medium .dot { background: var(--priority-medium); }
    .inline-priority-btn.low .dot { background: var(--priority-low); }

    .inline-priority-btn.selected {
      color: var(--fg);
      border-color: var(--border-light);
    }
    .inline-priority-btn.high.selected { border-color: var(--priority-high); background: rgba(232,84,84,0.08); color: var(--priority-high); }
    .inline-priority-btn.medium.selected { border-color: var(--priority-medium); background: var(--accent-soft); color: var(--priority-medium); }
    .inline-priority-btn.low.selected { border-color: var(--priority-low); background: rgba(62,207,122,0.08); color: var(--priority-low); }

    .inline-add-btn {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 7px;
      background: linear-gradient(135deg, var(--accent), var(--accent-dark));
      border: none;
      border-radius: var(--radius-sm);
      padding: 12px 22px;
      color: #0f0f0f;
      font-size: 14px;
      font-weight: 700;
      font-family: 'Space Grotesk', sans-serif;
      cursor: pointer;
      transition: all 0.25s ease;
      white-space: nowrap;
      flex-shrink: 0;
    }

    .inline-add-btn:hover { box-shadow: 0 4px 20px var(--accent-glow); }
    .inline-add-btn:active { transform: scale(0.96); }

    /* === Toast === */
    .toast-container {
      position: fixed;
      top: 20px;
      left: 50%;
      transform: translateX(-50%);
      z-index: 9999;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 8px;
      width: 90%;
      max-width: 380px;
      pointer-events: none;
    }

    .toast {
      padding: 12px 20px;
      border-radius: var(--radius-sm);
      font-size: 13.5px;
      font-weight: 500;
      font-family: 'Space Grotesk', sans-serif;
      display: flex;
      align-items: center;
      gap: 10px;
      animation: toastIn 0.45s cubic-bezier(0.16, 1, 0.3, 1);
      box-shadow: var(--shadow-lg);
      pointer-events: auto;
      width: 100%;
    }

    .toast.success { background: #142118; border: 1px solid rgba(62,207,122,0.2); color: var(--success); }
    .toast.error { background: #211414; border: 1px solid rgba(232,84,84,0.2); color: var(--danger); }
    .toast.info { background: #141821; border: 1px solid rgba(100,149,237,0.2); color: #6495ed; }

    .toast.removing { animation: toastOut 0.3s ease forwards; }

    @keyframes toastIn {
      from { opacity: 0; transform: translateY(-20px) scale(0.9); }
      to { opacity: 1; transform: translateY(0) scale(1); }
    }

    @keyframes toastOut {
      to { opacity: 0; transform: translateY(-20px) scale(0.9); }
    }

    /* === Confetti === */
    .confetti-container {
      position: fixed;
      inset: 0;
      pointer-events: none;
      z-index: 9998;
    }

    .confetti-piece {
      position: absolute;
      border-radius: 2px;
      animation: confettiFall 1.8s ease-in forwards;
    }

    @keyframes confettiFall {
      0% { opacity: 1; transform: translateY(0) rotate(0deg) scale(1); }
      100% { opacity: 0; transform: translateY(100vh) rotate(900deg) scale(0.2); }
    }

    /* === Footer === */
    .app-footer {
      text-align: center;
      margin-top: 32px;
      padding-top: 20px;
      border-top: 1px solid var(--border);
      animation: slideUp 0.7s cubic-bezier(0.16, 1, 0.3, 1) 0.6s both;
    }

    .footer-text {
      font-size: 12px;
      color: var(--fg-muted);
      opacity: 0.3;
    }

    /* === Responsive Breakpoints === */

    /* Tablets & small desktops */
    @media (min-width: 520px) {
      .greeting-text { font-size: 38px; }
      .stat-number { font-size: 34px; }
      .progress-ring-wrapper { width: 90px; height: 90px; }
      .progress-ring-svg { width: 90px; height: 90px; }
    }

    /* Mobile adjustments */
    @media (max-width: 600px) {
      .app-shell { padding: 0 14px; padding-bottom: calc(90px + var(--safe-bottom)); }

      .top-bar { padding: 14px 0 16px; }
      .logo-icon { width: 38px; height: 38px; font-size: 16px; }
      .logo-info h1 { font-size: 19px; }

      .greeting-section { padding: 16px 0 14px; }
      .greeting-text { font-size: 26px; }

      .stats-row { gap: 8px; }
      .stat-card { padding: 14px 10px; }
      .stat-number { font-size: 24px; }
      .stat-label { font-size: 10px; letter-spacing: 1px; }
      .stat-icon { width: 30px; height: 30px; font-size: 12px; margin-bottom: 8px; }

      .progress-card { padding: 18px; gap: 18px; }
      .progress-ring-wrapper { width: 68px; height: 68px; }
      .progress-ring-svg { width: 68px; height: 68px; }
      .progress-ring-percent { font-size: 17px; }

      /* Show FAB on mobile */
      .fab { display: flex; }

      /* Hide inline form on mobile, use modal instead */
      .inline-form { display: none; }

      .filter-tabs { gap: 2px; padding: 3px; }
      .filter-tab { padding: 7px 12px; font-size: 12px; }
      .filter-tab .count-badge { min-width: 16px; height: 16px; font-size: 9px; }

      .task-item { padding: 14px 14px; gap: 12px; }
      .task-delete { opacity: 0.7; width: 30px; height: 30px; }
      .task-text { font-size: 14px; }

      .clear-done-btn span { display: none; }
      .clear-done-btn { padding: 8px 10px; }
    }

    /* Very small phones */
    @media (max-width: 380px) {
      .app-shell { padding: 0 10px; padding-bottom: calc(90px + var(--safe-bottom)); }
      .greeting-text { font-size: 22px; }
      .stats-row { grid-template-columns: repeat(3, 1fr); gap: 6px; }
      .stat-card { padding: 12px 8px; }
      .stat-number { font-size: 22px; }
      .progress-card { padding: 14px; gap: 14px; }
      .filter-tab { padding: 6px 10px; font-size: 11px; }
      .task-item { padding: 12px; gap: 10px; }
      .task-text { font-size: 13px; }
    }

    /* Desktop: show inline form, hide FAB */
    @media (min-width: 601px) {
      .fab { display: none !important; }
      .inline-form { display: block !important; }
      .modal-overlay { display: none !important; }
    }

    /* Reduced motion */
    @media (prefers-reduced-motion: reduce) {
      *, *::before, *::after {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
      }
    }
  </style>
</head>
<body>

  <!-- Animated Background -->
  <div class="bg-layer" aria-hidden="true">
    <div class="bg-blob"></div>
    <div class="bg-blob"></div>
    <div class="bg-blob"></div>
  </div>
  <div class="bg-grid" aria-hidden="true"></div>
  <div class="bg-noise" aria-hidden="true"></div>

  <!-- Toast Container -->
  <div class="toast-container" id="toastContainer" aria-live="polite"></div>

  <!-- Confetti Container -->
  <div class="confetti-container" id="confettiContainer" aria-hidden="true"></div>

  <!-- SVG Gradient for Progress Ring -->
  <svg style="position:absolute;width:0;height:0" aria-hidden="true">
    <defs>
      <linearGradient id="progressGradient" x1="0%" y1="0%" x2="100%" y2="0%">
        <stop offset="0%" stop-color="#c4882a"/>
        <stop offset="50%" stop-color="#e8a838"/>
        <stop offset="100%" stop-color="#f0c060"/>
      </linearGradient>
    </defs>
  </svg>

  <!-- Floating Action Button (Mobile) -->
  <button class="fab" id="fab" aria-label="Add new task">
    <i class="fas fa-plus"></i>
  </button>

  <!-- Mobile Modal -->
  <div class="modal-overlay" id="modalOverlay">
    <div class="modal-sheet">
      <div class="modal-handle"></div>
      <h2 class="modal-title">Add New Task</h2>
      <div class="form-group">
        <label class="form-label" for="modalTaskInput">Task Description</label>
        <input type="text" class="form-input" id="modalTaskInput" placeholder="What needs to be done?" maxlength="150">
      </div>
      <div class="form-group">
        <label class="form-label">Priority Level</label>
        <div class="priority-selector" id="modalPrioritySelector">
          <div class="priority-option high selected" data-priority="high">
            <i class="fas fa-fire"></i> High
          </div>
          <div class="priority-option medium" data-priority="medium">
            <i class="fas fa-minus"></i> Medium
          </div>
          <div class="priority-option low" data-priority="low">
            <i class="fas fa-leaf"></i> Low
          </div>
        </div>
      </div>
      <div class="modal-actions">
        <button class="btn btn-secondary" id="modalCancel">Cancel</button>
        <button class="btn btn-primary" id="modalAdd">Add Task</button>
      </div>
    </div>
  </div>

  <!-- Main App -->
  <div class="app-shell">

    <!-- Top Bar -->
    <header class="top-bar">
      <div class="top-bar-left">
        <div class="logo-icon"><i class="fas fa-bolt"></i></div>
        <div class="logo-info">
          <h1>DailyFlow</h1>
          <p id="greetingLine">Good morning</p>
        </div>
      </div>
      <div class="top-bar-right">
        <button class="icon-btn" id="clearAllBtn" aria-label="Clear all tasks" title="Clear all tasks">
          <i class="fas fa-trash-can"></i>
          <span class="badge" id="clearBadge">0</span>
        </button>
      </div>
    </header>

    <!-- Greeting -->
    <section class="greeting-section">
      <h2 class="greeting-text" id="greetingText">
        <span class="wave" aria-hidden="true">&#128075;</span> Let's crush it today
      </h2>
      <p class="greeting-sub">You have <span class="date-highlight" id="taskSummary">no tasks</span> &middot; <span class="date-highlight" id="dateText"></span></p>
    </section>

    <!-- Stats -->
    <section class="stats-row" aria-label="Task statistics">
      <div class="stat-card">
        <div class="stat-icon"><i class="fas fa-layer-group"></i></div>
        <div class="stat-number" id="totalCount">0</div>
        <div class="stat-label">Total</div>
      </div>
      <div class="stat-card">
        <div class="stat-icon"><i class="fas fa-spinner"></i></div>
        <div class="stat-number" id="activeCount">0</div>
        <div class="stat-label">Active</div>
      </div>
      <div class="stat-card">
        <div class="stat-icon"><i class="fas fa-check-double"></i></div>
        <div class="stat-number" id="doneCount">0</div>
        <div class="stat-label">Done</div>
      </div>
    </section>

    <!-- Progress -->
    <section class="progress-section" aria-label="Completion progress">
      <div class="progress-card">
        <div class="progress-ring-wrapper">
          <svg class="progress-ring-svg" viewBox="0 0 80 80">
            <circle class="progress-ring-bg" cx="40" cy="40" r="34"/>
            <circle class="progress-ring-fill" id="progressRing" cx="40" cy="40" r="34"/>
          </svg>
          <div class="progress-ring-text">
            <span class="progress-ring-percent" id="progressPercent">0%</span>
            <span class="progress-ring-label">Done</span>
          </div>
        </div>
        <div class="progress-info">
          <h3 id="progressTitle">Get started</h3>
          <p id="progressDesc">Add tasks and start checking them off to see your progress grow.</p>
          <div class="progress-bar-track">
            <div class="progress-bar-fill" id="progressBarFill" style="width: 0%"></div>
          </div>
        </div>
      </div>
    </section>

    <!-- Desktop Inline Form -->
    <div class="inline-form" id="inlineForm">
      <div class="inline-form-row">
        <input type="text" id="inlineInput" placeholder="What needs to be done today?" maxlength="150" aria-label="Enter a new task">
        <button class="inline-add-btn" id="inlineAddBtn" aria-label="Add task">
          <i class="fas fa-plus"></i>
          <span>Add</span>
        </button>
      </div>
      <div class="inline-priority-row" id="inlinePriorityRow">
        <button class="inline-priority-btn high" data-priority="high">
          <span class="dot"></span> High
        </button>
        <button class="inline-priority-btn medium selected" data-priority="medium">
          <span class="dot"></span> Medium
        </button>
        <button class="inline-priority-btn low" data-priority="low">
          <span class="dot"></span> Low
        </button>
      </div>
    </div>

    <!-- Filter Row -->
    <section class="filter-row" aria-label="Filter tasks">
      <div class="filter-tabs" role="tablist">
        <button class="filter-tab active" data-filter="all" role="tab" aria-selected="true">
          All<span class="count-badge" id="countAll">0</span>
        </button>
        <button class="filter-tab" data-filter="active" role="tab" aria-selected="false">
          Active<span class="count-badge" id="countActive">0</span>
        </button>
        <button class="filter-tab" data-filter="completed" role="tab" aria-selected="false">
          Done<span class="count-badge" id="countDone">0</span>
        </button>
      </div>
      <button class="clear-done-btn" id="clearDoneBtn" aria-label="Clear completed tasks">
        <i class="fas fa-broom"></i>
        <span>Clear Done</span>
      </button>
    </section>

    <!-- Task List -->
    <section class="task-list" id="taskList" aria-label="Task list"></section>

    <!-- Footer -->
    <footer class="app-footer">
      <p class="footer-text">DailyFlow &mdash; Built for focus and productivity</p>
    </footer>
  </div>

  <script>
    // === App State ===
    let tasks = [];
    let currentFilter = 'all';
    let selectedPriority = 'medium';
    let modalPriority = 'high';
    let previousDoneCount = 0;

    // Load from localStorage
    try {
      const saved = localStorage.getItem('dailyflow_tasks_v2');
      if (saved) tasks = JSON.parse(saved);
    } catch (e) { tasks = []; }

    previousDoneCount = tasks.filter(t => t.completed).length;

    // === DOM References ===
    const $ = (sel) => document.querySelector(sel);
    const $$ = (sel) => document.querySelectorAll(sel);

    const taskList = $('#taskList');
    const totalCount = $('#totalCount');
    const activeCount = $('#activeCount');
    const doneCount = $('#doneCount');
    const progressRing = $('#progressRing');
    const progressPercent = $('#progressPercent');
    const progressBarFill = $('#progressBarFill');
    const progressTitle = $('#progressTitle');
    const progressDesc = $('#progressDesc');
    const toastContainer = $('#toastContainer');
    const confettiContainer = $('#confettiContainer');
    const dateText = $('#dateText');
    const greetingLine = $('#greetingLine');
    const greetingText = $('#greetingText');
    const taskSummary = $('#taskSummary');
    const clearBadge = $('#clearBadge');
    const fab = $('#fab');
    const modalOverlay = $('#modalOverlay');
    const modalTaskInput = $('#modalTaskInput');
    const modalPrioritySelector = $('#modalPrioritySelector');
    const inlineInput = $('#inlineInput');
    const inlinePriorityRow = $('#inlinePriorityRow');

    // === Initialize Date & Greeting ===
    function initGreeting() {
      const now = new Date();
      const hour = now.getHours();
      let greeting = 'Good evening';
      let emoji = '&#127769;';

      if (hour < 12) { greeting = 'Good morning'; emoji = '&#127749;'; }
      else if (hour < 17) { greeting = 'Good afternoon'; emoji = '&#9728;&#65039;'; }
      else { greeting = 'Good evening'; emoji = '&#127769;'; }

      greetingLine.textContent = greeting;

      const motivational = [
        "Let's crush it today",
        "Make every minute count",
        "One task at a time",
        "You've got this",
        "Stay focused, stay sharp",
        "Progress over perfection"
      ];
      const pick = motivational[Math.floor(Math.random() * motivational.length)];
      greetingText.innerHTML = `<span class="wave" aria-hidden="true">${emoji}</span> ${pick}`;

      const dateOptions = { weekday: 'short', month: 'short', day: 'numeric' };
      dateText.textContent = now.toLocaleDateString('en-US', dateOptions);
    }

    // === Save ===
    function saveTasks() {
      try {
        localStorage.setItem('dailyflow_tasks_v2', JSON.stringify(tasks));
      } catch (e) { /* Storage full */ }
    }

    // === Animate Number ===
    function animateNumber(el, from, to) {
      if (from === to) return;
      const duration = 350;
      const start = performance.now();
      function step(now) {
        const p = Math.min((now - start) / duration, 1);
        const eased = 1 - Math.pow(1 - p, 3);
        el.textContent = Math.round(from + (to - from) * eased);
        if (p < 1) requestAnimationFrame(step);
      }
      requestAnimationFrame(step);
    }

    // === Update Stats ===
    function updateStats() {
      const total = tasks.length;
      const done = tasks.filter(t => t.completed).length;
      const active = total - done;
      const percent = total === 0 ? 0 : Math.round((done / total) * 100);

      animateNumber(totalCount, parseInt(totalCount.textContent) || 0, total);
      animateNumber(activeCount, parseInt(activeCount.textContent) || 0, active);
      animateNumber(doneCount, parseInt(doneCount.textContent) || 0, done);

      // Progress ring
      const circumference = 2 * Math.PI * 34; // ~213.628
      const offset = circumference - (percent / 100) * circumference;
      progressRing.style.strokeDashoffset = offset;
      progressPercent.textContent = percent + '%';
      progressBarFill.style.width = percent + '%';

      // Progress text
      if (total === 0) {
        progressTitle.textContent = 'Get started';
        progressDesc.textContent = 'Add tasks and start checking them off to see your progress grow.';
      } else if (percent === 100) {
        progressTitle.textContent = 'All done!';
        progressDesc.textContent = 'Incredible work! You\'ve completed every task on your list.';
      } else if (percent >= 50) {
        progressTitle.textContent = 'Almost there';
        progressDesc.textContent = `${active} task${active > 1 ? 's' : ''} remaining. Keep pushing!`;
      } else {
        progressTitle.textContent = 'Keep going';
        progressDesc.textContent = `${done} of ${total} tasks completed. You can do this!`;
      }

      // Task summary
      if (total === 0) taskSummary.textContent = 'no tasks';
      else if (active === 0) taskSummary.textContent = 'all tasks done';
      else taskSummary.textContent = `${active} active task${active > 1 ? 's' : ''}`;

      // Filter count badges
      $('#countAll').textContent = total;
      $('#countActive').textContent = active;
      $('#countDone').textContent = done;

      // Clear badge
      clearBadge.textContent = total;
      if (total > 0) clearBadge.classList.add('visible');
      else clearBadge.classList.remove('visible');

      // Celebrate
      if (done > 0 && done === total && total > 0 && done > previousDoneCount) {
        showToast('All tasks completed! Outstanding!', 'success');
        launchConfetti();
      }
      previousDoneCount = done;
    }

    // === Format Time ===
    function formatTime(ts) {
      const d = new Date(ts);
      let h = d.getHours();
      const m = d.getMinutes().toString().padStart(2, '0');
      const ampm = h >= 12 ? 'PM' : 'AM';
      h = h % 12 || 12;
      return `${h}:${m} ${ampm}`;
    }

    // === Escape HTML ===
    function esc(text) {
      const d = document.createElement('div');
      d.textContent = text;
      return d.innerHTML;
    }

    // === Render Tasks ===
    function renderTasks() {
      const filtered = tasks.filter(t => {
        if (currentFilter === 'active') return !t.completed;
        if (currentFilter === 'completed') return t.completed;
        return true;
      });

      taskList.innerHTML = '';

      if (filtered.length === 0) {
        const msgs = {
          all: { icon: 'fa-clipboard-list', title: 'No tasks yet', desc: 'Tap the + button to add your first task' },
          active: { icon: 'fa-mug-hot', title: 'All caught up', desc: 'No active tasks — enjoy the moment' },
          completed: { icon: 'fa-trophy', title: 'No completed tasks', desc: 'Start checking off tasks to see them here' }
        };
        const m = msgs[currentFilter];
        taskList.innerHTML = `
          <div class="empty-state">
            <div class="empty-visual"><i class="fas ${m.icon}"></i></div>
            <div class="empty-title">${m.title}</div>
            <div class="empty-desc">${m.desc}</div>
          </div>`;
        updateStats();
        return;
      }

      // Sort: active first (high → medium → low), then completed
      const priorityOrder = { high: 0, medium: 1, low: 2 };
      const sorted = [...filtered].sort((a, b) => {
        if (a.completed !== b.completed) return a.completed ? 1 : -1;
        return priorityOrder[a.priority] - priorityOrder[b.priority];
      });

      sorted.forEach((task, i) => {
        const el = document.createElement('div');
        el.className = `task-item${task.completed ? ' completed' : ''}`;
        el.style.animationDelay = `${i * 0.04}s`;
        el.dataset.id = task.id;
        el.dataset.priority = task.priority;

        el.innerHTML = `
          <label class="check-wrapper">
            <input type="checkbox" ${task.completed ? 'checked' : ''} aria-label="Toggle task">
            <div class="check-visual"><i class="fas fa-check"></i></div>
          </label>
          <div class="task-content">
            <div class="task-text">${esc(task.text)}</div>
            <div class="task-meta">
              <span class="priority-dot ${task.priority}"></span>
              <span class="priority-label ${task.priority}">${task.priority}</span>
              <span class="task-time"><i class="far fa-clock"></i> ${formatTime(task.createdAt)}</span>
            </div>
          </div>
          <button class="task-delete" aria-label="Delete task"><i class="fas fa-xmark"></i></button>`;

        // Toggle
        el.querySelector('input[type="checkbox"]').addEventListener('change', function() {
          task.completed = this.checked;
          saveTasks();
          setTimeout(() => renderTasks(), 250);
          if (task.completed) showToast('Task completed!', 'success');
        });

        // Delete
        el.querySelector('.task-delete').addEventListener('click', () => deleteTask(task.id, el));

        // Swipe to delete (touch)
        let startX = 0, currentX = 0, swiping = false;
        el.addEventListener('touchstart', (e) => {
          startX = e.touches[0].clientX;
          swiping = true;
          el.style.transition = 'none';
        }, { passive: true });

        el.addEventListener('touchmove', (e) => {
          if (!swiping) return;
          currentX = e.touches[0].clientX;
          const diff = currentX - startX;
          if (diff < 0) {
            el.style.transform = `translateX(${Math.max(diff, -100)}px)`;
            el.style.opacity = Math.max(1 + diff / 300, 0.4);
          }
        }, { passive: true });

        el.addEventListener('touchend', () => {
          if (!swiping) return;
          swiping = false;
          el.style.transition = 'all 0.3s cubic-bezier(0.16, 1, 0.3, 1)';
          const diff = currentX - startX;
          if (diff < -80) {
            deleteTask(task.id, el);
          } else {
            el.style.transform = '';
            el.style.opacity = '';
          }
          currentX = 0;
        });

        taskList.appendChild(el);
      });

      updateStats();
    }

    // === Delete Task ===
    function deleteTask(id, el) {
      el.classList.add('removing');
      setTimeout(() => {
        tasks = tasks.filter(t => t.id !== id);
        saveTasks();
        renderTasks();
        showToast('Task removed', 'error');
      }, 380);
    }

    // === Add Task ===
    function addTask(text, priority) {
      text = text.trim();
      if (!text) {
        showToast('Please enter a task description', 'info');
        return false;
      }

      tasks.unshift({
        id: Date.now().toString(36) + Math.random().toString(36).slice(2, 8),
        text,
        priority: priority || 'medium',
        completed: false,
        createdAt: Date.now()
      });

      saveTasks();
      renderTasks();
      showToast('Task added successfully', 'success');
      return true;
    }

    // === Desktop Inline Form ===
    $('#inlineAddBtn').addEventListener('click', () => {
      if (addTask(inlineInput.value, selectedPriority)) {
        inlineInput.value = '';
        inlineInput.focus();
      }
    });

    inlineInput.addEventListener('keydown', (e) => {
      if (e.key === 'Enter') {
        if (addTask(inlineInput.value, selectedPriority)) {
          inlineInput.value = '';
        }
      }
    });

    // Inline priority buttons
    inlinePriorityRow.querySelectorAll('.inline-priority-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        inlinePriorityRow.querySelectorAll('.inline-priority-btn').forEach(b => b.classList.remove('selected'));
        btn.classList.add('selected');
        selectedPriority = btn.dataset.priority;
      });
    });

    // === Mobile FAB & Modal ===
    fab.addEventListener('click', () => {
      modalOverlay.classList.add('active');
      modalTaskInput.value = '';
      modalPriority = 'high';
      modalPrioritySelector.querySelectorAll('.priority-option').forEach(o => {
        o.classList.toggle('selected', o.dataset.priority === 'high');
      });
      setTimeout(() => modalTaskInput.focus(), 300);
    });

    $('#modalCancel').addEventListener('click', closeModal);
    modalOverlay.addEventListener('click', (e) => {
      if (e.target === modalOverlay) closeModal();
    });

    function closeModal() {
      modalOverlay.classList.remove('active');
    }

    // Modal priority selector
    modalPrioritySelector.querySelectorAll('.priority-option').forEach(opt => {
      opt.addEventListener('click', () => {
        modalPrioritySelector.querySelectorAll('.priority-option').forEach(o => o.classList.remove('selected'));
        opt.classList.add('selected');
        modalPriority = opt.dataset.priority;
      });
    });

    $('#modalAdd').addEventListener('click', () => {
      if (addTask(modalTaskInput.value, modalPriority)) {
        closeModal();
      }
    });

    modalTaskInput.addEventListener('keydown', (e) => {
      if (e.key === 'Enter') {
        if (addTask(modalTaskInput.value, modalPriority)) {
          closeModal();
        }
      }
    });

    // === Filter Tabs ===
    $$('.filter-tab').forEach(tab => {
      tab.addEventListener('click', () => {
        $$('.filter-tab').forEach(t => {
          t.classList.remove('active');
          t.setAttribute('aria-selected', 'false');
        });
        tab.classList.add('active');
        tab.setAttribute('aria-selected', 'true');
        currentFilter = tab.dataset.filter;
        renderTasks();
      });
    });

    // === Clear Completed ===
    $('#clearDoneBtn').addEventListener('click', () => {
      const count = tasks.filter(t => t.completed).length;
      if (count === 0) {
        showToast('No completed tasks to clear', 'info');
        return;
      }
      // Animate removal
      taskList.querySelectorAll('.task-item.completed').forEach(el => el.classList.add('removing'));
      setTimeout(() => {
        tasks = tasks.filter(t => !t.completed);
        saveTasks();
        renderTasks();
        showToast(`${count} completed task${count > 1 ? 's' : ''} cleared`, 'error');
      }, 380);
    });

    // === Clear All ===
    $('#clearAllBtn').addEventListener('click', () => {
      if (tasks.length === 0) {
        showToast('No tasks to clear', 'info');
        return;
      }
      taskList.querySelectorAll('.task-item').forEach(el => el.classList.add('removing'));
      setTimeout(() => {
        const count = tasks.length;
        tasks = [];
        saveTasks();
        renderTasks();
        showToast(`All ${count} tasks cleared`, 'error');
      }, 380);
    });

    // === Toast ===
    function showToast(message, type = 'info') {
      const toast = document.createElement('div');
      toast.className = `toast ${type}`;
      const icons = { success: 'fa-circle-check', error: 'fa-circle-xmark', info: 'fa-circle-info' };
      toast.innerHTML = `<i class="fas ${icons[type]}"></i> ${message}`;
      toastContainer.appendChild(toast);
      setTimeout(() => {
        toast.classList.add('removing');
        setTimeout(() => toast.remove(), 300);
      }, 2500);
    }

    // === Confetti ===
    function launchConfetti() {
      const colors = ['#e8a838', '#3ecf7a', '#e85454', '#6495ed', '#f0c060', '#ff6b9d', '#a855f7'];
      for (let i = 0; i < 70; i++) {
        const piece = document.createElement('div');
        piece.className = 'confetti-piece';
        piece.style.left = Math.random() * 100 + 'vw';
        piece.style.top = '-10px';
        piece.style.background = colors[Math.floor(Math.random() * colors.length)];
        piece.style.animationDelay = Math.random() * 0.7 + 's';
        piece.style.animationDuration = (1.2 + Math.random() * 1.5) + 's';
        const size = 4 + Math.random() * 9;
        piece.style.width = size + 'px';
        piece.style.height = size + 'px';
        piece.style.borderRadius = Math.random() > 0.5 ? '50%' : '2px';
        confettiContainer.appendChild(piece);
        setTimeout(() => piece.remove(), 3000);
      }
    }

    // === Keyboard: Escape closes modal ===
    document.addEventListener('keydown', (e) => {
      if (e.key === 'Escape' && modalOverlay.classList.contains('active')) {
        closeModal();
      }
    });

    // === Init ===
    initGreeting();
    renderTasks();
  </script>
</body>
</html>
