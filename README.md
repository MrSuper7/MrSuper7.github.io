<div id="fncrimson-widget-host"></div>
<script type="importmap">
{
  "imports": {
    "@google/genai": "https://esm.sh/@google/genai@^1.39.0",
    "react-markdown": "https://esm.sh/react-markdown@^10.1.0",
    "react-dom/client": "https://esm.sh/react-dom@^19.2.4/client",
    "react": "https://esm.sh/react@^19.2.4"
  }
}
</script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
<script>
    window.process = {
        env: {
            API_KEY: "AIzaSyApbTeQgRGUBAavEb9KAW9_ToXZjAQ_yBw"
        }
    };
</script>
<script type="text/babel" data-type="module" data-presets="react,typescript">
    import React, { useState, useRef, useEffect } from 'react';
    import ReactDOM from 'react-dom/client';
    import { GoogleGenAI } from "@google/genai";
    import ReactMarkdown from 'react-markdown';
    const LOGO_URL = "https://imgproxy.fourthwall.com/-SmtRY_NXiIAo9lKCguTZPjdkqcjPkbS5dFFnp5fy6I/w:150/dpr:2/sm:1/enc/uyZcIZ3KOhUuaqP2/lL7ZqRMaEkHsclZy/ecN7tOjPBxLox11Z/SEEMgLb9cifqUjju/crYbng-yhaSGpklB/c4XlywYnr2AvSnO5/fVxHLLBMz-qCHGpi/oEge9t1U6AkWpx65/1bdlCIlt_zoa-ikI/sR5XIVl_TdQHUKGa/m1202r6_VKbUYsmR/8nlYbnVJ9cTzIL0G/FqNtwauVAq9Do6yV/gf7gwHJwX4_KuLaq/L1UhXAxyaPG24YXq/Na-hZBv6m1NnL_fB";
    const widgetStyles = `
        :host {
            all: initial;
            font-family: '-apple-system', 'BlinkMacSystemFont', 'Segoe UI', sans-serif;
            position: fixed;
            bottom: 24px;
            right: 24px;
            z-index: 2147483647;
            display: flex;
            flex-direction: column;
            align-items: flex-end;
            gap: 12px;
            pointer-events: none;
            line-height: 1.5;
            direction: ltr;
        }
        *, *::before, *::after {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { 
            background: rgba(255, 255, 255, 0.2); 
            border-radius: 3px; 
        }
        ::-webkit-scrollbar-thumb:hover { 
            background: rgba(255, 255, 255, 0.3); 
        }
        .widget-window {
            pointer-events: auto;
            width: 90vw;
            max-width: 420px;
            height: 540px;
            max-height: 80vh;
            background: linear-gradient(135deg, #0f172a 0%, #1a1f3a 100%);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 20px;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.3), 
                        0 0 40px rgba(59, 130, 246, 0.1);
            display: flex;
            flex-direction: column;
            overflow: hidden;
            animation: slideIn 0.35s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
            font-size: 14px;
            color: #e2e8f0;
            backdrop-filter: blur(20px);
        }
        .header {
            flex: none;
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 14px 18px;
            background: linear-gradient(90deg, rgba(30, 41, 59, 0.8), rgba(15, 23, 42, 0.8));
            border-bottom: 1px solid rgba(255, 255, 255, 0.06);
            backdrop-filter: blur(10px);
        }
        .header-branding {
            display: flex;
            align-items: center;
            gap: 12px;
        }
        .header-logo {
            width: 28px;
            height: 28px;
            border-radius: 10px;
            overflow: hidden;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 4px 12px rgba(59, 130, 246, 0.15);
        }
        .header-logo img { 
            width: 100%; 
            height: 100%; 
            object-fit: cover; 
        }
        .header-title h1 {
            font-size: 15px;
            font-weight: 700;
            color: white;
            letter-spacing: -0.5px;
            background: linear-gradient(135deg, #fff 0%, #cbd5e1 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
        .header-title span {
            display: block;
            font-size: 11px;
            color: #94a3b8;
            line-height: 1;
            margin-top: 2px;
            font-weight: 500;
            letter-spacing: 0.5px;
        }
        .header-actions {
            display: flex;
            align-items: center;
            gap: 6px;
        }
        .icon-btn {
            background: transparent;
            border: none;
            color: #94a3b8;
            padding: 8px;
            border-radius: 8px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .icon-btn:hover { 
            background-color: rgba(255, 255, 255, 0.08); 
            color: #e2e8f0;
            transform: scale(1.08);
        }
        .icon-btn.danger:hover { 
            color: #ff6b6b; 
            background-color: rgba(255, 107, 107, 0.1);
        }
        .chat-scroll-area {
            flex: 1;
            overflow-y: auto;
            padding: 18px;
            background: transparent;
            scroll-behavior: smooth;
        }
        .empty-state {
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #475569;
        }
        .message-row {
            display: flex;
            width: 100%;
            margin-bottom: 14px;
            animation: fadeInUp 0.4s ease-out forwards;
            opacity: 0;
        }
        .message-row:nth-child(1) { animation-delay: 0s; }
        .message-row:nth-child(2) { animation-delay: 0.1s; }
        .message-row:nth-child(3) { animation-delay: 0.2s; }
        .message-row:nth-child(n+4) { opacity: 1; }
        .message-row.user { justify-content: flex-end; }
        .message-row.model { justify-content: flex-start; }
        .message-container {
            max-width: 85%;
            display: flex;
            gap: 10px;
            align-items: flex-start;
        }
        .message-row.user .message-container { flex-direction: row-reverse; }
        .avatar {
            flex-shrink: 0;
            width: 28px;
            height: 28px;
            border-radius: 10px;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-top: 4px;
            border: 1px solid rgba(255, 255, 255, 0.08);
        }
        .avatar.user { 
            background: linear-gradient(135deg, #3b82f6, #2563eb);
            color: white;
            box-shadow: 0 4px 12px rgba(59, 130, 246, 0.25);
        }
        .avatar.model { 
            background: rgba(255, 255, 255, 0.05);
        }
        .avatar img { 
            width: 100%; 
            height: 100%; 
            object-fit: cover; 
        }
        .bubble {
            padding: 11px 14px;
            border-radius: 14px;
            font-size: 14px;
            line-height: 1.5;
            position: relative;
            word-wrap: break-word;
            animation: scaleIn 0.3s ease-out;
        }
        .bubble.user {
            background: linear-gradient(135deg, #3b82f6, #2563eb);
            color: white;
            border: 1px solid rgba(59, 130, 246, 0.4);
            box-shadow: 0 4px 12px rgba(59, 130, 246, 0.2);
        }
        .bubble.model {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
            color: #e2e8f0;
            backdrop-filter: blur(10px);
        }
        .bubble p { margin-bottom: 8px; }
        .bubble p:last-child { margin-bottom: 0; }
        .bubble a { 
            color: #60a5fa;
            text-decoration: none;
            border-bottom: 1px solid rgba(96, 165, 250, 0.4);
            transition: all 0.2s;
        }
        .bubble a:hover { 
            color: #93c5fd;
            border-bottom-color: #93c5fd;
        }
        .bubble ul, .bubble ol { margin-left: 18px; margin-bottom: 8px; }
        .typing-indicator {
            display: inline-flex;
            align-items: center;
            gap: 3px;
            margin-left: 4px;
            vertical-align: middle;
        }
        .typing-dot {
            width: 5px;
            height: 5px;
            background-color: rgba(226, 232, 240, 0.6);
            border-radius: 50%;
            animation: typing 1.4s infinite ease-in-out both;
        }
        .typing-dot:nth-child(1) { animation-delay: -0.32s; }
        .typing-dot:nth-child(2) { animation-delay: -0.16s; }
        .input-area {
            flex: none;
            padding: 14px;
            background: linear-gradient(180deg, rgba(15, 23, 42, 0.5), rgba(15, 23, 42, 0.8));
            border-top: 1px solid rgba(255, 255, 255, 0.06);
            backdrop-filter: blur(10px);
        }
        .input-form {
            display: flex;
            align-items: flex-end;
            gap: 10px;
            background: rgba(30, 41, 59, 0.6);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 14px;
            padding: 8px;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            backdrop-filter: blur(10px);
        }
        .input-form:focus-within {
            border-color: rgba(59, 130, 246, 0.5);
            background: rgba(30, 41, 59, 0.9);
            box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.1), 
                        inset 0 0 0 1px rgba(59, 130, 246, 0.2);
        }
        textarea {
            flex: 1;
            background: transparent;
            border: none;
            color: white;
            padding: 8px;
            font-size: 14px;
            resize: none;
            max-height: 100px;
            font-family: inherit;
        }
        textarea:focus { outline: none; }
        textarea::placeholder { color: #64748b; }
        .send-btn {
            background: linear-gradient(135deg, #3b82f6, #2563eb);
            color: white;
            border: none;
            border-radius: 10px;
            width: 36px;
            height: 36px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
            margin-bottom: 2px;
            box-shadow: 0 4px 12px rgba(59, 130, 246, 0.3);
            flex-shrink: 0;
        }
        .send-btn:hover:not(:disabled) { 
            background: linear-gradient(135deg, #2563eb, #1d4ed8);
            transform: translateY(-2px);
            box-shadow: 0 6px 16px rgba(59, 130, 246, 0.4);
        }
        .send-btn:active:not(:disabled) {
            transform: translateY(0);
            box-shadow: 0 2px 8px rgba(59, 130, 246, 0.3);
        }
        .send-btn:disabled { 
            background: rgba(100, 116, 139, 0.3);
            color: #64748b;
            cursor: not-allowed;
            box-shadow: none;
        }
        .spinner {
            width: 16px;
            height: 16px;
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-top-color: white;
            border-radius: 50%;
            animation: spin 0.8s linear infinite;
        }
        .fab {
            pointer-events:auto;
            width:64px;
            height:64px;
            border-radius:50%;
            border:none;
            cursor:pointer;
            display:flex;
            align-items:center;
            justify-content:center;
            transition:all 0.3s cubic-bezier(0.34,1.56,0.64,1);
            overflow:visible;
            padding:0;
            position:relative;
            font-size:28px;
            line-height:1;
        }
        .fab::before {
            content:'';
            position:absolute;
            inset:0;
            background:linear-gradient(135deg,#ef4444 0%,#dc2626 50%,#ff6b6b 100%);
            z-index:1;
            transition:all 0.3s ease;
            border-radius:50%;
            animation:fabPulse 2.5s ease-in-out infinite;
        }
        .fab.closed::before {
            background:linear-gradient(135deg,#ef4444 0%,#dc2626 50%,#ff6b6b 100%);
            box-shadow:0 10px 30px rgba(239,68,68,0.4),0 0 60px rgba(239,68,68,0.2);
        }
        .fab::after {
            content:'';
            position:absolute;
            inset:-4px;
            background:linear-gradient(135deg,rgba(239,68,68,0.5),rgba(255,107,107,0.3));
            border-radius:50%;
            z-index:0;
            animation:fabRing 2.5s ease-in-out infinite;
            opacity:0.5;
        }
        .fab.open::before {
            background:linear-gradient(135deg,#1e293b,#0f172a);
            border:2px solid rgba(239,68,68,0.5);
            animation:none;
        }
        .fab.open::after {
            animation:none;
            opacity:0;
        }
        .fab-icon-wrapper {
            position:relative;
            z-index:2;
            display:flex;
            align-items:center;
            justify-content:center;
            width:100%;
            height:100%;
            color:white;
        }
        .fab-icon {
            width:24px;
            height:24px;
            display:flex;
            align-items:center;
            justify-content:center;
            transition:transform 0.3s ease;
        }
        .fab.closed .fab-icon {
            animation: popIn 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
        }
        .fab:hover {
            transform:scale(1.15)!important;
        }
        .fab:hover::before {
            box-shadow:0 15px 35px rgba(239,68,68,0.6),0 0 90px rgba(239,68,68,0.3);
        }
        .fab:hover::after {
            animation:fabRingActive 0.6s ease-out forwards;
        }
        .fab:active {
            transform:scale(0.95);
        }
        @keyframes fabPulse {
            0%,100% {box-shadow:0 10px 30px rgba(239,68,68,0.4),0 0 60px rgba(239,68,68,0.2);}
            50% {box-shadow:0 15px 35px rgba(239,68,68,0.5),0 0 75px rgba(239,68,68,0.3);}
        }
        @keyframes fabRing {
            0% {transform:scale(1);opacity:0.6;}
            100% {transform:scale(1.3);opacity:0;}
        }
        @keyframes fabRingActive {
            0% {transform:scale(1);opacity:0.8;}
            100% {transform:scale(1.5);opacity:0;}
        }
        @keyframes slideIn {
            from { 
                opacity: 0; 
                transform: translateY(30px) scale(0.9);
            }
            to { 
                opacity: 1; 
                transform: translateY(0) scale(1);
            }
        }
        @keyframes fadeInUp {
            from {
                opacity: 0;
                transform: translateY(10px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }
        @keyframes scaleIn {
            from {
                opacity: 0;
                transform: scale(0.95);
            }
            to {
                opacity: 1;
                transform: scale(1);
            }
        }
        @keyframes popIn {
            0% {
                transform: scale(0.7);
                opacity: 0;
            }
            50% {
                transform: scale(1.1);
            }
            100% {
                transform: scale(1);
                opacity: 1;
            }
        }
        @keyframes typing {
            0%, 80%, 100% { 
                transform: scale(0); 
                opacity: 0.4; 
            }
            40% { 
                transform: scale(1); 
                opacity: 1; 
            }
        }
        @keyframes spin { 
            to { transform: rotate(360deg); } 
        }
        @media (max-width: 480px) {
            :host {
                bottom: 16px;
                right: 16px;
            }
            .widget-window {
                width: calc(100vw - 32px);
                max-width: 100%;
                height: 70vh;
                max-height: 500px;
            }
            .fab {
                width: 56px;
                height: 56px;
            }
            .fab-icon {
                width: 20px;
                height: 20px;
            }
        }
    `;
    const SendIcon = () => (
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="16" height="16">
            <path d="M3.478 2.405a.75.75 0 00-.926.94l2.432 7.905H13.5a.75.75 0 010 1.5H4.984l-2.432 7.905a.75.75 0 00.926.94 60.519 60.519 0 0018.445-8.986.75.75 0 000-1.218A60.517 60.517 0 003.478 2.405z" />
        </svg>
    );
    const TrashIcon = () => (
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth={1.5} width="16" height="16">
            <path strokeLinecap="round" strokeLinejoin="round" d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0" />
        </svg>
    );
    const UserIcon = () => (
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="14" height="14">
            <path fillRule="evenodd" d="M7.5 6a4.5 4.5 0 119 0 4.5 4.5 0 01-9 0zM3.751 20.105a8.25 8.25 0 0116.498 0 .75.75 0 01-.437.695A18.683 18.683 0 0112 22.5c-2.786 0-5.433-.608-7.812-1.7a.75.75 0 01-.437-.695z" clipRule="evenodd" />
        </svg>
    );
    const ChatBubbleIcon = () => (
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24">
            <path fillRule="evenodd" d="M4.804 21.644A6.707 6.707 0 006 21.75a6.721 6.721 0 003.583-1.029c.774.182 1.584.279 2.417.279 5.322 0 9.75-3.97 9.75-9 0-5.03-4.428-9-9.75-9s-9.75 3.97-9.75 9c0 2.409 1.025 4.587 2.674 6.192.232.226.277.428.254.543a3.73 3.73 0 01-.814 1.686.75.75 0 00.44 1.223zM8.25 10.875a1.125 1.125 0 100 2.25 1.125 1.125 0 000-2.25zM10.875 12a1.125 1.125 0 112.25 0 1.125 1.125 0 01-2.25 0zm4.875-1.125a1.125 1.125 0 100 2.25 1.125 1.125 0 000-2.25z" clipRule="evenodd" />
        </svg>
    );
    const XMarkIcon = () => (
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24">
            <path fillRule="evenodd" d="M5.47 5.47a.75.75 0 011.06 0L12 10.94l5.47-5.47a.75.75 0 111.06 1.06L13.06 12l5.47 5.47a.75.75 0 11-1.06 1.06L12 13.06l-5.47 5.47a.75.75 0 01-1.06-1.06L10.94 12 5.47 6.53a.75.75 0 010-1.06z" clipRule="evenodd" />
        </svg>
    );
    const ChevronDownIcon = () => (
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="20" height="20">
            <path fillRule="evenodd" d="M12.53 16.28a.75.75 0 01-1.06 0l-7.5-7.5a.75.75 0 011.06-1.06L12 14.69l6.97-6.97a.75.75 0 111.06 1.06l-7.5 7.5z" clipRule="evenodd" />
        </svg>
    );
    const Role = { USER: 'user', MODEL: 'model' };
    const API_KEY = process.env.API_KEY;
    let ai;
    try {
        if (API_KEY) {
            ai = new GoogleGenAI({ apiKey: API_KEY });
        }
    } catch (e) { console.error("Error initializing GoogleGenAI", e); }
    const MODEL_NAME = 'gemini-2.5-flash-lite';
    let chatSession = null;
    const getChatSession = () => {
        if (!ai) throw new Error("API Key not found or SDK not initialized");
        if (!chatSession) {
            chatSession = ai.chats.create({
                model: MODEL_NAME,
                config: {
                    systemInstruction: "You are FNCrimson, a helpful, witty, and extremely concise assistant. Your goal is to provide answers as fast as possible. Keep responses brief unless asked for detail.",
                    temperature: 0.7,
                },
            });
        }
        return chatSession;
    };
    const resetChatSession = () => { chatSession = null; };
    const sendMessageStream = async (message) => {
        const chat = getChatSession();
        let attempts = 0;
        const maxAttempts = 4;
        let lastError;
        while (attempts < maxAttempts) {
            attempts++;
            try {
                const stream = await chat.sendMessageStream({ message });
                return stream;
            } catch (error) {
                lastError = error;
                console.warn(`Attempt ${attempts}/${maxAttempts} failed:`, error?.message);
                if (attempts >= maxAttempts) {
                    throw error;
                }
                const delay = Math.pow(2, attempts - 1) * 500;
                await new Promise(resolve => setTimeout(resolve, delay));
            }
        }
        throw lastError;
    };
    const MessageBubble = ({ message }) => {
        const isUser = message.role === Role.USER;
        const isStreaming = message.isStreaming;
        return (
            <div className={`message-row ${isUser ? 'user' : 'model'}`}>
                <div className="message-container">
                    <div className={`avatar ${isUser ? 'user' : 'model'}`}>
                        {isUser ? <UserIcon /> : <img src={LOGO_URL} alt="Bot" />}
                    </div>
                    <div className={`bubble ${isUser ? 'user' : 'model'}`}>
                        <ReactMarkdown>{message.text}</ReactMarkdown>
                        {isStreaming && (
                            <span className="typing-indicator">
                                <span className="typing-dot"></span>
                                <span className="typing-dot"></span>
                                <span className="typing-dot"></span>
                            </span>
                        )}
                    </div>
                </div>
            </div>
        );
    };
    const App = () => {
        const [isOpen, setIsOpen] = useState(false);
        const [messages, setMessages] = useState([]);
        const [inputValue, setInputValue] = useState('');
        const [isLoading, setIsLoading] = useState(false);
        const messagesEndRef = useRef(null);
        const inputRef = useRef(null);
        const scrollToBottom = () => {
            messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
        };
        useEffect(() => { if (isOpen) scrollToBottom(); }, [messages, isOpen]);
        useEffect(() => {
            if (messages.length === 0) {
                setMessages([{
                    id: 'welcome',
                    role: Role.MODEL,
                    text: "Hey there! 👋 I'm FNCrimson. What can I help you with?",
                    timestamp: Date.now(),
                }]);
            }
        }, [messages.length]);
        const handleClearChat = () => {
            if (window.confirm("Clear conversation history?")) {
                setMessages([]);
                resetChatSession();
                setMessages([{
                    id: 'welcome-reset',
                    role: Role.MODEL,
                    text: "Ready to assist.",
                    timestamp: Date.now(),
                }]);
            }
        };
        const handleSubmit = async (e) => {
            if (e) e.preventDefault();
            if (!inputValue.trim() || isLoading) return;
            const userText = inputValue.trim();
            setInputValue('');
            if (inputRef.current) inputRef.current.style.height = 'auto';
            const newMessageId = Date.now().toString();
            const modelMessageId = (Date.now() + 1).toString();
            setMessages(prev => [
                ...prev, 
                { id: newMessageId, role: Role.USER, text: userText, timestamp: Date.now() },
                { id: modelMessageId, role: Role.MODEL, text: '', timestamp: Date.now(), isStreaming: true }
            ]);
            setIsLoading(true);
            try {
                const result = await sendMessageStream(userText);
                let fullText = '';
                let lastUpdateTime = Date.now();
                const UPDATE_INTERVAL = 50;
                for await (const chunk of result) {
                    const chunkText = chunk.text;
                    if (chunkText) {
                        fullText += chunkText;
                        const now = Date.now();
                        if (fullText.length < 20 || now - lastUpdateTime > UPDATE_INTERVAL) {
                            setMessages(prev => prev.map(msg => 
                                msg.id === modelMessageId ? { ...msg, text: fullText } : msg
                            ));
                            lastUpdateTime = now;
                        }
                    }
                }
                const finalText = fullText || "Response complete";
                setMessages(prev => prev.map(msg => 
                    msg.id === modelMessageId ? { ...msg, text: finalText, isStreaming: false } : msg
                ));
            } catch (error) {
                console.error("Chat error:", error?.message);
                const errorMsg = error?.message || "Failed to get response";
                setMessages(prev => prev.map(msg => 
                    msg.id === modelMessageId ? { 
                        ...msg, 
                        text: "Sorry, something went wrong. Please try again.", 
                        isStreaming: false 
                    } : msg
                ));
            } finally {
                setIsLoading(false);
            }
        };
        const handleKeyDown = (e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                handleSubmit();
            }
        };
        const handleInput = (e) => {
            setInputValue(e.target.value);
            e.target.style.height = 'auto';
            e.target.style.height = `${Math.min(e.target.scrollHeight, 100)}px`;
        };
        return (
            <>
                {isOpen && (
                    <div className="widget-window">
                        <header className="header">
                            <div className="header-branding">
                                <div className="header-logo">
                                    <img src={LOGO_URL} alt="Logo" />
                                </div>
                                <div className="header-title">
                                    <h1>FNCrimson</h1>
                                    <span>Online</span>
                                </div>
                            </div>
                            <div className="header-actions">
                                <button onClick={handleClearChat} className="icon-btn danger" title="Clear Chat">
                                    <TrashIcon />
                                </button>
                                <button onClick={() => setIsOpen(false)} className="icon-btn" title="Close">
                                    <ChevronDownIcon />
                                </button>
                            </div>
                        </header>
                        <div className="chat-scroll-area">
                            {messages.map((msg) => (
                                <MessageBubble key={msg.id} message={msg} />
                            ))}
                            <div ref={messagesEndRef} style={{ height: '1px' }} />
                        </div>
                        <div className="input-area">
                            <form onSubmit={handleSubmit} className="input-form">
                                <textarea
                                    ref={inputRef}
                                    value={inputValue}
                                    onChange={handleInput}
                                    onKeyDown={handleKeyDown}
                                    placeholder="Message..."
                                    rows={1}
                                    disabled={isLoading}
                                />
                                <button type="submit" disabled={!inputValue.trim() || isLoading} className="send-btn">
                                    {isLoading ? <div className="spinner"></div> : <SendIcon />}
                                </button>
                            </form>
                        </div>
                    </div>
                )}
                {!isOpen && (
                    <button 
                        onClick={() => setIsOpen(true)} 
                        className="fab closed"
                        aria-label="Open Chat"
                    >
                        <div className="fab-icon-wrapper">
                            <ChatBubbleIcon />
                        </div>
                    </button>
                )}
            </>
        );
    };
    const host = document.getElementById('fncrimson-widget-host');
    if (host && host.parentElement !== document.body) {
        document.body.appendChild(host);
    }
    if (host && !host.shadowRoot) {
        const shadow = host.attachShadow({ mode: 'open' });
        const styleEl = document.createElement('style');
        styleEl.textContent = widgetStyles;
        shadow.appendChild(styleEl);
        const fontLink = document.createElement('link');
        fontLink.rel = 'stylesheet';
        fontLink.href = 'https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap';
        shadow.appendChild(fontLink);
        const rootEl = document.createElement('div');
        rootEl.id = 'root';
        shadow.appendChild(rootEl);
        const root = ReactDOM.createRoot(rootEl);
        root.render(
            <React.StrictMode>
                <App />
            </React.StrictMode>
        );
    }
</script>
