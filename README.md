<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VoxelCraft 3D - Edición Completa</title>
    <style>
        * {
            box-sizing: border-box;
            user-select: none;
            -webkit-user-select: none;
            margin: 0;
            padding: 0;
        }

        body, html {
            width: 100%;
            height: 100%;
            overflow: hidden;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #000;
            color: #fff;
        }

        #canvas-container {
            width: 100%;
            height: 100%;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 1;
        }

        #hud {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 10;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }

        #crosshair {
            position: absolute;
            top: 50%;
            left: 50%;
            width: 16px;
            height: 16px;
            transform: translate(-50%, -50%);
            pointer-events: none;
        }

        #crosshair::before, #crosshair::after {
            content: '';
            position: absolute;
            background-color: rgba(255, 255, 255, 0.8);
            box-shadow: 0 0 2px rgba(0,0,0,0.8);
        }

        #crosshair::before {
            top: 7px;
            left: 0;
            width: 16px;
            height: 2px;
        }

        #crosshair::after {
            top: 0;
            left: 7px;
            width: 2px;
            height: 16px;
        }

        #hotbar-container {
            align-self: center;
            margin-bottom: 20px;
            background: rgba(0, 0, 0, 0.65);
            border: 3px solid #4a4a4a;
            border-radius: 8px;
            padding: 6px;
            display: flex;
            gap: 6px;
            pointer-events: auto;
            backdrop-filter: blur(4px);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.5);
        }

        .slot {
            width: 52px;
            height: 52px;
            background: rgba(60, 60, 60, 0.6);
            border: 2px solid #2a2a2a;
            border-radius: 4px;
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
            cursor: pointer;
            transition: border-color 0.15s, background-color 0.15s;
        }

        .slot.active {
            border-color: #ffffff;
            background: rgba(100, 100, 100, 0.8);
            box-shadow: inset 0 0 8px rgba(255,255,255,0.4);
        }

        .slot-num {
            position: absolute;
            top: 2px;
            left: 4px;
            font-size: 10px;
            font-weight: bold;
            color: #aaa;
            text-shadow: 1px 1px 1px #000;
        }

        .item-count {
            position: absolute;
            bottom: 2px;
            right: 4px;
            font-size: 13px;
            font-weight: bold;
            color: #fff;
            text-shadow: 1px 1px 2px #000;
        }

        .item-icon {
            width: 32px;
            height: 32px;
            background-size: cover;
            image-rendering: pixelated;
            border-radius: 2px;
        }

        #top-info {
            padding: 15px;
            display: flex;
            justify-content: space-between;
            font-size: 14px;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.8);
        }

        .badge {
            background: rgba(0, 0, 0, 0.5);
            padding: 6px 12px;
            border-radius: 4px;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        #toast-notification {
            position: absolute;
            top: 70px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0, 0, 0, 0.85);
            border: 1px solid #55ff55;
            color: #55ff55;
            padding: 8px 16px;
            border-radius: 20px;
            font-weight: bold;
            font-size: 14px;
            display: none;
            z-index: 50;
            pointer-events: none;
            box-shadow: 0 4px 12px rgba(0,0,0,0.5);
        }

        .overlay-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.75);
            z-index: 100;
            display: flex;
            justify-content: center;
            align-items: center;
            backdrop-filter: blur(5px);
        }

        .panel {
            background: #2b2b2b;
            border: 3px solid #555;
            border-radius: 8px;
            padding: 25px;
            width: 90%;
            max-width: 600px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.8);
            text-align: center;
        }

        h1, h2 {
            color: #55ff55;
            text-shadow: 2px 2px 0px #000;
            margin-bottom: 15px;
        }

        p {
            margin-bottom: 12px;
            line-height: 1.5;
            color: #ddd;
        }

        .btn {
            background: #4a4a4a;
            border: 2px solid #777;
            color: white;
            padding: 10px 20px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            border-radius: 4px;
            display: block;
            margin: 10px auto;
            width: 80%;
            transition: all 0.1s;
        }

        .btn:hover {
            background: #666;
            border-color: #aaa;
        }

        .btn-primary {
            background: #2e7d32;
            border-color: #4caf50;
        }

        .grid-container {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
            margin: 15px 0;
        }

        .grid-item {
            background: rgba(20, 20, 20, 0.8);
            border: 1px solid #444;
            padding: 10px;
            border-radius: 4px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .hidden {
            display: none !important;
        }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>

    <div id="canvas-container"></div>

    <div id="hud">
        <div id="top-info">
            <div class="badge"><strong>VoxelCraft 3D</strong></div>
            <div class="badge">Pos: <span id="pos-display">0, 0, 0</span></div>
            <div class="badge">Atajos: T (Liberar) | L (Bloqueo T) | E (Inv) | I (Info) | K (Pausa)</div>
        </div>

        <div id="toast-notification"></div>
        <div id="crosshair"></div>

        <div id="hotbar-container"></div>
    </div>

    <!-- Panel de Menú / Pausa (K) -->
    <div id="menu-screen" class="overlay-screen">
        <div class="panel">
            <h1>MENÚ DE PAUSA</h1>
            <p>Opciones de juego y almacenamiento:</p>
            <button id="btn-resume" class="btn btn-primary">REANUDAR</button>
            <button id="btn-save" class="btn" style="background: #2980b9; border-color: #3498db;">GUARDAR PARTIDA</button>
            <button id="btn-load" class="btn" style="background: #8e44ad; border-color: #9b59b6;">CARGAR PARTIDA</button>
            <button id="btn-restart" class="btn" style="background: #c0392b; border-color: #e74c3c;">RESPAWN / REINICIAR</button>
        </div>
    </div>

    <!-- Panel de Inventario (E) -->
    <div id="inventory-screen" class="overlay-screen hidden">
        <div class="panel">
            <h2>INVENTARIO DE BLOQUES</h2>
            <div id="inventory-grid" class="grid-container"></div>
            <button id="btn-close-inv" class="btn btn-primary">CERRAR (E)</button>
        </div>
    </div>

    <!-- Panel de Información (I) -->
    <div id="info-screen" class="overlay-screen hidden">
        <div class="panel">
            <h2>ESTADÍSTICAS DEL MUNDO</h2>
            <div style="text-align: left; background: #1a1a1a; padding: 15px; border-radius: 6px; margin-bottom: 15px;">
                <p>📍 <strong>Posición:</strong> <span id="info-pos">0, 0, 0</span></p>
                <p>🗺️ <strong>Chunks Cargados:</strong> <span id="info-chunks">0</span></p>
                <p>👾 <strong>Mobs Activos:</strong> <span id="info-mobs">0</span></p>
                <p>🧱 <strong>Bloques Guardados:</strong> <span id="info-blocks">0</span></p>
            </div>
            <button id="btn-close-info" class="btn btn-primary">CERRAR (I)</button>
        </div>
    </div>

    <script>
        const BLOCK = { AIR: 0, GRASS: 1, DIRT: 2, STONE: 3, WOOD: 4, LEAVES: 5, GLASS: 6, COAL: 7, IRON: 8, DIAMOND: 9 };

        const ITEM_DATA = {
            [BLOCK.GRASS]: { name: 'Pasto', color: '#4caf50' },
            [BLOCK.DIRT]: { name: 'Tierra', color: '#795548' },
            [BLOCK.STONE]: { name: 'Piedra', color: '#9e9e9e' },
            [BLOCK.WOOD]: { name: 'Madera', color: '#8d6e63' },
            [BLOCK.LEAVES]: { name: 'Hojas', color: '#2e7d32' },
            [BLOCK.GLASS]: { name: 'Cristal', color: '#a3e4d7' },
            [BLOCK.COAL]: { name: 'Carbón', color: '#2c3e50' },
            [BLOCK.IRON]: { name: 'Hierro', color: '#d35400' },
            [BLOCK.DIAMOND]: { name: 'Diamante', color: '#1abc9c' }
        };

        const CHUNK_SIZE = 8;
        const RENDER_DISTANCE = 3;

        let scene, camera, renderer;
        let worldBlocks = new Map();
        let loadedChunks = new Set();
        let mobs = [];

        let moveForward = false, moveBackward = false, moveLeft = false, moveRight = false;
        let isSprinting = false, canJump = false;
        let velocityY = 0;
        let mouseSensitivity = 0.0025;
        let isTKeyEnabled = true;

        const player = {
            height: 1.6,
            speed: 7.0,
            sprintSpeed: 11.0,
            jumpForce: 8.5,
            gravity: 20.0,
            pos: new THREE.Vector3(0, 10, 0),
            activeHotbarSlot: 0
        };

        let hotbarData = [
            { type: BLOCK.GRASS, count: 32 },
            { type: BLOCK.DIRT, count: 32 },
            { type: BLOCK.WOOD, count: 16 },
            { type: BLOCK.LEAVES, count: 16 },
            { type: BLOCK.STONE, count: 32 }
        ];

        const raycaster = new THREE.Raycaster();
        const materials = {};

        function createTextures() {
            function gen(colorHex) {
                const canvas = document.createElement('canvas');
                canvas.width = 16; canvas.height = 16;
                const ctx = canvas.getContext('2d');
                ctx.fillStyle = colorHex;
                ctx.fillRect(0, 0, 16, 16);
                const t = new THREE.CanvasTexture(canvas);
                t.magFilter = THREE.NearestFilter;
                return t;
            }

            materials[BLOCK.GRASS] = [gen('#795548'), gen('#795548'), gen('#4caf50'), gen('#795548'), gen('#795548'), gen('#795548')].map(t => new THREE.MeshLambertMaterial({ map: t }));
            materials[BLOCK.DIRT] = new THREE.MeshLambertMaterial({ map: gen('#795548') });
            materials[BLOCK.STONE] = new THREE.MeshLambertMaterial({ map: gen('#9e9e9e') });
            materials[BLOCK.WOOD] = new THREE.MeshLambertMaterial({ map: gen('#8d6e63') });
            materials[BLOCK.LEAVES] = new THREE.MeshLambertMaterial({ map: gen('#2e7d32'), transparent: true, opacity: 0.85 });
            materials[BLOCK.GLASS] = new THREE.MeshLambertMaterial({ map: gen('#a3e4d7'), transparent: true, opacity: 0.5 });
            materials[BLOCK.COAL] = new THREE.MeshLambertMaterial({ map: gen('#2c3e50') });
            materials[BLOCK.IRON] = new THREE.MeshLambertMaterial({ map: gen('#d35400') });
            materials[BLOCK.DIAMOND] = new THREE.MeshLambertMaterial({ map: gen('#1abc9c') });
        }

        function initScene() {
            const container = document.getElementById('canvas-container');
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x87ceeb);

            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.rotation.order = 'YXZ';

            renderer = new THREE.WebGLRenderer({ antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            container.appendChild(renderer.domElement);

            scene.add(new THREE.AmbientLight(0xffffff, 0.6));
            const light = new THREE.DirectionalLight(0xffffff, 0.8);
            light.position.set(50, 100, 50);
            scene.add(light);

            createTextures();
            camera.position.copy(player.pos);

            window.addEventListener('resize', () => {
                camera.aspect = window.innerWidth / window.innerHeight;
                camera.updateProjectionMatrix();
                renderer.setSize(window.innerWidth, window.innerHeight);
            });
        }

        function getKey(x, y, z) { return `${Math.floor(x)},${Math.floor(y)},${Math.floor(z)}`; }

        function addBlock(x, y, z, type) {
            const key = getKey(x, y, z);
            if (worldBlocks.has(key)) return;
            const mat = materials[type] || materials[BLOCK.DIRT];
            const mesh = new THREE.Mesh(new THREE.BoxGeometry(1, 1, 1), mat);
            mesh.position.set(x + 0.5, y + 0.5, z + 0.5);
            scene.add(mesh);
            worldBlocks.set(key, { mesh, type });
        }

        function removeBlock(x, y, z) {
            const key = getKey(x, y, z);
            if (worldBlocks.has(key)) {
                const b = worldBlocks.get(key);
                scene.remove(b.mesh);
                worldBlocks.delete(key);
                return b.type;
            }
            return null;
        }

        function giveBlockToInventory(type) {
            if (!type) return;
            let slot = hotbarData.find(s => s && s.type === type);
            if (slot) {
                slot.count++;
            } else {
                let emptyIndex = hotbarData.findIndex(s => s === null);
                if (emptyIndex !== -1) {
                    hotbarData[emptyIndex] = { type: type, count: 1 };
                }
            }
            updateHotbarUI();
        }

        function generateTree(x, y, z) {
            const trunkHeight = 4 + Math.floor(Math.random() * 2);
            for (let i = 0; i < trunkHeight; i++) addBlock(x, y + i, z, BLOCK.WOOD);
            const leafStart = y + trunkHeight - 2;
            for (let lx = -2; lx <= 2; lx++) {
                for (let lz = -2; lz <= 2; lz++) {
                    for (let ly = 0; ly <= 2; ly++) {
                        if (Math.abs(lx) === 2 && Math.abs(lz) === 2) continue;
                        if (lx === 0 && lz === 0 && ly < 2) continue;
                        addBlock(x + lx, leafStart + ly, z + lz, BLOCK.LEAVES);
                    }
                }
            }
        }

        function generateChunk(cx, cz) {
            const chunkKey = `${cx},${cz}`;
            if (loadedChunks.has(chunkKey)) return;

            const startX = cx * CHUNK_SIZE;
            const startZ = cz * CHUNK_SIZE;

            for (let x = 0; x < CHUNK_SIZE; x++) {
                for (let z = 0; z < CHUNK_SIZE; z++) {
                    const worldX = startX + x;
                    const worldZ = startZ + z;
                    const height = Math.floor(Math.sin(worldX * 0.1) * 2 + Math.cos(worldZ * 0.1) * 2);

                    addBlock(worldX, height, worldZ, BLOCK.GRASS);
                    addBlock(worldX, height - 1, worldZ, BLOCK.DIRT);

                    // Minerales
                    const rand = Math.random();
                    if (rand < 0.05) addBlock(worldX, height - 2, worldZ, BLOCK.DIAMOND);
                    else if (rand < 0.15) addBlock(worldX, height - 2, worldZ, BLOCK.IRON);
                    else if (rand < 0.3) addBlock(worldX, height - 2, worldZ, BLOCK.COAL);
                    else addBlock(worldX, height - 2, worldZ, BLOCK.STONE);

                    if (Math.random() < 0.02 && Math.abs(worldX) > 3 && Math.abs(worldZ) > 3) {
                        generateTree(worldX, height + 1, worldZ);
                    }
                }
            }

            if (Math.random() < 0.3) spawnMob(startX + 2, 5, startZ + 2);
            loadedChunks.add(chunkKey);
        }

        function updateChunks() {
            const pChunkX = Math.floor(player.pos.x / CHUNK_SIZE);
            const pChunkZ = Math.floor(player.pos.z / CHUNK_SIZE);

            for (let x = -RENDER_DISTANCE; x <= RENDER_DISTANCE; x++) {
                for (let z = -RENDER_DISTANCE; z <= RENDER_DISTANCE; z++) {
                    generateChunk(pChunkX + x, pChunkZ + z);
                }
            }
        }

        function spawnMob(x, y, z) {
            const group = new THREE.Group();
            const bodyMat = new THREE.MeshLambertMaterial({ color: 0xe74c3c });
            const headMat = new THREE.MeshLambertMaterial({ color: 0xc0392b });

            const body = new THREE.Mesh(new THREE.BoxGeometry(0.8, 1.4, 0.5), bodyMat);
            body.position.y = 0.7;
            group.add(body);

            const head = new THREE.Mesh(new THREE.BoxGeometry(0.6, 0.6, 0.6), headMat);
            head.position.y = 1.7;
            group.add(head);

            group.position.set(x, y, z);
            scene.add(group);
            mobs.push({ mesh: group, health: 100 });
        }

        function updateMobs(delta) {
            mobs.forEach(mob => {
                const dir = new THREE.Vector3().subVectors(player.pos, mob.mesh.position);
                dir.y = 0;
                if (dir.length() < 12 && dir.length() > 1.2) {
                    dir.normalize();
                    mob.mesh.position.add(dir.multiplyScalar(2.5 * delta));
                    mob.mesh.lookAt(player.pos.x, mob.mesh.position.y, player.pos.z);
                }
            });
        }

        let pointerLocked = false;

        function showToast(text) {
            const toast = document.getElementById('toast-notification');
            toast.innerText = text;
            toast.style.display = 'block';
            setTimeout(() => { toast.style.display = 'none'; }, 2000);
        }

        function setupControls() {
            const canvas = renderer.domElement;

            document.getElementById('btn-resume').addEventListener('click', () => canvas.requestPointerLock());
            document.getElementById('btn-restart').addEventListener('click', () => {
                player.pos.set(0, 15, 0);
                velocityY = 0;
                showToast("¡Respawn realizado!");
                canvas.requestPointerLock();
            });

            document.getElementById('btn-save').addEventListener('click', () => {
                localStorage.setItem('voxel_player', JSON.stringify({ pos: player.pos }));
                localStorage.setItem('voxel_hotbar', JSON.stringify(hotbarData));
                showToast("¡Partida Guardada!");
            });

            document.getElementById('btn-load').addEventListener('click', () => {
                const p = localStorage.getItem('voxel_player');
                const h = localStorage.getItem('voxel_hotbar');
                if (p) player.pos.copy(JSON.parse(p).pos);
                if (h) hotbarData = JSON.parse(h);
                updateHotbarUI();
                showToast("¡Partida Cargada!");
                canvas.requestPointerLock();
            });

            document.getElementById('btn-close-inv').addEventListener('click', () => {
                document.getElementById('inventory-screen').classList.add('hidden');
                canvas.requestPointerLock();
            });

            document.getElementById('btn-close-info').addEventListener('click', () => {
                document.getElementById('info-screen').classList.add('hidden');
                canvas.requestPointerLock();
            });

            canvas.addEventListener('click', () => { if (!pointerLocked) canvas.requestPointerLock(); });

            document.addEventListener('pointerlockchange', () => {
                pointerLocked = document.pointerLockElement === canvas;
                const menuScreen = document.getElementById('menu-screen');
                if (pointerLocked) {
                    menuScreen.classList.add('hidden');
                    document.getElementById('inventory-screen').classList.add('hidden');
                    document.getElementById('info-screen').classList.add('hidden');
                } else {
                    menuScreen.classList.remove('hidden');
                }
            });

            document.addEventListener('mousemove', (e) => {
                if (!pointerLocked) return;
                camera.rotation.y -= e.movementX * mouseSensitivity;
                camera.rotation.x -= e.movementY * mouseSensitivity;
                camera.rotation.x = Math.max(-Math.PI / 2 + 0.01, Math.min(Math.PI / 2 - 0.01, camera.rotation.x));
            });

            document.addEventListener('keydown', (e) => {
                switch (e.code) {
                    case 'KeyW': case 'ArrowUp': moveForward = true; break;
                    case 'KeyS': case 'ArrowDown': moveBackward = true; break;
                    case 'KeyA': case 'ArrowLeft': moveLeft = true; break;
                    case 'KeyD': case 'ArrowRight': moveRight = true; break;
                    case 'Space': if (canJump) { velocityY = player.jumpForce; canJump = false; } break;
                    case 'ShiftLeft': isSprinting = true; break;
                    
                    case 'KeyL':
                        isTKeyEnabled = !isTKeyEnabled;
                        showToast(isTKeyEnabled ? 'Tecla T ACTIVADA' : 'Tecla T DESACTIVADA');
                        break;
                    case 'KeyT':
                        if (pointerLocked && isTKeyEnabled) document.exitPointerLock();
                        break;
                    case 'KeyK':
                        if (pointerLocked) document.exitPointerLock();
                        break;
                    case 'KeyE':
                        if (pointerLocked) document.exitPointerLock();
                        openInventory();
                        break;
                    case 'KeyI':
                        if (pointerLocked) document.exitPointerLock();
                        openInfo();
                        break;
                }

                if (e.code.startsWith('Digit') && e.code !== 'Digit0') {
                    const idx = parseInt(e.code.replace('Digit', '')) - 1;
                    if (idx >= 0 && idx < 5) {
                        player.activeHotbarSlot = idx;
                        updateHotbarUI();
                    }
                }
            });

            document.addEventListener('keyup', (e) => {
                switch (e.code) {
                    case 'KeyW': case 'ArrowUp': moveForward = false; break;
                    case 'KeyS': case 'ArrowDown': moveBackward = false; break;
                    case 'KeyA': case 'ArrowLeft': moveLeft = false; break;
                    case 'KeyD': case 'ArrowRight': moveRight = false; break;
                    case 'ShiftLeft': isSprinting = false; break;
                }
            });

            document.addEventListener('mousedown', (e) => {
                if (!pointerLocked) return;
                raycaster.setFromCamera(new THREE.Vector2(0, 0), camera);

                const mobMeshes = mobs.map(m => m.mesh);
                const mobHits = raycaster.intersectObjects(mobMeshes, true);
                if (e.button === 0 && mobHits.length > 0 && mobHits[0].distance <= 4) {
                    let rootGroup = mobHits[0].object;
                    while (rootGroup.parent && rootGroup.parent.type !== "Scene") rootGroup = rootGroup.parent;
                    const mobIdx = mobs.findIndex(m => m.mesh === rootGroup);
                    if (mobIdx !== -1) {
                        mobs[mobIdx].health -= 50;
                        if (mobs[mobIdx].health <= 0) {
                            scene.remove(mobs[mobIdx].mesh);
                            mobs.splice(mobIdx, 1);
                            showToast("¡Mob Derrotado!");
                        }
                    }
                    return;
                }

                const intersects = raycaster.intersectObjects(scene.children);
                if (e.button === 0) {
                    for (let hit of intersects) {
                        if (hit.distance <= 5) {
                            const p = hit.point.clone().sub(hit.face.normal.clone().multiplyScalar(0.1));
                            const removedType = removeBlock(Math.floor(p.x), Math.floor(p.y), Math.floor(p.z));
                            giveBlockToInventory(removedType);
                            break;
                        }
                    }
                } else if (e.button === 2) {
                    const active = hotbarData[player.activeHotbarSlot];
                    if (!active || active.count <= 0) return;

                    for (let hit of intersects) {
                        if (hit.distance <= 5) {
                            const p = hit.point.clone().add(hit.face.normal.clone().multiplyScalar(0.1));
                            addBlock(Math.floor(p.x), Math.floor(p.y), Math.floor(p.z), active.type);
                            active.count--;
                            if (active.count <= 0) hotbarData[player.activeHotbarSlot] = null;
                            updateHotbarUI();
                            break;
                        }
                    }
                }
            });

            document.addEventListener('contextmenu', e => e.preventDefault());
        }

        function openInventory() {
            const grid = document.getElementById('inventory-grid');
            grid.innerHTML = '';
            Object.keys(ITEM_DATA).forEach(typeKey => {
                const type = parseInt(typeKey);
                const item = ITEM_DATA[type];
                const div = document.createElement('div');
                div.className = 'grid-item';
                div.innerHTML = `<div class="item-icon" style="background:${item.color}"></div>
                                 <span style="font-size:12px; margin-top:4px;">${item.name}</span>`;
                div.onclick = () => {
                    hotbarData[player.activeHotbarSlot] = { type: type, count: 64 };
                    updateHotbarUI();
                    showToast(`Añadido ${item.name} a Slot ${player.activeHotbarSlot + 1}`);
                };
                grid.appendChild(div);
            });
            document.getElementById('inventory-screen').classList.remove('hidden');
        }

        function openInfo() {
            document.getElementById('info-pos').innerText = `${Math.floor(player.pos.x)}, ${Math.floor(player.pos.y)}, ${Math.floor(player.pos.z)}`;
            document.getElementById('info-chunks').innerText = loadedChunks.size;
            document.getElementById('info-mobs').innerText = mobs.length;
            document.getElementById('info-blocks').innerText = worldBlocks.size;
            document.getElementById('info-screen').classList.remove('hidden');
        }

        function updateHotbarUI() {
            const container = document.getElementById('hotbar-container');
            container.innerHTML = '';
            for (let i = 0; i < 5; i++) {
                const item = hotbarData[i];
                const slot = document.createElement('div');
                slot.className = `slot ${i === player.activeHotbarSlot ? 'active' : ''}`;
                slot.innerHTML = `<span class="slot-num">${i + 1}</span>`;
                if (item && item.count > 0) {
                    const info = ITEM_DATA[item.type];
                    slot.innerHTML += `<div class="item-icon" style="background:${info.color}"></div>`;
                    slot.innerHTML += `<span class="item-count">${item.count}</span>`;
                }
                container.appendChild(slot);
            }
        }

        let clock = new THREE.Clock();

        function updatePhysics(delta) {
            if (!pointerLocked) return;

            // --- DETECCIÓN DE CAÍDA AL VACÍO ---
            if (player.pos.y < -30) {
                player.pos.set(0, 15, 0);
                velocityY = 0;
                showToast("¡Caíste al vacío! Respawn en (0, 15, 0)");
            }

            updateChunks();
            updateMobs(delta);

            const speed = isSprinting ? player.sprintSpeed : player.speed;
            const moveVec = new THREE.Vector3();

            const forward = new THREE.Vector3(0, 0, -1).applyAxisAngle(new THREE.Vector3(0, 1, 0), camera.rotation.y);
            const side = new THREE.Vector3(1, 0, 0).applyAxisAngle(new THREE.Vector3(0, 1, 0), camera.rotation.y);

            if (moveForward) moveVec.add(forward);
            if (moveBackward) moveVec.sub(forward);
            if (moveRight) moveVec.add(side);
            if (moveLeft) moveVec.sub(side);

            if (moveVec.lengthSq() > 0) {
                moveVec.normalize().multiplyScalar(speed * delta);
                player.pos.add(moveVec);
            }

            velocityY -= player.gravity * delta;
            player.pos.y += velocityY * delta;

            const bx = Math.floor(player.pos.x);
            const bz = Math.floor(player.pos.z);
            let groundY = -100;

            for (let y = Math.floor(player.pos.y); y >= -100; y--) {
                if (worldBlocks.has(getKey(bx, y, bz))) {
                    groundY = y + 1 + player.height;
                    break;
                }
            }

            if (player.pos.y <= groundY) {
                player.pos.y = groundY;
                velocityY = 0;
                canJump = true;
            }

            camera.position.copy(player.pos);
            document.getElementById('pos-display').innerText = `${Math.floor(player.pos.x)}, ${Math.floor(player.pos.y)}, ${Math.floor(player.pos.z)}`;
        }

        function animate() {
            requestAnimationFrame(animate);
            const delta = Math.min(clock.getDelta(), 0.1);
            updatePhysics(delta);
            renderer.render(scene, camera);
        }

        window.onload = () => {
            initScene();
            setupControls();
            updateHotbarUI();
            animate();
        };
    </script>
</body>
</html>
