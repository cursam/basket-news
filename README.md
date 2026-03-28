<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8" />
  <title>Il mio guardaroba AI</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body {
      margin: 0;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
      background: #0f172a;
      color: #e5e7eb;
    }
    header {
      padding: 16px;
      text-align: center;
      background: #020617;
      position: sticky;
      top: 0;
      z-index: 10;
    }
    h1 {
      font-size: 20px;
      margin: 0;
    }
    main {
      padding: 16px;
      max-width: 480px;
      margin: 0 auto;
    }
    .card {
      background: #020617;
      border-radius: 16px;
      padding: 16px;
      margin-bottom: 16px;
      border: 1px solid #1f2937;
    }
    .card h2 {
      font-size: 16px;
      margin-top: 0;
    }
    .btn {
      display: inline-block;
      padding: 10px 14px;
      border-radius: 999px;
      border: none;
      background: #22c55e;
      color: #022c22;
      font-weight: 600;
      font-size: 14px;
      cursor: pointer;
    }
    .btn.secondary {
      background: #111827;
      color: #e5e7eb;
      border: 1px solid #374151;
    }
    .grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 8px;
      margin-top: 8px;
    }
    .item {
      position: relative;
      border-radius: 12px;
      overflow: hidden;
      border: 1px solid #1f2937;
      background: #020617;
      height: 100px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 12px;
      color: #9ca3af;
    }
    .item img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
    .tag {
      font-size: 10px;
      padding: 2px 6px;
      border-radius: 999px;
      background: #111827;
      color: #9ca3af;
      display: inline-block;
      margin-right: 4px;
      margin-top: 4px;
    }
    input[type="file"] {
      display: none;
    }
    label.file-label {
      display: inline-block;
      padding: 8px 12px;
      border-radius: 999px;
      border: 1px dashed #4b5563;
      font-size: 12px;
      color: #9ca3af;
      cursor: pointer;
      margin-top: 8px;
    }
    .outfit-result {
      margin-top: 8px;
      font-size: 14px;
      color: #d1d5db;
    }
  </style>
</head>
<body>
  <header>
    <h1>Il mio guardaroba AI</h1>
  </header>

  <main>
    <!-- Sezione: foto persona -->
    <section class="card">
      <h2>1. La tua foto</h2>
      <p>Carica una tua foto intera per creare outfit su di te.</p>
      <label for="person-photo" class="file-label">Carica foto persona</label>
      <input type="file" id="person-photo" accept="image/*" />
      <div id="person-preview" class="item" style="margin-top:8px; display:none;"></div>
    </section>

    <!-- Sezione: capi d'abbigliamento -->
    <section class="card">
      <h2>2. I tuoi capi</h2>
      <p>Fotografa solo i tuoi vestiti. Questo è il tuo guardaroba personale.</p>
      <label for="clothes-input" class="file-label">Aggiungi capi</label>
      <input type="file" id="clothes-input" accept="image/*" multiple />
      <div id="clothes-grid" class="grid"></div>
    </section>

    <!-- Sezione: generazione outfit -->
    <section class="card">
      <h2>3. Crea outfit</h2>
      <p>Scegli se lasciare decidere all’AI o selezionare tu alcuni capi.</p>
      <button class="btn" id="auto-outfit-btn">Crea outfit automatico</button>
      <button class="btn secondary" id="manual-outfit-btn">Seleziono io i capi</button>

      <div id="outfit-result" class="outfit-result"></div>
    </section>
  </main>

  <script>
    const personInput = document.getElementById('person-photo');
    const personPreview = document.getElementById('person-preview');
    const clothesInput = document.getElementById('clothes-input');
    const clothesGrid = document.getElementById('clothes-grid');
    const autoOutfitBtn = document.getElementById('auto-outfit-btn');
    const manualOutfitBtn = document.getElementById('manual-outfit-btn');
    const outfitResult = document.getElementById('outfit-result');

    const clothes = []; // qui salviamo i capi caricati

    personInput.addEventListener('change', (e) => {
      const file = e.target.files[0];
      if (!file) return;
      const url = URL.createObjectURL(file);
      personPreview.style.display = 'flex';
      personPreview.innerHTML = '<img src="' + url + '" alt="Persona" />';
    });

    clothesInput.addEventListener('change', (e) => {
      const files = Array.from(e.target.files);
      files.forEach((file) => {
        const url = URL.createObjectURL(file);
        const id = Date.now() + '_' + Math.random().toString(16).slice(2);
        clothes.push({ id, url });
        const div = document.createElement('div');
        div.className = 'item';
        div.dataset.id = id;
        div.innerHTML = '<img src="' + url + '" alt="Capo" />';
        clothesGrid.appendChild(div);
      });
    });

    // QUI andrà collegata l’AI vera
    function fakeAIChooseOutfit() {
      if (clothes.length === 0) {
        return {
          message: 'Carica almeno 3 capi per creare un outfit.',
          items: []
        };
      }
      const shuffled = [...clothes].sort(() => Math.random() - 0.5);
      const chosen = shuffled.slice(0, Math.min(3, shuffled.length));
      return {
        message: 'Ho creato un outfit di prova con ' + chosen.length + ' capi.',
        items: chosen
      };
    }

    autoOutfitBtn.addEventListener('click', () => {
      const result = fakeAIChooseOutfit();
      if (result.items.length === 0) {
        outfitResult.textContent = result.message;
        return;
      }
      outfitResult.innerHTML = `
        <p>${result.message}</p>
        <div class="grid">
          ${result.items
            .map(
              (c) =>
                `<div class="item"><img src="${c.url}" alt="Capo scelto" /></div>`
            )
            .join('')}
        </div>
      `;
    });

    manualOutfitBtn.addEventListener('click', () => {
      outfitResult.innerHTML =
        '<p>Modalità manuale (prototipo): tocca i capi che vuoi usare (da implementare).</p>';
    });
  </script>
</body>
</html>
