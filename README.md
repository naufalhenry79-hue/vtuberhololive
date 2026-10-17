# vtuberhololivequiz
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Kuis Tebak VTuber</title>
<link href="https://fonts.googleapis.com/css2?family=Zen+Maru+Gothic:wght@500;700&display=swap" rel="stylesheet">
<style>
  :root {
    --ink: #14122b;
    --panel: #1f1c3d;
    --paper: #f4eefc;
    --muted: #a59fc9;
    --accent: #ff6b9d;
    --ok: #5fd39a;
    --bad: #ff6b6b;
    color-scheme: dark;
  }
  @media (prefers-color-scheme: light) {
    :root:not([data-theme="dark"]) {
      --ink: #f4eefc;
      --panel: #ffffff;
      --paper: #14122b;
      --muted: #6b6690;
      --accent: #e0457b;
      --ok: #1f9d63;
      --bad: #d63c3c;
      color-scheme: light;
    }
  }
  * { box-sizing: border-box; }
  :root { padding-top: env(safe-area-inset-top, 0px); padding-bottom: env(safe-area-inset-bottom, 0px); }
  html, body { margin: 0; min-height: 100%; }
  body {
    background: var(--ink);
    color: var(--paper);
    font-family: "Zen Maru Gothic", "Hiragino Sans", "Segoe UI", system-ui, sans-serif;
    line-height: 1.6;
    padding: 20px 16px 40px;
  }
  main { max-width: 560px; margin: 0 auto; }
  h1 { font-size: 1.6rem; margin: 0 0 4px; font-weight: 700; }
  .sub { color: var(--muted); margin: 0 0 20px; font-size: 0.95rem; }
  .card {
    background: var(--panel);
    border-radius: 18px;
    padding: 22px 20px;
    margin-bottom: 16px;
  }
  .progress {
    height: 6px; border-radius: 99px; background: color-mix(in srgb, var(--paper) 12%, transparent);
    overflow: hidden; margin-bottom: 14px;
  }
  .progress > div { height: 100%; background: var(--accent); width: 0; transition: width .3s ease; }
  .meta { display: flex; justify-content: space-between; color: var(--muted); font-size: 0.9rem; margin-bottom: 8px; }
  .clue { font-size: 1.15rem; font-weight: 500; margin: 0 0 18px; }
  .opts { display: grid; gap: 10px; }
  button {
    font: inherit; color: inherit; cursor: pointer;
    border: 2px solid color-mix(in srgb, var(--paper) 18%, transparent);
    background: transparent; border-radius: 12px; padding: 12px 14px;
    text-align: left; font-size: 1rem; min-height: 46px;
  }
  button:focus-visible { outline: 3px solid var(--accent); outline-offset: 2px; }
  .opts button:hover:not(:disabled) { border-color: var(--accent); }
  .opts button.correct { border-color: var(--ok); background: color-mix(in srgb, var(--ok) 18%, transparent); }
  .opts button.wrong { border-color: var(--bad); background: color-mix(in srgb, var(--bad) 18%, transparent); }
  .opts button:disabled { cursor: default; }
  .primary {
    background: var(--accent); border-color: var(--accent); color: #fff;
    font-weight: 700; text-align: center; margin-top: 16px; width: 100%;
  }
  .hidden { display: none; }
  .feedback { margin-top: 12px; font-weight: 700; min-height: 1.5em; }
  .feedback.ok { color: var(--ok); }
  .feedback.bad { color: var(--bad); }
  .score-big { font-size: 3.6rem; font-weight: 700; text-align: center; margin: 8px 0; color: var(--accent); }
  .score-label { text-align: center; color: var(--muted); margin-bottom: 18px; }
  .review-item { border-top: 1px solid color-mix(in srgb, var(--paper) 12%, transparent); padding: 12px 0; }
  .review-item:first-child { border-top: none; padding-top: 0; }
  .review-q { font-weight: 500; margin: 0 0 6px; }
  .review-a { margin: 2px 0; font-size: 0.95rem; }
  .tag-ok { color: var(--ok); font-weight: 700; }
  .tag-bad { color: var(--bad); font-weight: 700; }
  .stats { display: flex; gap: 10px; margin-bottom: 4px; }
  .stat { flex: 1; text-align: center; background: color-mix(in srgb, var(--paper) 6%, transparent); border-radius: 12px; padding: 12px 6px; }
  .stat b { display: block; font-size: 1.5rem; }
  .stat span { color: var(--muted); font-size: 0.85rem; }
</style>
</head>
<body>
<main>
  <h1>Kuis Tebak VTuber</h1>
  <p class="sub">15 soal, tebak siapa VTuber-nya dari petunjuknya.</p>

  <section id="start" class="card">
    <p class="clue">Siap menebak? Setiap soal bernilai sekitar 6,67 poin, total skor 100.</p>
    <button class="primary" id="startBtn">Mulai kuis</button>
  </section>

  <section id="quiz" class="card hidden">
    <div class="meta"><span id="qNum"></span><span id="qScore"></span></div>
    <div class="progress"><div id="bar"></div></div>
    <p class="clue" id="qText"></p>
    <div class="opts" id="opts"></div>
    <div class="feedback" id="feedback"></div>
    <button class="primary hidden" id="nextBtn">Lanjut</button>
  </section>

  <section id="result" class="card hidden">
    <p class="score-label">Skor kamu</p>
    <div class="score-big" id="finalScore"></div>
    <p class="score-label" id="verdict"></p>
    <div class="stats">
      <div class="stat"><b id="cBenar">0</b><span>Benar</span></div>
      <div class="stat"><b id="cSalah">0</b><span>Salah</span></div>
      <div class="stat"><b id="cTotal">15</b><span>Total soal</span></div>
    </div>
  </section>

  <section id="review" class="card hidden">
    <h2 style="margin:0 0 12px;font-size:1.1rem;">Ulasan jawaban</h2>
    <div id="reviewList"></div>
    <button class="primary" id="restartBtn">Main lagi</button>
  </section>
</main>

<script>
const QUESTIONS = [
  { clue: "Maskot kelinci Hololive JP yang terkenal dengan tawa khasnya \"peko\".", options: ["Usada Pekora", "Shirakami Fubuki", "Tsunomaki Watame", "Ookami Mio"], answer: 0 },
  { clue: "VTuber hiu dari HoloEN generasi Myth.", options: ["Mori Calliope", "Gawr Gura", "Ceres Fauna", "Takanashi Kiara"], answer: 1 },
  { clue: "VTuber rubah putih dari Hololive generasi 0.", options: ["Shirakami Fubuki", "Nakiri Ayame", "Inugami Korone", "Hoshimachi Suisei"], answer: 0 },
  { clue: "VTuber bertema malaikat maut (Grim Reaper) dari HoloEN Myth.", options: ["Ninomae Ina'nis", "Mori Calliope", "Amane Kanata", "Houshou Marine"], answer: 1 },
  { clue: "VTuber gurita (tako) dari HoloEN Myth.", options: ["Ninomae Ina'nis", "Takanashi Kiara", "Gawr Gura", "Shirogane Noel"], answer: 0 },
  { clue: "VTuber phoenix atau burung api dari HoloEN Myth.", options: ["Ceres Fauna", "Sakura Miko", "Takanashi Kiara", "Mori Calliope"], answer: 2 },
  { clue: "VTuber bertema komet yang juga dikenal sebagai penyanyi.", options: ["Hoshimachi Suisei", "Amane Kanata", "Ookami Mio", "Usada Pekora"], answer: 0 },
  { clue: "VTuber bertema bajak laut dari Hololive JP.", options: ["Houshou Marine", "Tokino Sora", "Shirogane Noel", "Nakiri Ayame"], answer: 0 },
  { clue: "VTuber bertema miko (pendeta wanita) dari Hololive JP.", options: ["Sakura Miko", "Amane Kanata", "Inugami Korone", "Ceres Fauna"], answer: 0 },
  { clue: "VTuber bertema domba dengan tanduk dari Hololive JP.", options: ["Tsunomaki Watame", "Usada Pekora", "Hoshimachi Suisei", "Shirakami Fubuki"], answer: 0 },
  { clue: "VTuber bertema serigala dari Hololive JP.", options: ["Ookami Mio", "Inugami Korone", "Gawr Gura", "Tokino Sora"], answer: 0 },
  { clue: "VTuber bertema ksatria (knight) dari Hololive JP.", options: ["Nakiri Ayame", "Shirogane Noel", "Houshou Marine", "Amane Kanata"], answer: 1 },
  { clue: "VTuber bertema oni (iblis merah) dari Hololive JP.", options: ["Nakiri Ayame", "Usada Pekora", "Takanashi Kiara", "Ceres Fauna"], answer: 0 },
  { clue: "VTuber bertema anjing dari Hololive JP.", options: ["Ookami Mio", "Inugami Korone", "Tsunomaki Watame", "Shirakami Fubuki"], answer: 1 },
  { clue: "VTuber bertema malaikat putih dari Hololive JP.", options: ["Amane Kanata", "Sakura Miko", "Hoshimachi Suisei", "Tokino Sora"], answer: 0 }
];

const $ = id => document.getElementById(id);
let current = 0, correctCount = 0, answers = [];
const letters = ["A", "B", "C", "D"];

function show(id) {
  ["start", "quiz", "result", "review"].forEach(s => $(s).classList.add("hidden"));
  $(id).classList.remove("hidden");
}

function startQuiz() {
  current = 0; correctCount = 0; answers = [];
  show("quiz");
  renderQuestion();
}

function renderQuestion() {
  const q = QUESTIONS[current];
  $("qNum").textContent = `Soal ${current + 1} dari ${QUESTIONS.length}`;
  $("qScore").textContent = `Benar: ${correctCount}`;
  $("bar").style.width = `${(current / QUESTIONS.length) * 100}%`;
  $("qText").textContent = q.clue;
  $("feedback").textContent = "";
  $("feedback").className = "feedback";
  $("nextBtn").classList.add("hidden");
  $("nextBtn").textContent = current === QUESTIONS.length - 1 ? "Lihat hasil" : "Lanjut";

  const wrap = $("opts");
  wrap.innerHTML = "";
  q.options.forEach((opt, i) => {
    const b = document.createElement("button");
    b.textContent = `${letters[i]}. ${opt}`;
    b.addEventListener("click", () => choose(i));
    wrap.appendChild(b);
  });
}

function choose(i) {
  const q = QUESTIONS[current];
  const btns = $("opts").querySelectorAll("button");
  btns.forEach(b => b.disabled = true);
  const isRight = i === q.answer;
  if (isRight) correctCount++;
  answers.push({ chosen: i, correct: isRight });

  btns[q.answer].classList.add("correct");
  if (!isRight) btns[i].classList.add("wrong");

  const fb = $("feedback");
  fb.textContent = isRight ? "Benar!" : `Salah. Jawaban yang benar: ${q.options[q.answer]}`;
  fb.className = "feedback " + (isRight ? "ok" : "bad");
  $("qScore").textContent = `Benar: ${correctCount}`;
  $("nextBtn").classList.remove("hidden");
}

function nextStep() {
  current++;
  if (current < QUESTIONS.length) {
    renderQuestion();
  } else {
    finish();
  }
}

function finish() {
  $("bar").style.width = "100%";
  const total = QUESTIONS.length;
  const score = Math.round((correctCount / total) * 100);
  $("finalScore").textContent = `${score}/100`;
  $("cBenar").textContent = correctCount;
  $("cSalah").textContent = total - correctCount;
  $("cTotal").textContent = total;

  let verdict = "Cukup baik, coba lagi!";
  if (score === 100) verdict = "Sempurna! Kamu benar-benar hafal para VTuber.";
  else if (score >= 80) verdict = "Hebat, hampir sempurna!";
  else if (score >= 60) verdict = "Lumayan, kamu cukup kenal Hololive.";
  else if (score < 40) verdict = "Masih banyak yang bisa dipelajari, yuk kenalan lagi dengan talent-talent-nya!";
  $("verdict").textContent = verdict;

  renderReview();
  ["start", "quiz"].forEach(s => $(s).classList.add("hidden"));
  $("result").classList.remove("hidden");
  $("review").classList.remove("hidden");
  window.scrollTo(0, 0);
}

function renderReview() {
  const list = $("reviewList");
  list.innerHTML = "";
  QUESTIONS.forEach((q, idx) => {
    const a = answers[idx];
    const item = document.createElement("div");
    item.className = "review-item";

    const qp = document.createElement("p");
    qp.className = "review-q";
    qp.textContent = `${idx + 1}. ${q.clue}`;

    const yours = document.createElement("p");
    yours.className = "review-a";
    yours.innerHTML = a.correct
      ? `<span class="tag-ok">✔ Benar</span> Jawabanmu: ${q.options[a.chosen]}`
      : `<span class="tag-bad">✘ Salah</span> Jawabanmu: ${q.options[a.chosen]}`;

    item.appendChild(qp);
    item.appendChild(yours);
    if (!a.correct) {
      const right = document.createElement("p");
      right.className = "review-a";
      right.textContent = `Jawaban benar: ${q.options[q.answer]}`;
      item.appendChild(right);
    }
    list.appendChild(item);
  });
}

function restart() {
  show("start");
}

$("startBtn").addEventListener("click", startQuiz);
$("nextBtn").addEventListener("click", nextStep);
$("restartBtn").addEventListener("click", restart);
</script>
</body>
</html>
