---
title: "VVIQ2 — 視覚イメージ鮮明度質問票"
description: "VVIQ2テスト — アファンタジアからハイパーファンタジアまで、視覚的心的イメージの鮮明度を測定する32項目の質問票。"
---

<!-- section: $VIQ002 "VVIQ2テスト" -->

**視覚イメージ鮮明度質問票**（VVIQ2）は、David Marksが開発した心的イメージの明瞭さと鮮明度を測定する32項目の自己評価尺度である。**アファンタジア**（心的イメージなし）から**ハイパーファンタジア**（極めて鮮明なイメージ）までのスペクトルを評価する標準的な測定器具。

各項目について、目を閉じて描写された場面のイメージを心の中に形成し、心の目に見えるものの鮮明度を評価してください。

<div id="vviq-app">
<div id="vviq-quiz">
<div id="vviq-progress"></div>
<div id="vviq-section-name"></div>
<div id="vviq-section-prompt"></div>
<div id="vviq-question"></div>
<div id="vviq-options"></div>
<div id="vviq-nav"></div>
</div>
<div id="vviq-result" style="display:none">
<div id="vviq-score-title"></div>
<div id="vviq-score-value"></div>
<div id="vviq-score-bar-wrap"><div id="vviq-score-bar"></div></div>
<div id="vviq-score-label"></div>
<div id="vviq-score-desc"></div>
<div id="vviq-restart-wrap"><button id="vviq-restart">もう一度</button></div>
</div>
</div>

<style>
#vviq-app { font-family: 'IBM Plex Mono', monospace; margin: 32px 0; }
#vviq-progress { font-size: 11px; color: var(--text-dim); letter-spacing: 0.1em; margin-bottom: 16px; }
#vviq-section-name { font-size: 16px; color: var(--amber); font-weight: 600; margin-bottom: 8px; }
#vviq-section-prompt { font-family: 'Spectral', serif; font-size: 16px; color: var(--text); font-style: italic; margin-bottom: 24px; line-height: 1.7; max-width: 600px; }
#vviq-question { font-size: 15px; color: var(--text-bright); margin-bottom: 20px; line-height: 1.6; max-width: 600px; }
#vviq-options { display: flex; flex-direction: column; gap: 6px; margin-bottom: 24px; }
#vviq-options button { background: var(--bg-card); border: 1px solid var(--border); color: var(--text); font-family: 'IBM Plex Mono', monospace; font-size: 13px; padding: 10px 16px; text-align: left; cursor: pointer; border-radius: 3px; transition: border-color 0.15s, background 0.15s, color 0.15s; }
#vviq-options button:hover { border-color: var(--amber-dim); background: var(--bg-surface); }
#vviq-options button.selected { border-color: var(--amber); background: rgba(240,165,0,0.08); color: var(--amber); }
#vviq-options button .opt-score { color: var(--amber-dim); margin-right: 10px; }
#vviq-nav { display: flex; gap: 12px; }
#vviq-nav button { background: none; border: 1px solid var(--border); color: var(--text-dim); font-family: 'IBM Plex Mono', monospace; font-size: 12px; padding: 8px 20px; cursor: pointer; border-radius: 3px; transition: border-color 0.15s, color 0.15s; }
#vviq-nav button:hover { border-color: var(--amber-dim); color: var(--text); }
#vviq-nav button:disabled { opacity: 0.3; cursor: default; }
#vviq-nav button.primary { border-color: var(--amber-dim); color: var(--amber); }
#vviq-nav button.primary:hover { border-color: var(--amber); }
#vviq-result { text-align: center; padding: 40px 0; }
#vviq-score-title { font-size: 14px; color: var(--text-dim); letter-spacing: 0.15em; text-transform: uppercase; margin-bottom: 8px; }
#vviq-score-value { font-size: 48px; color: var(--amber); font-weight: 600; margin-bottom: 4px; }
#vviq-score-bar-wrap { max-width: 400px; margin: 16px auto; height: 6px; background: var(--border); border-radius: 3px; overflow: hidden; }
#vviq-score-bar { height: 100%; background: var(--amber); border-radius: 3px; transition: width 1s ease; }
#vviq-score-label { font-size: 18px; color: var(--text-bright); font-weight: 500; margin: 16px 0 12px; }
#vviq-score-desc { font-family: 'Spectral', serif; font-size: 16px; color: var(--text); max-width: 500px; margin: 0 auto; line-height: 1.7; }
#vviq-restart-wrap { margin-top: 32px; }
#vviq-restart { background: none; border: 1px solid var(--border); color: var(--text-dim); font-family: 'IBM Plex Mono', monospace; font-size: 12px; padding: 8px 20px; cursor: pointer; border-radius: 3px; }
#vviq-restart:hover { border-color: var(--amber-dim); color: var(--text); }
</style>

<script>
(function(){
var sections = [
  { name: "身近な人", prompt: "よく会う親戚や友人（今そばにいない人）を思い浮かべ、心の目に浮かぶ像をよく観察してください。", items: [
    "顔、頭、肩、体の正確な輪郭。",
    "頭の特徴的なポーズ、体の姿勢など。",
    "歩く時の正確な姿勢、歩幅など。",
    "よく着ている服の様々な色。"
  ]},
  { name: "日の出", prompt: "日の出を思い浮かべてください。心の目に浮かぶ像をよく観察してください。", items: [
    "太陽がもやのかかった空の地平線の上に昇っていく。",
    "空が晴れ、太陽を青さで包む。",
    "雲。嵐が起こり、稲妻が走る。",
    "虹が現れる。"
  ]},
  { name: "店の正面", prompt: "よく行く店の正面を思い浮かべてください。心の目に浮かぶ像をよく観察してください。", items: [
    "道の反対側から見た店の全体的な外観。",
    "ショーウィンドウの展示。色、形、個々の商品の詳細。",
    "入り口の近くにいる。ドアの色、形、詳細。",
    "店に入りカウンターに行く。店員が接客する。金銭のやり取り。"
  ]},
  { name: "田園風景", prompt: "木々、山、湖のある田舎の風景を思い浮かべてください。心の目に浮かぶ像をよく観察してください。", items: [
    "風景の地形。",
    "木々の色と形。",
    "湖の色と形。",
    "強い風が木々と湖に吹き、波が立つ。"
  ]},
  { name: "高速走行する車", prompt: "親戚や友人の運転する車で幹線道路を高速走行していることを思い浮かべてください。心の目に浮かぶ像をよく観察してください。", items: [
    "車の周りを最高速度で走る激しい交通を観察する。車両の全体的な外観、色、大きさ、形。",
    "車が加速し、すぐ前の車を追い越す。追い越す時、運転手や他の車の人々の切迫した表情が見える。",
    "大型トラックが真後ろでヘッドライトを点滅させている。車はすぐに寄ってトラックを通す。運転手が親しげに手を振る。",
    "路肩に故障車が見える。ライトが点滅している。運転手は心配そうな表情で携帯電話を使っている。"
  ]},
  { name: "ビーチの風景", prompt: "暖かい夏の日の海辺のビーチを思い浮かべてください。心の目に浮かぶ像をよく観察してください。", items: [
    "水、波、空の全体的な外観と色。",
    "水浴者が水の中で泳いだり水しぶきを上げている。カラフルなビーチボールで遊んでいる人もいる。",
    "外洋船が水平線を横切る。青い空に煙の軌跡を残す。",
    "美しい熱気球が4人を乗せて現れる。気球はほぼ真上を通り過ぎる。乗客が手を振って微笑む。あなたも手を振り返す。"
  ]},
  { name: "鉄道駅", prompt: "鉄道駅を思い浮かべてください。心の目に浮かぶ像をよく観察してください。", items: [
    "正面入り口の前から見た駅の全体的な外観。",
    "駅に入る。入口ホールの色、形、詳細。",
    "切符売り場に近づき、空いている窓口で切符を購入する。",
    "ホームに歩いて行き、他の乗客と線路を観察する。電車が到着する。乗り込む。"
  ]},
  { name: "庭園", prompt: "最後に、芝生、低木、花、灌木のある庭園を思い浮かべてください。心の目に浮かぶ像をよく観察してください。", items: [
    "庭園の全体的な外観とデザイン。",
    "低木と灌木の色と形。",
    "花の色と外観。",
    "鳥が芝生に降りてきて餌をついばみ始める。"
  ]}
];

var options = [
  { score: 1, label: "\u30a4\u30e1\u30fc\u30b8\u306a\u3057 \u2014 \u305d\u306e\u7269\u4f53\u306b\u3064\u3044\u3066\u300c\u77e5\u3063\u3066\u3044\u308b\u300d\u3060\u3051" },
  { score: 2, label: "\u6f20\u7136\u3068\u3057\u3066\u307c\u3093\u3084\u308a" },
  { score: 3, label: "\u307b\u3069\u307b\u3069\u660e\u78ba\u3067\u9bae\u660e" },
  { score: 4, label: "\u660e\u78ba\u3067\u304b\u306a\u308a\u9bae\u660e" },
  { score: 5, label: "\u5b8c\u5168\u306b\u660e\u78ba\u3067\u3001\u5b9f\u969b\u306b\u898b\u3066\u3044\u308b\u306e\u3068\u540c\u3058\u304f\u3089\u3044\u9bae\u660e" }
];

var answers = new Array(32).fill(0);
var current = 0;

function render() {
  var s = Math.floor(current / 4);
  var sec = sections[s];
  var localIdx = current % 4;

  document.getElementById('vviq-progress').textContent =
    '\u8cea\u554f ' + (current + 1) + ' / 32  \u00b7  \u30bb\u30af\u30b7\u30e7\u30f3 ' + (s + 1) + ' / 8';
  document.getElementById('vviq-section-name').textContent = sec.name;
  document.getElementById('vviq-section-prompt').textContent = sec.prompt;
  document.getElementById('vviq-question').textContent = (localIdx + 1) + '. ' + sec.items[localIdx];

  var optEl = document.getElementById('vviq-options');
  optEl.innerHTML = '';
  options.forEach(function(o) {
    var btn = document.createElement('button');
    btn.innerHTML = '<span class="opt-score">' + o.score + '</span>' + o.label;
    if (answers[current] === o.score) btn.className = 'selected';
    btn.addEventListener('click', function() {
      answers[current] = o.score;
      if (current < 31) { current++; render(); }
      else { render(); }
    });
    optEl.appendChild(btn);
  });

  var navEl = document.getElementById('vviq-nav');
  navEl.innerHTML = '';
  var prev = document.createElement('button');
  prev.textContent = '\u2190 \u524d\u3078';
  prev.disabled = current === 0;
  prev.addEventListener('click', function() { if (current > 0) { current--; render(); } });
  navEl.appendChild(prev);

  var allDone = answers.every(function(a) { return a > 0; });
  if (allDone) {
    var finish = document.createElement('button');
    finish.textContent = '\u7d50\u679c\u3092\u898b\u308b';
    finish.className = 'primary';
    finish.addEventListener('click', showResult);
    navEl.appendChild(finish);
  }
}

function showResult() {
  var total = answers.reduce(function(a, b) { return a + b; }, 0);
  var pct = ((total - 32) / (160 - 32)) * 100;

  document.getElementById('vviq-quiz').style.display = 'none';
  var res = document.getElementById('vviq-result');
  res.style.display = '';

  document.getElementById('vviq-score-title').textContent = 'VVIQ2\u30b9\u30b3\u30a2';
  document.getElementById('vviq-score-value').textContent = total + ' / 160';

  var bar = document.getElementById('vviq-score-bar');
  bar.style.width = '0%';
  setTimeout(function() { bar.style.width = pct + '%'; }, 50);

  var label, desc;
  if (total <= 32) {
    label = '\u30a2\u30d5\u30a1\u30f3\u30bf\u30b8\u30a2';
    desc = '\u81ea\u767a\u7684\u306a\u8996\u899a\u7684\u5fc3\u7684\u30a4\u30e1\u30fc\u30b8\u3092\u7d4c\u9a13\u3057\u307e\u305b\u3093\u3002\u30a4\u30e1\u30fc\u30b8\u3092\u6c42\u3081\u3089\u308c\u3066\u3082\u3001\u5bfe\u8c61\u304c\u4f55\u3067\u3042\u308b\u304b\u300c\u77e5\u3063\u3066\u3044\u308b\u300d\u3060\u3051\u3067\u3001\u5fc3\u306e\u76ee\u306b\u306f\u4f55\u3082\u898b\u3048\u307e\u305b\u3093\u3002\u300c\u76f2\u76ee\u306e\u5fc3\u300d\u3068\u547c\u3070\u308c\u308b\u3053\u3068\u3082\u3042\u308a\u307e\u3059\u3002';
  } else if (total <= 64) {
    label = '\u4f4e\u30a4\u30e1\u30fc\u30b8';
    desc = '\u5fc3\u7684\u30a4\u30e1\u30fc\u30b8\u306f\u6f20\u7136\u3068\u3057\u3066\u307c\u3093\u3084\u308a\u3057\u3066\u3044\u307e\u3059\u3002\u660e\u78ba\u306a\u50cf\u3067\u306f\u306a\u304f\u3001\u77ac\u9593\u7684\u306a\u5370\u8c61\u304c\u5f97\u3089\u308c\u308b\u7a0b\u5ea6\u304b\u3082\u3057\u308c\u307e\u305b\u3093\u3002\u4f4e\u30a4\u30e1\u30fc\u30b8\u306e\u4eba\u306f\u3001\u5f37\u3044\u7a7a\u9593\u7684\u30fb\u8a00\u8a9e\u7684\u30fb\u6982\u5ff5\u7684\u601d\u8003\u3067\u88dc\u3046\u3053\u3068\u304c\u591a\u3044\u3067\u3059\u3002';
  } else if (total <= 96) {
    label = '\u4e2d\u7a0b\u5ea6\u306e\u30a4\u30e1\u30fc\u30b8';
    desc = '\u8996\u899a\u30a4\u30e1\u30fc\u30b8\u306f\u307b\u3069\u307b\u3069\u660e\u78ba\u3067\u9bae\u660e\u3067\u3059\u3002\u5fc3\u7684\u50cf\u3092\u5f62\u6210\u3067\u304d\u307e\u3059\u304c\u3001\u8a73\u7d30\u3084\u5b89\u5b9a\u6027\u306b\u6b20\u3051\u308b\u304b\u3082\u3057\u308c\u307e\u305b\u3093\u3002\u4eba\u53e3\u306e\u5e73\u5747\u4ed8\u8fd1\u3067\u3059\u3002';
  } else if (total <= 128) {
    label = '\u9bae\u660e\u306a\u30a4\u30e1\u30fc\u30b8';
    desc = '\u5fc3\u7684\u30a4\u30e1\u30fc\u30b8\u306f\u660e\u78ba\u3067\u304b\u306a\u308a\u9bae\u660e\u3067\u3059\u3002\u8272\u5f69\u3001\u8a73\u7d30\u3001\u7a7a\u9593\u7684\u6b63\u78ba\u3055\u3092\u4f34\u3063\u3066\u5834\u9762\u3092\u30a4\u30e1\u30fc\u30b8\u3067\u304d\u307e\u3059\u3002\u591a\u304f\u306e\u4eba\u304c\u3053\u306e\u7bc4\u56f2\u306b\u5165\u308a\u307e\u3059\u3002';
  } else {
    label = '\u30cf\u30a4\u30d1\u30fc\u30d5\u30a1\u30f3\u30bf\u30b8\u30a2';
    desc = '\u5fc3\u7684\u30a4\u30e1\u30fc\u30b8\u306f\u975e\u5e38\u306b\u9bae\u660e\u3067\u3001\u5b9f\u969b\u306b\u898b\u3066\u3044\u308b\u306e\u3068\u307b\u307c\u540c\u3058\u304f\u3089\u3044\u660e\u78ba\u3067\u3059\u3002\u8c4a\u304b\u3067\u8a73\u7d30\u306a\u3001\u8272\u5f69\u9bae\u3084\u304b\u306a\u5fc3\u7684\u50cf\u304c\u73fe\u5b9f\u306e\u77e5\u899a\u306b\u8fd1\u3044\u3082\u306e\u3068\u3057\u3066\u4f53\u9a13\u3055\u308c\u308b\u3067\u3057\u3087\u3046\u3002';
  }

  document.getElementById('vviq-score-label').textContent = label;
  document.getElementById('vviq-score-desc').textContent = desc;

  document.getElementById('vviq-restart').addEventListener('click', function() {
    answers = new Array(32).fill(0);
    current = 0;
    document.getElementById('vviq-quiz').style.display = '';
    res.style.display = 'none';
    render();
  });
}

render();
})();
</script>
