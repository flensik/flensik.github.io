<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NFT Case Opener</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
            color: white;
            overflow-x: hidden;
            min-height: 100vh;
        }

        .container {
            max-width: 500px;
            margin: 0 auto;
            padding: 20px;
        }

        .header {
            text-align: center;
            margin-bottom: 30px;
            animation: fadeInDown 0.6s ease;
        }

        .header h1 {
            font-size: 28px;
            font-weight: 700;
            margin-bottom: 10px;
            text-shadow: 0 4px 10px rgba(0,0,0,0.5);
            background: linear-gradient(45deg, #00d2ff, #3a7bd5);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .balance {
            background: rgba(255,255,255,0.1);
            backdrop-filter: blur(10px);
            padding: 12px 25px;
            border-radius: 50px;
            display: inline-flex;
            align-items: center;
            gap: 10px;
            font-size: 20px;
            font-weight: 600;
            box-shadow: 0 8px 20px rgba(0,0,0,0.3);
            border: 1px solid rgba(255,255,255,0.2);
        }

        .coin-icon {
            font-size: 24px;
            animation: spin 3s linear infinite;
        }

        #canvas-container {
            width: 100%;
            height: 350px;
            margin: 20px 0;
            border-radius: 20px;
            overflow: hidden;
            box-shadow: 0 20px 60px rgba(0,0,0,0.5);
            position: relative;
        }

        .case-price {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0,0,0,0.7);
            backdrop-filter: blur(10px);
            padding: 12px 25px;
            border-radius: 30px;
            font-size: 18px;
            font-weight: 600;
            border: 1px solid rgba(255,255,255,0.2);
            z-index: 10;
        }

        .open-button {
            width: 100%;
            padding: 18px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
            border-radius: 15px;
            color: white;
            font-size: 20px;
            font-weight: 700;
            cursor: pointer;
            box-shadow: 0 10px 30px rgba(102, 126, 234, 0.4);
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 1px;
            border: 1px solid rgba(255,255,255,0.2);
        }

        .open-button:active {
            transform: scale(0.95);
        }

        .open-button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.95);
            display: none;
            align-items: center;
            justify-content: center;
            z-index: 1000;
            animation: fadeIn 0.3s ease;
        }

        .overlay.active {
            display: flex;
        }

        .reveal-container {
            text-align: center;
            animation: scaleIn 0.5s ease;
        }

        #nft-canvas {
            width: 300px;
            height: 300px;
            border-radius: 20px;
            margin: 0 auto;
        }

        .nft-info {
            margin-top: 30px;
            animation: fadeInUp 0.8s ease 0.3s both;
        }

        .nft-name {
            font-size: 28px;
            font-weight: 700;
            margin-bottom: 15px;
            text-shadow: 0 4px 10px rgba(0,0,0,0.5);
        }

        .nft-rarity {
            display: inline-block;
            padding: 10px 25px;
            border-radius: 25px;
            font-size: 14px;
            font-weight: 600;
            margin-bottom: 20px;
            text-transform: uppercase;
            box-shadow: 0 8px 20px rgba(0,0,0,0.4);
        }

        .rarity-common { background: linear-gradient(135deg, #95a5a6, #7f8c8d); }
        .rarity-rare { background: linear-gradient(135deg, #3498db, #2980b9); }
        .rarity-epic { background: linear-gradient(135deg, #9b59b6, #8e44ad); }
        .rarity-legendary { background: linear-gradient(135deg, #f39c12, #e67e22); }

        .close-button {
            margin-top: 30px;
            padding: 15px 40px;
            background: rgba(255,255,255,0.1);
            border: 2px solid rgba(255,255,255,0.3);
            border-radius: 30px;
            color: white;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            backdrop-filter: blur(10px);
        }

        .close-button:hover {
            background: rgba(255,255,255,0.2);
            transform: scale(1.05);
        }

        .inventory {
            margin-top: 40px;
        }

        .inventory h2 {
            font-size: 24px;
            margin-bottom: 20px;
            text-align: center;
            text-shadow: 0 4px 10px rgba(0,0,0,0.5);
        }

        .inventory-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
        }

        .inventory-item {
            background: rgba(255,255,255,0.05);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 15px;
            text-align: center;
            transition: transform 0.3s ease;
            cursor: pointer;
            border: 1px solid rgba(255,255,255,0.1);
            position: relative;
            overflow: hidden;
        }

        .inventory-item:hover {
            transform: scale(1.05);
            background: rgba(255,255,255,0.1);
        }

        .inventory-item img {
            width: 100%;
            height: 120px;
            object-fit: cover;
            border-radius: 10px;
            margin-bottom: 10px;
        }

        .inventory-item .name {
            font-size: 14px;
            font-weight: 600;
            margin-bottom: 5px;
        }

        .inventory-item .value {
            font-size: 12px;
            opacity: 0.8;
        }

        .particles {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 999;
        }

        .particle {
            position: absolute;
            width: 4px;
            height: 4px;
            background: rgba(255,255,255,0.8);
            border-radius: 50%;
            animation: particleFall 3s linear infinite;
            opacity: 0;
            box-shadow: 0 0 10px rgba(255,255,255,0.5);
        }

        @keyframes fadeInDown {
            from {
                opacity: 0;
                transform: translateY(-30px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        @keyframes fadeInUp {
            from {
                opacity: 0;
                transform: translateY(30px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        @keyframes scaleIn {
            from {
                opacity: 0;
                transform: scale(0.5);
            }
            to {
                opacity: 1;
                transform: scale(1);
            }
        }

        @keyframes particleFall {
            0% {
                opacity: 1;
                transform: translateY(0) rotate(0deg);
            }
            100% {
                opacity: 0;
                transform: translateY(100vh) rotate(360deg);
            }
        }
    </style>
</head>
<body>
    <div class="particles" id="particles"></div>

    <div class="container">
        <div class="header">
            <h1>🎁 NFT MYSTERY BOX</h1>
            <div class="balance">
                <span class="coin-icon">💎</span>
                <span id="balance">1000</span>
            </div>
        </div>

        <div id="canvas-container">
            <div class="case-price">💎 100</div>
        </div>

        <button class="open-button" id="openButton">Открыть кейс</button>

        <div class="inventory">
            <h2>📦 Моя коллекция</h2>
            <div class="inventory-grid" id="inventoryGrid"></div>
        </div>
    </div>

    <div class="overlay" id="overlay">
        <div class="reveal-container">
            <div id="nft-canvas"></div>
            <div class="nft-info" id="nftInfo"></div>
        </div>
    </div>

    <script>
        let tg = window.Telegram.WebApp;
        tg.expand();

        let balance = 1000;
        let inventory = [];
        let db;
        let scene, camera, renderer, caseModel;
        let nftScene, nftCamera, nftRenderer, nftModel;

        const nftItems = [
            { 
                name: 'Anonymous Telegram', 
                rarity: 'common', 
                value: 50,
                image: 'https://nft.fragment.com/preview/anonymous.jpg',
                color: 0x95a5a6
            },
            { 
                name: 'Telegram Premium', 
                rarity: 'common', 
                value: 50,
                image: 'https://nft.fragment.com/preview/premium.jpg',
                color: 0x3498db
            },
            { 
                name: 'Raccoon', 
                rarity: 'rare', 
                value: 150,
                image: 'https://nft.fragment.com/preview/raccoon.jpg',
                color: 0x8e44ad
            },
            { 
                name: 'Pepe', 
                rarity: 'rare', 
                value: 150,
                image: 'https://nft.fragment.com/preview/pepe.jpg',
                color: 0x27ae60
            },
            { 
                name: 'Duck', 
                rarity: 'epic', 
                value: 300,
                image: 'https://nft.fragment.com/preview/duck.jpg',
                color: 0xf39c12
            },
            { 
                name: 'Tiger', 
                rarity: 'epic', 
                value: 300,
                image: 'https://nft.fragment.com/preview/tiger.jpg',
                color: 0xe67e22
            },
            { 
                name: 'Gotham', 
                rarity: 'legendary', 
                value: 500,
                image: 'https://nft.fragment.com/preview/gotham.jpg',
                color: 0x1a1a2e
            },
            { 
                name: 'Founder', 
                rarity: 'legendary', 
                value: 500,
                image: 'https://nft.fragment.com/preview/founder.jpg',
                color: 0xe74c3c
            }
        ];

        function initDB() {
            return new Promise((resolve, reject) => {
                const request = indexedDB.open('NFTGameDB', 1);
                
                request.onerror = () => reject(request.error);
                request.onsuccess = () => {
                    db = request.result;
                    resolve(db);
                };
                
                request.onupgradeneeded = (e) => {
                    const db = e.target.result;
                    if (!db.objectStoreNames.contains('gameData')) {
                        db.createObjectStore('gameData', { keyPath: 'key' });
                    }
                };
            });
        }

        async function saveData() {
            const transaction = db.transaction(['gameData'], 'readwrite');
            const store = transaction.objectStore('gameData');
            
            await store.put({ key: 'balance', value: balance });
            await store.put({ key: 'inventory', value: inventory });
        }

        async function loadData() {
            const transaction = db.transaction(['gameData'], 'readonly');
            const store = transaction.objectStore('gameData');
            
            const balanceReq = store.get('balance');
            const inventoryReq = store.get('inventory');
            
            balanceReq.onsuccess = () => {
                if (balanceReq.result) {
                    balance = balanceReq.result.value;
                    updateBalance();
                }
            };
            
            inventoryReq.onsuccess = () => {
                if (inventoryReq.result) {
                    inventory = inventoryReq.result.value;
                    updateInventory();
                }
            };
        }

        function init3DCase() {
            const container = document.getElementById('canvas-container');
            
            scene = new THREE.Scene();
            camera = new THREE.PerspectiveCamera(45, container.clientWidth / container.clientHeight, 0.1, 1000);
            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            renderer.setSize(container.clientWidth, container.clientHeight);
            renderer.setClearColor(0x000000, 0);
            container.appendChild(renderer.domElement);

            const geometry = new THREE.BoxGeometry(2, 2, 2);
            const materials = [
                new THREE.MeshPhongMaterial({ color: 0x667eea, shininess: 100 }),
                new THREE.MeshPhongMaterial({ color: 0x764ba2, shininess: 100 }),
                new THREE.MeshPhongMaterial({ color: 0x667eea, shininess: 100 }),
                new THREE.MeshPhongMaterial({ color: 0x764ba2, shininess: 100 }),
                new THREE.MeshPhongMaterial({ color: 0x667eea, shininess: 100 }),
                new THREE.MeshPhongMaterial({ color: 0x764ba2, shininess: 100 })
            ];
            
            caseModel = new THREE.Mesh(geometry, materials);
            scene.add(caseModel);

            const light1 = new THREE.PointLight(0xffffff, 1, 100);
            light1.position.set(5, 5, 5);
            scene.add(light1);

            const light2 = new THREE.PointLight(0x667eea, 0.5, 100);
            light2.position.set(-5, -5, 5);
            scene.add(light2);

            const ambientLight = new THREE.AmbientLight(0x404040);
            scene.add(ambientLight);

            camera.position.z = 5;

            animate3DCase();
        }

        function animate3DCase() {
            requestAnimationFrame(animate3DCase);
            
            caseModel.rotation.x += 0.005;
            caseModel.rotation.y += 0.01;
            
            renderer.render(scene, camera);
        }

        function init3DNFT(nftData) {
            const container = document.getElementById('nft-canvas');
            container.innerHTML = '';
            
            nftScene = new THREE.Scene();
            nftCamera = new THREE.PerspectiveCamera(45, 1, 0.1, 1000);
            nftRenderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            nftRenderer.setSize(300, 300);
            nftRenderer.setClearColor(0x000000, 0);
            container.appendChild(nftRenderer.domElement);

            const geometry = new THREE.BoxGeometry(2.5, 3.5, 0.3);
            const texture = new THREE.TextureLoader().load(nftData.image);
            const material = new THREE.MeshPhongMaterial({ 
                map: texture,
                shininess: 100,
                emissive: nftData.color,
                emissiveIntensity: 0.2
            });
            
            nftModel = new THREE.Mesh(geometry, material);
            nftScene.add(nftModel);

            const edgesGeometry = new THREE.EdgesGeometry(geometry);
            const edgesMaterial = new THREE.LineBasicMaterial({ color: 0xffffff, linewidth: 2 });
            const edges = new THREE.LineSegments(edgesGeometry, edgesMaterial);
            nftModel.add(edges);

            const light1 = new THREE.PointLight(0xffffff, 1, 100);
            light1.position.set(5, 5, 5);
            nftScene.add(light1);

            const light2 = new THREE.PointLight(nftData.color, 0.8, 100);
            light2.position.set(-5, 0, 5);
            nftScene.add(light2);

            const ambientLight = new THREE.AmbientLight(0x404040);
            nftScene.add(ambientLight);

            nftCamera.position.z = 5;

            animate3DNFT();
        }

        function animate3DNFT() {
            requestAnimationFrame(animate3DNFT);
            
            nftModel.rotation.y += 0.01;
            
            nftRenderer.render(nftScene, nftCamera);
        }

        function updateBalance() {
            document.getElementById('balance').textContent = balance;
        }

        function createParticles() {
            const container = document.getElementById('particles');
            for (let i = 0; i < 50; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                particle.style.left = Math.random() * 100 + '%';
                particle.style.animationDelay = Math.random() * 3 + 's';
                particle.style.animationDuration = (Math.random() * 2 + 2) + 's';
                container.appendChild(particle);
            }
        }

        function openCase() {
            if (balance < 100) {
                tg.showAlert('Недостаточно монет! 💎');
                return;
            }

            balance -= 100;
            updateBalance();
            
            const openButton = document.getElementById('openButton');
            openButton.disabled = true;

            caseModel.rotation.x = 0;
            caseModel.rotation.y = 0;
            
            let speed = 0.1;
            const spinInterval = setInterval(() => {
                caseModel.rotation.y += speed;
                speed *= 1.05;
            }, 16);

            tg.HapticFeedback.impactOccurred('heavy');

            setTimeout(() => {
                clearInterval(spinInterval);
                revealNFT();
                openButton.disabled = false;
            }, 2000);
        }

        function revealNFT() {
            const rand = Math.random();
            let selectedNFT;

            if (rand < 0.5) {
                selectedNFT = nftItems.filter(i => i.rarity === 'common')[Math.floor(Math.random() * 2)];
            } else if (rand < 0.8) {
                selectedNFT = nftItems.filter(i => i.rarity === 'rare')[Math.floor(Math.random() * 2)];
            } else if (rand < 0.95) {
                selectedNFT = nftItems.filter(i => i.rarity === 'epic')[Math.floor(Math.random() * 2)];
            } else {
                selectedNFT = nftItems.filter(i => i.rarity === 'legendary')[Math.floor(Math.random() * 2)];
            }

            inventory.push({ ...selectedNFT, id: Date.now() });
            saveData();
            updateInventory();

            const overlay = document.getElementById('overlay');
            const nftInfo = document.getElementById('nftInfo');

            const rarityNames = {
                common: 'Обычный',
                rare: 'Редкий',
                epic: 'Эпический',
                legendary: 'Легендарный'
            };

            init3DNFT(selecte
