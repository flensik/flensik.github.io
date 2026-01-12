<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Задачник</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: var(--tg-theme-bg-color, #ffffff);
            color: var(--tg-theme-text-color, #000000);
            padding: 16px;
            padding-bottom: 80px;
        }

        .header {
            margin-bottom: 20px;
        }

        h1 {
            font-size: 24px;
            font-weight: 600;
            margin-bottom: 8px;
        }

        .stats {
            font-size: 14px;
            color: var(--tg-theme-hint-color, #999);
        }

        .add-task-form {
            background: var(--tg-theme-secondary-bg-color, #f4f4f5);
            padding: 16px;
            border-radius: 12px;
            margin-bottom: 20px;
        }

        .form-group {
            margin-bottom: 12px;
        }

        label {
            display: block;
            font-size: 14px;
            font-weight: 500;
            margin-bottom: 6px;
        }

        input[type="text"],
        input[type="datetime-local"],
        textarea {
            width: 100%;
            padding: 10px 12px;
            border: 1px solid var(--tg-theme-hint-color, #ddd);
            border-radius: 8px;
            font-size: 15px;
            background: var(--tg-theme-bg-color, #fff);
            color: var(--tg-theme-text-color, #000);
        }

        textarea {
            resize: vertical;
            min-height: 60px;
        }

        .btn {
            width: 100%;
            padding: 12px;
            border: none;
            border-radius: 8px;
            font-size: 15px;
            font-weight: 600;
            cursor: pointer;
            transition: opacity 0.2s;
        }

        .btn:active {
            opacity: 0.7;
        }

        .btn-primary {
            background: var(--tg-theme-button-color, #3390ec);
            color: var(--tg-theme-button-text-color, #fff);
        }

        .tasks-list {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .task-item {
            background: var(--tg-theme-secondary-bg-color, #f4f4f5);
            padding: 14px;
            border-radius: 12px;
            display: flex;
            gap: 12px;
            align-items: start;
        }

        .task-checkbox {
            width: 20px;
            height: 20px;
            margin-top: 2px;
            cursor: pointer;
        }

        .task-content {
            flex: 1;
        }

        .task-title {
            font-weight: 600;
            font-size: 15px;
            margin-bottom: 4px;
        }

        .task-deadline {
            font-size: 13px;
            color: var(--tg-theme-hint-color, #999);
            margin-bottom: 4px;
        }

        .task-description {
            font-size: 14px;
            color: var(--tg-theme-text-color, #000);
        }

        .task-item.completed .task-title,
        .task-item.completed .task-description {
            text-decoration: line-through;
            opacity: 0.5;
        }

        .task-delete {
            background: #ff3b30;
            color: white;
            border: none;
            padding: 6px 12px;
            border-radius: 6px;
            font-size: 13px;
            cursor: pointer;
        }

        .overdue {
            color: #ff3b30;
            font-weight: 600;
        }

        .empty-state {
            text-align: center;
            padding: 40px 20px;
            color: var(--tg-theme-hint-color, #999);
        }

        .filter-tabs {
            display: flex;
            gap: 8px;
            margin-bottom: 16px;
        }

        .filter-tab {
            flex: 1;
            padding: 8px;
            background: var(--tg-theme-secondary-bg-color, #f4f4f5);
            border: none;
            border-radius: 8px;
            font-size: 14px;
            cursor: pointer;
            color: var(--tg-theme-text-color, #000);
        }

        .filter-tab.active {
            background: var(--tg-theme-button-color, #3390ec);
            color: var(--tg-theme-button-text-color, #fff);
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>📝 Мои задачи</h1>
        <div class="stats" id="stats">Всего задач: 0</div>
    </div>

    <div class="add-task-form">
        <div class="form-group">
            <label for="taskTitle">Название задачи</label>
            <input type="text" id="taskTitle" placeholder="Например: Купить продукты">
        </div>
        <div class="form-group">
            <label for="taskDeadline">Срок выполнения</label>
            <input type="datetime-local" id="taskDeadline">
        </div>
        <div class="form-group">
            <label for="taskDesc">Описание (необязательно)</label>
            <textarea id="taskDesc" placeholder="Детали задачи..."></textarea>
        </div>
        <button class="btn btn-primary" onclick="addTask()">Добавить задачу</button>
    </div>

    <div class="filter-tabs">
        <button class="filter-tab active" onclick="setFilter('all')">Все</button>
        <button class="filter-tab" onclick="setFilter('active')">Активные</button>
        <button class="filter-tab" onclick="setFilter('completed')">Завершённые</button>
    </div>

    <div class="tasks-list" id="tasksList"></div>

    <script>
        let tg = window.Telegram.WebApp;
        tg.expand();

        let currentFilter = 'all';
        let db;

        // Инициализация IndexedDB
        function initDB() {
            return new Promise((resolve, reject) => {
                const request = indexedDB.open('TasksDB', 1);
                
                request.onerror = () => reject(request.error);
                request.onsuccess = () => {
                    db = request.result;
                    resolve(db);
                };
                
                request.onupgradeneeded = (e) => {
                    const db = e.target.result;
                    if (!db.objectStoreNames.contains('tasks')) {
                        const store = db.createObjectStore('tasks', { keyPath: 'id', autoIncrement: true });
                        store.createIndex('deadline', 'deadline', { unique: false });
                        store.createIndex('completed', 'completed', { unique: false });
                    }
                };
            });
        }

        // Добавить задачу
        async function addTask() {
            const title = document.getElementById('taskTitle').value.trim();
            const deadline = document.getElementById('taskDeadline').value;
            const description = document.getElementById('taskDesc').value.trim();

            if (!title) {
                tg.showAlert('Введите название задачи');
                return;
            }

            if (!deadline) {
                tg.showAlert('Укажите срок выполнения');
                return;
            }

            const task = {
                title,
                deadline,
                description,
                completed: false,
                createdAt: new Date().toISOString()
            };

            const transaction = db.transaction(['tasks'], 'readwrite');
            const store = transaction.objectStore('tasks');
            
            try {
                await store.add(task);
                document.getElementById('taskTitle').value = '';
                document.getElementById('taskDeadline').value = '';
                document.getElementById('taskDesc').value = '';
                loadTasks();
                tg.HapticFeedback.notificationOccurred('success');
            } catch (error) {
                tg.showAlert('Ошибка при добавлении задачи');
            }
        }

        // Загрузить задачи
        async function loadTasks() {
            const transaction = db.transaction(['tasks'], 'readonly');
            const store = transaction.objectStore('tasks');
            const request = store.getAll();

            request.onsuccess = () => {
                let tasks = request.result;
                
                // Фильтрация
                if (currentFilter === 'active') {
                    tasks = tasks.filter(t => !t.completed);
                } else if (currentFilter === 'completed') {
                    tasks = tasks.filter(t => t.completed);
                }

                // Сортировка по дедлайну
                tasks.sort((a, b) => new Date(a.deadline) - new Date(b.deadline));

                displayTasks(tasks);
                updateStats(request.result);
            };
        }

        // Отобразить задачи
        function displayTasks(tasks) {
            const container = document.getElementById('tasksList');
            
            if (tasks.length === 0) {
                container.innerHTML = '<div class="empty-state">Нет задач</div>';
                return;
            }

            container.innerHTML = tasks.map(task => {
                const deadline = new Date(task.deadline);
                const now = new Date();
                const isOverdue = deadline < now && !task.completed;
                
                const dateStr = deadline.toLocaleString('ru-RU', {
                    day: 'numeric',
                    month: 'short',
                    hour: '2-digit',
                    minute: '2-digit'
                });

                return `
                    <div class="task-item ${task.completed ? 'completed' : ''}">
                        <input type="checkbox" class="task-checkbox" 
                            ${task.completed ? 'checked' : ''} 
                            onchange="toggleTask(${task.id})">
                        <div class="task-content">
                            <div class="task-title">${task.title}</div>
                            <div class="task-deadline ${isOverdue ? 'overdue' : ''}">
                                ${isOverdue ? '⚠️ ' : '📅 '}${dateStr}
                            </div>
                            ${task.description ? `<div class="task-description">${task.description}</div>` : ''}
                        </div>
                        <button class="task-delete" onclick="deleteTask(${task.id})">🗑</button>
                    </div>
                `;
            }).join('');
        }

        // Обновить статистику
        function updateStats(tasks) {
            const active = tasks.filter(t => !t.completed).length;
            const completed = tasks.filter(t => t.completed).length;
            document.getElementById('stats').textContent = 
                `Всего: ${tasks.length} | Активных: ${active} | Завершённых: ${completed}`;
        }

        // Переключить статус задачи
        async function toggleTask(id) {
            const transaction = db.transaction(['tasks'], 'readwrite');
            const store = transaction.objectStore('tasks');
            const request = store.get(id);

            request.onsuccess = () => {
                const task = request.result;
                task.completed = !task.completed;
                store.put(task);
                loadTasks();
                tg.HapticFeedback.impactOccurred('light');
            };
        }

        // Удалить задачу
        async function deleteTask(id) {
            tg.showConfirm('Удалить эту задачу?', (confirmed) => {
                if (confirmed) {
                    const transaction = db.transaction(['tasks'], 'readwrite');
                    const store = transaction.objectStore('tasks');
                    store.delete(id);
                    loadTasks();
                    tg.HapticFeedback.notificationOccurred('success');
                }
            });
        }

        // Установить фильтр
        function setFilter(filter) {
            currentFilter = filter;
            document.querySelectorAll('.filter-tab').forEach(tab => {
                tab.classList.remove('active');
            });
            event.target.classList.add('active');
            loadTasks();
        }

        // Инициализация
        initDB().then(() => {
            loadTasks();
        }).catch(error => {
            tg.showAlert('Ошибка инициализации базы данных');
        });
    </script>
</body>
</html>
