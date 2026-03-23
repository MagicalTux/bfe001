---
title: "VVIQ2 — Vividness of Visual Imagery Questionnaire"
description: "Take the VVIQ2 test — a 32-item questionnaire measuring the vividness of your visual mental imagery, from aphantasia to hyperphantasia."
---

<!-- section: $VIQ002 "VVIQ2 Test" -->

The **Vividness of Visual Imagery Questionnaire** (VVIQ2) is a 32-item self-report measure of the clarity and vividness of your mental imagery, developed by David Marks. It is the standard instrument used to assess the spectrum from **aphantasia** (no mental imagery) to **hyperphantasia** (extremely vivid imagery).

For each item, close your eyes and try to form a mental image of the described scene, then rate the vividness of what you see in your mind's eye.

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
<div id="vviq-restart-wrap"><button id="vviq-restart">Take again</button></div>
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
  { name: "A Familiar Person", prompt: "Think of some relative or friend whom you frequently see (but who is not with you at present) and consider carefully the picture that comes before your mind\u2019s eye.", items: [
    "The exact contour of face, head, shoulders and body.",
    "Characteristic poses of head, attitudes of body etc.",
    "The precise carriage, length of step, etc. in walking.",
    "The different colours worn in some familiar clothes."
  ]},
  { name: "The Rising Sun", prompt: "Think of the rising sun. Consider carefully the picture that comes before your mind\u2019s eye.", items: [
    "The sun is rising above the horizon into a hazy sky.",
    "The sky clears and surrounds the sun with blueness.",
    "Clouds. A storm blows up, with flashes of lightning.",
    "A rainbow appears."
  ]},
  { name: "A Shop Front", prompt: "Think of the front of a shop which you often go to. Consider the picture that comes before your mind\u2019s eye.", items: [
    "The overall appearance of the shop from the opposite side of the road.",
    "A window display including colours, shape and details of individual items for sale.",
    "You are near the entrance. The colour, shape and details of the door.",
    "You enter the shop and go to the counter. The counter assistant serves you. Money changes hands."
  ]},
  { name: "A Country Landscape", prompt: "Think of a country scene which involves trees, mountains and a lake. Consider the picture that comes before your mind\u2019s eye.", items: [
    "The contours of the landscape.",
    "The colour and shape of the trees.",
    "The colour and shape of the lake.",
    "A strong wind blows on the trees and on the lake causing waves."
  ]},
  { name: "A Fast-Moving Car", prompt: "Think of being driven in a fast-moving car by a relative or friend along a major highway. Consider the picture that comes into your mind\u2019s eye.", items: [
    "You observe the heavy traffic travelling at maximum speed around your car. The overall appearance of vehicles, their colours, sizes and shapes.",
    "Your car accelerates to overtake the traffic directly in front of you. You see an urgent expression on the face of the driver and the people in the other vehicles as you pass.",
    "A large truck is flashing its headlights directly behind. Your car quickly moves over to let the truck pass. The driver signals with a friendly wave.",
    "You see a broken-down vehicle beside the road. Its lights are flashing. The driver is looking concerned and she is using a mobile phone."
  ]},
  { name: "A Beach Scene", prompt: "Think of the beach by the ocean on a warm summer\u2019s day. Consider the picture that comes before your mind\u2019s eye.", items: [
    "The overall appearance and colour of the water, surf, and sky.",
    "Bathers are swimming and splashing about in the water. Some are playing with a brightly coloured beach ball.",
    "An ocean liner crosses the horizon. It leaves a trail of smoke in the blue sky.",
    "A beautiful hot air balloon appears with four people aboard. The balloon drifts past you, almost directly overhead. The passengers wave and smile. You wave and smile back at them."
  ]},
  { name: "A Railway Station", prompt: "Think of a railway station. Consider the picture that comes before your mind\u2019s eye.", items: [
    "The overall appearance of the station viewed from in front of the main entrance.",
    "You walk into the station. The colour, shape and details of the entrance hall.",
    "You approach the ticket office, go to a vacant counter and purchase your ticket.",
    "You walk to the platform and observe other passengers and the railway lines. A train arrives. You climb aboard."
  ]},
  { name: "A Garden", prompt: "Finally, think of a garden with lawns, bushes, flowers and shrubs. Consider the picture that comes before your mind\u2019s eye.", items: [
    "The overall appearance and design of the garden.",
    "The colour and shape of the bushes and shrubs.",
    "The colour and appearance of the flowers.",
    "Some birds fly down onto the lawn and start pecking for food."
  ]}
];

var options = [
  { score: 1, label: "No image at all \u2014 you only \u201cknow\u201d that you are thinking of the object" },
  { score: 2, label: "Vague and dim" },
  { score: 3, label: "Moderately clear and vivid" },
  { score: 4, label: "Clear and reasonably vivid" },
  { score: 5, label: "Perfectly clear and as vivid as normal vision" }
];

var answers = new Array(32).fill(0);
var current = 0;

function secOf(q) { return Math.floor(q / 4); }

function render() {
  var s = secOf(current);
  var sec = sections[s];
  var localIdx = current % 4;

  document.getElementById('vviq-progress').textContent =
    'Question ' + (current + 1) + ' of 32  \u00b7  Section ' + (s + 1) + ' of 8';
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
  prev.textContent = '\u2190 Previous';
  prev.disabled = current === 0;
  prev.addEventListener('click', function() { if (current > 0) { current--; render(); } });
  navEl.appendChild(prev);

  var allDone = answers.every(function(a) { return a > 0; });
  if (allDone) {
    var finish = document.createElement('button');
    finish.textContent = 'See results';
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

  document.getElementById('vviq-score-title').textContent = 'Your VVIQ2 Score';
  document.getElementById('vviq-score-value').textContent = total + ' / 160';

  var bar = document.getElementById('vviq-score-bar');
  bar.style.width = '0%';
  setTimeout(function() { bar.style.width = pct + '%'; }, 50);

  var label, desc;
  if (total <= 32) {
    label = 'Aphantasia';
    desc = 'You experience no voluntary visual mental imagery. When asked to visualize, you "know" what the object is but see nothing in your mind\'s eye. This is sometimes called a "blind mind."';
  } else if (total <= 64) {
    label = 'Low Imagery';
    desc = 'Your mental images are vague and dim. You may get fleeting impressions rather than clear pictures. Many people with low imagery compensate with strong spatial, verbal, or conceptual thinking.';
  } else if (total <= 96) {
    label = 'Moderate Imagery';
    desc = 'Your visual imagery is moderately clear and vivid. You can form mental pictures but they may lack detail or stability. This is around the population average.';
  } else if (total <= 128) {
    label = 'Vivid Imagery';
    desc = 'Your mental imagery is clear and reasonably vivid. You can visualize scenes with good detail, colour, and spatial accuracy. Most people fall in this range.';
  } else {
    label = 'Hyperphantasia';
    desc = 'Your mental imagery is exceptionally vivid \u2014 almost as clear as actually seeing. You likely experience rich, detailed, colourful mental pictures that feel close to real perception.';
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
