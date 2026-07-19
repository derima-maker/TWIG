
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Twig · AI Chat</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz@14..32&display=swap" rel="stylesheet" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css" />
    <style>
        /* --- Splash Screen --- */
        #splash-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #1a2a6c, #2d4373);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 1000;
            transition: opacity 0.6s ease;
            color: #fff;
        }
        #splash-screen i {
            font-size: 80px;
            animation: spin 1s linear infinite;
        }
        #splash-screen p {
            margin-top: 20px;
            font-size: 20px;
            font-family: 'Inter', sans-serif;
            letter-spacing: 0.5px;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        #splash-screen.hidden {
            opacity: 0;
            pointer-events: none;
        }

        /* --- Chat container (hidden initially) --- */
        .chat-container {
            display: none; /* hidden until splash disappears */
            /* all existing styles remain */
        }

        /* --- All existing styles unchanged from previous version --- */
        *,
        *::before,
        *::after {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
            background: #f6f9fc;
            display: flex;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            transition: background 0.25s;
        }
        .chat-container {
            width: 100%;
            max-width: 480px;
            height: 700px;
            background: #ffffff;
            border-radius: 32px;
            box-shadow: 0 20px 60px rgba(0, 20, 40, 0.12), 0 8px 24px rgba(0, 0, 0, 0.04);
            display: flex;
            flex-direction: column;
            overflow: hidden;
            position: relative;
            transition: background 0.25s, box-shadow 0.25s;
        }
        .chat-header {
            background: linear-gradient(135deg, #1a2a6c, #2d4373);
            padding: 18px 24px;
            display: flex;
            align-items: center;
            gap: 14px;
            flex-shrink: 0;
            cursor: pointer;
            transition: background 0.25s;
            position: relative;
            z-index: 10;
        }
        .chat-header .avatar {
            width: 44px;
            height: 44px;
            background: rgba(255, 255, 255, 0.15);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 22px;
            color: #fff;
            backdrop-filter: blur(4px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: transform 0.15s;
        }
        .chat-header .avatar:hover {
            transform: scale(1.05);
        }
        .chat-header .info {
            flex: 1;
            pointer-events: none;
        }
        .chat-header .info h2 {
            font-size: 17px;
            font-weight: 600;
            color: #fff;
            letter-spacing: -0.2px;
        }
        .chat-header .info .status {
            font-size: 12px;
            color: rgba(255, 255, 255, 0.7);
            display: flex;
            align-items: center;
            gap: 6px;
        }
        .chat-header .info .status .dot {
            width: 7px;
            height: 7px;
            background: #4ade80;
            border-radius: 50%;
            display: inline-block;
            animation: pulse-dot 2s infinite;
        }
        @keyframes pulse-dot {
            0%,
            100% {
                opacity: 1;
                transform: scale(1);
            }
            50% {
                opacity: 0.5;
                transform: scale(0.85);
            }
        }
        .chat-header .actions {
            display: flex;
            gap: 6px;
            pointer-events: auto;
        }
        .chat-header .actions button {
            background: rgba(255, 255, 255, 0.08);
            border: none;
            color: rgba(255, 255, 255, 0.75);
            width: 36px;
            height: 36px;
            border-radius: 50%;
            cursor: pointer;
            font-size: 15px;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .chat-header .actions button:hover {
            background: rgba(255, 255, 255, 0.18);
            color: #fff;
        }
        .chat-messages {
            flex: 1;
            padding: 20px 18px 12px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 12px;
            background: #fafcff;
            scroll-behavior: smooth;
            transition: background 0.25s;
        }
        .chat-messages::-webkit-scrollbar {
            width: 4px;
        }
        .chat-messages::-webkit-scrollbar-track {
            background: transparent;
        }
        .chat-messages::-webkit-scrollbar-thumb {
            background: #d0d9e8;
            border-radius: 8px;
        }

        .message {
            padding: 12px 16px;
            border-radius: 18px;
            font-size: 14.5px;
            line-height: 1.6;
            word-wrap: break-word;
            animation: fade-in 0.25s ease;
            position: relative;
            transition: background 0.25s, color 0.25s, border-color 0.25s;
        }
        @keyframes fade-in {
            from {
                opacity: 0;
                transform: translateY(8px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .message.user {
            align-self: flex-end;
            max-width: 85%;
            background: #1a2a6c;
            color: #fff;
            border-bottom-right-radius: 6px;
            box-shadow: 0 2px 8px rgba(26, 42, 108, 0.15);
        }

        .message.bot {
            align-self: stretch;
            max-width: 100%;
            background: #ffffff;
            color: #1a1f2e;
            border-bottom-left-radius: 6px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.04), 0 1px 3px rgba(0, 0, 0, 0.03);
            border: 1px solid #eef2f7;
            padding: 12px 18px;
        }

        .message .message-time {
            font-size: 10px;
            opacity: 0.7;
            margin-top: 6px;
            display: block;
            text-align: right;
            letter-spacing: 0.2px;
        }
        .message.user .message-time {
            color: rgba(255, 255, 255, 0.6);
        }
        .message .copy-btn {
            position: absolute;
            top: 6px;
            right: 8px;
            background: rgba(0, 0, 0, 0.06);
            border: none;
            color: rgba(0, 0, 0, 0.4);
            width: 24px;
            height: 24px;
            border-radius: 50%;
            font-size: 12px;
            cursor: pointer;
            opacity: 0;
            transition: opacity 0.2s, background 0.2s, color 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .message:hover .copy-btn {
            opacity: 1;
        }
        .message .copy-btn:hover {
            background: rgba(0, 0, 0, 0.12);
            color: #1a2a6c;
        }
        .message.user .copy-btn {
            color: rgba(255, 255, 255, 0.5);
        }
        .message.user .copy-btn:hover {
            background: rgba(255, 255, 255, 0.15);
            color: #fff;
        }

        .code-block {
            background: #1e293b;
            border-radius: 10px;
            padding: 12px 14px;
            margin: 8px 0;
            overflow-x: auto;
            font-family: 'Courier New', monospace;
            font-size: 13px;
            line-height: 1.5;
            color: #e2e8f0;
            position: relative;
            border: 1px solid #2d3a4f;
        }
        .code-block .code-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 6px;
            font-size: 12px;
            color: #94a3b8;
            text-transform: uppercase;
            letter-spacing: 0.3px;
        }
        .code-block .code-header .lang {
            background: #334155;
            padding: 2px 10px;
            border-radius: 12px;
            font-size: 11px;
            font-weight: 600;
            color: #cbd5e1;
            display: flex;
            align-items: center;
            gap: 5px;
        }
        .code-block .code-header .copy-code-btn {
            background: rgba(255, 255, 255, 0.08);
            border: none;
            color: #94a3b8;
            width: 28px;
            height: 28px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 13px;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .code-block .code-header .copy-code-btn:hover {
            background: rgba(255, 255, 255, 0.16);
            color: #f1f5f9;
        }
        .code-block pre {
            margin: 0;
            white-space: pre-wrap;
            word-break: break-word;
        }
        .code-block code {
            font-family: inherit;
            font-size: inherit;
            color: inherit;
            background: transparent;
            padding: 0;
        }

        .math-block {
            background: #1e1b2b;
            border-radius: 10px;
            padding: 12px 14px;
            margin: 8px 0;
            overflow-x: auto;
            font-family: 'Courier New', monospace;
            font-size: 15px;
            line-height: 1.6;
            color: #e2e8f0;
            position: relative;
            border: 1px solid #4a3a6a;
            text-align: center;
        }
        .math-block .code-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 6px;
            font-size: 12px;
            color: #a89bc0;
            text-transform: uppercase;
            letter-spacing: 0.3px;
        }
        .math-block .code-header .lang {
            background: #4a3a6a;
            padding: 2px 10px;
            border-radius: 12px;
            font-size: 11px;
            font-weight: 600;
            color: #c4b5e0;
        }
        .math-block .code-header .copy-code-btn {
            background: rgba(255, 255, 255, 0.08);
            border: none;
            color: #a89bc0;
            width: 28px;
            height: 28px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 13px;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .math-block .code-header .copy-code-btn:hover {
            background: rgba(255, 255, 255, 0.16);
            color: #f1f5f9;
        }
        .math-block pre {
            margin: 0;
            white-space: pre-wrap;
            word-break: break-word;
            font-family: inherit;
        }
        .math-block code {
            font-family: inherit;
            font-size: inherit;
            color: inherit;
            background: transparent;
            padding: 0;
        }

        .typing-indicator {
            align-self: flex-start;
            background: #ffffff;
            padding: 12px 18px;
            border-radius: 18px;
            border-bottom-left-radius: 6px;
            border: 1px solid #eef2f7;
            display: none;
            align-items: center;
            gap: 4px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.03);
            transition: background 0.25s, border-color 0.25s;
        }
        .typing-indicator.show {
            display: flex;
        }
        .typing-indicator span {
            width: 8px;
            height: 8px;
            background: #8e9aaf;
            border-radius: 50%;
            display: inline-block;
            animation: typing-bounce 1.4s infinite ease-in-out both;
        }
        .typing-indicator span:nth-child(1) {
            animation-delay: -0.32s;
        }
        .typing-indicator span:nth-child(2) {
            animation-delay: -0.16s;
        }
        .typing-indicator span:nth-child(3) {
            animation-delay: 0s;
        }
        @keyframes typing-bounce {
            0%,
            80%,
            100% {
                transform: scale(0.6);
                opacity: 0.4;
            }
            40% {
                transform: scale(1);
                opacity: 1;
            }
        }
        .quick-replies {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-top: 4px;
        }
        .quick-replies .qr-btn {
            background: #f0f4fe;
            border: 1px solid #dae3f2;
            color: #1a2a6c;
            font-size: 12.5px;
            font-weight: 500;
            padding: 6px 16px;
            border-radius: 20px;
            cursor: pointer;
            transition: all 0.2s;
            font-family: 'Inter', sans-serif;
            letter-spacing: -0.1px;
        }
        .quick-replies .qr-btn:hover {
            background: #e2eafe;
            border-color: #b0c4e0;
            transform: translateY(-1px);
        }
        .quick-replies .qr-btn:active {
            transform: scale(0.96);
        }
        .chat-input {
            padding: 14px 18px 18px;
            background: #ffffff;
            border-top: 1px solid #edf2f7;
            display: flex;
            gap: 10px;
            align-items: flex-end;
            flex-shrink: 0;
            transition: background 0.25s, border-color 0.25s;
        }
        .chat-input .input-wrapper {
            flex: 1;
            background: #f2f6fb;
            border-radius: 24px;
            padding: 4px 4px 4px 18px;
            display: flex;
            align-items: flex-end;
            gap: 6px;
            border: 1px solid transparent;
            transition: border-color 0.2s, box-shadow 0.2s, background 0.25s;
        }
        .chat-input .input-wrapper:focus-within {
            border-color: #1a2a6c;
            box-shadow: 0 0 0 3px rgba(26, 42, 108, 0.08);
            background: #fff;
        }
        .chat-input textarea {
            flex: 1;
            border: none;
            background: transparent;
            resize: none;
            outline: none;
            font-family: 'Inter', sans-serif;
            font-size: 14px;
            line-height: 1.5;
            padding: 10px 0;
            max-height: 120px;
            min-height: 44px;
            color: #1a1f2e;
            transition: color 0.25s;
        }
        .chat-input textarea::placeholder {
            color: #9aa8bc;
        }
        .chat-input .send-btn {
            width: 44px;
            height: 44px;
            border: none;
            border-radius: 50%;
            background: #1a2a6c;
            color: #fff;
            font-size: 17px;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-shrink: 0;
        }
        .chat-input .send-btn:hover {
            background: #2d4373;
            transform: scale(1.02);
        }
        .chat-input .send-btn:active {
            transform: scale(0.94);
        }
        .chat-input .send-btn:disabled {
            opacity: 0.4;
            cursor: not-allowed;
            transform: none;
        }
        .history-overlay {
            display: none;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.3);
            backdrop-filter: blur(4px);
            z-index: 20;
            border-radius: 32px;
            transition: opacity 0.3s;
        }
        .history-overlay.active {
            display: block;
        }
        .history-panel {
            position: absolute;
            top: 0;
            left: -100%;
            width: 85%;
            max-width: 320px;
            height: 100%;
            background: #ffffff;
            border-radius: 32px 0 0 32px;
            box-shadow: 4px 0 30px rgba(0, 0, 0, 0.15);
            padding: 24px 20px;
            display: flex;
            flex-direction: column;
            transition: left 0.3s ease;
            z-index: 21;
            overflow-y: auto;
        }
        .history-panel.open {
            left: 0;
        }
        .history-panel .panel-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            flex-shrink: 0;
        }
        .history-panel .panel-header h3 {
            font-size: 18px;
            font-weight: 600;
            color: #1a1f2e;
        }
        .history-panel .panel-header .close-panel {
            background: none;
            border: none;
            font-size: 22px;
            color: #6b7280;
            cursor: pointer;
            padding: 4px;
        }
        .history-panel .panel-header .close-panel:hover {
            color: #1a1f2e;
        }
        .history-panel .new-chat-btn {
            width: 100%;
            padding: 12px;
            border-radius: 12px;
            background: #1a2a6c;
            color: white;
            font-weight: 600;
            border: none;
            cursor: pointer;
            font-size: 14px;
            transition: background 0.2s;
            margin-bottom: 20px;
            flex-shrink: 0;
        }
        .history-panel .new-chat-btn:hover {
            background: #2d4373;
        }
        .history-list {
            flex: 1;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 6px;
        }
        .history-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 12px;
            border-radius: 10px;
            background: #f8fafc;
            border: 1px solid transparent;
            cursor: pointer;
            transition: background 0.15s, border-color 0.15s;
        }
        .history-item:hover {
            background: #f1f4f9;
            border-color: #d0d9e8;
        }
        .history-item.active {
            background: #e2eafe;
            border-color: #1a2a6c;
        }
        .history-item .item-info {
            flex: 1;
            overflow: hidden;
        }
        .history-item .item-title {
            font-size: 14px;
            font-weight: 500;
            color: #1a1f2e;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        .history-item .item-date {
            font-size: 11px;
            color: #6b7280;
            margin-top: 2px;
        }
        .history-item .item-delete {
            background: none;
            border: none;
            color: #9ca3af;
            font-size: 16px;
            padding: 4px 8px;
            border-radius: 6px;
            cursor: pointer;
            transition: color 0.15s, background 0.15s;
        }
        .history-item .item-delete:hover {
            color: #ef4444;
            background: rgba(239, 68, 68, 0.08);
        }
        .history-empty {
            text-align: center;
            color: #9ca3af;
            font-size: 14px;
            padding: 40px 0;
        }

        @media (max-width: 540px) {
            .chat-container {
                height: 100vh;
                max-height: 100vh;
                border-radius: 0;
                padding: 0;
                box-shadow: none;
            }
            body {
                padding: 0;
            }
            .chat-header {
                padding: 14px 18px;
                border-radius: 0;
            }
            .chat-messages {
                padding: 16px 14px 10px;
            }
            .chat-input {
                padding: 10px 14px 14px;
            }
            .history-overlay {
                border-radius: 0;
            }
            .history-panel {
                border-radius: 0 0 0 0;
                width: 90%;
            }
        }
        @media (max-width: 380px) {
            .message {
                font-size: 13px;
                padding: 10px 14px;
            }
            .chat-header .info h2 {
                font-size: 15px;
            }
        }

        body.dark {
            background: #0f1219;
        }
        body.dark .chat-container {
            background: #181e2a;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.6);
        }
        body.dark .chat-header {
            background: linear-gradient(135deg, #0d1b3e, #1a2a5c);
        }
        body.dark .chat-messages {
            background: #121722;
        }
        body.dark .message.bot {
            background: #232b3b;
            color: #e8edf5;
            border-color: #2f3a4e;
        }
        body.dark .message.user {
            background: #1a2a6c;
        }
        body.dark .code-block {
            background: #0f172a;
            border-color: #2d3a4f;
        }
        body.dark .code-block .code-header .lang {
            background: #1e293b;
        }
        body.dark .math-block {
            background: #151022;
            border-color: #4a3a6a;
        }
        body.dark .math-block .code-header .lang {
            background: #3a2a5a;
        }
        body.dark .chat-input {
            background: #181e2a;
            border-top-color: #2f3a4e;
        }
        body.dark .chat-input .input-wrapper {
            background: #232b3b;
            border-color: #2f3a4e;
        }
        body.dark .chat-input .input-wrapper:focus-within {
            background: #2f3a4e;
            border-color: #3b82f6;
        }
        body.dark .chat-input textarea {
            color: #e8edf5;
        }
        body.dark .quick-replies .qr-btn {
            background: #232b3b;
            border-color: #3a465e;
            color: #b8c8e8;
        }
        body.dark .typing-indicator {
            background: #232b3b;
            border-color: #2f3a4e;
        }
        body.dark .typing-indicator span {
            background: #8e9aaf;
        }
        body.dark .history-panel {
            background: #1e293b;
        }
        body.dark .history-panel .panel-header h3 {
            color: #f1f5f9;
        }
        body.dark .history-panel .panel-header .close-panel {
            color: #94a3b8;
        }
        body.dark .history-item {
            background: #2d3748;
            border-color: #3a465e;
        }
        body.dark .history-item:hover {
            background: #3a465e;
        }
        body.dark .history-item.active {
            background: #1a2a6c;
            border-color: #3b82f6;
        }
        body.dark .history-item .item-title {
            color: #f1f5f9;
        }
        body.dark .history-item .item-date {
            color: #94a3b8;
        }
        body.dark .history-item .item-delete {
            color: #94a3b8;
        }
        body.dark .history-item .item-delete:hover {
            color: #f87171;
            background: rgba(248, 113, 113, 0.15);
        }
        body.dark .history-empty {
            color: #94a3b8;
        }
        body.dark .chat-messages::-webkit-scrollbar-thumb {
            background: #334155;
        }
    </style>
</head>
<body>
    <!-- Splash Screen -->
    <div id="splash-screen">
        <i class="fas fa-hourglass"></i>
        <p>Loading Twig…</p>
    </div>

    <!-- Chat Container (hidden initially) -->
    <div class="chat-container" id="chatContainer">
        <!-- Header -->
        <div class="chat-header" id="headerAvatar">
            <div class="avatar" id="avatarBtn">
                <i class="fas fa-hourglass"></i>
            </div>
            <div class="info">
                <h2 id="headerTitle">Twig</h2>
                <div class="status">
                    <span class="dot"></span>
                    <span>Online</span>
                </div>
            </div>
            <div class="actions">
                <button id="clearBtn" title="Clear current chat"><i class="fas fa-eraser"></i></button>
                <button id="themeBtn" title="Toggle theme"><i class="fas fa-moon"></i></button>
            </div>
        </div>

        <!-- Messages -->
        <div class="chat-messages" id="chatMessages">
            <div class="typing-indicator" id="typingIndicator">
                <span></span><span></span><span></span>
            </div>
            <div class="quick-replies" id="quickReplies">
                <button class="qr-btn" data-text="Hello">👋 Hello</button>
                <button class="qr-btn" data-text="What can you do?">⚡ What can you do?</button>
                <button class="qr-btn" data-text="Tell me a fun fact">🧠 Fun fact</button>
                <button class="qr-btn" data-text="Help me with code">💻 Code help</button>
                <button class="qr-btn" data-text="Solve x^2 = 4 for me">📐 Math help</button>
                <button class="qr-btn" data-text="Write a simple HTML page with a button">🌐 HTML+CSS</button>
            </div>
        </div>

        <!-- Input -->
        <div class="chat-input">
            <div class="input-wrapper">
                <textarea id="messageInput" rows="1" placeholder="Type a message…" aria-label="Type a message"></textarea>
                <button class="send-btn" id="sendBtn" aria-label="Send message"><i class="fas fa-paper-plane"></i></button>
            </div>
        </div>

        <!-- History -->
        <div class="history-overlay" id="historyOverlay"></div>
        <div class="history-panel" id="historyPanel">
            <div class="panel-header">
                <h3>📂 Chat History</h3>
                <button class="close-panel" id="closePanelBtn"><i class="fas fa-times"></i></button>
            </div>
            <button class="new-chat-btn" id="newChatBtn">＋ New Chat</button>
            <div class="history-list" id="historyList"></div>
        </div>
    </div>

    <script>
        // ─── SPLASH SCREEN LOGIC ───
        document.addEventListener('DOMContentLoaded', function() {
            const splash = document.getElementById('splash-screen');
            const chatContainer = document.getElementById('chatContainer');

            // After 2.5 seconds, fade out splash and show chat
            setTimeout(() => {
                splash.classList.add('hidden');
                // After fade transition (0.6s), hide splash and show chat
                setTimeout(() => {
                    splash.style.display = 'none';
                    chatContainer.style.display = 'flex'; // restore flex
                }, 600);
            }, 2500);
        });

        // ─── EXISTING CHAT LOGIC (unchanged) ───
        const CONFIG = {
            WELCOME_MESSAGE: '👋 Hello! I\'m Twig, your free AI assistant. How can I help you today?',
            SYSTEM_PROMPT: `You are Twig, a helpful, friendly AI assistant. Keep responses concise, clear, and warm.

            CRITICAL INSTRUCTION FOR CODE:
            When you write code in JavaScript, CSS, or HTML, you MUST wrap it in triple backticks with the language name.
            Example: \`\`\`js ... \`\`\`, \`\`\`css ... \`\`\`, \`\`\`html ... \`\`\`.
            For other languages (Python, Java, etc.) also use the same format.
            For mathematical formulas, use \`\`\`math ... \`\`\`.

            If you don't know something, say so honestly.`,
            POLLINATIONS_URLS: [
                'https://text.pollinations.ai/',
                'https://text.pollinations.ai/'
            ],
            MAX_CONTEXT_LENGTH: 3000,
            STORAGE_KEY: 'pollinations_chat_history',
        };

        let threads = [];
        let activeThreadId = null;
        let isProcessing = false;
        let isDark = false;

        const messagesEl = document.getElementById('chatMessages');
        const inputEl = document.getElementById('messageInput');
        const sendBtn = document.getElementById('sendBtn');
        const typingEl = document.getElementById('typingIndicator');
        const quickRepliesEl = document.getElementById('quickReplies');
        const clearBtn = document.getElementById('clearBtn');
        const themeBtn = document.getElementById('themeBtn');
        const avatarBtn = document.getElementById('avatarBtn');
        const historyOverlay = document.getElementById('historyOverlay');
        const historyPanel = document.getElementById('historyPanel');
        const closePanelBtn = document.getElementById('closePanelBtn');
        const newChatBtn = document.getElementById('newChatBtn');
        const historyList = document.getElementById('historyList');
        const quickReplyBtns = document.querySelectorAll('.qr-btn');

        function getTime() {
            return new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
        }

        function scrollToBottom() {
            messagesEl.scrollTop = messagesEl.scrollHeight;
        }

        function setProcessing(processing) {
            isProcessing = processing;
            sendBtn.disabled = processing;
            if (processing) typingEl.classList.add('show');
            else typingEl.classList.remove('show');
        }

        function ensureCodeFences(text) {
            if (/```/.test(text)) return text;
            let wrapped = text;
            const htmlMatch = wrapped.match(/(<!DOCTYPE\s+html|<html|<head|<body|<div|<span|<p|<a|<img|<button|<style|<script)/i);
            if (htmlMatch) {
                const endTag = wrapped.match(/<\/html>/i);
                const endIdx = endTag ? endTag.index + endTag[0].length : wrapped.length;
                const code = wrapped.substring(htmlMatch.index, endIdx);
                const before = wrapped.substring(0, htmlMatch.index);
                const after = wrapped.substring(endIdx);
                wrapped = before + '\n```html\n' + code.trim() + '\n```\n' + after;
                return wrapped;
            }
            const cssMatch = wrapped.match(/^(\s*)([.#]?[a-zA-Z][a-zA-Z0-9\-_]*\s*\{)/m);
            if (cssMatch) {
                let open = 0;
                let endIdx = cssMatch.index;
                for (let i = cssMatch.index; i < wrapped.length; i++) {
                    if (wrapped[i] === '{') open++;
                    if (wrapped[i] === '}') open--;
                    if (open === 0 && i > cssMatch.index) { endIdx = i + 1; break; }
                }
                if (endIdx > cssMatch.index) {
                    const code = wrapped.substring(cssMatch.index, endIdx);
                    const before = wrapped.substring(0, cssMatch.index);
                    const after = wrapped.substring(endIdx);
                    wrapped = before + '\n```css\n' + code.trim() + '\n```\n' + after;
                    return wrapped;
                }
            }
            const jsMatch = wrapped.match(/^\s*(function|const|let|var|class|import|export|console\.log|document\.|window\.)/m);
            if (jsMatch) {
                let endIdx = wrapped.indexOf('\n\n', jsMatch.index);
                if (endIdx === -1) endIdx = wrapped.length;
                const code = wrapped.substring(jsMatch.index, endIdx);
                const before = wrapped.substring(0, jsMatch.index);
                const after = wrapped.substring(endIdx);
                wrapped = before + '\n```js\n' + code.trim() + '\n```\n' + after;
                return wrapped;
            }
            return wrapped;
        }

        function parseMessage(content) {
            content = ensureCodeFences(content);
            const codeBlockRegex = /```(\w+)?\n([\s\S]*?)```/g;
            const parts = [];
            let lastIndex = 0;
            let match;
            while ((match = codeBlockRegex.exec(content)) !== null) {
                if (match.index > lastIndex) {
                    const text = content.substring(lastIndex, match.index).trim();
                    if (text) parts.push({ type: 'text', content: text });
                }
                const language = (match[1] || 'text').toLowerCase();
                const code = match[2].trim();
                const isMath = (language === 'math' || language === 'latex');
                parts.push({
                    type: isMath ? 'math' : 'code',
                    language: language,
                    code: code,
                });
                lastIndex = match.index + match[0].length;
            }
            if (lastIndex < content.length) {
                const remaining = content.substring(lastIndex).trim();
                if (remaining) parts.push({ type: 'text', content: remaining });
            }
            if (parts.length === 0) {
                parts.push({ type: 'text', content: content.trim() });
            }
            return parts;
        }

        const langIcons = {
            'js': '🟡',
            'javascript': '🟡',
            'html': '🌐',
            'css': '🎨',
            'python': '🐍',
            'java': '☕',
            'c': '⚙️',
            'cpp': '⚙️',
            'c++': '⚙️',
            'csharp': '🎯',
            'go': '🐹',
            'rust': '🦀',
            'sql': '🗄️',
            'ruby': '💎',
            'php': '🐘',
            'swift': '🕊️',
            'kotlin': '📱',
            'typescript': '🔷',
            'ts': '🔷',
            'json': '📦',
            'xml': '📋',
            'yaml': '📄',
            'markdown': '📝',
            'md': '📝',
            'text': '📄',
        };

        function getLangIcon(lang) {
            return langIcons[lang] || '💻';
        }

        function createMessageElement(role, content) {
            const div = document.createElement('div');
            div.className = `message ${role === 'user' ? 'user' : 'bot'}`;
            const parts = parseMessage(content);
            const contentContainer = document.createElement('div');
            contentContainer.style.width = '100%';
            for (const part of parts) {
                if (part.type === 'text') {
                    if (part.content) {
                        const p = document.createElement('p');
                        p.textContent = part.content;
                        p.style.margin = '4px 0';
                        contentContainer.appendChild(p);
                    }
                } else if (part.type === 'math') {
                    const block = document.createElement('div');
                    block.className = 'math-block';
                    const header = document.createElement('div');
                    header.className = 'code-header';
                    const langSpan = document.createElement('span');
                    langSpan.className = 'lang';
                    langSpan.textContent = '📐 Math';
                    header.appendChild(langSpan);
                    const copyBtn = document.createElement('button');
                    copyBtn.className = 'copy-code-btn';
                    copyBtn.innerHTML = '<i class="fas fa-copy"></i>';
                    copyBtn.title = 'Copy equation';
                    copyBtn.addEventListener('click', (e) => {
                        e.stopPropagation();
                        navigator.clipboard.writeText(part.code).then(() => {
                            const icon = copyBtn.querySelector('i');
                            icon.className = 'fas fa-check';
                            setTimeout(() => { icon.className = 'fas fa-copy'; }, 1500);
                        }).catch(() => alert('Failed to copy equation.'));
                    });
                    header.appendChild(copyBtn);
                    block.appendChild(header);
                    const pre = document.createElement('pre');
                    const code = document.createElement('code');
                    code.textContent = part.code;
                    pre.appendChild(code);
                    block.appendChild(pre);
                    contentContainer.appendChild(block);
                } else {
                    const block = document.createElement('div');
                    block.className = 'code-block';
                    const header = document.createElement('div');
                    header.className = 'code-header';
                    const langSpan = document.createElement('span');
                    langSpan.className = 'lang';
                    const icon = getLangIcon(part.language);
                    const displayName = part.language || 'code';
                    langSpan.textContent = `${icon} ${displayName}`;
                    header.appendChild(langSpan);
                    const copyBtn = document.createElement('button');
                    copyBtn.className = 'copy-code-btn';
                    copyBtn.innerHTML = '<i class="fas fa-copy"></i>';
                    copyBtn.title = 'Copy code';
                    copyBtn.addEventListener('click', (e) => {
                        e.stopPropagation();
                        navigator.clipboard.writeText(part.code).then(() => {
                            const iconEl = copyBtn.querySelector('i');
                            iconEl.className = 'fas fa-check';
                            setTimeout(() => { iconEl.className = 'fas fa-copy'; }, 1500);
                        }).catch(() => alert('Failed to copy code.'));
                    });
                    header.appendChild(copyBtn);
                    block.appendChild(header);
                    const pre = document.createElement('pre');
                    const code = document.createElement('code');
                    code.textContent = part.code;
                    pre.appendChild(code);
                    block.appendChild(pre);
                    contentContainer.appendChild(block);
                }
            }
            div.appendChild(contentContainer);
            const copyBtn = document.createElement('button');
            copyBtn.className = 'copy-btn';
            copyBtn.innerHTML = '<i class="fas fa-copy"></i>';
            copyBtn.title = 'Copy full message';
            copyBtn.addEventListener('click', (e) => {
                e.stopPropagation();
                navigator.clipboard.writeText(content).then(() => {
                    const icon = copyBtn.querySelector('i');
                    icon.className = 'fas fa-check';
                    setTimeout(() => { icon.className = 'fas fa-copy'; }, 1500);
                }).catch(() => alert('Failed to copy.'));
            });
            div.appendChild(copyBtn);
            const time = document.createElement('span');
            time.className = 'message-time';
            time.textContent = getTime();
            div.appendChild(time);
            return div;
        }

        // Threads management (unchanged)
        function loadThreads() {
            try {
                const raw = localStorage.getItem(CONFIG.STORAGE_KEY);
                if (raw) {
                    threads = JSON.parse(raw);
                    if (!Array.isArray(threads) || threads.length === 0) throw new Error('empty');
                } else {
                    throw new Error('none');
                }
            } catch {
                const id = Date.now().toString(36) + Math.random().toString(36).substr(2, 4);
                threads = [{
                    id,
                    title: 'New Chat',
                    messages: [],
                    createdAt: Date.now(),
                }];
            }
            threads = threads.map(t => ({ ...t, messages: t.messages || [] }));
        }

        function saveThreads() {
            localStorage.setItem(CONFIG.STORAGE_KEY, JSON.stringify(threads));
        }

        function getActiveThread() {
            return threads.find(t => t.id === activeThreadId) || threads[0];
        }

        function setActiveThread(id) {
            activeThreadId = id;
            renderHistoryList();
        }

        function getThreadMessages() {
            const thread = getActiveThread();
            return thread ? thread.messages : [];
        }

        function addMessageToThread(role, content) {
            const thread = getActiveThread();
            if (thread) {
                thread.messages.push({ role, content });
                if (role === 'user' && thread.messages.length === 1) {
                    thread.title = content.length > 30 ? content.substring(0, 30) + '…' : content;
                }
                saveThreads();
                renderHistoryList();
            }
        }

        function clearCurrentThread() {
            const thread = getActiveThread();
            if (thread) {
                if (thread.messages.length === 0) return;
                if (confirm('Clear all messages in this chat?')) {
                    thread.messages = [];
                    thread.title = 'New Chat';
                    saveThreads();
                    renderCurrentChat();
                    renderHistoryList();
                }
            }
        }

        function deleteThread(id) {
            if (threads.length <= 1) {
                alert('You must keep at least one chat. Use "Clear" to empty it.');
                return;
            }
            if (!confirm('Delete this chat permanently?')) return;
            threads = threads.filter(t => t.id !== id);
            if (activeThreadId === id) {
                activeThreadId = threads[0].id;
            }
            saveThreads();
            renderCurrentChat();
            renderHistoryList();
            closeHistoryPanel();
        }

        function createNewThread() {
            const id = Date.now().toString(36) + Math.random().toString(36).substr(2, 4);
            const newThread = {
                id,
                title: 'New Chat',
                messages: [],
                createdAt: Date.now(),
            };
            threads.push(newThread);
            activeThreadId = id;
            saveThreads();
            renderCurrentChat();
            renderHistoryList();
            closeHistoryPanel();
            inputEl.focus();
        }

        function switchToThread(id) {
            if (id === activeThreadId) {
                closeHistoryPanel();
                return;
            }
            activeThreadId = id;
            renderCurrentChat();
            renderHistoryList();
            closeHistoryPanel();
            inputEl.focus();
        }

        function renderCurrentChat() {
            while (messagesEl.firstChild) {
                messagesEl.removeChild(messagesEl.firstChild);
            }
            const messages = getThreadMessages();
            for (const msg of messages) {
                const el = createMessageElement(msg.role, msg.content);
                messagesEl.appendChild(el);
            }
            messagesEl.appendChild(typingEl);
            messagesEl.appendChild(quickRepliesEl);
            if (messages.length === 0) {
                const welcome = createMessageElement('assistant', CONFIG.WELCOME_MESSAGE);
                messagesEl.insertBefore(welcome, typingEl);
            }
            scrollToBottom();
        }

        function renderHistoryList() {
            historyList.innerHTML = '';
            if (threads.length === 0) {
                historyList.innerHTML = '<div class="history-empty">No chats yet</div>';
                return;
            }
            const sorted = [...threads].sort((a, b) => b.createdAt - a.createdAt);
            for (const t of sorted) {
                const item = document.createElement('div');
                item.className = 'history-item' + (t.id === activeThreadId ? ' active' : '');
                const info = document.createElement('div');
                info.className = 'item-info';
                const title = document.createElement('div');
                title.className = 'item-title';
                title.textContent = t.title || 'New Chat';
                const date = document.createElement('div');
                date.className = 'item-date';
                date.textContent = new Date(t.createdAt).toLocaleDateString('en-US', {
                    month: 'short',
                    day: 'numeric',
                    hour: '2-digit',
                    minute: '2-digit',
                });
                info.appendChild(title);
                info.appendChild(date);
                item.appendChild(info);

                const del = document.createElement('button');
                del.className = 'item-delete';
                del.innerHTML = '<i class="fas fa-trash-alt"></i>';
                del.addEventListener('click', (e) => {
                    e.stopPropagation();
                    deleteThread(t.id);
                });
                item.appendChild(del);

                item.addEventListener('click', () => {
                    switchToThread(t.id);
                });

                historyList.appendChild(item);
            }
        }

        function openHistoryPanel() {
            historyOverlay.classList.add('active');
            historyPanel.classList.add('open');
            renderHistoryList();
        }

        function closeHistoryPanel() {
            historyOverlay.classList.remove('active');
            historyPanel.classList.remove('open');
        }

        function buildPrompt() {
            const messages = getThreadMessages();
            let conversation = CONFIG.SYSTEM_PROMPT + '\n\n';
            for (const msg of messages) {
                const prefix = msg.role === 'user' ? 'User: ' : 'Twig: ';
                conversation += prefix + msg.content + '\n';
            }
            conversation += 'Twig: ';
            if (conversation.length > CONFIG.MAX_CONTEXT_LENGTH) {
                conversation = conversation.slice(-CONFIG.MAX_CONTEXT_LENGTH);
                const lastPeriod = conversation.lastIndexOf('.');
                if (lastPeriod > 0) conversation = conversation.substring(0, lastPeriod + 1);
            }
            return conversation;
        }

        async function getAIResponse(userMessage) {
            const prompt = buildPrompt();
            const encodedPrompt = encodeURIComponent(prompt);
            const endpoints = [
                `${CONFIG.POLLINATIONS_URLS[0]}${encodedPrompt}`,
                `${CONFIG.POLLINATIONS_URLS[1]}${encodedPrompt}?cb=${Date.now()}`
            ];
            let lastError = null;
            for (const url of endpoints) {
                try {
                    const controller = new AbortController();
                    const timeoutId = setTimeout(() => controller.abort(), 10000);
                    const response = await fetch(url, {
                        signal: controller.signal,
                        headers: { 'Accept': 'text/plain' }
                    });
                    clearTimeout(timeoutId);
                    if (!response.ok) {
                        throw new Error(`HTTP ${response.status}`);
                    }
                    let text = await response.text();
                    if (text.includes('<html')) {
                        const match = text.match(/<body[^>]*>(.*?)<\/body>/is);
                        if (match) text = match[1];
                        else text = text.replace(/<[^>]+>/g, ' ');
                    }
                    text = text.trim();
                    if (!text) throw new Error('Empty response');
                    return text;
                } catch (err) {
                    console.warn(`Pollinations attempt failed:`, err.message);
                    lastError = err;
                }
            }
            throw new Error(`Pollinations is not responding. Please try again later. (${lastError?.message || 'Unknown error'})`);
        }

        async function handleSend() {
            const text = inputEl.value.trim();
            if (!text || isProcessing) return;
            addMessageToThread('user', text);
            renderCurrentChat();
            inputEl.value = '';
            inputEl.style.height = 'auto';
            setProcessing(true);
            try {
                const reply = await getAIResponse(text);
                addMessageToThread('assistant', reply);
                renderCurrentChat();
            } catch (err) {
                addMessageToThread('assistant', `⚠️ Oops! ${err.message || 'Unknown error'}. Please try again.`);
                renderCurrentChat();
            } finally {
                setProcessing(false);
            }
        }

        function setTheme(theme) {
            isDark = theme === 'dark';
            document.body.classList.toggle('dark', isDark);
            themeBtn.innerHTML = isDark ? '<i class="fas fa-sun"></i>' : '<i class="fas fa-moon"></i>';
            localStorage.setItem('pollinations_theme', theme);
        }

        function toggleTheme() {
            setTheme(isDark ? 'light' : 'dark');
        }

        const savedTheme = localStorage.getItem('pollinations_theme') || 'light';
        setTheme(savedTheme);

        // Events
        sendBtn.addEventListener('click', handleSend);
        inputEl.addEventListener('keydown', (e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                handleSend();
            }
        });
        inputEl.addEventListener('input', () => {
            inputEl.style.height = 'auto';
            inputEl.style.height = Math.min(inputEl.scrollHeight, 120) + 'px';
        });
        quickReplyBtns.forEach((btn) => {
            btn.addEventListener('click', () => {
                const text = btn.getAttribute('data-text');
                if (text) {
                    inputEl.value = text;
                    handleSend();
                }
            });
        });
        clearBtn.addEventListener('click', clearCurrentThread);
        themeBtn.addEventListener('click', toggleTheme);
        avatarBtn.addEventListener('click', (e) => {
            e.stopPropagation();
            if (historyPanel.classList.contains('open')) {
                closeHistoryPanel();
            } else {
                openHistoryPanel();
            }
        });
        closePanelBtn.addEventListener('click', closeHistoryPanel);
        historyOverlay.addEventListener('click', closeHistoryPanel);
        newChatBtn.addEventListener('click', createNewThread);

        // Init
        function init() {
            loadThreads();
            if (!activeThreadId) {
                activeThreadId = threads[0]?.id || null;
            }
            if (!activeThreadId && threads.length > 0) {
                activeThreadId = threads[0].id;
            }
            if (!activeThreadId) {
                createNewThread();
            }
            renderCurrentChat();
            renderHistoryList();
            inputEl.focus();
            console.log('✅ Twig is ready – with splash screen!');
        }

        init();
    </script>
</body>
</html>
