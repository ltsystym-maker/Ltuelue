<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Neural VR Network - Smart Mode</title>
  <style>
    html, body { margin:0; padding:0; width:100%; height:100%; overflow:hidden; background:#0a0f18; }
    canvas { display:block; }
  </style>
</head>
<body>
  <canvas id="three-canvas"></canvas>

  <!-- مكتبة Three.js -->
  <script src="https://unpkg.com/three@0.152.2/build/three.min.js"></script>

  <script>
    // Renderer
    const canvas = document.getElementById('three-canvas');
    const renderer = new THREE.WebGLRenderer({ canvas, antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(window.devicePixelRatio);

    // Scene & Camera
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(60, window.innerWidth/window.innerHeight, 1, 5000);
    camera.position.set(0, 0, 1000);

    // Lighting
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
    scene.add(ambientLight);
    const pointLight = new THREE.PointLight(0xffffff, 1);
    pointLight.position.set(500, 500, 500);
    scene.add(pointLight);

    // Points Data
    const count = 300;
    const positions = new Float32Array(count * 3);
    const colors = new Float32Array(count * 3);
    const velocities = []; // لكل نقطة سرعة
    const color = new THREE.Color();

    for (let i = 0; i < count; i++) {
      positions[3*i  ] = (Math.random() - 0.5) * 2000;
      positions[3*i+1] = (Math.random() - 0.5) * 2000;
      positions[3*i+2] = (Math.random() - 0.5) * 2000;

      color.setHSL(i / count, 1.0, 0.5);
      colors[3*i] = color.r;
      colors[3*i+1] = color.g;
      colors[3*i+2] = color.b;

      velocities.push({
        x: (Math.random() - 0.5) * 0.5,
        y: (Math.random() - 0.5) * 0.5,
        z: (Math.random() - 0.5) * 0.5
      });
    }

    const geometry = new THREE.BufferGeometry();
    geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
    geometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));

    const material = new THREE.PointsMaterial({
      vertexColors: true,
      size: 8,
      transparent: true,
      opacity: 0.95
    });

    const points = new THREE.Points(geometry, material);
    scene.add(points);

    // Lines
    const maxConn = count * 5;
    const linePos = new Float32Array(maxConn * 2 * 3);
    const lineGeom = new THREE.BufferGeometry();
    lineGeom.setAttribute('position', new THREE.BufferAttribute(linePos, 3));
    const lineMat = new THREE.LineBasicMaterial({ color: 0xffffff, transparent: true, opacity: 0.4 });
    const lines = new THREE.LineSegments(lineGeom, lineMat);
    scene.add(lines);

    // Mouse Interaction
    const mouse = new THREE.Vector2(0, 0);
    window.addEventListener('mousemove', (event) => {
      mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    });

    // Animate
    function animate(time) {
      requestAnimationFrame(animate);

      const pos = geometry.attributes.position.array;

      // تحريك النقاط
      for (let i = 0; i < count; i++) {
        pos[3*i]   += velocities[i].x;
        pos[3*i+1] += velocities[i].y;
        pos[3*i+2] += velocities[i].z;

        // جذب النقاط للماوس
        const dx = pos[3*i] - mouse.x * 500;
        const dy = pos[3*i+1] - mouse.y * 500;
        const dist = Math.sqrt(dx*dx + dy*dy);
        if (dist < 200) {
          velocities[i].x += (Math.random() - 0.5) * 0.1;
          velocities[i].y += (Math.random() - 0.5) * 0.1;
        }

        // تغيير الألوان بمرور الوقت
        const hue = (time * 0.0001 + i / count) % 1;
        color.setHSL(hue, 1.0, 0.5);
        colors[3*i] = color.r;
        colors[3*i+1] = color.g;
        colors[3*i+2] = color.b;
      }

      geometry.attributes.color.needsUpdate = true;

      // تحديث الخطوط
      let idx = 0;
      for (let i = 0; i < count; i++) {
        for (let j = i+1; j < count; j++) {
          const dx = pos[3*i]   - pos[3*j];
          const dy = pos[3*i+1] - pos[3*j+1];
          const dz = pos[3*i+2] - pos[3*j+2];
          const d2 = dx*dx + dy*dy + dz*dz;
          if (d2 < 150000) {
            linePos[idx++] = pos[3*i];
            linePos[idx++] = pos[3*i+1];
            linePos[idx++] = pos[3*i+2];
            linePos[idx++] = pos[3*j];
            linePos[idx++] = pos[3*j+1];
            linePos[idx++] = pos[3*j+2];
          }
        }
      }
      lineGeom.setDrawRange(0, idx/3);
      lineGeom.attributes.position.needsUpdate = true;

      renderer.render(scene, camera);
    }
    animate();

    // Resize
    window.addEventListener('resize', () => {
      renderer.setSize(window.innerWidth, window.innerHeight);
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
    });
  </script>
</body>
</html>
