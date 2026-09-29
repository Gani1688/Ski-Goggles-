# Ski-Goggles-
Interactive view
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>SkiNav HUD AR Ski Goggles - 4-View Interactive Model</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      user-select: none;
    }
    body {
      background: radial-gradient(circle at 50% 30%, #1e2638 0%, #0d1117 80%, #05070a 100%);
      color: #e6edf3;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
      overflow: hidden;
      height: 100vh;
      width: 100vw;
    }
    #canvas-container {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 1;
    }
    .ui-layer {
      position: absolute;
      z-index: 10;
      pointer-events: none;
    }
    /* Header Overlay */
    .header {
      top: 24px;
      left: 28px;
      display: flex;
      flex-direction: column;
      gap: 6px;
    }
    .badge {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      background: rgba(0, 212, 255, 0.12);
      border: 1px solid rgba(0, 212, 255, 0.35);
      border-radius: 20px;
      padding: 4px 14px;
      width: fit-content;
      font-size: 11px;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: #00e5ff;
    }
    .badge::before {
      content: "";
      width: 7px;
      height: 7px;
      background: #00e5ff;
      border-radius: 50%;
      box-shadow: 0 0 8px #00e5ff;
      animation: pulse 1.8s infinite;
    }
    @keyframes pulse {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.3; }
    }
    .title {
      font-size: 24px;
      font-weight: 700;
      letter-spacing: -0.02em;
      color: #ffffff;
      text-shadow: 0 2px 10px rgba(0,0,0,0.5);
    }
    .subtitle {
      font-size: 13px;
      color: #8b949e;
      max-width: 380px;
      line-height: 1.4;
    }

    /* Telemetry Widget */
    .telemetry-card {
      top: 24px;
      right: 28px;
      background: rgba(13, 17, 23, 0.75);
      backdrop-filter: blur(16px);
      border: 1px solid rgba(255, 255, 255, 0.1);
      border-radius: 14px;
      padding: 16px 20px;
      display: flex;
      gap: 24px;
      box-shadow: 0 8px 32px rgba(0,0,0,0.4);
    }
    .stat {
      display: flex;
      flex-direction: column;
    }
    .stat-label {
      font-size: 10px;
      text-transform: uppercase;
      letter-spacing: 0.08em;
      color: #6e7681;
      margin-bottom: 2px;
    }
    .stat-val {
      font-size: 20px;
      font-weight: 700;
      color: #00e5ff;
      font-variant-numeric: tabular-nums;
    }
    .stat-unit {
      font-size: 11px;
      font-weight: 400;
      color: #8b949e;
      margin-left: 2px;
    }

    /* Bottom Control Bar */
    .controls-panel {
      bottom: 28px;
      left: 50%;
      transform: translateX(-50%);
      display: flex;
      align-items: center;
      gap: 12px;
      background: rgba(13, 17, 23, 0.85);
      backdrop-filter: blur(20px);
      border: 1px solid rgba(255, 255, 255, 0.12);
      border-radius: 50px;
      padding: 8px 12px;
      box-shadow: 0 16px 40px rgba(0, 0, 0, 0.6);
      pointer-events: auto;
    }
    .btn-group {
      display: flex;
      gap: 6px;
    }
    button {
      background: rgba(255, 255, 255, 0.05);
      border: 1px solid rgba(255, 255, 255, 0.08);
      color: #c9d1d9;
      font-size: 12px;
      font-weight: 600;
      padding: 10px 18px;
      border-radius: 40px;
      cursor: pointer;
      transition: all 0.2s cubic-bezier(0.16, 1, 0.3, 1);
      display: flex;
      align-items: center;
      gap: 6px;
    }
    button:hover {
      background: rgba(255, 255, 255, 0.12);
      color: #ffffff;
      border-color: rgba(255, 255, 255, 0.2);
      transform: translateY(-1px);
    }
    button.active {
      background: #00e5ff;
      color: #031017;
      border-color: #00e5ff;
      box-shadow: 0 0 16px rgba(0, 229, 255, 0.4);
    }
    .divider {
      width: 1px;
      height: 24px;
      background: rgba(255, 255, 255, 0.15);
      margin: 0 4px;
    }
    .hud-toggle-btn {
      background: rgba(0, 229, 255, 0.1);
      border-color: rgba(0, 229, 255, 0.3);
      color: #00e5ff;
    }
    .hud-toggle-btn.active {
      background: #00e5ff;
      color: #001017;
    }

    /* View Legend */
    .view-indicator {
      bottom: 32px;
      left: 32px;
      font-size: 11px;
      color: #484f58;
      letter-spacing: 0.1em;
      text-transform: uppercase;
    }
  </style>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
</head>
<body>
  <div id="canvas-container"></div>

  <div class="ui-layer header">
    <div class="badge">Aero-Low Profile HUD Prototype</div>
    <div class="title">SkiNav Horizon HUD Goggles</div>
    <div class="subtitle">Ultra-thin toric dual-pane ski goggles featuring optical infinity collimated projection in the lower temporal quadrant.</div>
  </div>

  <div class="ui-layer telemetry-card">
    <div class="stat">
      <span class="stat-label">Velocity</span>
      <div><span class="stat-val" id="tele-speed">64</span><span class="stat-unit">km/h</span></div>
    </div>
    <div class="stat">
      <span class="stat-label">Altitude</span>
      <div><span class="stat-val" id="tele-alt">3,050</span><span class="stat-unit">m</span></div>
    </div>
    <div class="stat">
      <span class="stat-label">Heading</span>
      <div><span class="stat-val">312°</span><span class="stat-unit">NW</span></div>
    </div>
  </div>

  <div class="ui-layer view-indicator" id="view-indicator">Current: 3D Free Perspective Orbit</div>

  <div class="controls-panel">
    <div class="btn-group">
      <button onclick="setView('front', this)">Front</button>
      <button onclick="setView('top', this)">Top</button>
      <button onclick="setView('side', this)">Side</button>
      <button onclick="setView('bottom', this)">Bottom</button>
      <button class="active" onclick="setView('perspective', this)">3D Orbit</button>
    </div>
    <div class="divider"></div>
    <button class="hud-toggle-btn active" id="hudBtn" onclick="toggleHUD()">HUD: ON</button>
  </div>

  <script>
    // --- 1. Scene, Camera, and Renderer Setup ---
    const container = document.getElementById('canvas-container');
    const scene = new THREE.Scene();
    scene.fog = new THREE.FogExp2(0x0d1117, 0.04);

    const camera = new THREE.PerspectiveCamera(42, window.innerWidth / window.innerHeight, 0.1, 100);
    camera.position.set(3.8, 1.4, 6.2);

    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true, powerPreference: "high-performance" });
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.15;
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.typeHere is the complete, self-contained HTML file containing the interactive 3D model viewer for the slim HUD ski goggles. It includes real-time lighting, materials, camera presets for all four orthographic projections (**Front**, **Top**, **Side**, **Bottom**), an orbit mode, and an interactive HUD display toggle.

Save the code below as an `.html` file (e.g., `ski_goggles_viewer.html`) and open it in any modern web browser.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Smart HUD Ski Goggles - Interactive 3D Model</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      user-select: none;
    }
    body {
      background: radial-gradient(circle at center, #1b222c 0%, #0a0d12 100%);
      color: #e0e6ed;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
      overflow: hidden;
      width: 100vw;
      height: 100vh;
    }
    #canvas-container {
      width: 100%;
      height: 100%;
      position: absolute;
      top: 0;
      left: 0;
    }
    /* Control Header & Panels */
    .ui-overlay {
      position: absolute;
      z-index: 10;
      pointer-events: none;
    }
    .top-header {
      top: 24px;
      left: 28px;
    }
    .top-header h1 {
      font-size: 20px;
      font-weight: 700;
      letter-spacing: 1.5px;
      text-transform: uppercase;
      color: #ffffff;
      display: flex;
      align-items: center;
      gap: 10px;
    }
    .top-header h1 span {
      background: #00d2ff;
      color: #000;
      font-size: 10px;
      padding: 2px 7px;
      border-radius: 4px;
      font-weight: 800;
    }
    .top-header p {
      font-size: 13px;
      color: #7d8b9d;
      margin-top: 4px;
    }
    /* Camera Projection Toolbar */
    .controls-panel {
      bottom: 28px;
      left: 50%;
      transform: translateX(-50%);
      background: rgba(14, 20, 27, 0.75);
      backdrop-filter: blur(12px);
      border: 1px solid rgba(255, 255, 255, 0.1);
      padding: 8px 12px;
      border-radius: 30px;
      display: flex;
      gap: 8px;
      pointer-events: auto;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
    }
    .btn {
      background: transparent;
      border: 1px solid transparent;
      color: #a2b4c7;
      padding: 8px 16px;
      font-size: 12px;
      font-weight: 600;
      letter-spacing: 0.8px;
      text-transform: uppercase;
      border-radius: 20px;
      cursor: pointer;
      transition: all 0.2s cubic-bezier(0.2, 0.8, 0.2, 1);
    }
    .btn:hover {
      color: #ffffff;
      background: rgba(255, 255, 255, 0.08);
    }
    .btn.active {
      background: #00d2ff;
      color: #071017;
      box-shadow: 0 0 12px rgba(0, 210, 255, 0.4);
    }
    /* HUD Toggle Switch */
    .hud-toggle-container {
      top: 24px;
      right: 28px;
      pointer-events: auto;
      background: rgba(14, 20, 27, 0.75);
      backdrop-filter: blur(12px);
      border: 1px solid rgba(255, 255, 255, 0.1);
      padding: 10px 18px;
      border-radius: 12px;
      display: flex;
      align-items: center;
      gap: 12px;
    }
    .switch-label {
      font-size: 12px;
      font-weight: 600;
      letter-spacing: 0.5px;
      text-transform: uppercase;
    }
    .switch {
      position: relative;
      display: inline-block;
      width: 44px;
      height: 24px;
    }
    .switch input {
      opacity: 0;
      width: 0;
      height: 0;
    }
    .slider {
      position: absolute;
      cursor: pointer;
      top: 0; left: 0; right: 0; bottom: 0;
      background-color: #2a3543;
      transition: 0.3s;
      border-radius: 24px;
    }
    .slider:before {
      position: absolute;
      content: "";
      height: 18px;
      width: 18px;
      left: 3px;
      bottom: 3px;
      background-color: #ffffff;
      transition: 0.3s;
      border-radius: 50%;
    }
    input:checked + .slider {
      background-color: #00d2ff;
    }
    input:checked + .slider:before {
      transform: translateX(20px);
    }
    /* Technical specs card */
    .telemetry-card {
      top: 80px;
      right: 28px;
      width: 200px;
      background: rgba(14, 20, 27, 0.65);
      backdrop-filter: blur(8px);
      border-left: 2px solid #00d2ff;
      border-top: 1px solid rgba(255, 255, 255, 0.05);
      border-right: 1px solid rgba(255, 255, 255, 0.05);
      border-bottom: 1px solid rgba(255, 255, 255, 0.05);
      padding: 14px;
      border-radius: 4px;
      font-family: monospace;
      font-size: 11px;
      line-height: 1.6;
    }
    .telemetry-card .title {
      color: #7d8b9d;
      font-size: 10px;
      text-transform: uppercase;
      margin-bottom: 6px;
    }
    .telemetry-card .val {
      color: #00d2ff;
      font-weight: 700;
    }
  </style>
</head>
<body>

  <div id="canvas-container"></div>

  <div class="ui-overlay top-header">
    <h1>Skinav AR <span>v2.4 Ultra-Slim</span></h1>
    <p>Integrated Micro-OLED Waveguide Goggle Model</p>
  </div>

  <div class="ui-overlay hud-toggle-container">
    <span class="switch-label">HUD Projector</span>
    <label class="switch">
      <input type="checkbox" id="hudToggle" checked>
      <span class="slider"></span>
    </label>
  </div>

  <div class="ui-overlay telemetry-card">
    <div class="title">Active Telemetry</div>
    Speed: <span class="val" id="tele-speed">64 km/h</span><br>
    Altitude: <span class="val" id="tele-alt">3,050 m</span><br>
    Heading: <span class="val">315° NW</span><br>
    Optic: <span class="val">Waveguide RGB</span><br>
    Field of View: <span class="val">185° Pan</span>
  </div>

  <div class="controls-panel">
    <button class="btn" onclick="setView('front', this)">Front</button>
    <button class="btn" onclick="setView('top', this)">Top</button>
    <button class="btn" onclick="setView('side', this)">Side</button>
    <button class="btn" onclick="setView('bottom', this)">Bottom</button>
    <button class="btn active" onclick="setView('orbit', this)">3D Orbit</button>
  </div>

  <!-- Three.js and OrbitControls -->
  <script src="[https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js](https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js)"></script>
  <script src="[https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js](https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js)"></script>

  <script>
    // -------------------------------------------------------------
    // 1. Scene, Camera & Renderer Setup
    // -------------------------------------------------------------
    const container = document.getElementById('canvas-container');
    const scene = new THREE.Scene();
    scene.fog = new THREE.FogExp2(0x0e1319, 0.035);

    const camera = new THREE.PerspectiveCamera(40, window.innerWidth / window.innerHeight, 0.1, 100);
    camera.position.set(4.5, 2.2, 6.5);

    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.1;
    container.appendChild(renderer.domElement);

    const controls = new THREE.OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.05;
    controls.maxDistance = 20;
    controls.minDistance = 2.5;

    // -------------------------------------------------------------
    // 2. Studio Lighting Setup
    // -------------------------------------------------------------
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
    scene.add(ambientLight);

    const keyLight = new THREE.DirectionalLight(0xd9f2ff, 1.8);
    keyLight.position.set(6, 8, 6);
    scene.add(keyLight);

    const fillLight = new THREE.DirectionalLight(0x5070a0, 0.9);
    fillLight.position.set(-6, -2, -4);
    scene.add(fillLight);

    const rimLight = new THREE.SpotLight(0x00d2ff, 2.5, 15, Math.PI / 4, 0.3);
    rimLight.position.set(0, 5, -5);
    scene.add(rimLight);

    // -------------------------------------------------------------
    // 3. Procedural HUD Texture Creation (Canvas)
    // -------------------------------------------------------------
    const hudCanvas = document.createElement('canvas');
    hudCanvas.width = 512;
    hudCanvas.height = 512;
    const ctx = hudCanvas.getContext('2d');

    function drawHUD(speed = 64, altitude = 3050) {
      ctx.clearRect(0, 0, 512, 512);

      // Outer targeting reticle ring
      ctx.strokeStyle = "rgba(0, 210, 255, 0.35)";
      ctx.lineWidth = 4;
      ctx.beginPath();
      ctx.arc(256, 256, 200, -Math.PI * 0.8, -Math.PI * 0.1);
      ctx.stroke();

      ctx.beginPath();
      ctx.arc(256, 256, 200, Math.PI * 0.2, Math.PI * 0.7);
      ctx.stroke();

      // Heading arrow & North indicator
      ctx.fillStyle = "#00d2ff";
      ctx.beginPath();
      ctx.moveTo(256, 60);
      ctx.lineTo(244, 85);
      ctx.lineTo(268, 85);
      ctx.closePath();
      ctx.fill();

      ctx.font = "bold 20px -apple-system, monospace";
      ctx.fillText("NW 315°", 290, 80);

      // Speed readout
      ctx.font = "bold 96px -apple-system, monospace";
      ctx.fillStyle = "#ffffff";
      ctx.textAlign = "center";
      ctx.fillText(speed.toString(), 245, 270);

      ctx.font = "24px -apple-system, sans-serif";
      ctx.fillStyle = "#00d2ff";
      ctx.fillText("KM/H", 345, 270);

      // Elevation readout
      ctx.strokeStyle = "rgba(0, 210, 255, 0.6)";
      ctx.lineWidth = 2;
      ctx.beginPath();
      ctx.moveTo(140, 310);
      ctx.lineTo(370, 310);
      ctx.stroke();

      ctx.font = "bold 34px -apple-system, monospace";
      ctx.fillStyle = "#e0f7ff";
      ctx.fillText(altitude.toLocaleString() + " m", 256, 360);

      ctx.font = "14px -apple-system, monospace";
      ctx.fillStyle = "rgba(0, 210, 255, 0.8)";
      ctx.fillText("SLOPE TRACKER ACTIVE", 256, 395);
    }
    drawHUD();

    const hudTexture = new THREE.CanvasTexture(hudCanvas);
    hudTexture.minFilter = THREE.LinearFilter;

    // -------------------------------------------------------------
    // 4. Constructing the Ultra-Thin Ski Goggle Model
    // -------------------------------------------------------------
    const gogglesGroup = new THREE.Group();

    // Custom Parametric Toric Curved Lens Geometry
    const lensWidth = 4.4;
    const lensHeight = 2.1;
    const lensSegmentsX = 48;
    const lensSegmentsY = 24;
    const lensGeo = new THREE.PlaneGeometry(lensWidth, lensHeight, lensSegmentsX, lensSegmentsY);
    const pos = lensGeo.attributes.position;

    for (let i = 0; i < pos.count; i++) {
      const x = pos.getX(i);
      let y = pos.getY(i);

      // Curvature radius (wraparound face curve)
      const arch = -Math.pow(x / 2.3, 2) * 0.45;

      // Ergonomic nose-bridge cutout curve
      if (Math.abs(x) < 0.85 && y < -0.3) {
        const factor = (0.85 - Math.abs(x)) / 0.85;
        y += Math.sin(factor * Math.PI * 0.5) * 0.48;
        pos.setY(i, y);
      }

      pos.setZ(i, arch);
    }
    lensGeo.computeVertexNormals();

    // High-tech reflective mirrored lens material
    const lensMaterial = new THREE.MeshPhysicalMaterial({
      color: 0x004477,
      emissive: 0x001525,
      roughness: 0.08,
      metalness: 0.1,
      transmission: 0.65,
      transparent: true,
      opacity: 0.88,
      ior: 1.52,
      reflectivity: 0.9,
      clearcoat: 1.0,
      clearcoatRoughness: 0.05,
      side: THREE.DoubleSide
    });

    const lensMesh = new THREE.Mesh(lensGeo, lensMaterial);
    gogglesGroup.add(lensMesh);

    // Slim Aerodynamic Outer Frame Bezel
    const frameMaterial = new THREE.MeshStandardMaterial({
      color: 0x22262c,
      metalness: 0.85,
      roughness: 0.35
    });

    const frameGeo = lensGeo.clone();
    const frameMesh = new THREE.Mesh(frameGeo, frameMaterial);
    frameMesh.scale.set(1.03, 1.04, 1.03);
    frameMesh.position.z = -0.02;
    gogglesGroup.add(frameMesh);

    // Breathable Face Foam Gasket (Dark matte cushion layer)
    const foamMaterial = new THREE.MeshStandardMaterial({
      color: 0x111316,
      roughness: 0.95,
      metalness: 0.0
    });
    const foamMesh = new THREE.Mesh(lensGeo.clone(), foamMaterial);
    foamMesh.scale.set(0.98, 0.96, 1.0);
    foamMesh.position.z = -0.22;
    gogglesGroup.add(foamMesh);

    // Upper Ventilation Grille Bar (Slim low-profile top scoop)
    const ventGeo = new THREE.CylinderGeometry(0.04, 0.04, 3.4, 16);
    const ventMat = new THREE.MeshStandardMaterial({ color: 0x11161d, roughness: 0.8 });
    const ventBar = new THREE.Mesh(ventGeo, ventMat);
    ventBar.rotation.z = Math.PI / 2;
    ventBar.position.set(0, 1.05, -0.15);
 