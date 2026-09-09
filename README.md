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
            padding: 40px;
        }
        .header {
            pointer-events: auto;
            background: rgba(10, 10, 15, 0.65);
            backdrop-filter: blur(12px);
            padding: 24px 32px;
            border-radius: 16px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            max-width: 420px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.5);
            transform: translateY(0);
            transition: transform 0.3s ease;
        }
        .header:hover {
            transform: translateY(-5px);
        }
        h1 {
            font-size: 2rem;
            font-weight: 700;
            letter-spacing: -0.5px;
            margin-bottom: 6px;
            background: linear-gradient(135deg, #a5f3fc, #0284c7);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        .subtitle {
            font-size: 0.95rem;
            color: #94a3b8;
            margin-bottom: 12px;
            font-weight: 500;
        }
        p.desc {
            font-size: 0.85rem;
            color: #cbd5e1;
            line-height: 1.5;
        }
        .badge-container {
            display: flex;
            gap: 8px;
            margin-top: 12px;
            flex-wrap: wrap;
        }
        .badge {
            font-size: 0.7rem;
            padding: 4px 10px;
            border-radius: 20px;
            background: rgba(56, 189, 248, 0.15);
            color: #38bdf8;
            border: 1px solid rgba(56, 189, 248, 0.3);
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }
        .controls-hint {
            pointer-events: auto;
            align-self: flex-start;
            background: rgba(10, 10, 15, 0.5);
            backdrop-filter: blur(8px);
            padding: 12px 20px;
            border-radius: 30px;
            border: 1px solid rgba(255, 255, 255, 0.08);
            font-size: 0.8rem;
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
            <p class="desc">ยินดีต้อนรับสู่ Interactive 3D Portfolio นำเสนอโมเดล PBR คุณภาพสูง ระบบจำลองฝนตกด้วย Particle System และพื้นหินผสมหญ้าพร้อม Custom Shader ทำปฏิกิริยากับการเลื่อนเมาส์</p>
            <div class="badge-container">
                <span class="badge">Three.js</span>
                <span class="badge">GLSL Shader</span>
                <span class="badge">PBR Material</span>
                <span class="badge">Rain Sim</span>
            </div>
        </div>
        <div class="controls-hint">
            💡 ใช้เมาส์คลิกซ้ายเพื่อหมุน / ล้อเมาส์เพื่อซูม / เลื่อนเมาส์เพื่อส่งคลื่นพลังลงบนพื้นหิน
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
        scene.fog = new THREE.FogExp2(0x0a0d14, 0.035);

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
        controls.maxPolarAngle = Math.PI / 2 - 0.01; // ห้ามมุมกล้องลงใต้พื้น
        controls.minDistance = 2;
        controls.maxDistance = 15;
        controls.target.set(0, 1, 0);

        // --- 2. LIGHTING & ENVIRONMENT (PBR Lighting) ---
        const ambientLight = new THREE.AmbientLight(0xdbeafe, 0.6);
        scene.add(ambientLight);

        const mainLight = new THREE.DirectionalLight(0x38bdf8, 2.5);
        mainLight.position.set(5, 12, 5);
        mainLight.castShadow = true;
        mainLight.shadow.mapSize.width = 2048;
        mainLight.shadow.mapSize.height = 2048;
        mainLight.shadow.bias = -0.0001;
        scene.add(mainLight);

        const rimLight = new THREE.PointLight(0x818cf8, 4, 10);
        rimLight.position.set(-4, 3, -3);
        scene.add(rimLight);

        // โหลด HDRI Map ให้กับ PBR Material Refraction/Reflection
        new RGBELoader()
            .setPath('https://threejs.org/examples/textures/equirectangular/')
            .load('royal_esplanade_1k.hdr', function (texture) {
                texture.mapping = THREE.EquirectangularReflectionMapping;
                scene.environment = texture;
            });

        // --- 3. ADVANCED GLSL SHADER (Interactive Ground with Moss & Ripple) ---
        // Vertex Shader: คำนวณ Procedural Bump, หญ้าเจริญเติบโต และคลื่นจาก Mouse
        const groundVertexShader = `
            uniform float uTime;
            uniform vec3 uMouseWorld;
            varying vec2 vUv;
            varying vec3 vPosition;
            varying vec3 vNormal;
            varying float vMossMask;
            varying float vInteraction;

            // Simplex Noise Function
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
                
                // Procedural Rock Displacement
                float rockNoise = snoise(pos.xz * 0.8) * 0.25 + snoise(pos.xz * 3.0) * 0.05;
                
                // Interaction with Mouse (Ripple Wave)
                float dist = distance(pos.xz, uMouseWorld.xz);
                float wave = sin(dist * 8.0 - uTime * 6.0) * exp(-dist * 1.5) * 0.15;
                
                pos.y += rockNoise + wave;

                // Pass info to Fragment Shader
                vPosition = (modelMatrix * vec4(pos, 1.0)).xyz;
                vNormal = normalMatrix * normal;
                
                // Mask สำหรับตำแหน่งหญ้าเกาะตามซอกหิน (ใช้ Noise)
                vMossMask = smoothstep(0.0, 0.4, snoise(pos.xz * 2.5));
                vInteraction = exp(-dist * 2.0);

                gl_Position = projectionMatrix * viewMatrix * vec4(vPosition, 1.0);
            }
        `;

        // Fragment Shader: เรนเดอร์ Texture หินผสมหญ้าแบบ PBR Procedural Lighting
        const groundFragmentShader = `
            uniform float uTime;
            varying vec2 vUv;
            varying vec3 vPosition;
            varying vec3 vNormal;
            varying float vMossMask;
            varying float vInteraction;

            void main() {
                vec3 normal = normalize(vNormal);
                
                // Base Rock Color (หินสีเทาเข้ม)
                vec3 rockColor = vec3(0.12, 0.14, 0.16);
                
                // Moss Color (สีหญ้าสด/ตะไคร่น้ำ)
                vec3 mossColor = vec3(0.1, 0.45, 0.15);
                
                // Glowing Interaction Color (แสงโกลว์เมื่อเมาส์เข้าใกล้)
                vec3 glowColor = vec3(0.1, 0.7, 1.0);

                // Mix Rock and Moss
                vec3 baseColor = mix(rockColor, mossColor, vMossMask);
                
                // Add Mouse Glow Effect
                baseColor += glowColor * vInteraction * 1.5;

                // Basic Lighting inside Shader
                vec3 lightDir = normalize(vec3(5.0, 10.0, 5.0));
                float diff = max(dot(normal, lightDir), 0.2);
                
                // PBR Wetness Effect (เงาสะท้อนจากน้ำบนหิน)
                vec3 viewDir = normalize(cameraPosition - vPosition);
                vec3 halfDir = normalize(lightDir + viewDir);
                float spec = pow(max(dot(normal, halfDir), 0.0), 32.0);

                vec3 finalColor = baseColor * diff + vec3(spec * 0.4);

                gl_FragColor = vec4(finalColor, 1.0);
            }
        `;

        const groundUniforms = {
            uTime: { value: 0 },
            uMouseWorld: { value: new THREE.Vector3(999, 0, 999) }
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

        // --- 4. PUBLIC PBR MODEL LOADING (GLTF/GLB) ---
        // ใช้โมเดล Damaged Helmet (PBR Metallic Roughness) จาก Khronos Group
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
                
                // ซ่อน Screen Loading
                loadingEl.style.opacity = '0';
                setTimeout(() => loadingEl.style.display = 'none', 500);
            },
            (xhr) => {
                // Progress
            },
            (error) => {
                console.error('An error happened loading the model:', error);
            }
        );

        // --- 5. RAIN SIMULATION SYSTEM (Particles) ---
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

        function updateRain() {
            const positions = rainGeometry.attributes.position.array;
            for (let i = 0; i < rainCount; i++) {
                positions[i * 3 + 1] -= rainVelocities[i];
                // เมื่อหยดน้ำตกถึงพื้น ให้รีเซ็ตกลับไปข้างบน
                if (positions[i * 3 + 1] < 0) {
                    positions[i * 3 + 1] = 10;
                }
            }
            rainGeometry.attributes.position.needsUpdate = true;
        }

        // --- 6. MOUSE INTERACTION (RAYCASTING TO SHADER) ---
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

        // --- 7. RESIZE & ANIMATION LOOP ---
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);

            const elapsedTime = clock.getElapsedTime();

            // Update Shader Time
            groundUniforms.uTime.value = elapsedTime;

            // Update Rain Simulation
            updateRain();

            // Update Orbit Controls
            controls.update();

            // Render
            renderer.render(scene, camera);
        }

        animate();
    </script>
</body>
</html>
