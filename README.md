<!DOCTYPE html>
<html lang="fr" class="scroll-smooth">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>AI Quiz – Professeurs France</title>
  <meta name="description" content="Génère ton quiz corrigé en 30s – niveau exact – programme respecté.">
  <style>
    /* ---------- RESET 2025 ---------- */
    *,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
    html{scroll-behavior:smooth}
    body{font-family:'Inter',-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,'Helvetica Neue',Arial,sans-serif;background:#0d1117;color:#c9d1d9;line-height:1.6}
    img{max-width:100%;display:block}
    button,input,select,textarea{font:inherit}

    /* ---------- FONTS ---------- */
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');

    /* ---------- VARIABLES 2025 ---------- */
    :root{
      --primary:#38bdf8;
      --primary-dark:#0ea5e9;
      --bg:#0d1117;
      --card:#161b22;
      --border:#30363d;
      --text:#c9d1d9;
      --text-light:#8b949e;
      --success:#34d399;
      --error:#f87171;
      --radius:12px;
      --shadow:0 10px 30px -10px rgba(0,0,0,.5);
    }

    /* ---------- UTILITIES ---------- */
    .container{width:90%;max-width:900px;margin:auto;padding:2rem}
    .flex{display:flex;align-items:center;justify-content:space-between}
    .btn{background:var(--primary);color:#fff;border:none;border-radius:var(--radius);padding:.9rem 1.8rem;font-weight:600;cursor:pointer;transition:.3s;box-shadow:var(--shadow)}
    .btn:hover{background:var(--primary-dark)}
    .glass{background:rgba(255,255,255,.05);backdrop-filter:blur(10px);border:1px solid var(--border);border-radius:var(--radius);padding:2rem;box-shadow:var(--shadow)}

    /* ---------- HERO ---------- */
    .hero{text-align:center;padding:4rem 0}
    .hero h1{font-size:clamp(2.2rem,5vw,3.5rem);font-weight:700;background:linear-gradient(90deg,var(--primary),#a78bfa);-webkit-background-clip:text;-webkit-text-fill-color:transparent}
    .hero p{font-size:1.1rem;color:var(--text-light);margin-top:.5rem}
    .hero .btn{margin-top:2rem}

    /* ---------- FORM ---------- */
    .form-section{padding:3rem 0}
    .form-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(250px,1fr));gap:1.2rem}
    .form-group{display:flex;flex-direction:column}
    .form-group label{font-weight:500;margin-bottom:.3rem}
    .form-group input,.form-group select,.form-group textarea{background:var(--card);border:1px solid var(--border);border-radius:var(--radius);padding:.9rem;color:var(--text);transition:.3s}
    .form-group input:focus,.form-group select:focus,.form-group textarea:focus{outline:none;border-color:var(--primary)}
    .file-input{padding:.6rem;background:var(--card);border:1px dashed var(--border);border-radius:var(--radius);cursor:pointer;text-align:center;color:var(--text-light)}
    .file-input:hover{border-color:var(--primary)}
    #pdfFile{display:none}
    #fileName{margin-top:.5rem;font-size:.9rem;color:var(--text-light)}

    /* ---------- PROGRESS ---------- */
    #progress{width:100%;background:var(--border);border-radius:var(--radius);height:8px;margin:1.5rem 0;overflow:hidden}
    #bar{height:100%;background:linear-gradient(90deg,var(--primary),var(--success));width:0%;border-radius:var(--radius);transition:width .4s ease}

    /* ---------- FOOTER ---------- */
    footer{text-align:center;padding:2rem 0;font-size:.9rem;color:var(--text-light)}
  </style>
</head>
<body>
  <header class="hero">
    <h1>AI Quiz</h1>
    <p>Génère ton quiz corrigé en 30s – niveau exact – programme respecté.</p>
    <a href="#form" class="btn">Créer mon quiz</a>
  </header>

  <section id="form" class="form-section container">
    <div class="glass">
      <form id="quizForm">
        <div class="form-grid">
          <div class="form-group"><label>Email</label><input type="email" name="email" required></div>
          <div class="form-group"><label>Niveau</label>
            <select name="niveau" required><option>CM1</option><option>CM2</option><option>6e</option><option>5e</option><option>4e</option><option>3e</option><option>2nde</option><option>1re</option><option>Term</option><option>BTS</option><option>Autre</option></select>
          </div>
          <div class="form-group"><label>Matière</label>
            <select name="matiere" required><option>Français</option><option>Maths</option><option>Physique</option><option>HG</option><option>SES</option><option>SVT</option><option>Techno</option><option>Autre</option></select>
          </div>
          <div class="form-group"><label>Type de questions</label>
            <select name="typeQ" required><option>QCM</option><option>Vrai/Faux</option><option>Ouvert</option><option>Numérique</option></select>
          </div>
          <div class="form-group"><label>Nombre de questions</label>
            <select name="nbQ" required><option>5</option><option>10</option><option>15</option><option>20</option><option>25</option></select>
          </div>
          <div class="form-group"><label>Temps par question</label>
            <select name="temps" required><option>30s</option><option>1min</option><option>2min</option><option>Sans chrono</option></select>
          </div>
          <div class="form-group"><label>Mots-clés à inclure</label><textarea name="motsIn" placeholder="photosynthèse, Thalès..."></textarea></div>
          <div class="form-group"><label>Mots à exclure</label><textarea name="motsOut" placeholder="Covid, guerre..."></textarea></div>
          <div class="form-group">
            <label>Upload ton cours (PDF)</label>
            <div class="file-input" onclick="document.getElementById('pdfFile').click()">📎 Clique ou dépose ton PDF ici</div>
            <input type="file" id="pdfFile" accept="application/pdf" required>
            <div id="fileName"></div>
          </div>
        </div>

        <div id="progress"><div id="bar"></div></div>
        <div id="msg"></div>
        <button type="submit" id="submitBtn">Générer mon quiz (12€)</button>
      </form>
    </div>
  </section>

  <footer class="container">
    © 2025 AI Quiz – France
  </footer>

  <script>
    const form = document.getElementById('quizForm');
    const bar = document.getElementById('bar');
    const msg = document.getElementById('msg');
    const submitBtn = document.getElementById('submitBtn');
    const fileInput = document.getElementById('pdfFile');
    const fileName = document.getElementById('fileName');

    fileInput.addEventListener('change', () => {
      fileName.textContent = fileInput.files[0]?.name || '';
    });

    form.addEventListener('submit', async (e) => {
      e.preventDefault();
      submitBtn.disabled = true;
      bar.style.width = '0%';
      msg.textContent = 'Upload en cours...';
      bar.style.width = '30%';

      const file = fileInput.files[0];
      const formData = new FormData();
      formData.append('file', file);
      formData.append('email', form.email.value);
      formData.append('niveau', form.niveau.value);
      formData.append('matiere', form.matiere.value);
      formData.append('typeQ', form.typeQ.value);
      formData.append('nbQ', form.nbQ.value);
      formData.append('temps', form.temps.value);
      formData.append('motsIn', form.motsIn.value);
      formData.append('motsOut', form.motsOut.value);

      bar.style.width = '70%';
      msg.textContent = 'Génération du quiz...';

      const SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbYOUR_SCRIPT_ID/exec'; // ← ton URL Web app
      const resp = await fetch(SCRIPT_URL, { method: 'POST', body: formData });
      const data = await resp.json();

      bar.style.width = '100%';
      msg.textContent = 'Redirection vers paiement...';
      setTimeout(() => {
        window.location.href = data.paymentUrl; // vers Airtm
      }, 800);
    });
  </script>
</body>
</html>
