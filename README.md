<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ultimate Task Manager</title>
    <style>
        /* --- 1. CORE VARIABLES & RESET --- */
        :root {
            --primary: #6366f1;
            --primary-hover: #4f46e5;
            --bg-gradient-1: #4f46e5;
            --bg-gradient-2: #8b5cf6;
            --bg-gradient-3: #ec4899;
            --glass-bg: rgba(255, 255, 255, 0.75);
            --glass-border: rgba(255, 255, 255, 0.6);
            --text-main: #1e293b;
            --text-secondary: #64748b;
            --danger: #ef4444;
            --success: #10b981;
            --shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
        }

        .dark-mode {
            --primary: #818cf8;
            --primary-hover: #6366f1;
            --bg-gradient-1: #0f172a;
            --bg-gradient-2: #1e293b;
            --bg-gradient-3: #334155;
            --glass-bg: rgba(30, 41, 59, 0.85);
            --glass-border: rgba(255, 255, 255, 0.1);
            --text-main: #f1f5f9;
            --text-secondary: #94a3b8;
            --shadow: 0 20px 50px rgba(0,0,0,0.5);
        }

        * { box-sizing: border-box; outline: none; }
        body {
            margin: 0;
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background: linear-gradient(135deg, var(--bg-gradient-1), var(--bg-gradient-2), var(--bg-gradient-3));
            background-size: 400% 400%;
            animation: gradientBG 15s ease infinite;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            color: var(--text-main);
            transition: color 0.3s ease;
            overflow-x: hidden;
        }

        @keyframes gradientBG {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        /* --- 2. MAIN CONTAINER (GLASS) --- */
        .app-container {
            width: 90%;
            max-width: 500px;
            background: var(--glass-bg);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border);
            border-radius: 24px;
            padding: 2rem;
            box-shadow: var(--shadow);
            transform: translateY(0);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            z-index: 10;
        }

        /* --- 3. HEADER & CONTROLS --- */
        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 1.5rem;
        }

        h1 {
            margin: 0;
            font-size: 1.75rem;
            font-weight: 800;
            letter-spacing: -0.5px;
            background: linear-gradient(to right, var(--primary), #ec4899);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .theme-toggle {
            background: none;
            border: none;
            cursor: pointer;
            font-size: 1.5rem;
            color: var(--text-main);
            transition: transform 0.5s;
            padding: 5px;
            border-radius: 50%;
        }
        .theme-toggle:hover { background: rgba(0,0,0,0.05); }

        /* Progress Bar */
        .progress-container {
            margin-bottom: 2rem;
        }
        .progress-info {
            display: flex;
            justify-content: space-between;
            font-size: 0.85rem;
            color: var(--text-secondary);
            margin-bottom: 0.5rem;
            font-weight: 600;
        }
        .progress-bar-bg {
            height: 8px;
            background: rgba(0,0,0,0.1);
            border-radius: 10px;
            overflow: hidden;
        }
        .dark-mode .progress-bar-bg { background: rgba(255,255,255,0.1); }
        .progress-bar-fill {
            height: 100%;
            width: 0%;
            background: linear-gradient(90deg, var(--primary), #ec4899);
            border-radius: 10px;
            transition: width 0.6s cubic-bezier(0.4, 0, 0.2, 1);
        }

        /* Input Area */
        .input-group {
            position: relative;
            display: flex;
            gap: 10px;
            margin-bottom: 2rem;
        }

        input[type="text"] {
            width: 100%;
            padding: 16px 20px;
            border-radius: 16px;
            border: 2px solid transparent;
            background: rgba(255,255,255,0.5);
            font-size: 1rem;
            color: var(--text-main);
            transition: all 0.3s;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05);
        }
        .dark-mode input[type="text"] { background: rgba(0,0,0,0.2); }

        input[type="text"]:focus {
            background: rgba(255,255,255,0.9);
            border-color: var(--primary);
            box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.2);
        }
        .dark-mode input[type="text"]:focus { background: rgba(0,0,0,0.4); }

        .add-btn {
            background: var(--primary);
            color: white;
            border: none;
            border-radius: 16px;
            padding: 0 24px;
            font-size: 1.5rem;
            cursor: pointer;
            transition: transform 0.2s, background 0.3s;
            box-shadow: 0 4px 12px rgba(99, 102, 241, 0.4);
        }
        .add-btn:hover { background: var(--primary-hover); transform: translateY(-2px); }
        .add-btn:active { transform: translateY(0); }

        /* Filter Tabs */
        .filters {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-bottom: 1.5rem;
        }
        .filter-btn {
            background: transparent;
            border: none;
            color: var(--text-secondary);
            font-weight: 600;
            cursor: pointer;
            padding: 8px 16px;
            border-radius: 20px;
            transition: all 0.3s;
        }
        .filter-btn.active {
            background: rgba(99, 102, 241, 0.1);
            color: var(--primary);
        }
        .dark-mode .filter-btn.active { background: rgba(129, 140, 248, 0.2); }

        /* --- 4. TASK LIST --- */
        ul {
            list-style: none;
            padding: 0;
            margin: 0;
            max-height: 400px;
            overflow-y: auto;
            padding-right: 5px;
        }
        /* Custom Scrollbar */
        ul::-webkit-scrollbar { width: 6px; }
        ul::-webkit-scrollbar-thumb { background: rgba(0,0,0,0.1); border-radius: 10px; }
        .dark-mode ul::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.1); }

        .task-item {
            background: rgba(255,255,255,0.4);
            margin-bottom: 12px;
            padding: 16px;
            border-radius: 16px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            border: 1px solid transparent;
            animation: slideIn 0.3s ease forwards;
        }
        .dark-mode .task-item { background: rgba(255,255,255,0.03); }

        .task-item:hover {
            transform: translateX(4px);
            border-color: rgba(99, 102, 241, 0.3);
            background: rgba(255,255,255,0.6);
        }
        .dark-mode .task-item:hover { background: rgba(255,255,255,0.07); }

        .task-content {
            display: flex;
            align-items: center;
            gap: 12px;
            flex-grow: 1;
            cursor: pointer;
        }

        .task-text {
            font-size: 1rem;
            transition: all 0.3s;
            word-break: break-all;
        }

        .completed .task-text {
            text-decoration: line-through;
            color: var(--text-secondary);
            opacity: 0.7;
        }

        /* Custom Checkbox */
        .checkbox {
            width: 24px;
            height: 24px;
            border: 2px solid var(--primary);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s;
            flex-shrink: 0;
        }
        .completed .checkbox {
            background: var(--primary);
            transform: scale(1.1);
        }
        .checkbox::after {
            content: '✓';
            color: white;
            font-size: 14px;
            display: none;
        }
        .completed .checkbox::after { display: block; }

        .delete-btn {
            background: none;
            border: none;
            color: var(--text-secondary);
            cursor: pointer;
            padding: 8px;
            border-radius: 8px;
            transition: all 0.2s;
            opacity: 0;
            margin-left: 10px;
        }
        .task-item:hover .delete-btn { opacity: 1; }
        .delete-btn:hover {
            color: var(--danger);
            background: rgba(239, 68, 68, 0.1);
        }

        .empty-state {
            text-align: center;
            color: var(--text-secondary);
            margin-top: 2rem;
            display: none;
        }
        .empty-state img { width: 120px; opacity: 0.5; margin-bottom: 1rem; }

        @keyframes slideIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        @keyframes deleteAnim {
            to { opacity: 0; transform: translateX(50px); height: 0; margin: 0; padding: 0; }
        }

        /* Confetti Canvas */
        #confetti-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 100;
        }
    </style>
</head>
<body>

    <canvas id="confetti-canvas"></canvas>

    <div class="app-container">
        <header>
            <h1>Мои Задачи</h1>
            <button class="theme-toggle" id="themeToggle">🌙</button>
        </header>

        <div class="progress-container">
            <div class="progress-info">
                <span id="progress-text">Прогресс</span>
                <span id="progress-percent">0%</span>
            </div>
            <div class="progress-bar-bg">
                <div class="progress-bar-fill" id="progressBar"></div>
            </div>
        </div>

        <div class="input-group">
            <input type="text" id="taskInput" placeholder="Что нужно сделать сегодня?">
            <button class="add-btn" id="addBtn">+</button>
        </div>

        <div class="filters">
            <button class="filter-btn active" data-filter="all">Все</button>
            <button class="filter-btn" data-filter="active">В процессе</button>
            <button class="filter-btn" data-filter="completed">Готово</button>
        </div>

        <ul id="taskList">
            </ul>

        <div class="empty-state" id="emptyState">
            <div style="font-size: 3rem;">📝</div>
            <p>Задач пока нет. Добавь первую!</p>
        </div>
    </div>

    <script>
        /* --- JAVASCRIPT LOGIC --- */

        // Elements
        const taskInput = document.getElementById('taskInput');
        const addBtn = document.getElementById('addBtn');
        const taskList = document.getElementById('taskList');
        const emptyState = document.getElementById('emptyState');
        const progressBar = document.getElementById('progressBar');
        const progressPercent = document.getElementById('progressPercent');
        const themeToggle = document.getElementById('themeToggle');
        const filterBtns = document.querySelectorAll('.filter-btn');

        // State
        let tasks = JSON.parse(localStorage.getItem('tasks')) || [];
        let currentFilter = 'all';

        // --- INIT ---
        document.addEventListener('DOMContentLoaded', () => {
            renderTasks();
            updateStats();
            applyTheme();
        });

        // --- THEME ---
        themeToggle.addEventListener('click', () => {
            document.body.classList.toggle('dark-mode');
            const isDark = document.body.classList.contains('dark-mode');
            localStorage.setItem('theme', isDark ? 'dark' : 'light');
            themeToggle.textContent = isDark ? '☀️' : '🌙';
        });

        function applyTheme() {
            const savedTheme = localStorage.getItem('theme');
            if (savedTheme === 'dark') {
                document.body.classList.add('dark-mode');
                themeToggle.textContent = '☀️';
            }
        }

        // --- ADD TASK ---
        function addTask() {
            const text = taskInput.value.trim();
            if (!text) return;

            const newTask = {
                id: Date.now(),
                text: text,
                completed: false,
                createdAt: new Date().toISOString()
            };

            tasks.unshift(newTask); // Add to top
            saveAndRender();
            taskInput.value = '';
            taskInput.focus();
        }

        addBtn.addEventListener('click', addTask);
        taskInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') addTask();
        });

        // --- TOGGLE & DELETE ---
        window.toggleTask = function(id) {
            tasks = tasks.map(task => {
                if (task.id === id) {
                    const newStatus = !task.completed;
                    if(newStatus) fireConfetti(); // Boom!
                    return { ...task, completed: newStatus };
                }
                return task;
            });
            saveAndRender();
        };

        window.deleteTask = function(id) {
            const taskEl = document.querySelector(`li[data-id="${id}"]`);
            if(taskEl) {
                taskEl.style.animation = 'deleteAnim 0.4s forwards';
                setTimeout(() => {
                    tasks = tasks.filter(task => task.id !== id);
                    saveAndRender();
                }, 400);
            }
        };

        // --- FILTERS ---
        filterBtns.forEach(btn => {
            btn.addEventListener('click', () => {
                filterBtns.forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
                currentFilter = btn.dataset.filter;
                renderTasks();
            });
        });

        // --- RENDER ---
        function saveAndRender() {
            localStorage.setItem('tasks', JSON.stringify(tasks));
            renderTasks();
            updateStats();
        }

        function renderTasks() {
            taskList.innerHTML = '';
            
            let filteredTasks = tasks;
            if (currentFilter === 'active') filteredTasks = tasks.filter(t => !t.completed);
            if (currentFilter === 'completed') filteredTasks = tasks.filter(t => t.completed);

            if (filteredTasks.length === 0) {
                emptyState.style.display = 'block';
            } else {
                emptyState.style.display = 'none';
                filteredTasks.forEach(task => {
                    const li = document.createElement('li');
                    li.className = `task-item ${task.completed ? 'completed' : ''}`;
                    li.dataset.id = task.id;
                    li.innerHTML = `
                        <div class="task-content" onclick="toggleTask(${task.id})">
                            <div class="checkbox"></div>
                            <span class="task-text">${escapeHtml(task.text)}</span>
                        </div>
                        <button class="delete-btn" onclick="deleteTask(${task.id})">
                            <svg width="20" height="20" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg>
                        </button>
                    `;
                    taskList.appendChild(li);
                });
            }
        }

        function updateStats() {
            const total = tasks.length;
            const completed = tasks.filter(t => t.completed).length;
            const percent = total === 0 ? 0 : Math.round((completed / total) * 100);
            
            progressBar.style.width = `${percent}%`;
            progressPercent.textContent = `${percent}%`;
        }

        function escapeHtml(text) {
            return text.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/"/g, "&quot;").replace(/'/g, "&#039;");
        }

        // --- CONFETTI ENGINE (Pure JS, Lightweight) ---
        const canvas = document.getElementById('confetti-canvas');
        const ctx = canvas.getContext('2d');
        let particles = [];

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function fireConfetti() {
            const colors = ['#6366f1', '#ec4899', '#10b981', '#f59e0b', '#3b82f6'];
            // Create particles from center
            for (let i = 0; i < 60; i++) {
                particles.push({
                    x: window.innerWidth / 2,
                    y: window.innerHeight / 2,
                    vx: (Math.random() - 0.5) * 20,
                    vy: (Math.random() - 1) * 20,
                    size: Math.random() * 8 + 4,
                    color: colors[Math.floor(Math.random() * colors.length)],
                    life: 100,
                    gravity: 0.5
                });
            }
            if (!isAnimating) requestAnimationFrame(animateConfetti);
        }

        let isAnimating = false;
        function animateConfetti() {
            if (particles.length === 0) {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                isAnimating = false;
                return;
            }
            isAnimating = true;
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            for (let i = 0; i < particles.length; i++) {
                let p = particles[i];
                p.x += p.vx;
                p.y += p.vy;
                p.vy += p.gravity;
                p.life -= 1.5;
                p.size *= 0.98;

                ctx.fillStyle = p.color;
                ctx.globalAlpha = p.life / 100;
                ctx.beginPath();
                ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                ctx.fill();

                if (p.life <= 0) {
                    particles.splice(i, 1);
                    i--;
                }
            }
            requestAnimationFrame(animateConfetti);
        }
    </script>
</body>
</html>
