# 一号链接倒计时页面 - 实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 构建移动端优先的单 HTML 倒计时页面，支持主题切换、自定义时长和活动列表管理。

**Architecture:** 单文件 `index.html`，内嵌 CSS（CSS 自定义属性驱动双主题）和 JS（原生 DOM，零依赖）。localStorage 持久化状态，setInterval 驱动倒计时。

**Tech Stack:** 纯 HTML/CSS/JS，无框架，无构建工具。

---

### Task 1: 创建 HTML 骨架和 CSS 主题系统

**Files:**
- Create: `index.html`

- [ ] **Step 1: 写入 HTML 骨架 + 暗夜主题 CSS 变量 + 篝火主题变量**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=no">
<title>一号链接倒计时</title>
<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --bg-start: #0a0a1a;
  --bg-end: #1a0a2e;
  --card-bg: rgba(255,255,255,0.06);
  --text: #e0d6f0;
  --text-secondary: rgba(224,214,240,0.6);
  --accent: #c084fc;
  --accent-glow: 0 0 20px rgba(192,132,252,0.5), 0 0 60px rgba(192,132,252,0.2);
  --accent-text-glow: 0 0 10px rgba(192,132,252,0.6), 0 0 30px rgba(192,132,252,0.3);
  --btn-bg: rgba(192,132,252,0.2);
  --btn-active: #c084fc;
  --divider: rgba(255,255,255,0.1);
  --toggle-bg: rgba(255,255,255,0.15);
  --toggle-dot: #c084fc;
}

body.warm {
  --bg-start: #1a0a00;
  --bg-end: #331100;
  --card-bg: rgba(255,255,255,0.06);
  --text: #f0d8b8;
  --text-secondary: rgba(240,216,184,0.6);
  --accent: #f59e0b;
  --accent-glow: 0 0 20px rgba(245,158,11,0.5), 0 0 60px rgba(245,158,11,0.2);
  --accent-text-glow: 0 0 10px rgba(245,158,11,0.6), 0 0 30px rgba(245,158,11,0.3);
  --btn-bg: rgba(245,158,11,0.2);
  --btn-active: #f59e0b;
  --divider: rgba(255,255,255,0.1);
  --toggle-bg: rgba(255,255,255,0.15);
  --toggle-dot: #f59e0b;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  background: linear-gradient(135deg, var(--bg-start), var(--bg-end));
  color: var(--text);
  min-height: 100dvh;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 24px 16px;
}

.container {
  width: 100%;
  max-width: 400px;
}

.title {
  text-align: center;
  font-size: 20px;
  font-weight: 600;
  letter-spacing: 2px;
  color: var(--accent);
  text-shadow: var(--accent-text-glow);
  margin-bottom: 20px;
}

/* countdown section */
.countdown-section {
  text-align: center;
  margin-bottom: 16px;
}

.timer {
  display: flex;
  justify-content: center;
  align-items: baseline;
  gap: 4px;
  margin-bottom: 6px;
}

.timer-digit {
  font-size: clamp(40px, 12vw, 72px);
  font-weight: 700;
  font-variant-numeric: tabular-nums;
  color: var(--accent);
  text-shadow: var(--accent-text-glow);
  min-width: 2ch;
  text-align: center;
  letter-spacing: 2px;
}

.timer-sep {
  font-size: clamp(32px, 10vw, 56px);
  color: var(--accent);
  opacity: 0.7;
}

.timer-labels {
  display: flex;
  justify-content: center;
  gap: 4px;
  font-size: 12px;
  color: var(--text-secondary);
  letter-spacing: 1px;
}

.timer-labels span {
  min-width: 2ch;
  text-align: center;
}

.settings-trigger {
  display: inline-block;
  margin-top: 12px;
  font-size: 13px;
  color: var(--accent);
  cursor: pointer;
  padding: 6px 16px;
  border-radius: 20px;
  background: var(--btn-bg);
  user-select: none;
  -webkit-tap-highlight-color: transparent;
}

/* theme toggle */
.theme-row {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
  font-size: 13px;
  color: var(--text-secondary);
}

.toggle {
  width: 48px;
  height: 26px;
  border-radius: 13px;
  background: var(--toggle-bg);
  cursor: pointer;
  position: relative;
  transition: background 0.3s;
  -webkit-tap-highlight-color: transparent;
}

.toggle::after {
  content: "";
  position: absolute;
  top: 3px;
  left: 3px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: var(--toggle-dot);
  transition: transform 0.3s;
}

body.warm .toggle::after {
  transform: translateX(22px);
}

/* activity list */
.activity-list {
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin-bottom: 12px;
}

.activity-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: var(--card-bg);
  border: 1px solid var(--divider);
  border-radius: 10px;
  padding: 12px 14px;
  font-size: 15px;
}

.activity-item .text {
  flex: 1;
  cursor: pointer;
  -webkit-tap-highlight-color: transparent;
}

.activity-item .actions {
  display: flex;
  gap: 8px;
  flex-shrink: 0;
  margin-left: 8px;
}

.activity-item .actions button {
  width: 32px;
  height: 32px;
  border: none;
  border-radius: 50%;
  background: rgba(255,255,255,0.1);
  color: var(--text-secondary);
  font-size: 14px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  -webkit-tap-highlight-color: transparent;
}

.activity-item .actions button:active {
  background: var(--btn-bg);
  color: var(--accent);
}

/* activity edit input */
.activity-item input {
  flex: 1;
  background: rgba(255,255,255,0.1);
  border: 1px solid var(--accent);
  border-radius: 6px;
  color: var(--text);
  font-size: 15px;
  padding: 6px 10px;
  outline: none;
  font-family: inherit;
}

.add-btn {
  width: 100%;
  padding: 12px;
  border: 1px dashed var(--divider);
  border-radius: 10px;
  background: transparent;
  color: var(--text-secondary);
  font-size: 15px;
  cursor: pointer;
  -webkit-tap-highlight-color: transparent;
}

.add-btn:active {
  background: var(--card-bg);
  color: var(--accent);
}

/* modal overlay */
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.6);
  display: flex;
  align-items: flex-end;
  justify-content: center;
  z-index: 100;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.25s;
}

.modal-overlay.active {
  opacity: 1;
  pointer-events: auto;
}

.modal {
  width: 100%;
  max-width: 400px;
  background: linear-gradient(135deg, #12122a, #1a1030);
  border-radius: 16px 16px 0 0;
  padding: 24px 20px 32px;
  transform: translateY(100%);
  transition: transform 0.3s;
}

body.warm .modal {
  background: linear-gradient(135deg, #1a1005, #2a1508);
}

.modal-overlay.active .modal {
  transform: translateY(0);
}

.modal h3 {
  font-size: 18px;
  color: var(--accent);
  margin-bottom: 20px;
  text-align: center;
}

.modal .field-row {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 16px;
}

.modal .field-row label {
  font-size: 14px;
  color: var(--text-secondary);
  min-width: 24px;
  text-align: center;
}

.modal .field-row input {
  flex: 1;
  background: rgba(255,255,255,0.08);
  border: 1px solid var(--divider);
  border-radius: 8px;
  color: var(--text);
  font-size: 20px;
  text-align: center;
  padding: 8px;
  outline: none;
  font-variant-numeric: tabular-nums;
  font-family: inherit;
}

.modal .field-row input:focus {
  border-color: var(--accent);
}

.modal .theme-options {
  display: flex;
  gap: 8px;
  margin-bottom: 20px;
}

.modal .theme-options button {
  flex: 1;
  padding: 10px;
  border-radius: 8px;
  border: 2px solid var(--divider);
  background: var(--card-bg);
  color: var(--text);
  font-size: 14px;
  cursor: pointer;
  transition: border-color 0.2s, background 0.2s;
  -webkit-tap-highlight-color: transparent;
}

.modal .theme-options button.active-theme {
  border-color: var(--accent);
  background: var(--btn-bg);
  color: var(--accent);
}

.modal .ctrl-btns {
  display: flex;
  gap: 8px;
}

.modal .ctrl-btns button {
  flex: 1;
  padding: 12px 8px;
  border-radius: 10px;
  border: none;
  font-size: 15px;
  font-weight: 600;
  cursor: pointer;
  color: #fff;
  -webkit-tap-highlight-color: transparent;
}

.modal .ctrl-btns .btn-start {
  background: var(--accent);
}

.modal .ctrl-btns .btn-pause {
  background: rgba(255,255,255,0.12);
  color: var(--text);
}

.modal .ctrl-btns .btn-reset {
  background: rgba(255,255,255,0.12);
  color: var(--text);
}
</style>
</head>
<body>
<div class="container">
  <div class="title">一号链接倒计时</div>

  <div class="countdown-section">
    <div class="timer">
      <span class="timer-digit" id="h">02</span>
      <span class="timer-sep">:</span>
      <span class="timer-digit" id="m">30</span>
      <span class="timer-sep">:</span>
      <span class="timer-digit" id="s">00</span>
    </div>
    <div class="timer-labels">
      <span style="min-width:2ch;">时</span>
      <span style="width:12px;"></span>
      <span style="min-width:2ch;">分</span>
      <span style="width:12px;"></span>
      <span style="min-width:2ch;">秒</span>
    </div>
    <div class="settings-trigger" id="settingsBtn">⚙ 设置</div>
  </div>

  <div class="theme-row">
    <span>🌙 暗夜舞台</span>
    <div class="toggle" id="themeToggle"></div>
    <span>🔥 篝火暖色</span>
  </div>

  <div class="activity-list" id="activityList"></div>
  <button class="add-btn" id="addBtn">+ 添加活动</button>
</div>

<div class="modal-overlay" id="modalOverlay">
  <div class="modal">
    <h3>⚙ 倒计时设置</h3>
    <div class="field-row">
      <label>时</label>
      <input type="number" id="inputH" min="0" max="99" value="2" inputmode="numeric">
      <label>分</label>
      <input type="number" id="inputM" min="0" max="59" value="30" inputmode="numeric">
      <label>秒</label>
      <input type="number" id="inputS" min="0" max="59" value="0" inputmode="numeric">
    </div>
    <div class="theme-options">
      <button id="themeDark" class="active-theme">🌙 暗夜</button>
      <button id="themeWarm">🔥 篝火</button>
    </div>
    <div class="ctrl-btns">
      <button class="btn-start" id="btnStart">▶ 开始</button>
      <button class="btn-pause" id="btnPause">⏸ 暂停</button>
      <button class="btn-reset" id="btnReset">↺ 重置</button>
    </div>
  </div>
</div>
```

- [ ] **Step 2: 验证页面在浏览器中打开，布局和样式正常**

在浏览器打开 `index.html`，检查标题、计时器、活动列表区域、设置按钮、主题切换等元素是否正确渲染。

- [ ] **Step 3: 提交**

```bash
git add index.html
git commit -m "feat: HTML骨架和CSS主题系统"
```

---

### Task 2: 倒计时核心逻辑

**Files:**
- Modify: `index.html`（在 `</body>` 前插入 JS）

- [ ] **Step 1: 在 `</body>` 前添加倒计时逻辑**

```html
<script>
// --- state ---
let totalSeconds = 9000; // default 2h30m
let remainingSeconds = totalSeconds;
let timerId = null;

const $h = document.getElementById('h');
const $m = document.getElementById('m');
const $s = document.getElementById('s');
const $btnStart = document.getElementById('btnStart');
const $btnPause = document.getElementById('btnPause');
const $btnReset = document.getElementById('btnReset');
const $inputH = document.getElementById('inputH');
const $inputM = document.getElementById('inputM');
const $inputS = document.getElementById('inputS');

function pad(n) { return String(n).padStart(2, '0'); }

function renderTimer() {
  const hh = Math.floor(remainingSeconds / 3600);
  const mm = Math.floor((remainingSeconds % 3600) / 60);
  const ss = remainingSeconds % 60;
  const totalH = Math.floor(totalSeconds / 3600);
  const totalM = Math.floor((totalSeconds % 3600) / 60);
  const totalS = totalSeconds % 60;
  $h.textContent = pad(totalH);
  $m.textContent = pad(totalM);
  $s.textContent = pad(totalS);
  $inputH.value = totalH;
  $inputM.value = totalM;
  $inputS.value = totalS;
}

function applySettings() {
  const h = parseInt($inputH.value) || 0;
  const mn = parseInt($inputM.value) || 0;
  const sc = parseInt($inputS.value) || 0;
  totalSeconds = h * 3600 + mn * 60 + sc;
  remainingSeconds = totalSeconds;
  renderTimer();
}

function tick() {
  if (remainingSeconds <= 0) {
    pauseTimer();
    return;
  }
  remainingSeconds--;
  renderTimer();
}

function startTimer() {
  if (remainingSeconds <= 0) return;
  if (timerId) return;
  applySettings();
  timerId = setInterval(tick, 1000);
}

function pauseTimer() {
  if (timerId) { clearInterval(timerId); timerId = null; }
}

function resetTimer() {
  pauseTimer();
  remainingSeconds = totalSeconds;
  renderTimer();
}

$btnStart.addEventListener('click', startTimer);
$btnPause.addEventListener('click', pauseTimer);
$btnReset.addEventListener('click', resetTimer);

// update input -> auto apply
[$inputH, $inputM, $inputS].forEach(el => {
  el.addEventListener('change', applySettings);
});

renderTimer();
```

- [ ] **Step 2: 浏览器验证** — 刷新页面，确认计时器显示 02:30:00，点击开始倒计时运行，暂停/重置正常。

- [ ] **Step 3: 提交**

```bash
git add index.html
git commit -m "feat: 倒计时核心逻辑"
```

---

### Task 3: 设置面板弹出/收起

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 在 `<script>` 中添加设置面板逻辑**

在现有 JS 代码后面追加：

```js
// --- modal ---
const $overlay = document.getElementById('modalOverlay');
const $settingsBtn = document.getElementById('settingsBtn');

function openModal() {
  $overlay.classList.add('active');
}
function closeModal() {
  $overlay.classList.remove('active');
}

$settingsBtn.addEventListener('click', openModal);
$overlay.addEventListener('click', function(e) {
  if (e.target === $overlay) closeModal();
});
```

- [ ] **Step 2: 浏览器验证** — 点击"⚙ 设置"弹出面板，点击外部遮罩关闭，面板内按钮正常工作。

- [ ] **Step 3: 提交**

```bash
git add index.html
git commit -m "feat: 设置面板弹出/收起交互"
```

---

### Task 4: 活动列表 CRUD

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 在 JS 中添加活动列表渲染和编辑逻辑**

在现有 JS 代码后面追加：

```js
// --- activities ---
const defaultActivities = ['烟花秀', '电音节', '星空大合唱', '灯光花海', '山体激光秀'];
let activities = [];

const $list = document.getElementById('activityList');
const $addBtn = document.getElementById('addBtn');

function saveActivities() { /* placeholder - Task 6 fills */ }
```

- [ ] **Step 2: 写 renderActivities 函数**

```js
function renderActivities() {
  $list.innerHTML = '';
  activities.forEach(function(item, i) {
    const div = document.createElement('div');
    div.className = 'activity-item';

    const text = document.createElement('span');
    text.className = 'text';
    text.textContent = item;
    text.addEventListener('click', function() { editActivity(i); });

    const actions = document.createElement('div');
    actions.className = 'actions';

    const editBtn = document.createElement('button');
    editBtn.textContent = '✏';
    editBtn.addEventListener('click', function() { editActivity(i); });

    const delBtn = document.createElement('button');
    delBtn.textContent = '✕';
    delBtn.addEventListener('click', function() { deleteActivity(i); });

    actions.appendChild(editBtn);
    actions.appendChild(delBtn);
    div.appendChild(text);
    div.appendChild(actions);
    $list.appendChild(div);
  });
}
```

- [ ] **Step 3: 写增删改逻辑**

```js
function editActivity(index) {
  const newText = prompt('编辑活动名称', activities[index]);
  if (newText !== null && newText.trim() !== '') {
    activities[index] = newText.trim();
    renderActivities();
    saveActivities();
  }
}

function deleteActivity(index) {
  activities.splice(index, 1);
  renderActivities();
  saveActivities();
}

$addBtn.addEventListener('click', function() {
  const newText = prompt('输入活动名称');
  if (newText !== null && newText.trim() !== '') {
    activities.push(newText.trim());
    renderActivities();
    saveActivities();
  }
});
```

- [ ] **Step 4: 初始化 activities 数组** — 在 JS 最前面（renderTimer 之前）加入：

```js
activities = [...defaultActivities];
renderActivities();
```

- [ ] **Step 5: 浏览器验证** — 刷新页面，活动列表显示默认 5 项，点击 ✏ 编辑、✕ 删除、底部新增均正常。

- [ ] **Step 6: 提交**

```bash
git add index.html
git commit -m "feat: 活动列表增删改"
```

---

### Task 5: 主题切换

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 添加主题切换逻辑**

在 JS 代码末尾追加：

```js
// --- theme ---
const $themeToggle = document.getElementById('themeToggle');
const $themeDark = document.getElementById('themeDark');
const $themeWarm = document.getElementById('themeWarm');

function setTheme(theme) {
  if (theme === 'warm') {
    document.body.classList.add('warm');
    $themeDark.classList.remove('active-theme');
    $themeWarm.classList.add('active-theme');
  } else {
    document.body.classList.remove('warm');
    $themeDark.classList.add('active-theme');
    $themeWarm.classList.remove('active-theme');
  }
}

$themeToggle.addEventListener('click', function() {
  const next = document.body.classList.contains('warm') ? 'dark' : 'warm';
  setTheme(next);
  saveTheme();
});

$themeDark.addEventListener('click', function() { setTheme('dark'); saveTheme(); });
$themeWarm.addEventListener('click', function() { setTheme('warm'); saveTheme(); });

function saveTheme() { /* placeholder - Task 6 fills */ }
```

- [ ] **Step 2: 浏览器验证** — 点击主题切换按钮和面板内主题选择，暗夜↔篝火切换正常，颜色即时变化。

- [ ] **Step 3: 提交**

```bash
git add index.html
git commit -m "feat: 暗夜/篝火主题切换"
```

---

### Task 6: localStorage 持久化

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 实现存储和恢复逻辑**

替换原有的 `saveActivities` 和 `saveTheme` 占位函数，并在 JS 初始化阶段添加恢复逻辑：

将这段代码放在所有变量声明之后、`renderActivities()` 等初始化调用之前：

```js
// --- persistence ---
const STORAGE_KEY = 'countdown_state';

function saveState() {
  const state = {
    totalSeconds: totalSeconds,
    remainingSeconds: remainingSeconds,
    activities: activities,
    theme: document.body.classList.contains('warm') ? 'warm' : 'dark'
  };
  localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
}

function loadState() {
  const raw = localStorage.getItem(STORAGE_KEY);
  if (!raw) return false;
  try {
    const state = JSON.parse(raw);
    totalSeconds = state.totalSeconds || 9000;
    remainingSeconds = state.remainingSeconds != null ? state.remainingSeconds : totalSeconds;
    activities = state.activities || [...defaultActivities];
    setTheme(state.theme || 'dark');
    return true;
  } catch(e) {
    return false;
  }
}

// replace saveActivities / saveTheme with saveState
function saveActivities() {} // stub replaced
function saveTheme() {} // stub replaced
```

然后修改所有导致状态变更的函数，末尾调用 `saveState()`：
- `tick()` 中 `renderTimer()` 后加 `saveState();`
- `applySettings()` 中 `renderTimer()` 后加 `saveState();`
- `startTimer()` 中已经有 `applySettings()` 调用，会间接触发
- `resetTimer()` 中 `renderTimer()` 后加 `saveState();`
- `renderActivities()` 调用后不需要单独加（增删改处已调用 `saveActivities()`，但我们已经把 `saveActivities` 替换为空函数）

需要重写增删改部分，将 `saveActivities()` 替换为 `saveState()`：

在 `editActivity` 中：`renderActivities(); saveState();`
在 `deleteActivity` 中：`renderActivities(); saveState();`
在 `$addBtn` 回调中：`renderActivities(); saveState();`
在 `setTheme` 中：末尾加 `saveState();`

修改初始化流程，用 `loadState()` 恢复，然后渲染：

```js
// 保持原有默认值，尝试加载持久化状态
if (!loadState()) {
  activities = [...defaultActivities];
}
renderTimer();
renderActivities();
```

- [ ] **Step 2: 浏览器验证** — 修改时长、添加活动、切换主题后刷新页面，状态正确恢复。

- [ ] **Step 3: 提交**

```bash
git add index.html
git commit -m "feat: localStorage持久化"
```

---

### Task 7: 最终验证与收尾

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 移动端体验验证** — 用手机浏览器打开 `index.html`，检查：
  - 数字键盘弹出（输入框使用 `inputmode="numeric"`）
  - 按钮尺寸适合手指点击（已确保 ≥44px 触控区域）
  - 页面无横向溢出
  - 倒计时运行流畅

- [ ] **Step 2: 边界情况检查** — 验证：
  - 倒计时归零后自动暂停
  - 活动列表为空时显示正常（无活动项 + 添加按钮可用）
  - 输入设为 0h 0m 0s 时开始按钮不启动计时

- [ ] **Step 3: 提交最终版本**

```bash
git add index.html
git commit -m "feat: 一号链接倒计时页面完成"
```
