<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Portfolio - kan18122004-beep</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        body, html {
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #050505;
            color: #fff;
        }
        #webgl-container {
            width: 100%;
            height: 100%;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 1;
        }
        #ui-container {
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
            padding: 30px;
        }
        .header {
            pointer-events: auto;
            background: rgba(10, 10, 15, 0.65);
            backdrop-filter: blur(12px);
            padding: 20px 28px;
            border-radius: 16px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            max-width: 380px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.5);
            transform: translateY(0);
            transition: transform 0.3s ease;
        }
        .header:hover {
            transform: translateY(-3px);
        }
        h1 {
            font-size: 1.8rem;
            font-weight: 700;
            letter-spacing: -0.5px;
            margin-bottom: 4px;
            background: linear-gradient(135deg, #a5f3fc, #0284c7);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        .subtitle {
            font-size: 0.9rem;
            color: #94a3b8;
            margin-bottom: 10px;
            font-weight: 500;
        }
        p.desc {
            font-size: 0.8rem;
            color: #cbd5e1;
            line-height: 1.4;
        }
        .badge-container {
            display: flex;
            gap: 6px;
            margin-top: 10px;
            flex-wrap: wrap;
        }
        .badge {
            font-size: 0.65rem;
            padding: 3px 8px;
            border-radius: 20px;
            background: rgba(56, 189, 248, 0.15);
            color: #38bdf8;
            border: 1px solid rgba(56, 189, 248, 0.3);
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        /* Control Panel Styles */
        .controls-panel {
            pointer-events: auto;
            background: rgba(10, 10, 15, 0.65);
            backdrop-filter: blur(12px);
            padding: 16px 20px;
            border-radius: 16px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            display: flex;
            gap: 20px;
            align-self: flex-start;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
        }
        .control-group {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        .control-label {
            font-size: 0.85rem;
            color: #e2e8f0;
            font-weight: 500;
        }
        .toggle-btn {
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.2);
            color: #fff;
            padding: 6px 14px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 0.8rem;
            transition: all 0.2s ease;
            display: flex;
            align-items: center;
            gap: 6px;
        }
        .toggle-btn:hover {
            background: rgba(56, 189, 248, 0.2);
            border-color: #38bdf8;
        }
        .toggle-btn.active {
            background: #0284c7;
            border-color: #38bdf8;
            box-shadow: 0 0 10px rgba(56, 189, 248, 0.4);
        }

        .bottom-bar {
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            width: 100%;
        }

        .controls-hint {
            pointer-events: auto;
            background: rgba(10, 10, 15, 0.5);
            backdrop-filter: blur(8px);
            padding: 10px 16px;
            border-radius: 30px;
            border: 1px solid rgba(255, 255, 255, 0.08);
            font-size: 0.75rem;
            color: #94a3b8;
        }

        #loading {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            z-index: 100;
            color: #38bdf8;
            font-size: 1.2rem;
            font-weight: 500;
            letter-spacing: 2px;
            pointer-events: none;
            transition: opacity 0.5s ease;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 15px;
        }
        .spinner {
            width: 40px;
            height: 40px;
            border: 3px solid rgba(56, 189, 248, 0.2);
            border-top-color: #38bdf8;
            border-radius: 50%;
            animation: spin 1s infinite linear;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>

    <!-- Import Importmap for Three.js ES Modules -->
    <script type="importmap">
        {
            "imports": {
                "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
                "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
            }
        }
    </script>
</head>
<body>

    <div id="loading">
        <div class="spinner"></div>
        <span>LOADING PORTFOLIO...</span>
    </div>

    <div id="ui-container">
        <div class="header">
            <h1>kan18122004-beep</h1>
            <div class="subtitle">3D Developer & Technical Artist</div>
            <p class="desc">Interactive 3D Portfolio นำเสนอโมเดล PBR คุณภาพสูง ระบบจำลองฝนตก และพื้นหินผสมหญ้าพร้อม Custom Shader ทำปฏิกิริยากับการเลื่อนเมาส์</p>
            <div class="badge-container">
                <span class="badge">Three.js</span>
                <span class="badge">GLSL Shader</span>
                <span class="badge">PBR Material</span>
            </div>
        </div>

        <div class="bottom-bar">
            <!-- Control Panel -->
            <div class="controls-panel">
                <div class="control-group">
                    <span class="control-label">🌧️ ฝนตก:</span>
                    <button id="btn-rain" class="toggle-btn active">เปิด</button>
                </div>
                <div class="control-group">
                    <span class="control-label">☀️ เวลา:</span>
                    <button id="btn-time" class="toggle-btn">กลางคืน 🌙</button>
                </div>
            </div>

            <div class="controls-hint">
                💡 ใช้เมาส์คลิกซ้ายเพื่อหมุน / ล้อเมาส์เพื่อซูม / เลื่อนเมาส์เพื่อส่งคลื่นพลังลงบนพื้นหิน
            </div>
        </div>
    </div>

    <div id="webgl-container"></div>

    <script type="module">
        import * as THREE from 'three';
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
        import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
        import { RGBELoader } from 'three/addons/loaders/RGBELoader.js';

        // --- 1. SETUP SCENE, CAMERA, RENDERER ---
        const container = document.getElementById('webgl-container');
        const scene = new THREE.Scene();
        
        // Fog Preset สำหรับกลางคืน
        const fogNight = new THREE.FogExp2(0x0a0d14, 0.035);
        const fogDay = new THREE.FogExp2(0x94a3b8, 0.015);
        scene.fog = fogNight;

        const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 100);
        camera.position.set(0, 2.5, 7.5);

        const renderer = new THREE.WebGLRenderer({ antialias: true, powerPreference: "high-performance" });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.toneMapping = THREE.ACESFilmicToneMapping;
        renderer.toneMappingExposure = 1.2;
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap;
        container.appendChild(renderer.domElement);

        const controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;
        controls.dampingFactor = 0.05;
        controls.maxPolarAngle = Math.PI / 2 - 0.01;
        controls.minDistance = 2;
        controls.maxDistance = 15;
        controls.target.set(0, 1, 0);

        // --- 2. LIGHTING & ENVIRONMENT (Day/Night Presets) ---
        const ambientLight = new THREE.AmbientLight(0xdbeafe, 0.4);
        scene.add(ambientLight);

        const mainLight = new THREE.DirectionalLight(0x38bdf8, 2.0);
        mainLight.position.set(5, 12, 5);
        mainLight.castShadow = true;
        mainLight.shadow.mapSize.width = 2048;
        mainLight.shadow.mapSize.height = 2048;
        mainLight.shadow.bias = -0.0001;
        scene.add(mainLight);

        const rimLight = new THREE.PointLight(0x818cf8, 3, 10);
        rimLight.position.set(-4, 3, -3);
        scene.add(rimLight);

        // HDRI Environment Mapping
        new RGBELoader()
            .setPath('https://threejs.org/examples/textures/equirectangular/')
            .load('royal_esplanade_1k.hdr', function (texture) {
                texture.mapping = THREE.EquirectangularReflectionMapping;
                scene.environment = texture;
            });

        // --- 3. ADVANCED GLSL SHADER (Interactive Ground with Moss & Ripple) ---
        const groundVertexShader = `
            uniform float uTime;
            uniform vec3 uMouseWorld;
            varying vec2 vUv;
            varying vec3 vPosition;
            varying vec3 vNormal;
            varying float vMossMask;
            varying float vInteraction;

            vec3 mod289(vec3 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
            vec2 mod289(vec2 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
            vec3 permute(vec3 x) { return mod289(((x*34.0)+1.0)*x); }
            float snoise(vec2 v){
                const vec4 C = vec4(0.211324865405187, 0.366025403784439, -0.577350269189626, 0.024390243902439);
                vec2 i  = floor(v + dot(v, C.yy) );
                vec2 x0 = v -   i + dot(i, C.xx);
                vec2 i1 = (x0.x > x0.y) ? vec2(1.0, 0.0) : vec2(0.0, 1.0);
                vec4 x12 = x0.xyxy + C.xxzz;
                x12.xy -= i1;
                i = mod289(i);
                vec3 p = permute( permute( i.y + vec3(0.0, i1.y, 1.0 )) + i.x + vec3(0.0, i1.x, 1.0 ));
                vec3 m = max(0.5 - vec3(dot(x0,x0), dot(x12.xy,x12.xy), dot(x12.zw,x12.zw)), 0.0);
                m = m*m ; m = m*m ;
                vec3 x = 2.0 * fract(p * C.www) - 1.0;
                vec3 h = abs(x) - 0.5;
                vec3 ox = floor(x + 0.5);
                vec3 a0 = x - ox;
                m *= 1.79284291400159 - 0.85373472095314 * (a0*a0 + h*h);
                vec3 g;
                g.x  = a0.x  * x0.x  + h.x  * x0.y;
                g.yz = a0.yz * x12.xz + h.yz * x12.yw;
                return 130.0 * dot(m, g);
            }

            void main() {
                vUv = uv;
                vec3 pos = position;
                
                float rockNoise = snoise(pos.xz * 0.8) * 0.25 + snoise(pos.xz * 3.0) * 0.05;
                float dist = distance(pos.xz, uMouseWorld.xz);
                float wave = sin(dist * 8.0 - uTime * 6.0) * exp(-dist * 1.5) * 0.15;
                
                pos.y += rockNoise + wave;

                vPosition = (modelMatrix * vec4(pos, 1.0)).xyz;
                vNormal = normalMatrix * normal;
                
                vMossMask = smoothstep(0.0, 0.4, snoise(pos.xz * 2.5));
                vInteraction = exp(-dist * 2.0);

                gl_Position = projectionMatrix * viewMatrix * vec4(vPosition, 1.0);
            }
        `;

        const groundFragmentShader = `
            uniform float uTime;
            uniform float uIsNight;
            varying vec2 vUv;
            varying vec3 vPosition;
            varying vec3 vNormal;
            varying float vMossMask;
            varying float vInteraction;

            void main() {
                vec3 normal = normalize(vNormal);
                
                vec3 rockColor = mix(vec3(0.35, 0.35, 0.38), vec3(0.12, 0.14, 0.16), uIsNight);
                vec3 mossColor = mix(vec3(0.2, 0.65, 0.2), vec3(0.1, 0.45, 0.15), uIsNight);
                vec3 glowColor = vec3(0.1, 0.7, 1.0);

                vec3 baseColor = mix(rockColor, mossColor, vMossMask);
                baseColor += glowColor * vInteraction * 1.5;

                vec3 lightDir = normalize(vec3(5.0, 10.0, 5.0));
                float diff = max(dot(normal, lightDir), uIsNight > 0.5 ? 0.2 : 0.5);
                
                vec3 viewDir = normalize(cameraPosition - vPosition);
                vec3 halfDir = normalize(lightDir + viewDir);
                float spec = pow(max(dot(normal, halfDir), 0.0), 32.0);

                vec3 finalColor = baseColor * diff + vec3(spec * 0.4);

                gl_FragColor = vec4(finalColor, 1.0);
            }
        `;

        const groundUniforms = {
            uTime: { value: 0 },
            uMouseWorld: { value: new THREE.Vector3(999, 0, 999) },
            uIsNight: { value: 1.0 } // 1.0 = กลางคืน, 0.0 = กลางวัน
        };

        const groundMaterial = new THREE.ShaderMaterial({
            vertexShader: groundVertexShader,
            fragmentShader: groundFragmentShader,
            uniforms: groundUniforms,
        });

        const groundGeometry = new THREE.PlaneGeometry(16, 16, 128, 128);
        groundGeometry.rotateX(-Math.PI / 2);
        const groundMesh = new THREE.Mesh(groundGeometry, groundMaterial);
        groundMesh.receiveShadow = true;
        scene.add(groundMesh);

        // --- 4. PUBLIC PBR MODEL LOADING ---
        const loader = new GLTFLoader();
        const loadingEl = document.getElementById('loading');
        const modelUrl = 'https://raw.githubusercontent.com/KhronosGroup/glTF-Sample-Models/main/2.0/DamagedHelmet/glTF-Binary/DamagedHelmet.glb';

        loader.load(
            modelUrl,
            (gltf) => {
                const model = gltf.scene;
                model.position.set(0, 1.3, 0);
                model.scale.set(1.2, 1.2, 1.2);
                
                model.traverse((child) => {
                    if (child.isMesh) {
                        child.castShadow = true;
                        child.receiveShadow = true;
                    }
                });

                scene.add(model);
                
                loadingEl.style.opacity = '0';
                setTimeout(() => loadingEl.style.display = 'none', 500);
            }
        );

        // --- 5. RAIN SIMULATION SYSTEM ---
        const rainCount = 3000;
        const rainGeometry = new THREE.BufferGeometry();
        const rainPositions = new Float32Array(rainCount * 3);
        const rainVelocities = new Float32Array(rainCount);

        for (let i = 0; i < rainCount; i++) {
            rainPositions[i * 3] = (Math.random() - 0.5) * 15;
            rainPositions[i * 3 + 1] = Math.random() * 10 + 2;
            rainPositions[i * 3 + 2] = (Math.random() - 0.5) * 15;
            rainVelocities[i] = 0.15 + Math.random() * 0.1;
        }

        rainGeometry.setAttribute('position', new THREE.BufferAttribute(rainPositions, 3));

        const rainMaterial = new THREE.PointsMaterial({
            color: 0x93c5fd,
            size: 0.03,
            transparent: true,
            opacity: 0.6,
            blending: THREE.AdditiveBlending
        });

        const rainParticles = new THREE.Points(rainGeometry, rainMaterial);
        scene.add(rainParticles);

        let isRainActive = true; // สถานะการเปิด/ปิดฝน

        function updateRain() {
            if (!isRainActive) return;

            const positions = rainGeometry.attributes.position.array;
            for (let i = 0; i < rainCount; i++) {
                positions[i * 3 + 1] -= rainVelocities[i];
                if (positions[i * 3 + 1] < 0) {
                    positions[i * 3 + 1] = 10;
                }
            }
            rainGeometry.attributes.position.needsUpdate = true;
        }

        // --- 6. CONTROLS INTERACTION (Day/Night & Rain Toggle) ---
        const btnRain = document.getElementById('btn-rain');
        const btnTime = document.getElementById('btn-time');
        let isNight = true;

        // สลับฝน
        btnRain.addEventListener('click', () => {
            isRainActive = !isRainActive;
            rainParticles.visible = isRainActive;
            btnRain.classList.toggle('active', isRainActive);
            btnRain.innerText = isRainActive ? 'เปิด' : 'ปิด';
        });

        // สลับกลางวัน / กลางคืน
        btnTime.addEventListener('click', () => {
            isNight = !isNight;
            groundUniforms.uIsNight.value = isNight ? 1.0 : 0.0;

            if (isNight) {
                // โหมดกลางคืน
                scene.fog = fogNight;
                scene.background = null;
                renderer.toneMappingExposure = 1.2;
                ambientLight.color.setHex(0xdbeafe);
                ambientLight.intensity = 0.4;
                mainLight.color.setHex(0x38bdf8);
                mainLight.intensity = 2.0;
                rimLight.intensity = 3;
                btnTime.innerText = 'กลางคืน 🌙';
            } else {
                // โหมดกลางวัน
                scene.fog = fogDay;
                scene.background = new THREE.Color(0xcfe2fe);
                renderer.toneMappingExposure = 1.0;
                ambientLight.color.setHex(0xffffff);
                ambientLight.intensity = 1.2;
                mainLight.color.setHex(0xfffaed);
                mainLight.intensity = 3.5;
                rimLight.intensity = 0.5;
                btnTime.innerText = 'กลางวัน ☀️';
            }
        });

        // --- 7. MOUSE INTERACTION (Raycasting) ---
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();

        window.addEventListener('mousemove', (event) => {
            mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObject(groundMesh);

            if (intersects.length > 0) {
                groundUniforms.uMouseWorld.value.copy(intersects[0].point);
            }
        });

        // --- 8. RESIZE & ANIMATION LOOP ---
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);

            const elapsedTime = clock.getElapsedTime();

            groundUniforms.uTime.value = elapsedTime;
            updateRain();
            controls.update();

            renderer.render(scene, camera);
        }

        animate();
    </script>
</body>
</html>
