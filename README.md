# kan18122004-beep.github.io
<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>kan18122004-beep | 3D Portfolio</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }
    body, html {
      width: 100%;
      height: 100%;
      overflow: hidden;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #050508;
      color: #ffffff;
    }
    #canvas-container {
      width: 100%;
      height: 100%;
      position: absolute;
      top: 0;
      left: 0;
      z-index: 1;
    }
    .ui-layer {
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
      padding: 2rem;
    }
    .header {
      background: rgba(15, 15, 25, 0.6);
      backdrop-filter: blur(12px);
      padding: 1.5rem 2rem;
      border-radius: 16px;
      border: 1px solid rgba(255, 255, 255, 0.1);
      max-width: 400px;
      box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
      pointer-events: auto;
    }
    .header h1 {
      font-size: 1.8rem;
      font-weight: 700;
      letter-spacing: 1px;
      background: linear-gradient(135deg, #00f2fe 0%, #4facfe 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin-bottom: 0.5rem;
    }
    .header p {
      font-size: 0.95rem;
      color: #a0a5c0;
      line-height: 1.4;
    }
    .instructions {
      background: rgba(15, 15, 25, 0.6);
      backdrop-filter: blur(12px);
      padding: 1rem 1.5rem;
      border-radius: 12px;
      border: 1px solid rgba(255, 255, 255, 0.1);
      align-self: flex-start;
      font-size: 0.85rem;
      color: #d1d5db;
      pointer-events: auto;
    }
    .instructions ul {
      list-style-type: square;
      margin-left: 1.2rem;
      margin-top: 0.3rem;
    }
    .loading {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 1.2rem;
      color: #4facfe;
      z-index: 20;
      pointer-events: none;
      transition: opacity 0.5s ease;
    }
  </style>

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

  <div id="canvas-container"></div>

  <div id="loading" class="loading">Loading 3D Scene & PBR Model...</div>

  <div class="ui-layer">
    <div class="header">
      <h1>kan18122004-beep</h1>
      <p>3D Technical Artist & Interactive Developer Portfolio</p>
      <p style="margin-top: 0.5rem; font-size: 0.8rem; color: #6b7280;">
        Interactive Scene with Custom Shaders, Rain Simulation, and Flight Helmet Model.
      </p>
    </div>

    <div class="instructions">
      <strong>Controls & Features:</strong>
      <ul>
        <li>🖱️ <strong>Drag Mouse:</strong> หมุนกล้อง (Orbit Controls)</li>
        <li>📜 <strong>Scroll:</strong> ย่อ/ขยาย (Zoom)</li>
        <li>✨ <strong>Move Cursor:</strong> เมาส์ส่งคลื่นกระแทก Shader บนพื้นหิน</li>
        <li>🌧️ <strong>Rain Simulation:</strong> จำลองสายฝนกระทบพื้นแบบ Real-time</li>
      </ul>
    </div>
  </div>

  <script type="module">
    import * as THREE from 'three';
    import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
    import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
    import { RGBELoader } from 'three/addons/loaders/RGBELoader.js';

    // --- Scene, Camera, Renderer Setup ---
    const container = document.getElementById('canvas-container');
    const scene = new THREE.Scene();
    scene.fog = new THREE.FogExp2(0x050508, 0.035);

    const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100);
    camera.position.set(0, 1.8, 3.5);

    const renderer = new THREE.WebGLRenderer({ antialias: true, powerPreference: "high-performance" });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.2;
    container.appendChild(renderer.domElement);

    // --- Orbit Controls ---
    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.05;
    controls.maxPolarAngle = Math.PI / 2 - 0.01;
    controls.minDistance = 1.5;
    controls.maxDistance = 8;
    controls.target.set(0, 0.6, 0);

    // --- Lighting Setup ---
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.3);
    scene.add(ambientLight);

    const dirLight = new THREE.DirectionalLight(0x4facfe, 2.5);
    dirLight.position.set(5, 8, 5);
    dirLight.castShadow = true;
    dirLight.shadow.mapSize.width = 2048;
    dirLight.shadow.mapSize.height = 2048;
    dirLight.shadow.bias = -0.0001;
    scene.add(dirLight);

    const rimLight = new THREE.PointLight(0x00f2fe, 3, 10);
    rimLight.position.set(-3, 2, -3);
    scene.add(rimLight);

    // --- HDRI Environment Map ---
    new RGBELoader().load('https://dl.polyhaven.org/file/ph-assets/HDRIs/hdr/1k/night_puresky_1k.hdr', (texture) => {
      texture.mapping = THREE.EquirectangularReflectionMapping;
      scene.environment = texture;
    });

    // --- Advanced Custom GLSL Shader Ground ---
    const customGroundVertexShader = `
      varying vec2 vUv;
      varying vec3 vWorldPosition;
      varying vec3 vNormal;
      uniform float uTime;
      uniform vec3 uMouseWorld;

      vec3 mod289(vec3 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
      vec2 mod289(vec2 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
      vec3 permute(vec3 x) { return mod289(((x*34.0)+1.0)*x); }
      float snoise(vec2 v) {
        const vec4 C = vec4(0.211324865405187, 0.366025403784439, -0.577350269189626, 0.024390243902439);
        vec2 i  = floor(v + dot(v, C.yy) );
        vec2 x0 = v - i + dot(i, C.xx);
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
        m *= 1.79284291400159 - 0.85373472095314 * ( a0*a0 + h*h );
        vec3 g;
        g.x  = a0.x  * x0.x  + h.x  * x0.y;
        g.yz = a0.yz * x12.xz + h.yz * x12.yw;
        return 130.0 * dot(m, g);
      }

      void main() {
        vUv = uv;
        vec3 pos = position;

        float stonePattern = snoise(uv * 12.0) * 0.08;
        float distToMouse = distance(position.xz, uMouseWorld.xz);
        float wave = sin(distToMouse * 10.0 - uTime * 6.0) * exp(-distToMouse * 1.5) * 0.15;

        pos.z += stonePattern + wave;

        vec4 worldPos = modelMatrix * vec4(pos, 1.0);
        vWorldPosition = worldPos.xyz;
        vNormal = normalMatrix * normal;

        gl_Position = projectionMatrix * viewMatrix * worldPos;
      }
    `;

    const customGroundFragmentShader = `
      varying vec2 vUv;
      varying vec3 vWorldPosition;
      varying vec3 vNormal;
      uniform float uTime;
      uniform vec3 uMouseWorld;

      void main() {
        vec3 baseColor = vec3(0.08, 0.09, 0.12);
        vec3 highlightColor = vec3(0.0, 0.95, 0.99);

        float dist = distance(vWorldPosition.xz, uMouseWorld.xz);
        float glow = exp(-dist * 1.8) * 1.2;

        vec2 grid = abs(fract(vUv * 30.0 - 0.5) - 0.5) / fwidth(vUv * 30.0);
        float line = min(grid.x, grid.y);
        float gridPattern = 1.0 - min(line, 1.0);

        vec3 finalColor = mix(baseColor, highlightColor, glow + (gridPattern * 0.15));
        
        gl_FragColor = vec4(finalColor, 1.0);
      }
    `;

    const groundUniforms = {
      uTime: { value: 0.0 },
      uMouseWorld: { value: new THREE.Vector3(999, 999, 999) }
    };

    const groundGeometry = new THREE.PlaneGeometry(20, 20, 128, 128);
    const groundMaterial = new THREE.ShaderMaterial({
      vertexShader: customGroundVertexShader,
      fragmentShader: customGroundFragmentShader,
      uniforms: groundUniforms,
      wireframe: false
    });

    const ground = new THREE.Mesh(groundGeometry, groundMaterial);
    ground.rotation.x = -Math.PI / 2;
    ground.receiveShadow = true;
    scene.add(ground);

    // --- Rain Simulation ---
    const rainCount = 3000;
    const rainGeometry = new THREE.BufferGeometry();
    const rainPositions = new Float32Array(rainCount * 3);
    const rainVelocities = new Float32Array(rainCount);

    for (let i = 0; i < rainCount; i++) {
      rainPositions[i * 3] = (Math.random() - 0.5) * 15;
      rainPositions[i * 3 + 1] = Math.random() * 10;
      rainPositions[i * 3 + 2] = (Math.random() - 0.5) * 15;
      rainVelocities[i] = 0.15 + Math.random() * 0.2;
    }

    rainGeometry.setAttribute('position', new THREE.BufferAttribute(rainPositions, 3));

    const rainMaterial = new THREE.PointsMaterial({
      color: 0x88ccff,
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
        if (positions[i * 3 + 1] < 0) {
          positions[i * 3 + 1] = 10;
        }
      }
      rainGeometry.attributes.position.needsUpdate = true;
    }

    // --- Load Flight Helmet PBR Model ---
    const loader = new GLTFLoader();
    const modelUrl = 'https://raw.githubusercontent.com/KhronosGroup/glTF-Sample-Models/main/2.0/FlightHelmet/glTF-Binary/FlightHelmet.glb';

    loader.load(modelUrl, (gltf) => {
      const model = gltf.scene;
      model.position.set(0, 0, 0);
      model.scale.set(2.5, 2.5, 2.5);

      model.traverse((child) => {
        if (child.isMesh) {
          child.castShadow = true;
          child.receiveShadow = true;
        }
      });

      scene.add(model);

      const loadingEl = document.getElementById('loading');
      if (loadingEl) loadingEl.style.opacity = '0';
    }, undefined, (error) => {
      console.error('An error occurred while loading GLTF model:', error);
    });

    // --- Mouse Interaction Raycasting ---
    const raycaster = new THREE.Raycaster();
    const mouse = new THREE.Vector2(-999, -999);

    window.addEventListener('pointermove', (event) => {
      mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

      raycaster.setFromCamera(mouse, camera);
      const intersects = raycaster.intersectObject(ground);

      if (intersects.length > 0) {
        groundUniforms.uMouseWorld.value.copy(intersects[0].point);
      }
    });

    // --- Window Resize Handler ---
    window.addEventListener('resize', () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });

    // --- Animation Loop ---
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
