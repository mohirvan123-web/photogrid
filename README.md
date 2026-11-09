<!DOCTYPE html>
<html lang="id" class="h-full bg-slate-900 text-slate-100">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Photo Collage Studio - No Ads, 100% Client-Side</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <!-- Google Font: Inter -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">

  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
          },
          colors: {
            brand: {
              50: '#eff6ff',
              500: '#3b82f6',
              600: '#2563eb',
              700: '#1d4ed8',
            }
          }
        }
      }
    }
  </script>
  <style>
    /* Custom Scrollbar */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #0f172a;
    }
    ::-webkit-scrollbar-thumb {
      background: #334155;
      border-radius: 9999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #475569;
    }
    .canvas-container {
      box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
      background-image: radial-gradient(#334155 1px, transparent 1px);
      background-size: 16px 16px;
    }
  </style>
</head>
<body class="h-full font-sans antialiased flex flex-col bg-slate-950 text-slate-100 overflow-x-hidden select-none">

  <!-- HEADER -->
  <header class="h-16 border-b border-slate-800 bg-slate-900/80 backdrop-blur px-4 md:px-6 flex items-center justify-between shrink-0 z-20">
    <div class="flex items-center gap-3">
      <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-blue-600 to-indigo-500 flex items-center justify-center text-white shadow-lg shadow-blue-500/30">
        <i class="fa-solid fa-shapes text-xl"></i>
      </div>
      <div>
        <h1 class="font-bold text-lg leading-tight tracking-tight text-white flex items-center gap-2">
          CollageStudio
          <span class="text-[10px] uppercase tracking-wider font-semibold bg-blue-500/10 text-blue-400 px-2 py-0.5 rounded-full border border-blue-500/20">Free & Ad-Free</span>
        </h1>
        <p class="text-xs text-slate-400 hidden sm:block">Buat kolase foto instan tanpa iklan & tanpa upload server</p>
      </div>
    </div>

    <!-- Quick Action / Resolution Indicator -->
    <div class="flex items-center gap-3">
      <div class="hidden lg:flex items-center gap-2 bg-slate-800/80 px-3 py-1.5 rounded-lg border border-slate-700/50 text-xs text-slate-300">
        <i class="fa-solid fa-expand text-blue-400"></i>
        <span>Dimensi: <strong id="resIndicatorText" class="text-white font-mono">1080 x 1080 px</strong></span>
      </div>
      <button id="btnDownloadHeader" class="bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-500 hover:to-indigo-500 text-white font-medium text-sm px-4 py-2 rounded-lg flex items-center gap-2 shadow-lg shadow-blue-600/25 transition active:scale-95">
        <i class="fa-solid fa-download"></i>
        <span class="hidden sm:inline">Unduh Kolase</span>
      </button>
    </div>
  </header>

  <!-- MAIN APP CONTAINER -->
  <div class="flex-1 flex flex-col md:flex-row overflow-hidden relative">

    <!-- LEFT SIDEBAR: CONTROLS & UPLOAD -->
    <aside class="w-full md:w-80 lg:w-96 bg-slate-900 border-r border-slate-800 flex flex-col shrink-0 overflow-y-auto z-10 max-h-[50vh] md:max-h-none">
      
      <div class="p-4 space-y-6">

        <!-- 1. UPLOAD SECTION -->
        <section class="space-y-3">
          <div class="flex items-center justify-between">
            <h2 class="text-sm font-semibold uppercase tracking-wider text-slate-400 flex items-center gap-2">
              <i class="fa-solid fa-images text-blue-400"></i> 1. Unggah Foto
            </h2>
            <span id="imageCountBadge" class="text-xs font-mono bg-slate-800 px-2 py-0.5 rounded text-slate-400">0 Foto</span>
          </div>

          <!-- Dropzone -->
          <label id="dropZone" class="border-2 border-dashed border-slate-700 hover:border-blue-500 bg-slate-800/50 hover:bg-slate-800 transition rounded-xl p-5 text-center cursor-pointer flex flex-col items-center justify-center gap-2 group">
            <input type="file" id="fileInput" multiple accept="image/*" class="hidden">
            <div class="w-12 h-12 rounded-full bg-slate-800 group-hover:bg-blue-600/20 text-slate-400 group-hover:text-blue-400 flex items-center justify-center transition">
              <i class="fa-solid fa-cloud-arrow-up text-xl"></i>
            </div>
            <div>
              <p class="text-sm font-medium text-slate-200">Klik atau drag foto ke sini</p>
              <p class="text-xs text-slate-400 mt-0.5">Mendukung JPG, PNG, WEBP (Bisa banyak)</p>
            </div>
          </label>

          <!-- Uploaded Images Thumbnails List -->
          <div id="thumbnailsList" class="grid grid-cols-4 gap-2 pt-1 max-h-40 overflow-y-auto pr-1">
            <!-- Dynamic thumbnails added via JS -->
          </div>
        </section>

        <hr class="border-slate-800">

        <!-- 2. LAYOUT & ASPECT RATIO -->
        <section class="space-y-4">
          <h2 class="text-sm font-semibold uppercase tracking-wider text-slate-400 flex items-center gap-2">
            <i class="fa-solid fa-table-cells text-blue-400"></i> 2. Layout & Ukuran
          </h2>

          <!-- Aspect Ratio Options -->
          <div>
            <label class="text-xs text-slate-400 mb-1.5 block">Rasio Aspek Canvas</label>
            <div class="grid grid-cols-4 gap-2">
              <button data-aspect="1:1" class="btn-aspect active border border-blue-500 bg-blue-600/10 text-blue-400 p-2 rounded-lg text-xs font-medium flex flex-col items-center gap-1 transition">
                <i class="fa-regular fa-square text-base"></i> 1:1
              </button>
              <button data-aspect="4:5" class="btn-aspect border border-slate-700 bg-slate-800/60 text-slate-300 hover:border-slate-600 p-2 rounded-lg text-xs font-medium flex flex-col items-center gap-1 transition">
                <i class="fa-solid fa-mobile-screen text-base"></i> 4:5
              </button>
              <button data-aspect="16:9" class="btn-aspect border border-slate-700 bg-slate-800/60 text-slate-300 hover:border-slate-600 p-2 rounded-lg text-xs font-medium flex flex-col items-center gap-1 transition">
                <i class="fa-solid fa-desktop text-base"></i> 16:9
              </button>
              <button data-aspect="9:16" class="btn-aspect border border-slate-700 bg-slate-800/60 text-slate-300 hover:border-slate-600 p-2 rounded-lg text-xs font-medium flex flex-col items-center gap-1 transition">
                <i class="fa-solid fa-file-image text-base"></i> 9:16
              </button>
            </div>
          </div>

          <!-- Grid Presets -->
          <div>
            <label class="text-xs text-slate-400 mb-1.5 block">Pola Grid Slot</label>
            <div class="grid grid-cols-3 gap-2" id="gridPresetsContainer">
              <!-- Dynamically populated grid preset buttons -->
            </div>
          </div>

          <!-- Quality / Resolution Selector -->
          <div>
            <label class="text-xs text-slate-400 mb-1.5 block">Kualitas Hasil (Resolusi)</label>
            <select id="selectResolution" class="w-full bg-slate-800 border border-slate-700 rounded-lg px-3 py-2 text-xs text-slate-200 focus:outline-none focus:border-blue-500">
              <option value="1080">Standard HD (1080p Max)</option>
              <option value="2048" selected>High Quality 2K (2048p Max)</option>
              <option value="3840">Ultra HD 4K (3840p Max)</option>
            </select>
          </div>
        </section>

        <hr class="border-slate-800">

        <!-- 3. STYLING CONTROLS -->
        <section class="space-y-4">
          <h2 class="text-sm font-semibold uppercase tracking-wider text-slate-400 flex items-center gap-2">
            <i class="fa-solid fa-sliders text-blue-400"></i> 3. Kustomisasi Desain
          </h2>

          <!-- Inner Gap / Padding -->
          <div class="space-y-1">
            <div class="flex justify-between text-xs">
              <span class="text-slate-300">Jarak Antar Foto (Gap)</span>
              <span id="valGap" class="text-slate-400 font-mono">12px</span>
            </div>
            <input type="range" id="sliderGap" min="0" max="50" value="12" class="w-full accent-blue-500 bg-slate-800 h-1.5 rounded-lg appearance-none cursor-pointer">
          </div>

          <!-- Outer Border / Margin -->
          <div class="space-y-1">
            <div class="flex justify-between text-xs">
              <span class="text-slate-300">Bingkai Luar (Outer Margin)</span>
              <span id="valMargin" class="text-slate-400 font-mono">12px</span>
            </div>
            <input type="range" id="sliderMargin" min="0" max="50" value="12" class="w-full accent-blue-500 bg-slate-800 h-1.5 rounded-lg appearance-none cursor-pointer">
          </div>

          <!-- Corner Radius -->
          <div class="space-y-1">
            <div class="flex justify-between text-xs">
              <span class="text-slate-300">Kelengkungan Sudut (Radius)</span>
              <span id="valRadius" class="text-slate-400 font-mono">8px</span>
            </div>
            <input type="range" id="sliderRadius" min="0" max="50" value="8" class="w-full accent-blue-500 bg-slate-800 h-1.5 rounded-lg appearance-none cursor-pointer">
          </div>

          <!-- Background Color -->
          <div class="flex items-center justify-between pt-1">
            <span class="text-xs text-slate-300">Warna Background / Bingkai</span>
            <div class="flex items-center gap-2">
              <input type="color" id="bgColorPicker" value="#ffffff" class="w-8 h-8 rounded border border-slate-700 bg-transparent cursor-pointer p-0.5">
              <div class="flex gap-1">
                <button class="w-5 h-5 rounded-full bg-white border border-slate-600" onclick="setBgColor('#ffffff')"></button>
                <button class="w-5 h-5 rounded-full bg-slate-950 border border-slate-600" onclick="setBgColor('#020617')"></button>
                <button class="w-5 h-5 rounded-full bg-rose-500" onclick="setBgColor('#f43f5e')"></button>
                <button class="w-5 h-5 rounded-full bg-amber-400" onclick="setBgColor('#fbbf24')"></button>
                <button class="w-5 h-5 rounded-full bg-emerald-500" onclick="setBgColor('#10b981')"></button>
              </div>
            </div>
          </div>
        </section>

      </div>

      <!-- FOOTER NOTE -->
      <div class="mt-auto p-4 border-t border-slate-800 text-[11px] text-slate-500 flex items-center gap-2">
        <i class="fa-solid fa-shield-halved text-emerald-500 text-sm"></i>
        <span>Privasi Aman: Foto Anda 100% diproses di browser tanpa diunggah ke internet.</span>
      </div>

    </aside>

    <!-- RIGHT AREA: LIVE PREVIEW CANVAS -->
    <main class="flex-1 bg-slate-950 flex flex-col items-center justify-center p-4 md:p-8 relative overflow-hidden">
      
      <!-- Canvas Wrapper -->
      <div class="canvas-container relative rounded-xl p-3 border border-slate-800 max-w-full max-h-full flex items-center justify-center">
        <canvas id="collageCanvas" class="max-w-full max-h-[70vh] md:max-h-[75vh] object-contain rounded cursor-grab active:cursor-grabbing shadow-2xl transition-all"></canvas>
        
        <!-- Empty State Helper -->
        <div id="emptyState" class="absolute inset-0 flex flex-col items-center justify-center p-6 text-center bg-slate-900/90 rounded-xl backdrop-blur-sm">
          <div class="w-16 h-16 rounded-full bg-slate-800 border border-slate-700 flex items-center justify-center text-slate-400 mb-3">
            <i class="fa-solid fa-photo-film text-2xl"></i>
          </div>
          <h3 class="text-base font-semibold text-slate-200">Belum Ada Foto Terpilih</h3>
          <p class="text-xs text-slate-400 max-w-xs mt-1 mb-4">Unggah 1 atau beberapa foto di sidebar kiri untuk mulai membuat kolase.</p>
          <button onclick="document.getElementById('fileInput').click()" class="bg-blue-600 hover:bg-blue-500 text-white text-xs px-4 py-2 rounded-lg font-medium transition">
            Pilih Foto Sekarang
          </button>
        </div>
      </div>

      <!-- Interactive Drag Hint -->
      <div class="mt-3 text-xs text-slate-400 flex items-center gap-2 bg-slate-900/80 px-3 py-1.5 rounded-full border border-slate-800 shadow-sm">
        <i class="fa-solid fa-hand-pointer text-blue-400"></i>
        <span>Tips: Drag/geser gambar pada canvas untuk menyesuaikan posisi crop.</span>
      </div>

    </main>

  </div>

  <!-- JAVASCRIPT APP LOGIC -->
  <script>
    // --- STATE MANAGEMENT ---
    const state = {
      images: [], // Array of { id, imgElement, offsetX, offsetY }
      layout: '2x2', // Current layout grid type
      aspectRatio: '1:1', // Aspect ratio
      maxRes: 2048, // Resolution ceiling
      gap: 12,
      margin: 12,
      radius: 8,
      bgColor: '#ffffff',
      draggedImageIndex: -1,
      isDraggingCanvas: false,
      dragStartX: 0,
      dragStartY: 0,
      initialImgOffsetX: 0,
      initialImgOffsetY: 0
    };

    // --- GRID LAYOUT PRESETS DEFINITION ---
    // Returns array of slot coordinates normalized from 0 to 1 [x, y, w, h]
    const LAYOUT_PRESETS = {
      '1x1': { name: 'Single', count: 1, slots: [[0, 0, 1, 1]] },
      '1x2': { name: '2 Kolom', count: 2, slots: [[0, 0, 0.5, 1], [0.5, 0, 0.5, 1]] },
      '2x1': { name: '2 Baris', count: 2, slots: [[0, 0, 1, 0.5], [0, 0.5, 1, 0.5]] },
      '2x2': { name: 'Grid 2x2', count: 4, slots: [[0, 0, 0.5, 0.5], [0.5, 0, 0.5, 0.5], [0, 0.5, 0.5, 0.5], [0.5, 0.5, 0.5, 0.5]] },
      '3x1': { name: '3 Kolom', count: 3, slots: [[0, 0, 0.333, 1], [0.333, 0, 0.334, 1], [0.667, 0, 0.333, 1]] },
      '3x3': { name: 'Grid 3x3', count: 9, slots: [
        [0,0,0.333,0.333], [0.333,0,0.334,0.333], [0.667,0,0.333,0.333],
        [0,0.333,0.333,0.334], [0.333,0.333,0.334,0.334], [0.667,0.333,0.333,0.334],
        [0,0.667,0.333,0.333], [0.333,0.667,0.334,0.333], [0.667,0.667,0.333,0.333]
      ]},
      '1main-2side': { name: '1 Utama + 2', count: 3, slots: [[0, 0, 0.667, 1], [0.667, 0, 0.333, 0.5], [0.667, 0.5, 0.333, 0.5]] },
      '2main-1bottom': { name: '2 Atas + 1', count: 3, slots: [[0, 0, 0.5, 0.667], [0.5, 0, 0.5, 0.667], [0, 0.667, 1, 0.333]] }
    };

    // --- DOM ELEMENTS ---
    const canvas = document.getElementById('collageCanvas');
    const ctx = canvas.getContext('2d');
    const fileInput = document.getElementById('fileInput');
    const dropZone = document.getElementById('dropZone');
    const thumbnailsList = document.getElementById('thumbnailsList');
    const emptyState = document.getElementById('emptyState');
    const imageCountBadge = document.getElementById('imageCountBadge');
    const gridPresetsContainer = document.getElementById('gridPresetsContainer');
    const resIndicatorText = document.getElementById('resIndicatorText');

    // Controls
    const sliderGap = document.getElementById('sliderGap');
    const sliderMargin = document.getElementById('sliderMargin');
    const sliderRadius = document.getElementById('sliderRadius');
    const bgColorPicker = document.getElementById('bgColorPicker');
    const selectResolution = document.getElementById('selectResolution');

    // --- INITIALIZATION ---
    window.onload = () => {
      renderGridPresetButtons();
      setupEventListeners();
      updateCanvasDimensions();
      drawCanvas();
    };

    // Render Grid Preset Buttons dynamically
    function renderGridPresetButtons() {
      gridPresetsContainer.innerHTML = '';
      Object.keys(LAYOUT_PRESETS).forEach(key => {
        const preset = LAYOUT_PRESETS[key];
        const btn = document.createElement('button');
        btn.className = `btn-layout border p-2 rounded-lg text-xs font-medium text-center transition ${state.layout === key ? 'border-blue-500 bg-blue-600/10 text-blue-400' : 'border-slate-700 bg-slate-800/60 text-slate-300 hover:border-slate-600'}`;
        btn.setAttribute('data-layout', key);
        btn.innerText = preset.name;
        btn.onclick = () => {
          document.querySelectorAll('.btn-layout').forEach(b => {
            b.classList.remove('border-blue-500', 'bg-blue-600/10', 'text-blue-400');
            b.classList.add('border-slate-700', 'bg-slate-800/60', 'text-slate-300');
          });
          btn.classList.add('border-blue-500', 'bg-blue-600/10', 'text-blue-400');
          state.layout = key;
          drawCanvas();
        };
        gridPresetsContainer.appendChild(btn);
      });
    }

    // --- EVENT LISTENERS SETUP ---
    function setupEventListeners() {
      // File upload inputs
      fileInput.addEventListener('change', handleFileSelect);
      dropZone.addEventListener('dragover', (e) => { e.preventDefault(); dropZone.classList.add('border-blue-500', 'bg-slate-800'); });
      dropZone.addEventListener('dragleave', () => { dropZone.classList.remove('border-blue-500', 'bg-slate-800'); });
      dropZone.addEventListener('drop', (e) => {
        e.preventDefault();
        dropZone.classList.remove('border-blue-500', 'bg-slate-800');
        if (e.dataTransfer.files.length) handleFiles(e.dataTransfer.files);
      });

      // Aspect Ratio Selector
      document.querySelectorAll('.btn-aspect').forEach(btn => {
        btn.addEventListener('click', () => {
          document.querySelectorAll('.btn-aspect').forEach(b => {
            b.classList.remove('border-blue-500', 'bg-blue-600/10', 'text-blue-400');
            b.classList.add('border-slate-700', 'bg-slate-800/60', 'text-slate-300');
          });
          btn.classList.add('border-blue-500', 'bg-blue-600/10', 'text-blue-400');
          state.aspectRatio = btn.dataset.aspect;
          updateCanvasDimensions();
          drawCanvas();
        });
      });

      // Sliders & Controls
      sliderGap.addEventListener('input', (e) => {
        state.gap = parseInt(e.target.value);
        document.getElementById('valGap').innerText = state.gap + 'px';
        drawCanvas();
      });

      sliderMargin.addEventListener('input', (e) => {
        state.margin = parseInt(e.target.value);
        document.getElementById('valMargin').innerText = state.margin + 'px';
        drawCanvas();
      });

      sliderRadius.addEventListener('input', (e) => {
        state.radius = parseInt(e.target.value);
        document.getElementById('valRadius').innerText = state.radius + 'px';
        drawCanvas();
      });

      bgColorPicker.addEventListener('input', (e) => {
        state.bgColor = e.target.value;
        drawCanvas();
      });

      selectResolution.addEventListener('change', (e) => {
        state.maxRes = parseInt(e.target.value);
        updateCanvasDimensions();
        drawCanvas();
      });

      // Download Buttons
      document.getElementById('btnDownloadHeader').addEventListener('click', downloadCanvas);

      // Canvas Mouse Dragging (Adjust Crop Position)
      canvas.addEventListener('mousedown', onCanvasMouseDown);
      window.addEventListener('mousemove', onCanvasMouseMove);
      window.addEventListener('mouseup', onCanvasMouseUp);

      // Touch support for mobile dragging
      canvas.addEventListener('touchstart', (e) => {
        if(e.touches.length === 1) onCanvasMouseDown(e.touches[0]);
      });
      window.addEventListener('touchmove', (e) => {
        if(state.isDraggingCanvas && e.touches.length === 1) onCanvasMouseMove(e.touches[0]);
      });
      window.addEventListener('touchend', onCanvasMouseUp);
    }

    // --- FILE HANDLING ---
    function handleFileSelect(e) {
      if (e.target.files.length) handleFiles(e.target.files);
    }

    function handleFiles(files) {
      Array.from(files).forEach(file => {
        if (!file.type.startsWith('image/')) return;
        const reader = new FileReader();
        reader.onload = (e) => {
          const img = new Image();
          img.onload = () => {
            state.images.push({
              id: Date.now() + Math.random(),
              imgElement: img,
              offsetX: 0,
              offsetY: 0
            });
            updateThumbnails();
            autoSelectBestLayout();
            drawCanvas();
          };
          img.src = e.target.result;
        };
        reader.readAsDataURL(file);
      });
    }

    function autoSelectBestLayout() {
      const count = state.images.length;
      if (count === 1) state.layout = '1x1';
      else if (count === 2) state.layout = '1x2';
      else if (count === 3) state.layout = '1main-2side';
      else if (count === 4) state.layout = '2x2';
      else if (count > 4) state.layout = '3x3';
      
      renderGridPresetButtons();
    }

    function removeImage(index) {
      state.images.splice(index, 1);
      updateThumbnails();
      drawCanvas();
    }

    function setBgColor(hex) {
      state.bgColor = hex;
      bgColorPicker.value = hex;
      drawCanvas();
    }

    function updateThumbnails() {
      thumbnailsList.innerHTML = '';
      imageCountBadge.innerText = `${state.images.length} Foto`;

      if (state.images.length === 0) {
        emptyState.classList.remove('hidden');
      } else {
        emptyState.classList.add('hidden');
      }

      state.images.forEach((item, index) => {
        const div = document.createElement('div');
        div.className = 'relative group rounded-lg overflow-hidden border border-slate-700 aspect-square bg-slate-800';
        div.innerHTML = `
          <img src="${item.imgElement.src}" class="w-full h-full object-cover">
          <button onclick="removeImage(${index})" class="absolute top-1 right-1 bg-red-600/90 text-white w-5 h-5 rounded-full text-[10px] flex items-center justify-center opacity-0 group-hover:opacity-100 transition shadow">
            <i class="fa-solid fa-xmark"></i>
          </button>
          <span class="absolute bottom-1 left-1 bg-slate-900/80 text-slate-300 text-[9px] px-1 rounded font-mono">${index + 1}</span>
        `;
        thumbnailsList.appendChild(div);
      });
    }

    // --- CANVAS RESIZING & ASPECT RATIO ---
    function updateCanvasDimensions() {
      const [wRatio, hRatio] = state.aspectRatio.split(':').map(Number);
      let targetW = state.maxRes;
      let targetH = Math.round((targetW / wRatio) * hRatio);

      if (targetH > state.maxRes) {
        targetH = state.maxRes;
        targetW = Math.round((targetH / hRatio) * wRatio);
      }

      canvas.width = targetW;
      canvas.height = targetH;
      resIndicatorText.innerText = `${targetW} x ${targetH} px`;
    }

    // --- CANVAS MAIN RENDER ENGINE ---
    function drawCanvas() {
      const w = canvas.width;
      const h = canvas.height;

      // Clear & Draw Background
      ctx.clearRect(0, 0, w, h);
      ctx.fillStyle = state.bgColor;
      ctx.fillRect(0, 0, w, h);

      const preset = LAYOUT_PRESETS[state.layout] || LAYOUT_PRESETS['2x2'];
      const slots = preset.slots;

      // Scale factors for margin and gap based on high-res canvas scale
      const scaleFactor = w / 1000;
      const scaledMargin = state.margin * scaleFactor;
      const scaledGap = state.gap * scaleFactor;
      const scaledRadius = state.radius * scaleFactor;

      const availableW = w - (scaledMargin * 2);
      const availableH = h - (scaledMargin * 2);

      slots.forEach((slot, index) => {
        // Calculate raw slot bounding box
        let sx = scaledMargin + (slot[0] * availableW);
        let sy = scaledMargin + (slot[1] * availableH);
        let sw = slot[2] * availableW;
        let sh = slot[3] * availableH;

        // Apply Gap
        const halfGap = scaledGap / 2;
        sx += halfGap;
        sy += halfGap;
        sw -= scaledGap;
        sh -= scaledGap;

        if (sw <= 0 || sh <= 0) return;

        // Save slot area coordinates for interaction hit-testing
        slot._computedRect = { x: sx, y: sy, w: sw, h: sh };

        // Draw image if available for this slot
        if (state.images[index]) {
          const imgObj = state.images[index];
          ctx.save();

          // Create rounded corner clipping path
          createRoundedRectPath(ctx, sx, sy, sw, sh, scaledRadius);
          ctx.clip();

          // Object-fit COVER calculation
          const img = imgObj.imgElement;
          const imgAspect = img.width / img.height;
          const slotAspect = sw / sh;

          let renderW, renderH;
          if (imgAspect > slotAspect) {
            renderH = sh;
            renderW = sh * imgAspect;
          } else {
            renderW = sw;
            renderH = sw / imgAspect;
          }

          // Center position + user offsets
          let rx = sx + (sw - renderW) / 2 + (imgObj.offsetX * scaleFactor);
          let ry = sy + (sh - renderH) / 2 + (imgObj.offsetY * scaleFactor);

          // Draw the photo
          ctx.drawImage(img, rx, ry, renderW, renderH);
          ctx.restore();
        } else {
          // Draw empty slot placeholder
          ctx.save();
          createRoundedRectPath(ctx, sx, sy, sw, sh, scaledRadius);
          ctx.fillStyle = 'rgba(255, 255, 255, 0.05)';
          ctx.fill();
          ctx.strokeStyle = 'rgba(255, 255, 255, 0.15)';
          ctx.lineWidth = 2 * scaleFactor;
          ctx.setLineDash([8 * scaleFactor, 8 * scaleFactor]);
          ctx.stroke();
          ctx.restore();
        }
      });
    }

    // Helper: Rounded Rectangle Path
    function createRoundedRectPath(context, x, y, width, height, radius) {
      const r = Math.min(radius, width / 2, height / 2);
      context.beginPath();
      context.moveTo(x + r, y);
      context.arcTo(x + width, y, x + width, y + height, r);
      context.arcTo(x + width, y + height, x, y + height, r);
      context.arcTo(x, y + height, x, y, r);
      context.arcTo(x, y, x + width, y, r);
      context.closePath();
    }

    // --- CANVAS INTERACTION (DRAG TO OFFSET CROP) ---
    function getCanvasCoordinates(e) {
      const rect = canvas.getBoundingClientRect();
      const scaleX = canvas.width / rect.width;
      const scaleY = canvas.height / rect.height;
      return {
        x: (e.clientX - rect.left) * scaleX,
        y: (e.clientY - rect.top) * scaleY
      };
    }

    function onCanvasMouseDown(e) {
      const coords = getCanvasCoordinates(e);
      const preset = LAYOUT_PRESETS[state.layout];
      if (!preset) return;

      // Find which slot was clicked
      preset.slots.forEach((slot, index) => {
        if (slot._computedRect && state.images[index]) {
          const { x, y, w, h } = slot._computedRect;
          if (coords.x >= x && coords.x <= x + w && coords.y >= y && coords.y <= y + h) {
            state.isDraggingCanvas = true;
            state.draggedImageIndex = index;
            state.dragStartX = coords.x;
            state.dragStartY = coords.y;
            state.initialImgOffsetX = state.images[index].offsetX;
            state.initialImgOffsetY = state.images[index].offsetY;
          }
        }
      });
    }

    function onCanvasMouseMove(e) {
      if (!state.isDraggingCanvas || state.draggedImageIndex === -1) return;

      const coords = getCanvasCoordinates(e);
      const scaleFactor = canvas.width / 1000;
      
      const deltaX = (coords.x - state.dragStartX) / scaleFactor;
      const deltaY = (coords.y - state.dragStartY) / scaleFactor;

      const imgObj = state.images[state.draggedImageIndex];
      imgObj.offsetX = state.initialImgOffsetX + deltaX;
      imgObj.offsetY = state.initialImgOffsetY + deltaY;

      drawCanvas();
    }

    function onCanvasMouseUp() {
      state.isDraggingCanvas = false;
      state.draggedImageIndex = -1;
    }

    // --- DOWNLOAD FUNCTIONALITY ---
    function downloadCanvas() {
      if (state.images.length === 0) {
        alert('Silakan unggah minimal 1 foto terlebih dahulu.');
        return;
      }

      // Create temporary download link
      const dataURL = canvas.toDataURL('image/png', 1.0);
      const link = document.createElement('a');
      link.download = `kolase-foto-${Date.now()}.png`;
      link.href = dataURL;
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }
  </script>
</body>
</html>
