<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#060B14">
    <title>Abdelrahman Abaza</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fira+Code:wght@300;400;500&family=Inter:wght@300;400;500;600;700;800;900&display=swap" rel="stylesheet">
    <style>
        /* ───────────────────────────────────────────
           DESIGN TOKEN SYSTEM
           Three-layer semantic palette:
           cyan  = network / transport layer
           purple = automation / intelligence layer
           green  = system status / uptime
        ─────────────────────────────────────────── */
        :root {
            --bg:          #060B14;
            --card:        rgba(9, 15, 27, 0.78);
            --border:      rgba(255, 255, 255, 0.065);
            --cyan:        #00D4FF;
            --purple:      #8B5CF6;
            --green:       #10B981;
            --text:        #EEF2FF;
            --muted:       #5A6A82;
            --subtle:      #8899AA;
            --cyan-12:     rgba(0,  212, 255, 0.12);
            --purple-12:   rgba(139, 92, 246, 0.12);
            --green-12:    rgba( 16, 185, 129, 0.12);
        }

        *, *::before, *::after {
            box-sizing: border-box;
            margin: 0; padding: 0;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background: var(--bg);
            color: var(--text);
            font-family: 'Inter', sans-serif;
            min-height: 100dvh;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            padding: 28px 18px 56px;
            overflow-x: hidden;
        }

        /* ── LIVE NETWORK TOPOLOGY CANVAS ──────── */
        #netCanvas {
            position: fixed;
            inset: 0;
            z-index: 0;
            pointer-events: none;
            opacity: 0.65;
        }

        /* ── SCANLINE TEXTURE ───────────────────── */
        body::before {
            content: '';
            position: fixed;
            inset: 0;
            background: repeating-linear-gradient(
                0deg, transparent, transparent 3px,
                rgba(0,0,0,0.045) 3px, rgba(0,0,0,0.045) 4px
            );
            pointer-events: none;
            z-index: 1;
        }

        /* ── AMBIENT CURSOR GLOW (desktop) ──────── */
        #cglow {
            position: fixed;
            width: 480px; height: 480px;
            border-radius: 50%;
            background: radial-gradient(circle,
                rgba(0,212,255,0.042) 0%, transparent 70%);
            pointer-events: none;
            transform: translate(-50%, -50%);
            z-index: 1;
            opacity: 0;
            transition: opacity 0.6s;
        }

        /* ── LAYOUT WRAPPER ─────────────────────── */
        .wrap {
            width: 100%;
            max-width: 420px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            position: relative;
            z-index: 2;
            transform-style: preserve-3d;
        }

        /* ── BASE CARD ──────────────────────────── */
        .card {
            background: var(--card);
            border: 1px solid var(--border);
            border-radius: 20px;
            backdrop-filter: blur(32px) saturate(170%);
            -webkit-backdrop-filter: blur(32px) saturate(170%);
            box-shadow:
                0 1px 0 rgba(255,255,255,0.045) inset,
                0 28px 56px rgba(0,0,0,0.60);
            position: relative;
            transition: border-color 0.35s ease, box-shadow 0.35s ease;
        }

        /* Top-edge light reflection */
        .card::before {
            content: '';
            position: absolute;
            top: 0; left: 20%; right: 20%;
            height: 1px;
            background: linear-gradient(90deg,
                transparent, rgba(255,255,255,0.1), transparent);
            pointer-events: none;
            border-radius: 0;
        }

        /* Spotlight radial gradient (moves with cursor) */
        .card::after {
            content: '';
            position: absolute;
            inset: 0;
            border-radius: 20px;
            background: radial-gradient(
                480px circle at var(--sx, 50%) var(--sy, 50%),
                rgba(0,212,255,0.055), transparent 50%
            );
            opacity: 0;
            transition: opacity 0.4s ease;
            pointer-events: none;
        }

        .card:hover::after  { opacity: 1; }
        .card:hover         { border-color: rgba(0,212,255,0.16); }

        /* ── 1. IDENTITY CARD ───────────────────── */
        .identity {
            display: flex;
            align-items: center;
            gap: 18px;
            padding: 22px 22px 22px 24px;
        }

        /* Avatar with spinning conic-gradient ring */
        .avatar-wrap {
            flex-shrink: 0;
            position: relative;
            width: 72px; height: 72px;
        }

        .avatar-wrap::before {
            content: '';
            position: absolute;
            inset: -2.5px;
            border-radius: 50%;
            background: conic-gradient(
                from 0deg,
                var(--cyan) 0%,
                var(--purple) 35%,
                var(--green) 65%,
                var(--cyan) 100%
            );
            animation: spin-ring 4s linear infinite;
            box-shadow: 0 0 22px rgba(0,212,255,0.28);
        }

        @keyframes spin-ring { to { transform: rotate(360deg); } }

        .avatar-inner {
            position: absolute;
            inset: 2.5px;
            border-radius: 50%;
            background: #07101F;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 1;
        }

        .avatar-inner img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .id-info  { flex: 1; min-width: 0; }

        .id-name {
            font-size: 21px;
            font-weight: 800;
            letter-spacing: -0.8px;
            line-height: 1.1;
            margin-bottom: 5px;
            background: linear-gradient(125deg, #ffffff 0%, rgba(255,255,255,0.6) 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .id-role {
            font-size: 12px;
            color: var(--muted);
            font-weight: 500;
            letter-spacing: 0.2px;
            margin-bottom: 11px;
        }

        .id-badge {
            display: inline-flex;
            align-items: center;
            gap: 6px;
            background: var(--green-12);
            border: 1px solid rgba(16,185,129,0.22);
            border-radius: 20px;
            padding: 3px 11px 3px 7px;
            font-size: 10.5px;
            font-weight: 700;
            color: var(--green);
            letter-spacing: 0.5px;
            text-transform: uppercase;
        }

        .pulse-dot {
            width: 6px; height: 6px;
            border-radius: 50%;
            background: var(--green);
            animation: pulse 2.2s infinite;
        }

        @keyframes pulse {
            0%  { box-shadow: 0 0 0 0   rgba(16,185,129,0.75); }
            70% { box-shadow: 0 0 0 7px rgba(16,185,129,0);    }
            100%{ box-shadow: 0 0 0 0   rgba(16,185,129,0);    }
        }

        /* ── 2. TERMINAL CARD ───────────────────── */
        .terminal {
            padding: 16px 20px 20px;
            background: rgba(3, 6, 13, 0.93);
            border-color: rgba(0,212,255,0.1);
            font-family: 'Fira Code', monospace;
            font-size: 12.5px;
            overflow: hidden;
        }

        .term-bar {
            display: flex;
            align-items: center;
            gap: 8px;
            margin-bottom: 14px;
        }

        .tdots { display: flex; gap: 5px; }
        .tdot  { width: 9px; height: 9px; border-radius: 50%; }
        .td-r  { background: #FF5F57; }
        .td-y  { background: #FEBC2E; }
        .td-g  { background: #28C840; }

        .term-win-title {
            flex: 1;
            text-align: center;
            font-size: 10.5px;
            color: var(--muted);
            font-family: 'Inter', sans-serif;
            letter-spacing: 0.8px;
            opacity: 0.6;
        }

        #termBody {
            min-height: 72px;
            display: flex;
            flex-direction: column;
            gap: 0;
        }

        .tline {
            display: flex;
            align-items: baseline;
            gap: 6px;
            line-height: 1.95;
        }

        .t-prompt { color: var(--cyan);   font-weight: 500; user-select: none; }
        .t-cmd    { color: rgba(235,242,255,0.88); }
        .t-out    { color: var(--green);  padding-left: 14px; }

        .t-cursor {
            display: inline-block;
            width: 7px; height: 13px;
            background: var(--cyan);
            animation: blink 0.9s step-end infinite;
            vertical-align: text-bottom;
            margin-left: 1px;
        }

        @keyframes blink {
            0%, 100% { opacity: 1; }
            50%       { opacity: 0; }
        }

        /* ── 3. CTA BUTTON ──────────────────────── */
        .cta-btn {
            width: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
            padding: 19px 24px;
            background: linear-gradient(135deg, #1E3FA0 0%, #5B21B6 55%, #6D28D9 100%);
            border: 1px solid rgba(139,92,246,0.25);
            border-radius: 20px;
            color: #fff;
            font-family: 'Inter', sans-serif;
            font-size: 15px;
            font-weight: 700;
            letter-spacing: -0.2px;
            cursor: pointer;
            box-shadow:
                0 1px 0 rgba(255,255,255,0.12) inset,
                0 10px 40px rgba(109,40,217,0.42);
            position: relative;
            overflow: hidden;
            transition: filter 0.2s, transform 0.15s, box-shadow 0.2s;
        }

        .cta-btn:hover {
            filter: brightness(1.1);
            box-shadow: 0 1px 0 rgba(255,255,255,0.12) inset,
                        0 12px 48px rgba(109,40,217,0.55);
        }

        .cta-btn:active { transform: scale(0.97); filter: brightness(0.92); }

        .cta-btn svg { width: 19px; height: 19px; flex-shrink: 0; }

        /* Shimmer sweep animation */
        .cta-btn::after {
            content: '';
            position: absolute;
            top: 0; left: -80%;
            width: 55%; height: 100%;
            background: linear-gradient(90deg,
                transparent, rgba(255,255,255,0.17), transparent);
            animation: cta-shimmer 3s 1.5s infinite;
        }

        @keyframes cta-shimmer { to { left: 160%; } }

        /* ── 4. LINKS GRID ──────────────────────── */
        .links-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
        }

        .lcard {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            gap: 10px;
            padding: 22px 16px;
            text-decoration: none;
            color: var(--text);
            transition: transform 0.2s;
        }

        .lcard:active { transform: scale(0.95); }

        .licon {
            width: 44px; height: 44px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: transform 0.25s ease, box-shadow 0.25s ease;
        }

        .lcard:hover .licon { transform: translateY(-3px); }

        .licon svg { width: 22px; height: 22px; }

        .l-name {
            font-size: 13px;
            font-weight: 600;
            letter-spacing: -0.2px;
            line-height: 1;
        }

        .l-sub {
            font-size: 10.5px;
            color: var(--muted);
            font-weight: 400;
            margin-top: -5px;
        }

        /* Custom branding link tokens */
        .li-ph { background: var(--cyan-12);          color: var(--cyan); }
        .li-wa { background: rgba(37,211,102,0.12);   color: #25D366; }
        .li-ip { background: rgba(255,255,255,0.06);  color: #FFFFFF; font-style: italic; font-weight: 900; font-size: 13px !important; }
        .li-em { background: rgba(139,92,246,0.12);   color: var(--purple); }

        .lcard.ph:hover .licon { box-shadow: 0 0 20px rgba(0,212,255,0.28); }
        .lcard.wa:hover .licon { box-shadow: 0 0 20px rgba(37,211,102,0.28); }
        .lcard.ip:hover .licon { box-shadow: 0 0 20px rgba(255,255,255,0.2); border: 1px solid rgba(255,255,255,0.2); }
        .lcard.em:hover .licon { box-shadow: 0 0 20px rgba(139,92,246,0.28); }

        /* ── 5. FOOTER ──────────────────────────── */
        .footer {
            text-align: center;
            font-family: 'Fira Code', monospace;
            font-size: 10.5px;
            color: var(--muted);
            padding: 8px 0 4px;
            opacity: 0.5;
            letter-spacing: 0.4px;
            user-select: none;
        }

        /* ── ENTRY ANIMATIONS ───────────────────── */
        @keyframes fadeUp {
            from { opacity: 0; transform: translateY(24px); }
            to   { opacity: 1; transform: translateY(0);    }
        }

        .wrap > * {
            animation: fadeUp 0.75s cubic-bezier(0.16, 1, 0.3, 1) both;
        }

        .wrap > *:nth-child(1) { animation-delay: 0.00s; }
        .wrap > *:nth-child(2) { animation-delay: 0.08s; }
        .wrap > *:nth-child(3) { animation-delay: 0.16s; }
        .wrap > *:nth-child(4) { animation-delay: 0.24s; }
        .wrap > *:nth-child(5) { animation-delay: 0.32s; }

        /* ── REDUCED MOTION SAFETY ──────────────── */
        @media (prefers-reduced-motion: reduce) {
            *, *::before, *::after {
                animation: none !important;
                transition: none !important;
            }
        }
    </style>
</head>
<body>

    <canvas id="netCanvas"></canvas>
    <div id="cglow"></div>

    <main class="wrap" id="wrap">

        <!-- ① IDENTITY ──────────────────────────── -->
        <div class="card identity">
            <div class="avatar-wrap">
                <div class="avatar-inner">
                    <!-- Replace with your real image link or file name -->
                    <img src="Abd1.jpg" alt="Abdelrahman Abaza">
                </div>
            </div>
            <div class="id-info">
                <div class="id-name">Abdelrahman Abaza</div>
<!--                 <div class="id-role">IT Manager · Network Engineer</div>  -->
<!--                 <div class="id-badge">
                    <div class="pulse-dot"></div>
                    System Online
                </div> -->
            </div>
        </div>

        <!-- ② LIVE TERMINAL ─────────────────────── -->
        <div class="card terminal" id="termCard">
            <div class="term-bar">
                <div class="tdots">
                    <div class="tdot td-r"></div>
                    <div class="tdot td-y"></div>
                    <div class="tdot td-g"></div>
                </div>
                <div class="term-win-title">Bio</div>
            </div>
            <div id="termBody"></div>
        </div>

        <!-- ③ CTA vCARD DOWNLOAD ───────────────── -->
        <button class="cta-btn" id="ctaBtn" type="button">
            <svg fill="none" stroke="white" stroke-width="2.2" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round"
                    d="M16 7a4 4 0 11-8 0 4 4 0 018 0z
                       M12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"/>
            </svg>
            Save Contact · Download vCard
        </button>

        <!-- ④ INTERACTIVE LINKS GRID ────────────── -->
        <div class="links-grid">
            <!-- CALL DIRECT -->
            <a href="tel:+201273666906" class="card lcard ph">
                <div class="licon li-ph">
                    <svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498
                               4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042
                               11.042 0 005.516 5.516l1.13-2.257a1 1 0
                               011.21-.502l4.493 1.498a1 1 0 01.684.948V19
                               a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"/>
                    </svg>
                </div>
                <div class="l-name">Call Direct</div>
                <div class="l-sub">Voice Line</div>
            </a>

            <!-- WHATSAPP -->
            <a href="https://wa.me/201273666906" target="_blank" rel="noopener" class="card lcard wa">
                <div class="licon li-wa">
                    <svg fill="currentColor" viewBox="0 0 24 24">
                        <path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L0 24l6.335-1.662c1.746.953 3.71 1.455 5.703 1.456h.004c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 00-3.48-8.413z"/>
                    </svg>
                </div>
                <div class="l-name">WhatsApp</div>
                <div class="l-sub">Instant Chat</div>
            </a>

            <!-- INSTAPAY -->
            <a href="https://ipn.eg/S/abdulrhmanabaza16399/instapay/4fe5DR" target="_blank" rel="noopener" class="card lcard ip">
                <div class="licon li-ip">
                    <i>ip</i>
                </div>
                <div class="l-name">Instapay</div>
                <div class="l-sub">Direct Transfer</div>
            </a>

            <!-- EMAIL -->
            <a href="mailto:Abdelrahman@example.com" class="card lcard em">
                <div class="licon li-em">
                    <svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8
                               M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5
                               a2 2 0 00-2 2v10a2 2 0 002 2z"/>
                    </svg>
                </div>
                <div class="l-name">Email</div>
                <div class="l-sub">Mail Me</div>
            </a>
        </div>

        <!-- ⑤ FOOTER ─────────────────────────────── -->
        <footer class="footer">
            © 2026 · Abdelrahman.abaza · <span id="clk">--:--:--</span> EET
        </footer>

    </main>

    <script>
    (() => {
        'use strict';

        /* ────────────────────────────────────────────
           1. ANIMATED NETWORK TOPOLOGY CANVAS
        ──────────────────────────────────────────── */
        const canvas = document.getElementById('netCanvas');
        const ctx    = canvas.getContext('2d');
        const N      = 48;
        const REACH  = 130;
        let   nodes  = [];

        function resizeCanvas() {
            canvas.width  = window.innerWidth;
            canvas.height = window.innerHeight;
        }

        class NetNode {
            constructor() { this.init(); }
            init() {
                this.x  = Math.random() * canvas.width;
                this.y  = Math.random() * canvas.height;
                this.vx = (Math.random() - 0.5) * 0.42;
                this.vy = (Math.random() - 0.5) * 0.42;
                this.r  = Math.random() * 1.3 + 0.4;
            }
            tick() {
                this.x += this.vx; this.y += this.vy;
                if (this.x < 0 || this.x > canvas.width)  this.vx *= -1;
                if (this.y < 0 || this.y > canvas.height) this.vy *= -1;
            }
            draw() {
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.r, 0, Math.PI * 2);
                ctx.fillStyle = 'rgba(0,212,255,0.55)';
                ctx.fill();
            }
        }

        function drawEdges() {
            for (let i = 0; i < N; i++) {
                for (let j = i + 1; j < N; j++) {
                    const dx = nodes[i].x - nodes[j].x;
                    const dy = nodes[i].y - nodes[j].y;
                    const d  = Math.sqrt(dx*dx + dy*dy);
                    if (d < REACH) {
                        const alpha = (1 - d / REACH) * 0.22;
                        ctx.beginPath();
                        ctx.moveTo(nodes[i].x, nodes[i].y);
                        ctx.lineTo(nodes[j].x, nodes[j].y);
                        ctx.strokeStyle = `rgba(0,212,255,${alpha})`;
                        ctx.lineWidth   = 0.75;
                        ctx.stroke();
                    }
                }
            }
        }

        resizeCanvas();
        nodes = Array.from({ length: N }, () => new NetNode());

        (function frame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            nodes.forEach(n => { n.tick(); n.draw(); });
            drawEdges();
            requestAnimationFrame(frame);
        })();

        window.addEventListener('resize', () => {
            resizeCanvas();
            nodes.forEach(n => n.init());
        });


        /* ────────────────────────────────────────────
           2. LIVE TERMINAL TYPEWRITER
        ──────────────────────────────────────────── */
        const SEQS = [
            { cmd: 'whoami',            out: 'Abdelrahman_abaza' },
         /*   { cmd: 'ping backbone',     out: 'Reply 0ms — All nodes reachable' },
            { cmd: 'status --infra',    out: '● IPTV Stack  ●  Core Network  →  ONLINE' },
            { cmd: 'stack --active',    out: 'DVB · Exterity · Anevia · Python 3 · Linux' },
            { cmd: 'engine --mode',     out: 'Automation Engine  →  Running  [v3.2.1]' } */
        ];

        const termBody = document.getElementById('termBody');
        let seqIdx = 0, charIdx = 0, phase = 'cmd';
        let done = [], pauseCnt = 0;

        function buildLine(type, text) {
            const d = document.createElement('div');
            d.className = 'tline';
            d.innerHTML = type === 'cmd'
                ? `<span class="t-prompt">$</span><span class="t-cmd"> ${text}</span>`
                : `<span class="t-out">${text}</span>`;
            return d;
        }

        function renderTerm() {
            termBody.innerHTML = '';
            done.forEach(l => termBody.appendChild(buildLine(l.t, l.v)));

            const src   = phase === 'cmd' ? SEQS[seqIdx].cmd : SEQS[seqIdx].out;
            const typed = src.slice(0, charIdx);
            const live  = document.createElement('div');
            live.className = 'tline';
            live.innerHTML = phase === 'cmd'
                ? `<span class="t-prompt">$</span><span class="t-cmd"> ${typed}</span><span class="t-cursor"></span>`
                : `<span class="t-out">${typed}</span><span class="t-cursor"></span>`;
            termBody.appendChild(live);
        }

        function tickTerm() {
            if (phase === 'pause') {
                if (++pauseCnt > 22) {
                    seqIdx = (seqIdx + 1) % SEQS.length;
                    done = []; charIdx = 0; pauseCnt = 0; phase = 'cmd';
                }
            } else {
                const src = phase === 'cmd' ? SEQS[seqIdx].cmd : SEQS[seqIdx].out;
                if (charIdx < src.length) {
                    charIdx++;
                } else {
                    done.push({ t: phase, v: src });
                    charIdx = 0;
                    phase = phase === 'cmd' ? 'out' : 'pause';
                }
            }
            renderTerm();
        }

        setInterval(tickTerm, 52);


        /* ────────────────────────────────────────────
           3. SPOTLIGHT CARD EFFECT
        ──────────────────────────────────────────── */
        document.querySelectorAll('.card').forEach(card => {
            card.addEventListener('mousemove', e => {
                const r = card.getBoundingClientRect();
                card.style.setProperty('--sx',
                    ((e.clientX - r.left) / r.width  * 100).toFixed(1) + '%');
                card.style.setProperty('--sy',
                    ((e.clientY - r.top)  / r.height * 100).toFixed(1) + '%');
            });
        });


        /* ────────────────────────────────────────────
           4. AMBIENT CURSOR GLOW (desktop only)
        ──────────────────────────────────────────── */
        const cglow = document.getElementById('cglow');
        let glowVisible = false;
        document.addEventListener('mousemove', e => {
            cglow.style.left = e.clientX + 'px';
            cglow.style.top  = e.clientY + 'px';
            if (!glowVisible) { cglow.style.opacity = '1'; glowVisible = true; }
        });


        /* ────────────────────────────────────────────
           5. GYROSCOPE TILT (mobile)
        ──────────────────────────────────────────── */
        const wrap = document.getElementById('wrap');
        if (window.DeviceOrientationEvent) {
            window.addEventListener('deviceorientation', e => {
                if (e.gamma == null) return;
                const rx = Math.min(Math.max((e.beta  || 0) - 45, -18), 18) / 3.8;
                const ry = Math.min(Math.max( e.gamma,          -18), 18) / 3.8;
                wrap.style.transform =
                    `perspective(1000px) rotateX(${rx}deg) rotateY(${ry}deg)`;
            });
        }


        /* ────────────────────────────────────────────
           6. HAPTIC FEEDBACK
        ──────────────────────────────────────────── */
        function haptic(pattern = [35, 20, 35]) {
            if ('vibrate' in navigator) navigator.vibrate(pattern);
        }

        document.querySelectorAll('.lcard').forEach(el => {
            el.addEventListener('click', () => haptic());
        });


        /* ────────────────────────────────────────────
           7. VCARD DOWNLOAD + NATIVE SHARE
        ──────────────────────────────────────────── */
        const VCARD =
            'BEGIN:VCARD\r\n' +
            'VERSION:3.0\r\n' +
            'N:Abaza;Abdelrahman;;;\r\n' +
            'FN:Abdelrahman Abaza\r\n' +
/*             'TITLE:IT Manager & Network Engineer\r\n' +  */
            'EMAIL;TYPE=INTERNET:Abdelrahman@example.com\r\n' +
            'TEL;TYPE=CELL:+201273666906\r\n' +
 /*            'ORG:Network Operations\r\n' + 
            'NOTE:Triple Play · IPTV · Network Automation · Python\r\n' + */
            'END:VCARD';

        document.getElementById('ctaBtn').addEventListener('click', async () => {
            haptic([25, 15, 25, 15, 25]);

            if (navigator.share) {
                try {
                    await navigator.share({
                        title: 'Abdelrahman Abaza',
                        url: location.href,
                    });
                    return;
                } catch (_) { /* share cancelled */ }
            }

            const blob = new Blob([VCARD], { type: 'text/vcard;charset=utf-8' });
            const url  = URL.createObjectURL(blob);
            const a    = Object.assign(document.createElement('a'), {
                href: url, download: 'Abdelrahman_Abaza.vcf',
            });
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
        });


        /* ────────────────────────────────────────────
           8. LIVE SYSTEM CLOCK (Cairo / EET)
        ──────────────────────────────────────────── */
        const clkEl = document.getElementById('clk');
        function updateClock() {
            clkEl.textContent = new Date().toLocaleTimeString('en-GB', {
                hour12: false, timeZone: 'Africa/Cairo',
            });
        }
        updateClock();
        setInterval(updateClock, 1000);

    })();
    </script>
</body>
</html>
