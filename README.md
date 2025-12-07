<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NexusAI | AI Interaction Platform</title>
    <!-- 使用Tailwind CSS CDN快速实现样式 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 引入Lucide图标 -->
    <script src="https://unpkg.com/lucide@latest/dist/umd/lucide.js"></script>
    <!-- 引入Framer Motion Lite用于粒子动画 -->
    <script src="https://unpkg.com/framer-motion@latest/dist/framer-motion.js"></script>
    <style>
        /* 自定义粒子效果和关键帧动画的CSS */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, sans-serif;
            overflow-x: hidden;
            background: #0f172a; /* slate-950 */
        }
        /* 粒子画布 */
        #particle-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 0;
        }
        /* 主内容区 */
        .content {
            position: relative;
            z-index: 10;
        }
        /* 几何图形动画 */
        @keyframes float-rotate {
            0%, 100% { transform: translateY(0px) rotate(0deg); opacity: 0.2; }
            50% { transform: translateY(-20px) rotate(180deg); opacity: 0.4; }
        }
        @keyframes float-reverse {
            0%, 100% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(20px) rotate(-180deg); }
        }
        @keyframes pulse-glow {
            0%, 100% { opacity: 0.5; }
            50% { opacity: 1; }
        }
        @keyframes border-shimmer {
            0% { background-position: -200% center; }
            100% { background-position: 200% center; }
        }
        .floating-shape {
            animation: float-rotate 8s ease-in-out infinite;
        }
        .floating-shape-reverse {
            animation: float-reverse 10s ease-in-out infinite;
        }
        .pulse-glow {
            animation: pulse-glow 3s ease-in-out infinite;
        }
        .shimmer-border {
            background: linear-gradient(90deg, 
                transparent, 
                rgba(255,255,255,0.3), 
                transparent);
            background-size: 200% 100%;
            animation: border-shimmer 2s linear infinite;
        }
        /* 消息区域滚动条样式 */
        .messages-container::-webkit-scrollbar {
            width: 6px;
        }
        .messages-container::-webkit-scrollbar-track {
            background: rgba(30, 41, 59, 0.3);
            border-radius: 3px;
        }
        .messages-container::-webkit-scrollbar-thumb {
            background: rgba(59, 130, 246, 0.5);
            border-radius: 3px;
        }
        .typing-indicator span {
            animation: bounce 1.4s infinite ease-in-out both;
        }
        .typing-indicator span:nth-child(1) { animation-delay: -0.32s; }
        .typing-indicator span:nth-child(2) { animation-delay: -0.16s; }
        @keyframes bounce {
            0%, 80%, 100% { transform: scale(0); opacity: 0.5; }
            40% { transform: scale(1); opacity: 1; }
        }
    </style>
</head>
<body class="text-gray-100">
    <!-- 粒子背景画布 -->
    <canvas id="particle-canvas"></canvas>

    <!-- 应用主容器 - 使用条件渲染显示首页或聊天界面 -->
    <div id="app-container">
        <!-- 首页界面 (默认显示) -->
        <div id="homepage" class="content min-h-screen relative">
            <!-- 动态几何背景元素 -->
            <div class="fixed inset-0 pointer-events-none overflow-hidden">
                <div class="absolute top-[-10%] right-[-5%] w-[600px] h-[600px] border border-blue-500/10 rounded-full floating-shape"></div>
                <div class="absolute bottom-[-10%] left-[-5%] w-[500px] h-[500px] border border-blue-500/10 rounded-full floating-shape-reverse"></div>
                <div class="absolute top-[20%] left-[15%] w-32 h-32 border border-blue-400/20 floating-shape" style="clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%);"></div>
                <div class="absolute bottom-[25%] right-[20%] w-24 h-24 border border-blue-400/20 floating-shape-reverse" style="clip-path: polygon(30% 0%, 70% 0%, 100% 30%, 100% 70%, 70% 100%, 30% 100%, 0% 70%, 0% 30%);"></div>
            </div>

            <!-- 主内容区 -->
            <div class="relative z-10 flex items-center justify-center min-h-screen px-4">
                <div class="max-w-4xl w-full mx-auto text-center">
                    <!-- Logo/Icon -->
                    <div class="inline-flex items-center justify-center mb-8">
                        <div class="relative">
                            <div class="absolute inset-0 bg-blue-500/30 blur-3xl rounded-full pulse-glow"></div>
                            <div class="relative bg-gradient-to-br from-blue-500/20 to-blue-600/10 backdrop-blur-sm border border-blue-400/30 rounded-2xl p-6">
                                <i data-lucide="sparkles" class="w-16 h-16 text-blue-400 mx-auto"></i>
                            </div>
                        </div>
                    </div>

                    <!-- AI 名称 -->
                    <h1 class="text-5xl md:text-7xl font-bold tracking-tight mb-4 bg-gradient-to-r from-slate-100 via-blue-100 to-slate-100 bg-clip-text text-transparent">
                        NexusAI
                    </h1>

                    <!-- 标语 -->
                    <p class="text-xl text-slate-300 mb-12 max-w-2xl mx-auto">
                        Where Intelligence Meets Innovation — Your Gateway to the Future of AI Interaction
                    </p>

                    <!-- 按钮容器 -->
                    <div class="flex flex-col sm:flex-row gap-4 justify-center items-center">
                        <!-- 主要按钮 - 开始对话 -->
                        <button id="start-conversation-btn" class="group relative px-8 py-4 rounded-xl overflow-hidden min-w-[200px] transition-transform hover:scale-105 active:scale-98">
                            <div class="absolute inset-0 bg-gradient-to-r from-blue-600 to-blue-500 opacity-100 group-hover:opacity-90 transition-opacity"></div>
                            <div class="absolute inset-0 bg-blue-400/20 blur-xl group-hover:blur-2xl transition-all"></div>
                            <div class="absolute inset-0 opacity-0 group-hover:opacity-100 shimmer-border"></div>
                            <div class="relative flex items-center justify-center gap-2 text-white">
                                <i data-lucide="zap" class="w-5 h-5"></i>
                                <span class="font-medium">Start Conversation</span>
                            </div>
                        </button>

                        <!-- 次要按钮 - 连接 API -->
                        <button class="group relative px-8 py-4 rounded-xl overflow-hidden min-w-[200px] backdrop-blur-sm transition-all hover:scale-105 active:scale-98">
                            <div class="absolute inset-0 bg-slate-800/40 border border-slate-600/50 group-hover:border-blue-500/50 transition-all"></div>
                            <div class="absolute inset-0 bg-gradient-to-br from-blue-500/10 to-transparent opacity-0 group-hover:opacity-100 transition-opacity"></div>
                            <div class="relative flex items-center justify-center gap-2 text-slate-200 group-hover:text-white transition-colors">
                                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4"/>
                                </svg>
                                <span class="font-medium">Connect API</span>
                            </div>
                        </button>
                    </div>

                    <!-- 附加信息 -->
                    <div class="mt-16 flex flex-col sm:flex-row items-center justify-center gap-4 sm:gap-8 text-sm text-slate-400">
                        <div class="flex items-center gap-2">
                            <div class="w-2 h-2 bg-green-400 rounded-full pulse-glow"></div>
                            <span>System Online</span>
                        </div>
                        <div class="hidden sm:block w-px h-4 bg-slate-600"></div>
                        <span>Powered by Next-Gen AI</span>
                    </div>
                </div>
            </div>

            <!-- 底部渐变遮罩 -->
            <div class="fixed bottom-0 left-0 right-0 h-32 bg-gradient-to-t from-slate-950/50 to-transparent pointer-events-none"></div>
        </div>

        <!-- 聊天界面 (初始隐藏) -->
        <div id="chat-interface" class="hidden h-screen bg-gradient-to-b from-slate-950 to-slate-900">
            <!-- 侧边栏 -->
            <div id="sidebar" class="w-64 bg-slate-900/80 backdrop-blur-sm border-r border-slate-800 flex flex-col transition-all duration-300">
                <!-- 侧边栏内容占位 -->
                <div class="p-4 border-b border-slate-800">
                    <div class="flex items-center gap-3">
                        <div class="w-8 h-8 rounded-lg bg-gradient-to-br from-blue-500 to-blue-600 flex items-center justify-center">
                            <i data-lucide="brain" class="w-4 h-4 text-white"></i>
                        </div>
                        <span class="font-semibold text-slate-200">Conversations</span>
                    </div>
                </div>
                <div class="flex-1 p-4">
                    <p class="text-slate-400 text-sm">Chat history would appear here.</p>
                </div>
                <div class="p-4 border-t border-slate-800">
                    <select id="model-selector" class="w-full bg-slate-800/50 border border-slate-700 rounded-lg px-3 py-2 text-sm text-slate-200 focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option value="gpt-4">GPT-4</option>
                        <option value="claude-3">Claude 3</option>
                        <option value="gemini-pro">Gemini Pro</option>
                    </select>
                </div>
            </div>

            <!-- 主聊天区域 -->
            <div class="flex-1 flex flex-col">
                <!-- 消息区域 -->
                <div id="messages-container" class="messages-container flex-1 overflow-y-auto px-4 py-6">
                    <!-- 初始欢迎屏幕 -->
                    <div id="welcome-screen" class="max-w-4xl mx-auto">
                        <div class="text-center mb-10">
                            <div class="inline-flex items-center justify-center w-20 h-20 rounded-2xl bg-gradient-to-br from-blue-500/20 to-blue-600/10 border border-blue-400/30 mb-6">
                                <i data-lucide="bot" class="w-10 h-10 text-blue-400"></i>
                            </div>
                            <h2 class="text-2xl font-bold text-slate-100 mb-3">How can I help you today?</h2>
                            <p class="text-slate-400 mb-8">Upload documents, images, or ask any question to get started.</p>
                        </div>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-3">
                            <button class="prompt-btn p-4 bg-slate-800/50 hover:bg-slate-800 border border-slate-700/50 rounded-xl text-left transition-colors">
                                <div class="flex items-center gap-3 mb-2">
                                    <div class="w-8 h-8 rounded-lg bg-blue-500/20 flex items-center justify-center">
                                        <i data-lucide="file-text" class="w-4 h-4 text-blue-400"></i>
                                    </div>
                                    <span class="font-medium text-slate-200">Analyze document</span>
                                </div>
                                <p class="text-sm text-slate-400">Upload PDF, Word or text files</p>
                            </button>
                            <button class="prompt-btn p-4 bg-slate-800/50 hover:bg-slate-800 border border-slate-700/50 rounded-xl text-left transition-colors">
                                <div class="flex items-center gap-3 mb-2">
                                    <div class="w-8 h-8 rounded-lg bg-purple-500/20 flex items-center justify-center">
                                        <i data-lucide="image" class="w-4 h-4 text-purple-400"></i>
                                    </div>
                                    <span class="font-medium text-slate-200">Process images</span>
                                </div>
                                <p class="text-sm text-slate-400">Analyze and describe visual content</p>
                            </button>
                        </div>
                    </div>
                    <!-- 消息将动态插入到这里 -->
                </div>

                <!-- 输入区域 -->
                <div class="border-t border-slate-800 bg-slate-900/50 backdrop-blur-sm p-4">
                    <div class="max-w-4xl mx-auto">
                        <div id="typing-indicator" class="hidden mb-4">
                            <div class="flex gap-3">
                                <div class="flex-shrink-0">
                                    <div class="w-8 h-8 rounded-lg bg-gradient-to-br from-blue-500 to-blue-600 flex items-center justify-center">
                                        <i data-lucide="sparkles" class="w-4 h-4 text-white"></i>
                                    </div>
                                </div>
                                <div class="typing-indicator flex items-center gap-1 px-4 py-3 bg-slate-800/50 backdrop-blur-sm border border-slate-700/50 rounded-xl">
                                    <span class="w-2 h-2 bg-slate-400 rounded-full"></span>
                                    <span class="w-2 h-2 bg-slate-400 rounded-full"></span>
                                    <span class="w-2 h-2 bg-slate-400 rounded-full"></span>
                                </div>
                            </div>
                        </div>
                        <div class="flex gap-3">
                            <div class="flex-1 relative">
                                <input type="text" id="message-input" placeholder="Type your message or upload a file..." class="w-full bg-slate-800/50 border border-slate-700 rounded-xl px-4 py-3 pr-12 text-slate-200 placeholder-slate-500 focus:outline-none focus:ring-2 focus:ring-blue-500">
                                <button id="file-upload-btn" class="absolute right-3 top-3 text-slate-400 hover:text-slate-300">
                                    <i data-lucide="paperclip" class="w-5 h-5"></i>
                                </button>
                            </div>
                            <button id="send-message-btn" class="px-6 bg-gradient-to-r from-blue-600 to-blue-500 hover:from-blue-500 hover:to-blue-400 border border-blue-500 text-white font-medium rounded-xl flex items-center justify-center transition-all">
                                <i data-lucide="send" class="w-5 h-5"></i>
                            </button>
                        </div>
                        <p class="text-xs text-slate-500 mt-3 text-center">Supports text, documents, images, audio and video</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // 初始化Lucide图标
        lucide.createIcons();

        // ========== 状态管理 ==========
        let messages = [];
        let isTyping = false;

        // ========== DOM元素引用 ==========
        const homepage = document.getElementById('homepage');
        const chatInterface = document.getElementById('chat-interface');
        const startConversationBtn = document.getElementById('start-conversation-btn');
        const messagesContainer = document.getElementById('messages-container');
        const welcomeScreen = document.getElementById('welcome-screen');
        const messageInput = document.getElementById('message-input');
        const sendMessageBtn = document.getElementById('send-message-btn');
        const typingIndicator = document.getElementById('typing-indicator');
        const promptButtons = document.querySelectorAll('.prompt-btn');
        const fileUploadBtn = document.getElementById('file-upload-btn');

        // ========== 粒子系统 ==========
        class ParticleSystem {
            constructor() {
                this.canvas = document.getElementById('particle-canvas');
                this.ctx = this.canvas.getContext('2d');
                this.particles = [];
                this.mouse = { x: 0, y: 0 };
                this.init();
            }

            init() {
                // 调整画布尺寸
                this.resize();
                window.addEventListener('resize', () => this.resize());

                // 鼠标移动追踪
                window.addEventListener('mousemove', (e) => {
                    this.mouse.x = e.clientX;
                    this.mouse.y = e.clientY;
                });

                // 创建初始粒子
                this.createParticles(50);

                // 开始动画循环
                this.animate();
            }

            resize() {
                this.canvas.width = window.innerWidth;
                this.canvas.height = window.innerHeight;
            }

            createParticles(count) {
                for (let i = 0; i < count; i++) {
                    this.particles.push({
                        x: Math.random() * this.canvas.width,
                        y: Math.random() * this.canvas.height,
                        size: Math.random() * 2 + 0.5,
                        speedX: (Math.random() - 0.5) * 0.5,
                        speedY: (Math.random() - 0.5) * 0.5,
                        color: `rgba(59, 130, 246, ${Math.random() * 0.3 + 0.1})`,
                        originalSize: 0
                    });
                }
            }

            animate() {
                this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);

                // 更新和绘制每个粒子
                this.particles.forEach(particle => {
                    // 向鼠标位置移动
                    const dx = this.mouse.x - particle.x;
                    const dy = this.mouse.y - particle.y;
                    const distance = Math.sqrt(dx * dx + dy * dy);

                    // 如果粒子靠近鼠标，产生排斥效果
                    if (distance < 100) {
                        const force = (100 - distance) / 100;
                        particle.x -= (dx / distance) * force * 2;
                        particle.y -= (dy / distance) * force * 2;
                    }

                    // 应用随机运动
                    particle.x += particle.speedX;
                    particle.y += particle.speedY;

                    // 边界检查
                    if (particle.x < 0 || particle.x > this.canvas.width) particle.speedX *= -1;
                    if (particle.y < 0 || particle.y > this.canvas.height) particle.speedY *= -1;

                    // 绘制粒子
                    this.ctx.beginPath();
                    this.ctx.fillStyle = particle.color;
                    this.ctx.arc(particle.x, particle.y, particle.size, 0, Math.PI * 2);
                    this.ctx.fill();

                    // 绘制粒子之间的连线
                    this.particles.forEach(otherParticle => {
                        const dx = particle.x - otherParticle.x;
                        const dy = particle.y - otherParticle.y;
                        const distance = Math.sqrt(dx * dx + dy * dy);

                        if (distance < 100) {
                            this.ctx.beginPath();
                            this.ctx.strokeStyle = `rgba(59, 130, 246, ${0.1 * (1 - distance / 100)})`;
                            this.ctx.lineWidth = 0.5;
                            this.ctx.moveTo(particle.x, particle.y);
                            this.ctx.lineTo(otherParticle.x, otherParticle.y);
                            this.ctx.stroke();
                        }
                    });
                });

                requestAnimationFrame(() => this.animate());
            }
        }

        // 初始化粒子系统
        const particleSystem = new ParticleSystem();

        // ========== 界面切换 ==========
        startConversationBtn.addEventListener('click', () => {
            // 淡出首页
            homepage.style.opacity = '0';
            homepage.style.transform = 'scale(0.95)';
            
            setTimeout(() => {
                homepage.classList.add('hidden');
                chatInterface.classList.remove('hidden');
                // 触发入场动画
                chatInterface.style.opacity = '0';
                chatInterface.style.transform = 'scale(1.05)';
                
                setTimeout(() => {
                    chatInterface.style.transition = 'opacity 0.5s, transform 0.5s';
                    chatInterface.style.opacity = '1';
                    chatInterface.style.transform = 'scale(1)';
                }, 50);
            }, 300);
        });

        // ========== 消息处理 ==========
        function addMessage(message, type) {
            const messageId = Date.now().toString();
            const timestamp = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
            
            const messageElement = document.createElement('div');
            messageElement.className = `max-w-4xl mx-auto ${type === 'user' ? 'ml-auto' : ''} mb-6`;
            messageElement.innerHTML = `
                <div class="flex gap-3 ${type === 'user' ? 'flex-row-reverse' : ''}">
                    <div class="flex-shrink-0">
                        <div class="w-8 h-8 rounded-lg ${type === 'user' ? 'bg-slate-700' : 'bg-gradient-to-br from-blue-500 to-blue-600'} flex items-center justify-center">
                            ${type === 'user' ? 
                                '<i data-lucide="user" class="w-4 h-4 text-slate-300"></i>' : 
                                '<i data-lucide="sparkles" class="w-4 h-4 text-white"></i>'}
                        </div>
                    </div>
                    <div class="${type === 'user' ? 'bg-blue-900/30 border border-blue-800/50' : 'bg-slate-800/50 backdrop-blur-sm border border-slate-700/50'} rounded-xl px-4 py-3 max-w-[80%]">
                        <div class="flex justify-between items-center mb-2">
                            <span class="font-medium text-sm ${type === 'user' ? 'text-blue-300' : 'text-slate-300'}">
                                ${type === 'user' ? 'You' : 'NexusAI'}
                            </span>
                            <span class="text-xs text-slate-500">${timestamp}</span>
                        </div>
                        <p class="text-slate-200 whitespace-pre-wrap">${message}</p>
                        ${type === 'ai' ? `
                            <div class="flex gap-2 mt-4">
                                <button class="message-action text-xs px-3 py-1 rounded-lg bg-slate-700/50 hover:bg-slate-700 text-slate-300 transition-colors">
                                    <i data-lucide="refresh-cw" class="w-3 h-3 inline mr-1"></i>Regenerate
                                </button>
                                <button class="message-action text-xs px-3 py-1 rounded-lg bg-slate-700/50 hover:bg-slate-700 text-slate-300 transition-colors">
                                    <i data-lucide="copy" class="w-3 h-3 inline mr-1"></i>Copy
                                </button>
                            </div>
                        ` : ''}
                    </div>
                </div>
            `;
            
            // 隐藏欢迎屏幕
            if (welcomeScreen && messages.length === 0) {
                welcomeScreen.style.display = 'none';
            }
            
            // 添加消息到容器
            messagesContainer.appendChild(messageElement);
            
            // 滚动到底部
            messagesContainer.scrollTop = messagesContainer.scrollHeight;
            
            // 更新图标（为新添加的元素）
            lucide.createIcons();
            
            // 为AI消息的操作按钮添加事件
            if (type === 'ai') {
                messageElement.querySelectorAll('.message-action').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        const action = e.target.closest('button').textContent.trim();
                        handleMessageAction(action, message);
                    });
                });
            }
            
            return messageElement;
        }

        function handleSendMessage() {
            const messageText = messageInput.value.trim();
            if (!messageText || isTyping) return;
            
            // 添加用户消息
            addMessage(messageText, 'user');
            
            // 清空输入框
            messageInput.value = '';
            
            // 显示"正在输入"指示器
            isTyping = true;
            typingIndicator.classList.remove('hidden');
            messagesContainer.scrollTop = messagesContainer.scrollHeight;
            
            // 模拟AI回复（2秒后）
            setTimeout(() => {
                typingIndicator.classList.add('hidden');
                isTyping = false;
                
                const aiResponses = [
                    `I understand you want to ${messageText.toLowerCase()}. I'm here to help! This is a demonstration of the NexusAI chat interface with multimodal capabilities. You can upload documents, images, videos, and audio files for analysis.\n\nKey features:\n• Intelligent content analysis\n• Multi-model support (GPT-4, Claude 3, Gemini Pro)\n• Conversation history and categorization\n• File processing and preview\n\nHow would you like to proceed?`,
                    `Great question about "${messageText}". As an AI assistant, I can help you analyze content, generate insights, and process various file formats. The interface you're using showcases a modern AI interaction platform with real-time responses and visual feedback.\n\nTry uploading a file or asking me to analyze specific content!`,
                    `You mentioned: "${messageText}". This platform demonstrates seamless human-AI collaboration with a focus on intuitive design and powerful functionality. The particle effects and animations are rendered in real-time using pure JavaScript and Canvas.\n\nWhat would you like to explore next?`
                ];
                
                const randomResponse = aiResponses[Math.floor(Math.random() * aiResponses.length)];
                addMessage(randomResponse, 'ai');
            }, 2000);
        }

        function handleMessageAction(action, message) {
            switch(action) {
                case 'Regenerate':
                    alert('This would regenerate the AI response in a real application.');
                    break;
                case 'Copy':
                    navigator.clipboard.writeText(message);
                    showToast('Message copied to clipboard!');
                    break;
            }
        }

        function showToast(message) {
            const toast = document.createElement('div');
            toast.className = 'fixed bottom-4 right-4 bg-slate-800 text-slate-200 px-4 py-3 rounded-lg shadow-lg border border-slate-700 z-50';
            toast.textContent = message;
            document.body.appendChild(toast);
            
            setTimeout(() => {
                toast.remove();
            }, 3000);
        }

        // ========== 事件监听器 ==========
        sendMessageBtn.addEventListener('click', handleSendMessage);
        
        messageInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                handleSendMessage();
            }
        });

        // 预设提示词按钮
        promptButtons.forEach(button => {
            button.addEventListener('click', () => {
                const promptText = button.querySelector('.font-medium').textContent;
                messageInput.value = `Can you help me with: ${promptText}`;
                messageInput.focus();
            });
        });

        // 文件上传
        fileUploadBtn.addEventListener('click', () => {
            const input = document.createElement('input');
            input.type = 'file';
            input.accept = '.pdf,.doc,.docx,.txt,.jpg,.jpeg,.png,.mp4,.mp3';
            input.multiple = true;
            
            input.addEventListener('change', (e) => {
                const files = Array.from(e.target.files);
                if (files.length > 0) {
                    const fileNames = files.map(f => f.name).join(', ');
                    const fileMessage = `Uploaded ${files.length} file(s): ${fileNames}`;
                    addMessage(fileMessage, 'user');
                    
                    // 模拟文件处理
                    isTyping = true;
                    typingIndicator.classList.remove('hidden');
                    
                    setTimeout(() => {
                        typingIndicator.classList.add('hidden');
                        isTyping = false;
                        addMessage(`I've received and processed your ${files.length} file(s). I can now analyze the content and provide insights. What specific analysis would you like me to perform on these files?`, 'ai');
                    }, 2500);
                }
            });
            
            input.click();
        });

        // 模型选择器
        document.getElementById('model-selector').addEventListener('change', (e) => {
            showToast(`AI model changed to: ${e.target.options[e.target.selectedIndex].text}`);
        });
    </script>
</body>
</html>
