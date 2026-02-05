# Astro on Netlify Platform Starter

[Live Demo](https://astro-platform-starter.netlify.app/)

A modern starter based on Astro.js, Tailwind, and [Netlify Core Primitives](https://docs.netlify.com/core/overview/#develop) (Edge Functions, Image CDN, Blobs).

## Astro Commands

All commands are run from the root of the project, from a terminal:

| Command                   | Action                                           |
| :------------------------ | :----------------------------------------------- |
| `npm install`             | Installs dependencies                            |
| `npm run dev`             | Starts local dev server at `localhost:4321`      |
| `npm run build`           | Build your production site to `./dist/`          |
| `npm run preview`         | Preview your build locally, before deploying     |
| `npm run astro ...`       | Run CLI commands like `astro add`, `astro check` |
| `npm run astro -- --help` | Get help using the Astro CLI                     |

## Deploying to Netlify

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/netlify-templates/astro-platform-starter)

## Developing Locally

| Prerequisites                                                                |
| :--------------------------------------------------------------------------- |
| [Node.js](https://nodejs.org/) v18.20.8+.                                    |
| (optional) [nvm](https://github.com/nvm-sh/nvm) for Node version management. |

1. Clone this repository, then run `npm install` in its root directory.

2. Recommended: link your local repository to a Netlify project. This will ensure you're using the same runtime version for both local development and your deployed project.

```
netlify link
```

3. Run the Astro.js development server:

```
npm run dev
```
<!DOCTYPE html>
<html lang="pt-PT">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MechAI Simulator 3D</title>
    <meta name="description" content="Simulador 3D de física com mecânica realista. Adiciona objetos, controla gravidade e cria explosões!">
    <meta property="og:title" content="MechAI Simulator 3D">
    <meta property="og:description" content="Experimenta este incrível simulador de física 3D!">
    <meta property="og:image" content="https://i.imgur.com/7VJ0L0M.png">
    <meta property="og:url" content="https://mecha-simulator.netlify.app">
    <meta name="twitter:card" content="summary_large_image">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            user-select: none;
        }
        
        body {
            background: linear-gradient(135deg, #0a192f 0%, #0d2140 100%);
            color: #e6f1ff;
            min-height: 100vh;
            overflow-x: hidden;
            -webkit-tap-highlight-color: transparent;
        }
        
        /* Header */
        .header {
            background: rgba(10, 25, 47, 0.95);
            padding: 1rem 1.5rem;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid #64ffda;
            position: sticky;
            top: 0;
            z-index: 1000;
            backdrop-filter: blur(10px);
        }
        
        .logo {
            display: flex;
            align-items: center;
            gap: 0.8rem;
        }
        
        .logo h1 {
            color: #64ffda;
            font-size: 1.4rem;
            font-weight: 700;
            background: linear-gradient(45deg, #64ffda, #52e0c4);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        
        .nav-buttons {
            display: flex;
            gap: 0.8rem;
        }
        
        .nav-btn {
            background: rgba(35, 53, 84, 0.8);
            color: #ccd6f6;
            border: none;
            padding: 0.6rem 1rem;
            border-radius: 5px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.2s;
            font-size: 0.9rem;
        }
        
        .nav-btn:hover {
            background: #64ffda;
            color: #0a192f;
            transform: translateY(-2px);
        }
        
        /* Container principal */
        .app-container {
            display: flex;
            min-height: calc(100vh - 70px);
        }
        
        /* Painel de controle */
        .control-panel {
            width: 300px;
            background: rgba(16, 32, 58, 0.95);
            padding: 1.2rem;
            border-right: 2px solid #233554;
            overflow-y: auto;
            backdrop-filter: blur(10px);
            flex-shrink: 0;
        }
        
        .panel-section {
            margin-bottom: 1.5rem;
            padding-bottom: 1.2rem;
            border-bottom: 1px solid #233554;
        }
        
        .section-title {
            color: #64ffda;
            font-size: 1.1rem;
            margin-bottom: 1rem;
            display: flex;
            align-items: center;
            gap: 0.5rem;
        }
        
        .button-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 0.7rem;
            margin-bottom: 1rem;
        }
        
        .btn {
            padding: 0.8rem;
            border: none;
            border-radius: 6px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 0.5rem;
            font-size: 0.9rem;
        }
        
        .btn-primary {
            background: linear-gradient(135deg, #64ffda, #52e0c4);
            color: #0a192f;
        }
        
        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(100, 255, 218, 0.3);
        }
        
        .btn-danger {
            background: linear-gradient(135deg, #ff6b6b, #ff5252);
            color: white;
        }
        
        .btn-danger:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(255, 107, 107, 0.3);
        }
        
        .btn-warning {
            background: linear-gradient(135deg, #ffd166, #ffc145);
            color: #0a192f;
        }
        
        /* Sliders */
        .slider-group {
            margin-bottom: 1.2rem;
        }
        
        .slider-label {
            display: flex;
            justify-content: space-between;
            margin-bottom: 0.5rem;
            color: #8892b0;
            font-size: 0.85rem;
        }
        
        .slider-value {
            color: #64ffda;
            font-weight: bold;
        }
        
        input[type="range"] {
            width: 100%;
            height: 6px;
            background: #233554;
            border-radius: 3px;
            outline: none;
            -webkit-appearance: none;
        }
        
        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 18px;
            height: 18px;
            border-radius: 50%;
            background: #64ffda;
            cursor: pointer;
            border: 2px solid #0a192f;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
        }
        
        /* Status */
        .status-panel {
            background: rgba(35, 53, 84, 0.7);
            padding: 1rem;
            border-radius: 8px;
            border-left: 4px solid #64ffda;
        }
        
        .stats {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 0.8rem;
            margin-bottom: 0.8rem;
        }
        
        .stat-item {
            text-align: center;
            padding: 0.5rem;
            background: rgba(10, 25, 47, 0.5);
            border-radius: 5px;
        }
        
        .stat-value {
            color: #64ffda;
            font-weight: bold;
            font-size: 1.2rem;
        }
        
        .stat-label {
            color: #8892b0;
            font-size: 0.8rem;
        }
        
        /* Área 3D */
        .scene-container {
            flex: 1;
            position: relative;
            overflow: hidden;
        }
        
        #sceneCanvas {
            display: block;
            outline: none;
        }
        
        .loading-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(10, 25, 47, 0.9);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            transition: opacity 0.3s;
        }
        
        .loader {
            width: 50px;
            height: 50px;
            border: 3px solid #233554;
            border-top: 3px solid #64ffda;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-bottom: 1rem;
        }
        
        /* Controles de câmera */
        .camera-controls {
            position: absolute;
            top: 15px;
            right: 15px;
            display: flex;
            flex-direction: column;
            gap: 8px;
            z-index: 100;
        }
        
        .cam-btn {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: rgba(16, 32, 58, 0.9);
            border: 1px solid #64ffda;
            color: #64ffda;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.2s;
            font-size: 1.1rem;
        }
        
        .cam-btn:hover {
            background: #64ffda;
            color: #0a192f;
            transform: scale(1.1);
        }
        
        /* Instructions */
        .instructions {
            position: absolute;
            bottom: 15px;
            left: 15px;
            background: rgba(16, 32, 58, 0.9);
            padding: 1rem;
            border-radius: 8px;
            border: 1px solid #233554;
            max-width: 300px;
            backdrop-filter: blur(5px);
        }
        
        .instructions h4 {
            color: #64ffda;
            margin-bottom: 0.5rem;
            font-size: 0.9rem;
        }
        
        .instructions ul {
            padding-left: 1.2rem;
            font-size: 0.8rem;
            color: #8892b0;
        }
        
        .instructions li {
            margin-bottom: 0.3rem;
        }
        
        /* Footer */
        .footer {
            background: rgba(10, 25, 47, 0.95);
            padding: 1rem;
            text-align: center;
            border-top: 1px solid #233554;
            font-size: 0.8rem;
            color: #8892b0;
        }
        
        /* Animações */
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
        
        .pulse {
            animation: pulse 2s infinite;
        }
        
        /* Responsividade */
        @media (max-width: 1024px) {
            .app-container {
                flex-direction: column;
            }
            
            .control-panel {
                width: 100%;
                max-height: 40vh;
                border-right: none;
                border-bottom: 2px solid #233554;
            }
            
            .scene-container {
                min-height: 60vh;
            }
            
            .instructions {
                display: none;
            }
        }
        
        @media (max-width: 768px) {
            .header {
                flex-direction: column;
                gap: 0.8rem;
                padding: 0.8rem;
            }
            
            .nav-buttons {
                width: 100%;
                justify-content: center;
            }
            
            .button-grid {
                grid-template-columns: repeat(3, 1fr);
            }
            
            .camera-controls {
                top: 10px;
                right: 10px;
            }
            
            .cam-btn {
                width: 35px;
                height: 35px;
                font-size: 1rem;
            }
        }
        
        @media (max-width: 480px) {
            .button-grid {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .stats {
                grid-template-columns: 1fr;
            }
            
            .logo h1 {
                font-size: 1.2rem;
            }
        }
    </style>
</head>
<body>
    <!-- Header -->
    <header class="header">
        <div class="logo">
            <div style="font-size: 1.5rem;">🤖</div>
            <h1>MechAI Simulator</h1>
        </div>
        <div class="nav-buttons">
            <button class="nav-btn" onclick="resetScene()">🔄 Resetar</button>
            <button class="nav-btn" onclick="showInstructions()">❓ Ajuda</button>
            <button class="nav-btn" onclick="toggleFullscreen()">⛶ Tela Cheia</button>
        </div>
    </header>

    <!-- Container principal -->
    <div class="app-container">
        <!-- Painel de controle -->
        <div class="control-panel">
            <!-- Objetos -->
            <div class="panel-section">
                <h3 class="section-title">🎮 Adicionar Objetos</h3>
                <div class="button-grid">
                    <button class="btn btn-primary" onclick="addBox()">
                        <span>⬜</span> Caixa
                    </button>
                    <button class="btn btn-primary" onclick="addSphere()">
                        <span>🔵</span> Esfera
                    </button>
                    <button class="btn btn-primary" onclick="addGear()">
                        <span>⚙️</span> Engrenagem
                    </button>
                    <button class="btn btn-primary" onclick="addCylinder()">
                        <span>🟦</span> Cilindro
                    </button>
                    <button class="btn btn-primary" onclick="addPyramid()">
                        <span>🔺</span> Pirâmide
                    </button>
                    <button class="btn btn-primary" onclick="addComposite()">
                        <span>🧩</span> Composto
                    </button>
                </div>
            </div>

            <!-- Controles -->
            <div class="panel-section">
                <h3 class="section-title">⚙️ Controles</h3>
                <div class="button-grid">
                    <button class="btn btn-danger" onclick="clearObjects()">
                        <span>🗑️</span> Limpar
                    </button>
                    <button class="btn btn-warning" onclick="toggleGravity()" id="gravityBtn">
                        <span>🌎</span> Gravidade
                    </button>
                    <button class="btn btn-warning" onclick="explode()" style="grid-column: span 2;">
                        <span>💥</span> EXPLODIR TUDO!
                    </button>
                </div>
            </div>

            <!-- Física -->
            <div class="panel-section">
                <h3 class="section-title">📊 Configurações</h3>
                <div class="slider-group">
                    <div class="slider-label">
                        <span>Gravidade</span>
                        <span class="slider-value" id="gravityValue">9.8</span>
                    </div>
                    <input type="range" min="0" max="30" step="0.1" value="9.8" 
                           id="gravitySlider" oninput="updateGravity(this.value)">
                </div>
                <div class="slider-group">
                    <div class="slider-label">
                        <span>Atrito</span>
                        <span class="slider-value" id="frictionValue">0.3</span>
                    </div>
                    <input type="range" min="0" max="1" step="0.05" value="0.3" 
                           id="frictionSlider" oninput="updateFriction(this.value)">
                </div>
                <div class="slider-group">
                    <div class="slider-label">
                        <span>Bounce</span>
                        <span class="slider-value" id="restitutionValue">0.3</span>
                    </div>
                    <input type="range" min="0" max="1" step="0.05" value="0.3" 
                           id="restitutionSlider" oninput="updateRestitution(this.value)">
                </div>
            </div>

            <!-- Status -->
            <div class="status-panel">
                <div class="stats">
                    <div class="stat-item">
                        <div class="stat-value" id="objectCount">0</div>
                        <div class="stat-label">Objetos</div>
                    </div>
                    <div class="stat-item">
                        <div class="stat-value" id="fpsCounter">60</div>
                        <div class="stat-label">FPS</div>
                    </div>
                </div>
                <div id="statusMessage" style="color: #64ffda; font-size: 0.85rem; text-align: center;">
                    Pronto para simular!
                </div>
            </div>
        </div>

        <!-- Área 3D -->
        <div class="scene-container">
            <!-- Overlay de loading -->
            <div class="loading-overlay" id="loadingOverlay">
                <div class="loader"></div>
                <div style="color: #64ffda; margin-bottom: 0.5rem;">Carregando simulador 3D...</div>
                <div style="color: #8892b0; font-size: 0.8rem;">Pode levar alguns segundos</div>
            </div>
            
            <!-- Canvas será inserido aqui -->
            <div class="camera-controls">
                <button class="cam-btn" onclick="setCameraView('top')" title="Vista de Topo">
                    ↑
                </button>
                <button class="cam-btn" onclick="setCameraView('front')" title="Vista Frontal">
                    →
                </button>
                <button class="cam-btn" onclick="setCameraView('side')" title="Vista Lateral">
                    ←
                </button>
                <button class="cam-btn" onclick="resetCamera()" title="Resetar Câmera">
                    ↻
                </button>
            </div>
            
            <div class="instructions">
                <h4>🎮 Como Jogar:</h4>
                <ul>
                    <li>Clique para adicionar objetos</li>
                    <li>Arraste para rodar a cena</li>
                    <li>Roda do rato para zoom</li>
                    <li>Clique nos objetos para empurrar</li>
                    <li>Espaço = Explodir tudo!</li>
                </ul>
            </div>
        </div>
    </div>

    <!-- Footer -->
    <footer class="footer">
        <p>MechAI Simulator 3D | Criado com Three.js + Cannon.js | <span id="year">2024</span></p>
    </footer>

    <!-- Scripts -->
    <script src="https://cdn.jsdelivr.net/npm/three@0.169.0/build/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.169.0/examples/js/controls/OrbitControls.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/cannon@0.6.2/build/cannon.min.js"></script>

    <!-- Código principal -->
    <script>
        // ============= INICIALIZAÇÃO =============
        let scene, camera, renderer, controls, world;
        let bodies = [], meshes = [];
        let isGravityEnabled = true;
        let lastTime = 0, fps = 60;
        let objectCount = 0;
        let gravityValue = 9.8;

        // Elementos DOM
        const loadingOverlay = document.getElementById('loadingOverlay');
        const statusMessage = document.getElementById('statusMessage');
        const yearSpan = document.getElementById('year');
        yearSpan.textContent = new Date().getFullYear();

        // Inicializar
        function init() {
            showStatus('Iniciando simulador...');
            createScene();
            createPhysicsWorld();
            setupLighting();
            createGround();
            setupEventListeners();
            
            // Adicionar objetos iniciais
            setTimeout(() => {
                addBox([-4, 5, 0]);
                addSphere([4, 5, 0]);
                addGear([0, 8, -3]);
                hideLoading();
                showStatus('Pronto! Adiciona objetos e diverte-te! 🎮');
            }, 500);
            
            animate();
        }

        function showStatus(message) {
            statusMessage.textContent = message;
            console.log('Status:', message);
        }

        function hideLoading() {
            loadingOverlay.style.opacity = '0';
            setTimeout(() => {
                loadingOverlay.style.display = 'none';
            }, 300);
        }

        function createScene() {
            // Cena
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x0a192f);
            scene.fog = new THREE.Fog(0x0a192f, 20, 100);

            // Câmera
            const width = document.querySelector('.scene-container').clientWidth;
            const height = document.querySelector('.scene-container').clientHeight;
            camera = new THREE.PerspectiveCamera(60, width / height, 0
