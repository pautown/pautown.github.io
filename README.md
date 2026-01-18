<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>townhaus</title>
    <link rel="stylesheet" href="shared.css">
    <style>
        body {
            background: var(--bone);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 2rem;
            position: relative;
            overflow-x: hidden;
            transition: background-color 2s ease;
        }

        body.deeper {
            background: var(--slate-deep);
            --bone: #1a1a1a;
            --paper: #252525;
            --ink: #e0d8c8;
            --slate: #a0a0a0;
        }

        body.breathing { animation: bodyBreathe 6s ease-in-out infinite; }

        @keyframes bodyBreathe {
            0%, 100% { filter: brightness(1); }
            50% { filter: brightness(1.02); }
        }

        /* Static grain - no animation */
        .grain {
            position: fixed;
            inset: 0;
            background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.8' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
            opacity: 0.025;
            pointer-events: none;
            z-index: 1000;
        }

        /* CSS-only particles */
        .particle {
            position: fixed;
            width: 2px;
            height: 2px;
            background: var(--amber);
            border-radius: 50%;
            opacity: 0;
            pointer-events: none;
            animation: particleFloat var(--dur, 25s) linear infinite;
            animation-delay: var(--del, 0s);
            left: var(--x, 50%);
        }

        @keyframes particleFloat {
            0% { transform: translateY(100vh); opacity: 0; }
            5% { opacity: 0.25; }
            95% { opacity: 0.15; }
            100% { transform: translateY(-5vh); opacity: 0; }
        }

        /* Mist */
        .mist {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 35%;
            background: linear-gradient(to top, rgba(245, 240, 230, 0.06) 0%, transparent 100%);
            pointer-events: none;
            z-index: 4;
            animation: mistRise 35s ease-in-out infinite;
        }

        @keyframes mistRise {
            0%, 100% { opacity: 0; transform: translateY(5%); }
            50% { opacity: 1; transform: translateY(0); }
        }

        /* Presence */
        .presence {
            position: fixed;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            background: radial-gradient(circle, var(--amber) 0%, transparent 70%);
            opacity: 0;
            pointer-events: none;
            z-index: 3;
            filter: blur(25px);
            transition: opacity 3s ease;
        }

        .presence.watching { opacity: 0.12; }

        .header {
            text-align: center;
            margin-bottom: 3rem;
            opacity: 0;
            animation: fadeIn 2s ease 0.3s forwards;
            z-index: 10;
        }

        .title {
            font-size: 3.5rem;
            font-weight: 300;
            letter-spacing: 0.4em;
            color: var(--slate-deep);
            margin-bottom: 0.5rem;
        }

        .subtitle {
            font-size: 0.9rem;
            font-style: italic;
            color: var(--slate);
            opacity: 0.6;
        }

        .arrangement-hint {
            font-family: 'JetBrains Mono', monospace;
            font-size: 0.65rem;
            color: var(--slate-light);
            margin-top: 1.5rem;
            opacity: 0;
            transition: opacity 1s ease;
            letter-spacing: 0.05em;
        }

        .header:hover .arrangement-hint { opacity: 0.5; }

        /* Stillness message */
        .stillness-message {
            position: fixed;
            bottom: 30%;
            left: 50%;
            transform: translateX(-50%);
            font-family: 'Cormorant Garamond', serif;
            font-size: 1.1rem;
            font-style: italic;
            color: var(--slate);
            opacity: 0;
            pointer-events: none;
            z-index: 100;
            transition: opacity 2s ease;
        }

        .stillness-message.visible { opacity: 0.5; }

        .fragments-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
            gap: 1.5rem;
            max-width: 1200px;
            width: 100%;
            padding: 1rem;
            z-index: 10;
        }

        .fragment {
            aspect-ratio: 1;
            background: var(--paper);
            border: 1px solid var(--bone-dark);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-decoration: none;
            color: var(--ink);
            position: relative;
            overflow: hidden;
            cursor: pointer;
            transition: transform 0.4s ease, box-shadow 0.4s ease, border-color 0.4s ease;
            opacity: 0;
            animation: fragmentAppear 0.8s ease forwards;
            animation-delay: var(--delay, 0s);
        }

        @keyframes fragmentAppear {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .fragment:hover {
            border-color: var(--amber);
            box-shadow: 0 8px 30px var(--shadow);
            transform: translateY(-4px);
        }

        .fragment.dragging {
            opacity: 0.4;
            transform: scale(0.95);
            z-index: 100;
        }

        .fragment.drag-over {
            border-color: var(--amber-glow);
            background: var(--cream);
        }

        .fragment-glyph {
            font-size: 2.5rem;
            margin-bottom: 0.5rem;
            opacity: 0.7;
            transition: opacity 0.3s ease, transform 0.3s ease;
        }

        .fragment:hover .fragment-glyph {
            opacity: 1;
            transform: scale(1.1);
        }

        .fragment-name {
            font-size: 0.85rem;
            font-weight: 400;
            letter-spacing: 0.15em;
            text-transform: lowercase;
            color: var(--slate);
        }

        .fragment-whisper {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            padding: 0.8rem;
            font-family: 'JetBrains Mono', monospace;
            font-size: 0.55rem;
            color: var(--slate);
            text-align: center;
            opacity: 0;
            transform: translateY(5px);
            transition: opacity 0.3s ease, transform 0.3s ease;
            background: linear-gradient(transparent, var(--paper));
        }

        .fragment:hover .fragment-whisper {
            opacity: 0.6;
            transform: translateY(0);
        }

        /* Fragment themes */
        .fragment[data-page="silence"] { background: linear-gradient(135deg, var(--paper) 0%, var(--bone-dark) 100%); }
        .fragment[data-page="jungle"] { background: linear-gradient(135deg, var(--paper) 0%, rgba(134, 179, 152, 0.1) 100%); }
        .fragment[data-page="orb"] { background: linear-gradient(135deg, var(--paper) 0%, var(--green-translucent) 100%); }
        .fragment[data-page="shadow"] { background: linear-gradient(135deg, var(--paper) 0%, var(--slate) 100%); }
        .fragment[data-page="stone"] { background: linear-gradient(135deg, var(--bone-dark) 0%, var(--slate-light) 100%); }
        .fragment[data-page="barn"] { background: linear-gradient(135deg, var(--paper) 0%, #8b7355 20%, var(--paper) 100%); }
        .fragment[data-page="forest"] { background: linear-gradient(135deg, var(--green-deep) 0%, var(--paper) 100%); }
        .fragment[data-page="shop"] { background: linear-gradient(135deg, var(--paper) 0%, var(--amber-soft) 100%); }
        .fragment[data-page="switch"] { background: var(--paper); }
        .fragment[data-page="time"] { background: linear-gradient(135deg, var(--golden) 0%, var(--slate-deep) 50%, var(--night) 100%); }
        .fragment[data-page="rhyme"] { background: linear-gradient(135deg, var(--paper) 0%, var(--amber-glow) 100%); }
        .fragment[data-page="question"] { background: radial-gradient(circle at center, var(--paper) 0%, var(--bone-dark) 100%); }
        .fragment[data-page="mystery"] { background: var(--slate-deep); }
        .fragment[data-page="is"] { background: var(--cream); }
        .fragment[data-page="door"] { background: linear-gradient(180deg, var(--paper) 0%, var(--amber-dark) 100%); }
        .fragment[data-page="between"] { background: linear-gradient(90deg, var(--paper) 0%, transparent 50%, var(--paper) 100%); }
        .fragment[data-page="found"] { background: linear-gradient(135deg, var(--bone) 0%, var(--amber-glow) 50%, var(--bone) 100%); }
        .fragment[data-page="ruby"] { background: linear-gradient(135deg, var(--paper) 0%, #dc2850 30%, #b91c3c 70%, var(--paper) 100%); }
        .fragment[data-page="ruby"] .fragment-name { color: #7f1d3d; }
        .fragment[data-page="ruby"] .fragment-whisper { color: #b91c3c; }
        .fragment[data-page="words"] { background: linear-gradient(135deg, #64748b 0%, var(--paper) 50%, var(--amber) 100%); }
        .fragment[data-page="saturn"] { background: linear-gradient(135deg, #1a1025 0%, #0f1729 100%); }
        .fragment[data-page="saturn"] .fragment-name { color: var(--bone); }
        .fragment[data-page="saturn"] .fragment-whisper { color: var(--bone-dark); background: linear-gradient(transparent, #0f1729); }
        .fragment[data-page="doors"] { background: linear-gradient(180deg, var(--paper) 0%, var(--amber-dark) 40%, #3d2810 100%); }
        .fragment[data-page="states"] { background: linear-gradient(180deg, rgba(200, 220, 235, 0.3) 0%, var(--paper) 30%, rgba(180, 210, 230, 0.2) 70%, rgba(74, 85, 104, 0.15) 100%); }
        .fragment[data-page="spectrum"] { background: linear-gradient(135deg, hsl(0, 60%, 85%) 0%, hsl(60, 60%, 85%) 25%, hsl(120, 50%, 80%) 50%, hsl(240, 50%, 85%) 75%, hsl(300, 50%, 85%) 100%); }
        .fragment[data-page="oma"] { background: linear-gradient(135deg, #f5ebe0 0%, #e8c89e 40%, #d4a056 100%); }
        .fragment[data-page="oma"] .fragment-glyph { color: #8b7355; }
        .fragment[data-page="oma"] .fragment-whisper { color: #8b7355; background: linear-gradient(transparent, #f5ebe0); }
        .fragment[data-page="office"] { background: linear-gradient(135deg, var(--paper) 0%, #fff59d 20%, var(--bone-dark) 60%, #f8bbd9 90%, var(--paper) 100%); }
        .fragment[data-page="office"] .fragment-whisper { color: var(--slate); }
        .fragment[data-page="almost"] { background: linear-gradient(135deg, var(--paper) 0%, transparent 30%, var(--bone-dark) 50%, transparent 70%, var(--paper) 100%); border-style: dashed; }
        .fragment[data-page="almost"] .fragment-glyph { opacity: 0.5; }
        .fragment[data-page="almost"]:hover .fragment-glyph { opacity: 0.8; }
        .fragment[data-page="gary"] { background: linear-gradient(135deg, var(--paper) 0%, var(--amber-soft) 30%, var(--bone-dark) 70%, var(--slate-light) 100%); }
        .fragment[data-page="gary"] .fragment-glyph { font-family: 'JetBrains Mono', monospace; }
        .fragment[data-page="gary"] .fragment-whisper { color: var(--amber-deep); }

        .fragment[data-page="shadow"] .fragment-name,
        .fragment[data-page="mystery"] .fragment-name,
        .fragment[data-page="time"] .fragment-name { color: var(--bone); }

        .fragment[data-page="shadow"] .fragment-whisper,
        .fragment[data-page="mystery"] .fragment-whisper,
        .fragment[data-page="time"] .fragment-whisper {
            color: var(--bone-dark);
            background: linear-gradient(transparent, var(--slate-deep));
        }

        .fragment[data-page="forest"] .fragment-name { color: var(--cream); }

        /* Flip tile */
        .fragment-flip-container {
            aspect-ratio: 1;
            perspective: 1000px;
            opacity: 0;
            animation: fragmentAppear 0.8s ease forwards;
            animation-delay: var(--delay, 0s);
        }

        .fragment-flip-container:hover { transform: translateY(-4px); }
        .fragment-flip-container.dragging { opacity: 0.4; transform: scale(0.95); z-index: 100; }

        .fragment-flip {
            width: 100%;
            height: 100%;
            transform-style: preserve-3d;
            transition: transform 0.8s ease;
        }

        .fragment-flip.flipped { transform: rotateY(180deg); }

        .fragment-flip-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border: 1px solid var(--bone-dark);
            cursor: pointer;
        }

        .fragment-evil {
            background: linear-gradient(135deg, #722F37 0%, #4A0E0E 40%, #8B0000 70%, #5C1A1A 100%);
        }

        .fragment-evil .fragment-glyph { color: #1a0505; font-size: 3rem; }
        .fragment-evil .fragment-name { color: #2a0a0a; letter-spacing: 0.2em; }
        .fragment-evil .fragment-whisper { color: #3a1515; }

        .fragment-beauty {
            transform: rotateY(180deg);
            background: linear-gradient(135deg, #E8E4E1, #D4EAE7, #E8D4EA, #EAE4D4, #D4E8EA);
            background-size: 300% 300%;
            animation: opalShimmer 10s ease infinite;
        }

        @keyframes opalShimmer {
            0%, 100% { background-position: 0% 50%; }
            50% { background-position: 100% 100%; }
        }

        .fragment-beauty .fragment-glyph { color: rgba(100, 80, 120, 0.6); }
        .fragment-beauty .fragment-name { color: rgba(100, 80, 120, 0.7); }
        .fragment-beauty .fragment-whisper { color: rgba(100, 80, 120, 0.5); }

        /* Edge hints */
        .edge-hint {
            position: fixed;
            font-family: 'JetBrains Mono', monospace;
            font-size: 0.5rem;
            color: var(--slate);
            opacity: 0;
            letter-spacing: 0.15em;
            text-transform: uppercase;
            pointer-events: none;
            transition: opacity 1.5s ease;
            z-index: 50;
        }

        .edge-hint.visible { opacity: 0.2; }
        .edge-hint.left { left: 1rem; top: 50%; transform: translateY(-50%) rotate(-90deg); }
        .edge-hint.right { right: 1rem; top: 50%; transform: translateY(-50%) rotate(90deg); }
        .edge-hint.bottom { bottom: 1rem; left: 50%; transform: translateX(-50%); }

        /* Keyboard hint */
        .keyboard-hint {
            position: fixed;
            bottom: 2rem;
            left: 2rem;
            font-family: 'JetBrains Mono', monospace;
            font-size: 0.55rem;
            color: var(--slate);
            opacity: 0;
            transition: opacity 1.5s ease;
            z-index: 50;
        }

        .keyboard-hint.visible { opacity: 0.3; }
        .keyboard-hint kbd {
            display: inline-block;
            padding: 0.1rem 0.25rem;
            border: 1px solid var(--slate-light);
            border-radius: 2px;
            margin-right: 0.25rem;
            font-size: 0.5rem;
        }

        /* Reset button */
        .reset-btn {
            position: fixed;
            bottom: 2rem;
            right: 2rem;
            font-family: 'JetBrains Mono', monospace;
            font-size: 0.6rem;
            color: var(--slate);
            background: none;
            border: 1px solid var(--slate-light);
            padding: 0.5rem 0.8rem;
            cursor: pointer;
            opacity: 0;
            transition: opacity 0.4s ease, border-color 0.3s ease;
            z-index: 50;
        }

        .reset-btn:hover { opacity: 0.8; border-color: var(--amber); }
        body:hover .reset-btn { opacity: 0.3; }

        .footer {
            margin-top: 4rem;
            text-align: center;
            opacity: 0;
            animation: fadeIn 2s ease 1.5s forwards;
            z-index: 10;
        }

        .footer-text {
            font-size: 0.75rem;
            font-style: italic;
            color: var(--slate);
            opacity: 0.4;
        }

        @media (max-width: 600px) {
            .title { font-size: 2rem; letter-spacing: 0.2em; }
            .fragments-container { gap: 1rem; }
            .fragment { aspect-ratio: 1.2; }
            .edge-hint, .particle { display: none; }
        }

        /* ═══════════════════════════════════════════════════════════════
           Fake Music Player - Winamp/Windows XP Aero Glass Style
           A player that plays nothing, for sounds that almost exist
        ═══════════════════════════════════════════════════════════════ */

        .fake-player {
            position: fixed;
            bottom: 1.5rem;
            left: 1.5rem;
            width: 280px;
            background: rgba(245, 240, 230, 0.75);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(212, 160, 86, 0.3);
            border-radius: 8px;
            box-shadow:
                0 4px 24px rgba(0, 0, 0, 0.12),
                0 0 0 1px rgba(255, 255, 255, 0.4) inset,
                0 1px 0 rgba(255, 255, 255, 0.6) inset;
            font-family: 'JetBrains Mono', monospace;
            z-index: 500;
            opacity: 0;
            transform: translateY(20px);
            transition: opacity 1.5s ease, transform 1.5s ease;
            overflow: hidden;
        }

        .fake-player.visible {
            opacity: 1;
            transform: translateY(0);
        }

        .fake-player.minimized {
            width: 48px;
            height: 48px;
            border-radius: 50%;
            cursor: pointer;
        }

        .fake-player.minimized .player-body,
        .fake-player.minimized .player-titlebar-title,
        .fake-player.minimized .player-btn-minimize,
        .fake-player.minimized .player-btn-close {
            display: none;
        }

        .fake-player.minimized .player-titlebar {
            height: 100%;
            padding: 0;
            justify-content: center;
            border-radius: 50%;
        }

        .fake-player.minimized .player-titlebar-icon {
            font-size: 1rem;
        }

        .fake-player.fading-out {
            opacity: 0;
            transform: translateY(10px) scale(0.95);
            transition: opacity 2s ease, transform 2s ease;
        }

        /* Title bar */
        .player-titlebar {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0.4rem 0.5rem;
            background: linear-gradient(180deg,
                rgba(212, 160, 86, 0.25) 0%,
                rgba(212, 160, 86, 0.1) 50%,
                rgba(212, 160, 86, 0.15) 100%);
            border-bottom: 1px solid rgba(212, 160, 86, 0.2);
            cursor: default;
            user-select: none;
        }

        .player-titlebar-left {
            display: flex;
            align-items: center;
            gap: 0.4rem;
        }

        .player-titlebar-icon {
            font-size: 0.7rem;
            opacity: 0.7;
        }

        .player-titlebar-title {
            font-size: 0.55rem;
            letter-spacing: 0.08em;
            color: var(--slate-deep);
            text-transform: uppercase;
        }

        .player-titlebar-buttons {
            display: flex;
            gap: 0.3rem;
        }

        .player-btn-minimize,
        .player-btn-close {
            width: 14px;
            height: 14px;
            border-radius: 50%;
            border: 1px solid rgba(0, 0, 0, 0.15);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.5rem;
            line-height: 1;
            transition: transform 0.2s ease, background 0.2s ease;
        }

        .player-btn-minimize {
            background: linear-gradient(135deg, #f5dd9d 0%, #e8c05a 100%);
            color: #8b6914;
        }

        .player-btn-close {
            background: linear-gradient(135deg, #f5a09d 0%, #e85a5a 100%);
            color: #8b1414;
        }

        .player-btn-minimize:hover,
        .player-btn-close:hover {
            transform: scale(1.15);
        }

        /* Player body */
        .player-body {
            padding: 0.6rem;
        }

        /* Track display */
        .player-track-display {
            background: rgba(0, 0, 0, 0.06);
            border: 1px solid rgba(0, 0, 0, 0.08);
            border-radius: 3px;
            padding: 0.4rem 0.5rem;
            margin-bottom: 0.5rem;
            overflow: hidden;
            position: relative;
        }

        .player-track-display::before,
        .player-track-display::after {
            content: '';
            position: absolute;
            top: 0;
            bottom: 0;
            width: 20px;
            z-index: 1;
            pointer-events: none;
        }

        .player-track-display::before {
            left: 0;
            background: linear-gradient(90deg, rgba(245, 240, 230, 0.9) 0%, transparent 100%);
        }

        .player-track-display::after {
            right: 0;
            background: linear-gradient(-90deg, rgba(245, 240, 230, 0.9) 0%, transparent 100%);
        }

        .player-track-name {
            font-size: 0.65rem;
            color: var(--slate-deep);
            white-space: nowrap;
            animation: marquee 12s linear infinite;
        }

        @keyframes marquee {
            0% { transform: translateX(100%); }
            100% { transform: translateX(-100%); }
        }

        .player-track-name.paused {
            animation-play-state: paused;
        }

        /* Time display */
        .player-time {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-bottom: 0.4rem;
        }

        .player-time-current,
        .player-time-total {
            font-size: 0.5rem;
            color: var(--slate);
            letter-spacing: 0.05em;
        }

        /* Progress bar */
        .player-progress {
            height: 4px;
            background: rgba(0, 0, 0, 0.1);
            border-radius: 2px;
            margin-bottom: 0.5rem;
            overflow: hidden;
            position: relative;
        }

        .player-progress-fill {
            height: 100%;
            background: linear-gradient(90deg, var(--amber) 0%, var(--amber-glow) 100%);
            border-radius: 2px;
            width: 0%;
            transition: width 0.5s linear;
        }

        /* Controls */
        .player-controls {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 0.3rem;
            margin-bottom: 0.4rem;
        }

        .player-control-btn {
            width: 24px;
            height: 24px;
            border-radius: 50%;
            background: linear-gradient(135deg, rgba(255, 255, 255, 0.8) 0%, rgba(240, 235, 225, 0.9) 100%);
            border: 1px solid rgba(0, 0, 0, 0.1);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.55rem;
            color: var(--slate-deep);
            transition: transform 0.15s ease, box-shadow 0.15s ease;
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
        }

        .player-control-btn:hover {
            transform: scale(1.1);
            box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
        }

        .player-control-btn:active {
            transform: scale(0.95);
        }

        .player-control-btn.play-pause {
            width: 28px;
            height: 28px;
            font-size: 0.6rem;
        }

        /* Volume */
        .player-volume {
            display: flex;
            align-items: center;
            gap: 0.4rem;
        }

        .player-volume-icon {
            font-size: 0.55rem;
            color: var(--slate);
        }

        .player-volume-slider {
            flex: 1;
            height: 3px;
            background: rgba(0, 0, 0, 0.1);
            border-radius: 2px;
            position: relative;
            cursor: pointer;
        }

        .player-volume-fill {
            height: 100%;
            background: var(--slate);
            border-radius: 2px;
            width: 70%;
            transition: width 0.2s ease;
        }

        .player-volume-knob {
            position: absolute;
            top: 50%;
            left: 70%;
            transform: translate(-50%, -50%);
            width: 8px;
            height: 8px;
            background: linear-gradient(135deg, #fff 0%, #ddd 100%);
            border: 1px solid rgba(0, 0, 0, 0.2);
            border-radius: 50%;
            box-shadow: 0 1px 2px rgba(0, 0, 0, 0.15);
        }

        /* Fake visualizer */
        .player-visualizer {
            display: flex;
            align-items: flex-end;
            justify-content: center;
            gap: 2px;
            height: 16px;
            margin-top: 0.4rem;
            padding-top: 0.3rem;
            border-top: 1px solid rgba(0, 0, 0, 0.05);
        }

        .player-viz-bar {
            width: 3px;
            background: linear-gradient(0deg, var(--amber) 0%, var(--amber-glow) 100%);
            border-radius: 1px;
            animation: vizBounce var(--viz-dur, 0.8s) ease-in-out infinite;
            animation-delay: var(--viz-del, 0s);
            opacity: 0.7;
        }

        @keyframes vizBounce {
            0%, 100% { height: var(--viz-min, 3px); }
            50% { height: var(--viz-max, 12px); }
        }

        .fake-player.minimized .player-visualizer {
            display: none;
        }

        @media (max-width: 600px) {
            .fake-player {
                width: 240px;
                bottom: 1rem;
                left: 1rem;
            }

            .fake-player.minimized {
                width: 40px;
                height: 40px;
            }
        }
    </style>
</head>
<body>
    <div class="grain"></div>
    <div class="mist"></div>

    <!-- CSS-only particles (8 instead of 15) -->
    <div class="particle" style="--x: 10%; --dur: 22s; --del: 0s;"></div>
    <div class="particle" style="--x: 25%; --dur: 28s; --del: 4s;"></div>
    <div class="particle" style="--x: 40%; --dur: 24s; --del: 8s;"></div>
    <div class="particle" style="--x: 55%; --dur: 30s; --del: 2s;"></div>
    <div class="particle" style="--x: 70%; --dur: 26s; --del: 6s;"></div>
    <div class="particle" style="--x: 85%; --dur: 23s; --del: 10s;"></div>
    <div class="particle" style="--x: 33%; --dur: 27s; --del: 12s;"></div>
    <div class="particle" style="--x: 77%; --dur: 25s; --del: 14s;"></div>

    <div class="presence" id="presence1"></div>
    <div class="presence" id="presence2"></div>

    <div class="edge-hint left" id="edgeLeft">stillness reveals</div>
    <div class="edge-hint right" id="edgeRight">patience rewarded</div>
    <div class="edge-hint bottom" id="edgeBottom">the fragments remember</div>

    <div class="stillness-message" id="stillnessMessage"></div>

    <div class="keyboard-hint" id="keyboardHint">
        <kbd>b</kbd> breathe <kbd>d</kbd> deeper <kbd>s</kbd> scatter
    </div>

    <header class="header">
        <h1 class="title">townhaus</h1>
        <p class="subtitle">a place that is here because it is</p>
        <p class="arrangement-hint">drag to rearrange. the order is yours.</p>
    </header>

    <div class="fragments-container" id="fragments"></div>

    <button class="reset-btn" id="resetBtn">scatter</button>

    <footer class="footer">
        <p class="footer-text">enter any door. they all lead somewhere.</p>
    </footer>

    <!-- Fake Music Player - plays nothing, for sounds that almost exist -->
    <div class="fake-player" id="fakePlayer">
        <div class="player-titlebar">
            <div class="player-titlebar-left">
                <span class="player-titlebar-icon">♫</span>
                <span class="player-titlebar-title">nowhere.mp3</span>
            </div>
            <div class="player-titlebar-buttons">
                <button class="player-btn-minimize" id="playerMinimize" title="minimize">−</button>
                <button class="player-btn-close" id="playerClose" title="close">×</button>
            </div>
        </div>
        <div class="player-body">
            <div class="player-track-display">
                <div class="player-track-name" id="playerTrackName">loading silence...</div>
            </div>
            <div class="player-time">
                <span class="player-time-current" id="playerTimeCurrent">0:00</span>
                <span class="player-time-total" id="playerTimeTotal">∞:∞</span>
            </div>
            <div class="player-progress">
                <div class="player-progress-fill" id="playerProgressFill"></div>
            </div>
            <div class="player-controls">
                <button class="player-control-btn" id="playerPrev" title="previous">⏮</button>
                <button class="player-control-btn play-pause" id="playerPlayPause" title="play/pause">▶</button>
                <button class="player-control-btn" id="playerNext" title="next">⏭</button>
            </div>
            <div class="player-volume">
                <span class="player-volume-icon">🔊</span>
                <div class="player-volume-slider">
                    <div class="player-volume-fill"></div>
                    <div class="player-volume-knob"></div>
                </div>
            </div>
            <div class="player-visualizer" id="playerVisualizer"></div>
        </div>
    </div>

    <script>
    (function() {
        'use strict';

        // Data
        const FRAGMENTS = [
            { page: 'silence', glyph: ')', name: 'silence', whisper: 'louder than you think' },
            { page: 'jungle', glyph: '%', name: 'jungle', whisper: 'it grows where it wants' },
            { page: 'orb', glyph: 'o', name: 'orb', whisper: 'cold, then warm' },
            { page: 'shadow', glyph: '/', name: 'shadow', whisper: 'of what?' },
            { page: 'stone', glyph: '#', name: 'stone', whisper: 'it pulses if you wait' },
            { page: 'barn', glyph: '^', name: 'barn', whisper: 'the masks are watching' },
            { page: 'forest', glyph: '|', name: 'forest', whisper: 'rows and rows and rows' },
            { page: 'shop', glyph: '~', name: 'shop', whisper: 'the cat knows you' },
            { page: 'switch', glyph: '!', name: 'switch', whisper: 'try it' },
            { page: 'time', glyph: ':', name: 'time', whisper: 'all of it, at once' },
            { page: 'rhyme', glyph: '&', name: 'rhyme', whisper: 'what stays will stay' },
            { page: 'question', glyph: '?', name: 'question', whisper: 'what use is there' },
            { page: 'mystery', glyph: '_', name: 'mystery', whisper: 'click to reveal' },
            { page: 'is', glyph: '.', name: 'is', whisper: '' },
            { page: 'door', glyph: '[', name: 'door', whisper: 'in and out are the same' },
            { page: 'between', glyph: '-', name: 'between', whisper: 'neither here nor there' },
            { page: 'found', glyph: '*', name: 'found', whisper: 'was it ever lost?' },
            { page: 'evil', glyph: '6', name: 'evil', whisper: 'do not look away', flip: true },
            { page: 'ruby', glyph: '◇', name: 'ruby', whisper: 'scrape to remember' },
            { page: 'words', glyph: '≋', name: 'words', whisper: 'they change when you look' },
            { page: 'saturn', glyph: '♄', name: 'saturn', whisper: 'the rings remember' },
            { page: 'feedback', glyph: '◌', name: 'feedback', whisper: 'how this was made' },
            { page: 'doors', glyph: '⌸', name: 'doors', whisper: 'within within within' },
            { page: 'spectrum', glyph: '◐', name: 'spectrum', whisper: 'colors that become sound' },
            { page: 'states', glyph: '◊', name: 'states', whisper: 'all moods of water' },
            { page: 'mirrors', glyph: '◯', name: 'mirrors', whisper: 'you, but listening' },
            { page: 'clocks', glyph: '◷', name: 'clocks', whisper: 'they have been guessing' },
            { page: 'oma', glyph: '❋', name: 'oma', whisper: 'she knew you were coming' },
            { page: 'office', glyph: '▤', name: 'office', whisper: 'the printer is jammed' },
            { page: 'almost', glyph: '≈', name: 'almost', whisper: 'look away to see' },
            { page: 'gary', glyph: '№', name: 'gary', whisper: 'the numbers know' },
            { page: 'glyph1', glyph: 'A', name: 'glyph', whisper: 'or perhaps B' },
            { page: 'glyph2', glyph: '◯', name: 'glyph', whisper: 'sometimes ◌' },
            { page: 'glyph3', glyph: '3', name: 'glyph', whisper: 'or was it 7' }
        ];

        const STILLNESS_MESSAGES = [
            'something notices your stillness',
            'the fragments settle around you',
            'a presence draws near',
            'the room breathes with you',
            'you have been here before'
        ];

        // DOM cache
        const $ = id => document.getElementById(id);
        const container = $('fragments');
        const resetBtn = $('resetBtn');
        const stillnessMsg = $('stillnessMessage');
        const keyboardHint = $('keyboardHint');
        const presence1 = $('presence1');
        const presence2 = $('presence2');
        const edgeLeft = $('edgeLeft');
        const edgeRight = $('edgeRight');
        const edgeBottom = $('edgeBottom');

        // State
        let lastActivity = Date.now();
        let stillnessLevel = 0;
        let draggedEl = null;
        let rafId = null;

        // Storage
        const STORAGE_KEY = 'townhaus-order';
        const pages = FRAGMENTS.map(f => f.page);

        function getOrder() {
            try {
                const saved = JSON.parse(localStorage.getItem(STORAGE_KEY));
                if (saved && saved.length === pages.length && saved.every(p => pages.includes(p))) {
                    return saved;
                }
            } catch (e) {}
            return pages;
        }

        function saveOrder(order) {
            localStorage.setItem(STORAGE_KEY, JSON.stringify(order));
        }

        // Render
        function render() {
            const order = getOrder();
            const frag = document.createDocumentFragment();

            order.forEach((pageName, i) => {
                const data = FRAGMENTS.find(f => f.page === pageName);
                if (!data) return;

                const delay = `${i * 0.06}s`;

                if (data.flip) {
                    const wrap = document.createElement('div');
                    wrap.className = 'fragment-flip-container';
                    wrap.dataset.page = data.page;
                    wrap.draggable = true;
                    wrap.style.setProperty('--delay', delay);
                    wrap.innerHTML = `
                        <div class="fragment-flip">
                            <div class="fragment-flip-face fragment-evil">
                                <span class="fragment-glyph mono">${data.glyph}</span>
                                <span class="fragment-name">${data.name}</span>
                                <span class="fragment-whisper">${data.whisper}</span>
                            </div>
                            <div class="fragment-flip-face fragment-beauty">
                                <span class="fragment-glyph mono">✧</span>
                                <span class="fragment-name">beauty</span>
                                <span class="fragment-whisper">the question awaits</span>
                            </div>
                        </div>`;
                    frag.appendChild(wrap);
                } else {
                    const el = document.createElement('a');
                    el.href = `pages/${data.page}.html`;
                    el.className = 'fragment';
                    el.dataset.page = data.page;
                    el.draggable = true;
                    el.style.setProperty('--delay', delay);
                    el.innerHTML = `
                        <span class="fragment-glyph mono">${data.glyph}</span>
                        <span class="fragment-name">${data.name}</span>
                        <span class="fragment-whisper">${data.whisper}</span>`;
                    frag.appendChild(el);
                }
            });

            container.innerHTML = '';
            container.appendChild(frag);
        }

        // Drag & Drop - Event delegation
        container.addEventListener('dragstart', e => {
            const el = e.target.closest('.fragment, .fragment-flip-container');
            if (!el) return;
            draggedEl = el;
            el.classList.add('dragging');
            e.dataTransfer.effectAllowed = 'move';
        });

        container.addEventListener('dragend', e => {
            const el = e.target.closest('.fragment, .fragment-flip-container');
            if (!el) return;
            el.classList.remove('dragging');
            container.querySelectorAll('.drag-over').forEach(f => f.classList.remove('drag-over'));

            const newOrder = Array.from(container.children).map(c => c.dataset.page);
            saveOrder(newOrder);
            draggedEl = null;
        });

        container.addEventListener('dragover', e => {
            e.preventDefault();
            const el = e.target.closest('.fragment, .fragment-flip-container');
            if (el && el !== draggedEl) {
                el.classList.add('drag-over');
            }
        });

        container.addEventListener('dragleave', e => {
            const el = e.target.closest('.fragment, .fragment-flip-container');
            if (el) el.classList.remove('drag-over');
        });

        container.addEventListener('drop', e => {
            e.preventDefault();
            const target = e.target.closest('.fragment, .fragment-flip-container');
            if (!target || !draggedEl || target === draggedEl) return;

            target.classList.remove('drag-over');
            const children = Array.from(container.children);
            const dragIdx = children.indexOf(draggedEl);
            const targetIdx = children.indexOf(target);

            if (dragIdx < targetIdx) {
                target.after(draggedEl);
            } else {
                target.before(draggedEl);
            }
        });

        // Flip tile click - delegation
        container.addEventListener('click', e => {
            const evil = e.target.closest('.fragment-evil');
            const beauty = e.target.closest('.fragment-beauty');

            if (evil) {
                e.preventDefault();
                evil.parentElement.classList.add('flipped');
            } else if (beauty) {
                e.preventDefault();
                location.href = 'pages/question.html';
            }
        });

        // Stillness detection - using rAF instead of setInterval
        function checkStillness() {
            const elapsed = Date.now() - lastActivity;

            if (elapsed > 5000 && stillnessLevel < 1) {
                edgeLeft.classList.add('visible');
                edgeRight.classList.add('visible');
                stillnessLevel = 1;
            }
            if (elapsed > 8000 && stillnessLevel < 2) {
                stillnessMsg.textContent = STILLNESS_MESSAGES[Math.floor(Math.random() * STILLNESS_MESSAGES.length)];
                stillnessMsg.classList.add('visible');
                stillnessLevel = 2;
            }
            if (elapsed > 12000 && stillnessLevel < 3) {
                presence1.style.left = `${15 + Math.random() * 25}%`;
                presence1.style.top = `${20 + Math.random() * 50}%`;
                presence1.classList.add('watching');
                stillnessLevel = 3;
            }
            if (elapsed > 18000 && stillnessLevel < 4) {
                presence2.style.right = `${15 + Math.random() * 25}%`;
                presence2.style.top = `${25 + Math.random() * 40}%`;
                presence2.classList.add('watching');
                edgeBottom.classList.add('visible');
                stillnessLevel = 4;
            }

            rafId = requestAnimationFrame(checkStillness);
        }

        function resetStillness() {
            lastActivity = Date.now();
            if (stillnessLevel > 0) {
                stillnessMsg.classList.remove('visible');
                edgeLeft.classList.remove('visible');
                edgeRight.classList.remove('visible');
                edgeBottom.classList.remove('visible');
                presence1.classList.remove('watching');
                presence2.classList.remove('watching');
                stillnessLevel = 0;
            }
        }

        // Throttled mousemove
        let moveThrottle = false;
        document.addEventListener('mousemove', () => {
            if (moveThrottle) return;
            moveThrottle = true;
            resetStillness();
            setTimeout(() => moveThrottle = false, 100);
        }, { passive: true });

        // Keyboard
        document.addEventListener('keydown', e => {
            resetStillness();
            const key = e.key.toLowerCase();
            if (key === 'b') document.body.classList.toggle('breathing');
            if (key === 'd') document.body.classList.toggle('deeper');
            if (key === 's') {
                const shuffled = [...pages].sort(() => Math.random() - 0.5);
                saveOrder(shuffled);
                render();
            }
        });

        // Scatter button
        resetBtn.addEventListener('click', e => {
            e.preventDefault();
            const shuffled = [...pages].sort(() => Math.random() - 0.5);
            saveOrder(shuffled);
            render();
        });

        // Right-click whisper
        document.addEventListener('contextmenu', e => {
            e.preventDefault();
            const phrases = ['you looked', 'still searching?', 'here is here', 'what did you expect?'];
            const whisper = document.createElement('div');
            whisper.style.cssText = `
                position:fixed; left:${e.clientX}px; top:${e.clientY}px;
                font-family:'Cormorant Garamond',serif; font-size:0.8rem;
                font-style:italic; color:var(--amber); opacity:0;
                pointer-events:none; transform:translate(-50%,-50%);
                transition:opacity 0.8s ease, transform 2s ease; z-index:1000;`;
            whisper.textContent = phrases[Math.floor(Math.random() * phrases.length)];
            document.body.appendChild(whisper);

            requestAnimationFrame(() => {
                whisper.style.opacity = '0.6';
                whisper.style.transform = 'translate(-50%, -50%) translateY(-15px)';
            });

            setTimeout(() => {
                whisper.style.opacity = '0';
                setTimeout(() => whisper.remove(), 800);
            }, 1500);
        });

        // Init
        render();
        rafId = requestAnimationFrame(checkStillness);

        // Deferred: keyboard hint
        setTimeout(() => keyboardHint.classList.add('visible'), 12000);

        // Console poetry (deferred)
        setTimeout(() => {
            console.log('%ctownhaus', 'font-size:20px;font-family:Georgia,serif;color:#d4a056;font-weight:100;letter-spacing:0.3em;');
            console.log('%ca forest clearing where fragments gather', 'font-size:10px;color:#4a5568;font-style:italic;');
            console.log('%c[b] breathe  [d] deeper  [s] scatter', 'font-size:9px;font-family:monospace;color:#a0aec0;');
        }, 100);

        // ═══════════════════════════════════════════════════════════════
        // Fake Music Player - plays nothing, for sounds that almost exist
        // ═══════════════════════════════════════════════════════════════
        (function initFakePlayer() {
            const player = $('fakePlayer');
            const trackNameEl = $('playerTrackName');
            const timeCurrentEl = $('playerTimeCurrent');
            const timeTotalEl = $('playerTimeTotal');
            const progressFillEl = $('playerProgressFill');
            const visualizerEl = $('playerVisualizer');
            const minimizeBtn = $('playerMinimize');
            const closeBtn = $('playerClose');
            const playPauseBtn = $('playerPlayPause');
            const prevBtn = $('playerPrev');
            const nextBtn = $('playerNext');

            // Track names that evoke sounds that cannot quite exist
            const TRACKS = [
                'clouds on a sunny day',
                'rain in september',
                'the sound of almost',
                'elevator to nowhere',
                'hold music for the universe',
                'dial-up memories',
                'windows shutting down (10 hour mix)',
                'lo-fi beats to contemplate existence to',
                'your song (you know the one)',
                'silence (explicit version)',
                'the hum of fluorescent lights',
                'waiting room jazz',
                'birdsong.exe',
                'ocean.wav (corrupted)',
                'thursday afternoon forever',
                'static from a radio between stations',
                'the last song before sleep',
                'ambient parking lot at 3am',
                'someone typing in another room',
                'wind through an open window',
                'microwave countdown symphony',
                'untitled (final final v2).mp3',
                'a door closing softly',
                'rain on a window you cannot see',
                'the space between heartbeats',
                'forgotten voicemail (instrumental)',
                'loading... please wait',
                'autumn leaves falling in slow motion',
                'the sound of almost remembering',
                'a phone ringing in an empty house',
                'white noise (artisanal)',
                'midnight refrigerator hum',
                'distant train at 4am',
                'ceiling fan in summer',
                'thoughts before thoughts',
                'the last track on the album'
            ];

            // Track durations (fake, in seconds)
            const TRACK_DURATIONS = [
                '3:42', '4:17', '2:58', '∞:∞', '5:03',
                '0:56', '10:00:00', '∞:∞', '?:??', '0:00',
                '24:00', '3:33', '2:08', 'ERR', '∞:∞'
            ];

            let isPlaying = true;
            let isMinimized = false;
            let isVisible = false;
            let currentTrackIndex = 0;
            let currentTime = 0;
            let trackDuration = 0;
            let trackChangeInterval = null;
            let timeUpdateInterval = null;

            // Format time as m:ss
            function formatTime(seconds) {
                const m = Math.floor(seconds / 60);
                const s = Math.floor(seconds % 60);
                return `${m}:${s.toString().padStart(2, '0')}`;
            }

            // Generate random track duration between 2-5 minutes
            function getRandomDuration() {
                return 120 + Math.random() * 180;
            }

            // Create visualizer bars
            function createVisualizer() {
                visualizerEl.innerHTML = '';
                for (let i = 0; i < 12; i++) {
                    const bar = document.createElement('div');
                    bar.className = 'player-viz-bar';
                    bar.style.setProperty('--viz-dur', `${0.4 + Math.random() * 0.6}s`);
                    bar.style.setProperty('--viz-del', `${Math.random() * 0.5}s`);
                    bar.style.setProperty('--viz-min', `${2 + Math.random() * 3}px`);
                    bar.style.setProperty('--viz-max', `${8 + Math.random() * 8}px`);
                    visualizerEl.appendChild(bar);
                }
            }

            // Change to a new track
            function changeTrack(direction = 1) {
                currentTrackIndex = (currentTrackIndex + direction + TRACKS.length) % TRACKS.length;
                trackNameEl.textContent = TRACKS[currentTrackIndex];
                currentTime = 0;
                trackDuration = getRandomDuration();

                // Random track duration display
                const randomDurationDisplay = TRACK_DURATIONS[Math.floor(Math.random() * TRACK_DURATIONS.length)];
                timeTotalEl.textContent = randomDurationDisplay;
                timeCurrentEl.textContent = '0:00';
                progressFillEl.style.width = '0%';

                // Regenerate visualizer for variety
                createVisualizer();

                // Schedule next track change (15-30 seconds)
                clearInterval(trackChangeInterval);
                const nextChange = 15000 + Math.random() * 15000;
                trackChangeInterval = setTimeout(() => changeTrack(1), nextChange);
            }

            // Update time display
            function updateTime() {
                if (!isPlaying || isMinimized) return;

                currentTime += 0.5;
                timeCurrentEl.textContent = formatTime(currentTime);

                // Update progress bar
                const progress = Math.min((currentTime / trackDuration) * 100, 100);
                progressFillEl.style.width = `${progress}%`;

                // If track "ends", change to next
                if (currentTime >= trackDuration) {
                    changeTrack(1);
                }
            }

            // Show the player
            function showPlayer() {
                if (isVisible) return;
                isVisible = true;
                player.classList.add('visible');
                player.classList.remove('fading-out');

                // Start playing
                currentTrackIndex = Math.floor(Math.random() * TRACKS.length);
                changeTrack(0);
                timeUpdateInterval = setInterval(updateTime, 500);
            }

            // Hide the player
            function hidePlayer(gentle = true) {
                if (!isVisible) return;
                isVisible = false;

                if (gentle) {
                    player.classList.add('fading-out');
                    setTimeout(() => {
                        player.classList.remove('visible', 'fading-out');
                    }, 2000);
                } else {
                    player.classList.remove('visible');
                }

                clearInterval(timeUpdateInterval);
                clearInterval(trackChangeInterval);
            }

            // Toggle minimize
            function toggleMinimize() {
                isMinimized = !isMinimized;
                player.classList.toggle('minimized', isMinimized);

                if (isMinimized) {
                    console.log('%c♫ the music continues, smaller', 'font-size:9px;color:#d4a056;font-style:italic;');
                }
            }

            // Close player
            function closePlayer() {
                console.log('%c♫ the music continues elsewhere', 'font-size:9px;color:#d4a056;font-style:italic;');
                hidePlayer(true);
            }

            // Event listeners
            minimizeBtn.addEventListener('click', e => {
                e.stopPropagation();
                toggleMinimize();
            });

            closeBtn.addEventListener('click', e => {
                e.stopPropagation();
                closePlayer();
            });

            // Click on minimized player to expand
            player.addEventListener('click', e => {
                if (isMinimized && e.target === player || e.target.closest('.player-titlebar') && isMinimized) {
                    toggleMinimize();
                }
            });

            // Play/pause (does nothing meaningful, but satisfying)
            playPauseBtn.addEventListener('click', () => {
                isPlaying = !isPlaying;
                playPauseBtn.textContent = isPlaying ? '▶' : '⏸';
                trackNameEl.classList.toggle('paused', !isPlaying);

                // Pause visualizer
                const bars = visualizerEl.querySelectorAll('.player-viz-bar');
                bars.forEach(bar => {
                    bar.style.animationPlayState = isPlaying ? 'running' : 'paused';
                });

                console.log(`%c♫ ${isPlaying ? 'playing...' : 'paused (the silence deepens)'}`, 'font-size:9px;color:#a0aec0;font-style:italic;');
            });

            // Prev/next buttons
            prevBtn.addEventListener('click', () => {
                changeTrack(-1);
                console.log('%c♫ rewinding through the void', 'font-size:9px;color:#a0aec0;font-style:italic;');
            });

            nextBtn.addEventListener('click', () => {
                changeTrack(1);
                console.log('%c♫ skipping to the next silence', 'font-size:9px;color:#a0aec0;font-style:italic;');
            });

            // Initialize visualizer
            createVisualizer();

            // 50% chance to appear on page load
            if (Math.random() > 0.5) {
                // Appear after a gentle delay
                setTimeout(showPlayer, 3000 + Math.random() * 4000);
            }

            // Stillness behavior: might appear or disappear after 20+ seconds of no movement
            let playerLastActivity = Date.now();

            function checkPlayerStillness() {
                const elapsed = Date.now() - playerLastActivity;

                if (elapsed > 20000) {
                    // 30% chance to toggle visibility
                    if (Math.random() < 0.3) {
                        if (isVisible) {
                            hidePlayer(true);
                            console.log('%c♫ the player fades with the stillness', 'font-size:9px;color:#a0aec0;font-style:italic;');
                        } else {
                            showPlayer();
                            console.log('%c♫ something begins to play', 'font-size:9px;color:#a0aec0;font-style:italic;');
                        }
                        playerLastActivity = Date.now();
                    }
                }
            }

            // Hook into existing mousemove to track activity
            document.addEventListener('mousemove', () => {
                playerLastActivity = Date.now();
            }, { passive: true });

            // Check stillness periodically
            setInterval(checkPlayerStillness, 25000);

        })();
    })();
    </script>
</body>
</html>
