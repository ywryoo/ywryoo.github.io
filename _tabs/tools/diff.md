---
title: 문서 비교
layout: page
permalink: /tools/diff/
---

두 텍스트의 차이를 행, 단어, 글자 단위로 확인합니다.

<div class="diff-tool">
  <div class="diff-columns">
    <section class="diff-panel">
      <div class="diff-panel-heading">
        <h2>원본 문서</h2>
        <span id="sourceStats" class="diff-stats">0자 / 0행</span>
      </div>
      <textarea id="sourceText" aria-label="원본 문서" spellcheck="false" placeholder="비교할 첫 번째 텍스트"></textarea>
    </section>
    <section class="diff-panel">
      <div class="diff-panel-heading">
        <h2>비교 문서</h2>
        <span id="targetStats" class="diff-stats">0자 / 0행</span>
      </div>
      <textarea id="targetText" aria-label="비교 문서" spellcheck="false" placeholder="비교할 두 번째 텍스트"></textarea>
    </section>
  </div>

  <div class="diff-toolbar" aria-label="비교 설정">
    <label class="diff-field">
      <span>비교 단위</span>
      <select id="compareMode">
        <option value="line">행 단위</option>
        <option value="word">단어 단위</option>
        <option value="char">글자 단위</option>
      </select>
    </label>

    <label class="diff-check">
      <input id="ignoreWhitespace" type="checkbox">
      <span>공백 무시</span>
    </label>

    <label class="diff-check">
      <input id="ignoreCase" type="checkbox">
      <span>대소문자 무시</span>
    </label>

    <label class="diff-check">
      <input id="showOnlyChanges" type="checkbox">
      <span>변경만 보기</span>
    </label>

    <div class="diff-actions">
      <button id="swapBtn" type="button" class="btn btn-outline-secondary" title="원본과 비교 문서 바꾸기">
        <i class="fas fa-right-left" aria-hidden="true"></i>
        <span>바꾸기</span>
      </button>
      <button id="compareBtn" type="button" class="btn btn-primary">
        <i class="fas fa-code-compare" aria-hidden="true"></i>
        <span>비교하기</span>
      </button>
      <button id="clearBtn" type="button" class="btn btn-outline-secondary">
        <i class="fas fa-rotate-left" aria-hidden="true"></i>
        <span>초기화</span>
      </button>
      <button id="copyBtn" type="button" class="btn btn-outline-secondary" disabled>
        <i class="fas fa-copy" aria-hidden="true"></i>
        <span>결과 복사</span>
      </button>
    </div>
  </div>

  <section class="result">
    <div class="result-heading">
      <h2>비교 결과</h2>
      <span id="copyStatus" class="copy-status" aria-live="polite"></span>
    </div>
    <div id="resultSummary" class="result-summary" aria-live="polite">텍스트를 입력하면 차이가 표시됩니다.</div>
    <div id="resultArea" class="result-area"></div>
  </section>
</div>

<style>
.diff-tool {
  display: grid;
  gap: 1.5rem;
}

.diff-columns {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 1rem;
}

.diff-panel {
  border: 1px solid var(--main-border-color);
  border-radius: 8px;
  padding: 1rem;
  background: var(--card-bg);
}

.diff-panel-heading,
.result-heading {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
  align-items: center;
  margin-bottom: 0.75rem;
}

.diff-panel h2,
.result h2 {
  margin: 0;
  border-left: 0;
  padding-left: 0;
  font-size: 1.1rem;
}

.diff-stats,
.copy-status {
  color: var(--text-muted-color);
  font-size: 0.85rem;
  white-space: nowrap;
}

.diff-panel textarea {
  width: 100%;
  min-height: 280px;
  border: 1px solid var(--main-border-color);
  border-radius: 8px;
  background: var(--main-bg);
  color: var(--text-color);
  padding: 0.85rem;
  resize: vertical;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  line-height: 1.6;
}

.diff-panel textarea:focus {
  border-color: var(--brand-accent);
  box-shadow: 0 0 0 0.2rem var(--brand-accent-soft);
  outline: 0;
}

.diff-toolbar {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
  align-items: center;
  border: 1px solid var(--main-border-color);
  border-radius: 8px;
  background: var(--card-bg);
  padding: 0.85rem;
}

.diff-field,
.diff-check {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}

.diff-field span,
.diff-check span {
  color: var(--text-muted-color);
  font-size: 0.9rem;
}

.diff-field select {
  min-height: 2.25rem;
  border: 1px solid var(--main-border-color);
  border-radius: 8px;
  background: var(--main-bg);
  color: var(--text-color);
  padding: 0.35rem 0.65rem;
}

.diff-check input {
  width: 1rem;
  height: 1rem;
  accent-color: var(--brand-accent);
}

.diff-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  align-items: center;
  margin-left: auto;
}

.diff-actions .btn {
  display: inline-flex;
  gap: 0.4rem;
  align-items: center;
  border-radius: 8px;
  white-space: nowrap;
}

.result {
  border: 1px solid var(--main-border-color);
  border-radius: 8px;
  padding: 1rem;
  background: var(--card-bg);
}

.result-summary {
  margin-bottom: 1rem;
  color: var(--text-muted-color);
}

.result-area {
  display: grid;
  gap: 0.5rem;
}

.result-empty {
  border: 1px dashed var(--main-border-color);
  border-radius: 8px;
  color: var(--text-muted-color);
  padding: 1rem;
  text-align: center;
}

.diff-grid {
  display: grid;
  overflow: auto;
  border: 1px solid var(--main-border-color);
  border-radius: 8px;
}

.diff-grid-header,
.diff-line-row {
  display: grid;
  grid-template-columns: 4rem minmax(14rem, 1fr) 4rem minmax(14rem, 1fr);
  min-width: 48rem;
}

.diff-grid-header {
  background: var(--main-bg);
  color: var(--text-muted-color);
  font-size: 0.85rem;
  font-weight: 700;
}

.diff-grid-header span,
.diff-line-no,
.diff-line-text {
  border-bottom: 1px solid var(--main-border-color);
  padding: 0.55rem 0.7rem;
}

.diff-grid-header span:nth-child(2),
.diff-line-left {
  border-right: 1px solid var(--main-border-color);
}

.diff-line-no {
  color: var(--text-muted-color);
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  text-align: right;
  user-select: none;
}

.diff-line-text {
  min-height: 2.4rem;
  white-space: pre-wrap;
  word-break: break-word;
  line-height: 1.5;
}

.diff-row {
  display: block;
  width: 100%;
  padding: 0.65rem 0.85rem;
  border-radius: 8px;
  white-space: pre-wrap;
  word-break: break-word;
  line-height: 1.5;
}

.diff-equal {
  background: transparent;
}

.diff-insert {
  background: rgba(16, 185, 129, 0.12);
  color: #047857;
}

.diff-delete {
  background: rgba(239, 68, 68, 0.12);
  color: #991b1b;
}

.diff-replace {
  background: rgba(245, 158, 11, 0.12);
  color: #92400e;
}

.diff-inline-columns {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 1rem;
}

.diff-inline-panel {
  min-height: 8rem;
  border: 1px solid var(--main-border-color);
  border-radius: 8px;
  background: var(--main-bg);
  padding: 0.85rem;
}

.diff-inline-panel h3 {
  margin: 0 0 0.75rem;
  color: var(--text-muted-color);
  font-size: 0.9rem;
  font-weight: 700;
}

.diff-inline-body {
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  line-height: 1.8;
  white-space: pre-wrap;
  word-break: break-word;
}

.diff-token {
  border-radius: 6px;
  padding: 0.08rem 0.2rem;
}

.diff-token.inserted {
  background: rgba(16, 185, 129, 0.18);
  color: #065f46;
}

.diff-token.deleted {
  background: rgba(239, 68, 68, 0.18);
  color: #831010;
}

html[data-mode='dark'] .diff-insert {
  color: #6ee7b7;
}

html[data-mode='dark'] .diff-delete {
  color: #fca5a5;
}

html[data-mode='dark'] .diff-replace {
  color: #fcd34d;
}

html[data-mode='dark'] .diff-token.inserted {
  color: #a7f3d0;
}

html[data-mode='dark'] .diff-token.deleted {
  color: #fecaca;
}

@media (prefers-color-scheme: dark) {
  html:not([data-mode]) .diff-insert {
    color: #6ee7b7;
  }

  html:not([data-mode]) .diff-delete {
    color: #fca5a5;
  }

  html:not([data-mode]) .diff-replace {
    color: #fcd34d;
  }

  html:not([data-mode]) .diff-token.inserted {
    color: #a7f3d0;
  }

  html:not([data-mode]) .diff-token.deleted {
    color: #fecaca;
  }
}

@media (max-width: 768px) {
  .diff-columns,
  .diff-inline-columns {
    grid-template-columns: 1fr;
  }

  .diff-toolbar {
    align-items: stretch;
  }

  .diff-actions {
    width: 100%;
    margin-left: 0;
  }

  .diff-actions .btn {
    flex: 1 1 9rem;
    justify-content: center;
  }
}
</style>

<script>
const MAX_DIFF_CELLS = 2000000;
const EMPTY_MESSAGE = '텍스트를 입력하면 차이가 표시됩니다.';
const modeLabels = {
  line: '행',
  word: '단어',
  char: '글자'
};

let lastDiff = null;

const elements = {
  sourceText: document.getElementById('sourceText'),
  targetText: document.getElementById('targetText'),
  sourceStats: document.getElementById('sourceStats'),
  targetStats: document.getElementById('targetStats'),
  compareMode: document.getElementById('compareMode'),
  ignoreWhitespace: document.getElementById('ignoreWhitespace'),
  ignoreCase: document.getElementById('ignoreCase'),
  showOnlyChanges: document.getElementById('showOnlyChanges'),
  compareBtn: document.getElementById('compareBtn'),
  clearBtn: document.getElementById('clearBtn'),
  copyBtn: document.getElementById('copyBtn'),
  swapBtn: document.getElementById('swapBtn'),
  copyStatus: document.getElementById('copyStatus'),
  resultSummary: document.getElementById('resultSummary'),
  resultArea: document.getElementById('resultArea')
};

function getOptions() {
  return {
    mode: elements.compareMode.value,
    ignoreWhitespace: elements.ignoreWhitespace.checked,
    ignoreCase: elements.ignoreCase.checked,
    showOnlyChanges: elements.showOnlyChanges.checked
  };
}

function countLines(text) {
  return text.length ? text.split(/\r?\n/).length : 0;
}

function updateStats() {
  elements.sourceStats.textContent = `${elements.sourceText.value.length}자 / ${countLines(elements.sourceText.value)}행`;
  elements.targetStats.textContent = `${elements.targetText.value.length}자 / ${countLines(elements.targetText.value)}행`;
}

function normalizeToken(text, options) {
  let value = text;

  if (options.ignoreWhitespace) {
    value = value.replace(/\s+/g, '');
  }

  if (options.ignoreCase) {
    value = value.toLocaleLowerCase('ko-KR');
  }

  return value;
}

function makeToken(text, options) {
  return {
    text,
    key: normalizeToken(text, options)
  };
}

function splitLineTokens(text, options) {
  if (!text) {
    return [];
  }

  return text.split(/\r?\n/).map(line => makeToken(line, options));
}

function splitWordTokens(text, options) {
  if (!text) {
    return [];
  }

  const parts = options.ignoreWhitespace
    ? text.match(/\S+/g) || []
    : text.split(/(\s+)/).filter(Boolean);

  return parts.map(part => makeToken(part, options));
}

function splitCharTokens(text, options) {
  if (!text) {
    return [];
  }

  let parts;

  if ('Segmenter' in Intl) {
    const segmenter = new Intl.Segmenter('ko', { granularity: 'grapheme' });
    parts = Array.from(segmenter.segment(text), item => item.segment);
  } else {
    parts = Array.from(text);
  }

  if (options.ignoreWhitespace) {
    parts = parts.filter(part => !/\s/.test(part));
  }

  return parts.map(part => makeToken(part, options));
}

function splitTokens(text, options) {
  if (options.mode === 'line') {
    return splitLineTokens(text, options);
  }

  if (options.mode === 'word') {
    return splitWordTokens(text, options);
  }

  return splitCharTokens(text, options);
}

function buildDiff(a, b) {
  const m = a.length;
  const n = b.length;
  const dp = Array.from({ length: m + 1 }, () => new Uint32Array(n + 1));

  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (a[i - 1].key === b[j - 1].key) {
        dp[i][j] = dp[i - 1][j - 1] + 1;
      } else {
        dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
      }
    }
  }

  const parts = [];
  let i = m;
  let j = n;

  while (i > 0 || j > 0) {
    if (i > 0 && j > 0 && a[i - 1].key === b[j - 1].key) {
      parts.unshift({ type: 'equal', text: a[i - 1].text });
      i -= 1;
      j -= 1;
    } else if (j > 0 && (i === 0 || dp[i][j - 1] >= dp[i - 1][j])) {
      parts.unshift({ type: 'insert', text: b[j - 1].text });
      j -= 1;
    } else {
      parts.unshift({ type: 'delete', text: a[i - 1].text });
      i -= 1;
    }
  }

  return parts;
}

function summarizeParts(parts) {
  return parts.reduce((summary, part) => {
    summary[part.type] += 1;
    return summary;
  }, { equal: 0, insert: 0, delete: 0 });
}

function mergeParts(parts) {
  return parts.reduce((merged, part) => {
    const previous = merged[merged.length - 1];

    if (previous && previous.type === part.type) {
      previous.text += part.text;
    } else {
      merged.push({ ...part });
    }

    return merged;
  }, []);
}

function buildAlignedRows(parts) {
  const rows = [];
  let sourceLine = 1;
  let targetLine = 1;
  let index = 0;

  while (index < parts.length) {
    const part = parts[index];

    if (part.type === 'equal') {
      rows.push({
        type: 'equal',
        sourceLine: sourceLine,
        targetLine: targetLine,
        sourceText: part.text,
        targetText: part.text
      });
      sourceLine += 1;
      targetLine += 1;
      index += 1;
      continue;
    }

    const deletes = [];
    const inserts = [];

    while (index < parts.length && parts[index].type !== 'equal') {
      if (parts[index].type === 'delete') {
        deletes.push(parts[index]);
      } else {
        inserts.push(parts[index]);
      }

      index += 1;
    }

    const length = Math.max(deletes.length, inserts.length);

    for (let i = 0; i < length; i++) {
      const deleted = deletes[i];
      const inserted = inserts[i];

      rows.push({
        type: deleted && inserted ? 'replace' : deleted ? 'delete' : 'insert',
        sourceLine: deleted ? sourceLine++ : '',
        targetLine: inserted ? targetLine++ : '',
        sourceText: deleted ? deleted.text : '',
        targetText: inserted ? inserted.text : ''
      });
    }
  }

  return rows;
}

function setEmptyResult(message) {
  elements.resultArea.innerHTML = '';
  const empty = document.createElement('div');
  empty.className = 'result-empty';
  empty.textContent = message;
  elements.resultArea.appendChild(empty);
}

function appendText(parent, text) {
  parent.appendChild(document.createTextNode(text));
}

function createLineCell(className, text) {
  const cell = document.createElement('div');
  cell.className = className;
  cell.textContent = text;
  return cell;
}

function renderLineDiff(parts, options) {
  const rows = buildAlignedRows(parts).filter(row => !options.showOnlyChanges || row.type !== 'equal');

  elements.resultArea.innerHTML = '';

  if (!rows.length) {
    setEmptyResult('변경된 내용이 없습니다.');
    return;
  }

  const grid = document.createElement('div');
  grid.className = 'diff-grid';

  const header = document.createElement('div');
  header.className = 'diff-grid-header';
  header.innerHTML = '<span>원본 행</span><span>원본 문서</span><span>비교 행</span><span>비교 문서</span>';
  grid.appendChild(header);

  rows.forEach(row => {
    const line = document.createElement('div');
    line.className = `diff-line-row diff-${row.type}`;
    line.appendChild(createLineCell('diff-line-no', row.sourceLine));
    line.appendChild(createLineCell('diff-line-text diff-line-left', row.sourceText));
    line.appendChild(createLineCell('diff-line-no', row.targetLine));
    line.appendChild(createLineCell('diff-line-text', row.targetText));
    grid.appendChild(line);
  });

  elements.resultArea.appendChild(grid);
}

function renderInlinePane(title, parts, keepType, tokenClass) {
  const pane = document.createElement('section');
  pane.className = 'diff-inline-panel';

  const heading = document.createElement('h3');
  heading.textContent = title;
  pane.appendChild(heading);

  const body = document.createElement('div');
  body.className = 'diff-inline-body';

  parts.forEach(part => {
    if (part.type !== 'equal' && part.type !== keepType) {
      return;
    }

    const token = document.createElement('span');
    token.className = part.type === keepType ? `diff-token ${tokenClass}` : 'diff-token';
    appendText(token, part.text);
    body.appendChild(token);
  });

  pane.appendChild(body);
  return pane;
}

function renderInlineDiff(parts, options) {
  const visibleParts = options.showOnlyChanges
    ? parts.filter(part => part.type !== 'equal')
    : parts;

  elements.resultArea.innerHTML = '';

  if (!visibleParts.length) {
    setEmptyResult('변경된 내용이 없습니다.');
    return;
  }

  const columns = document.createElement('div');
  columns.className = 'diff-inline-columns';
  columns.appendChild(renderInlinePane('원본 문서', visibleParts, 'delete', 'deleted'));
  columns.appendChild(renderInlinePane('비교 문서', visibleParts, 'insert', 'inserted'));
  elements.resultArea.appendChild(columns);
}

function renderDiff(parts, options) {
  if (options.mode === 'line') {
    renderLineDiff(parts, options);
  } else {
    renderInlineDiff(parts, options);
  }
}

function updateSummary(parts, options) {
  const summary = summarizeParts(parts);
  const labels = [];

  if (options.ignoreWhitespace) {
    labels.push('공백 무시');
  }

  if (options.ignoreCase) {
    labels.push('대소문자 무시');
  }

  if (options.showOnlyChanges) {
    labels.push('변경만 표시');
  }

  elements.resultSummary.textContent = `${modeLabels[options.mode]} 비교: +${summary.insert} 삽입, -${summary.delete} 삭제, ${summary.equal} 동일${labels.length ? ` (${labels.join(', ')})` : ''}`;
}

function compareTexts() {
  const sourceText = elements.sourceText.value;
  const targetText = elements.targetText.value;
  const options = getOptions();

  if (!sourceText && !targetText) {
    lastDiff = null;
    elements.resultSummary.textContent = EMPTY_MESSAGE;
    elements.resultArea.innerHTML = '';
    elements.copyBtn.disabled = true;
    return;
  }

  const a = splitTokens(sourceText, options);
  const b = splitTokens(targetText, options);
  const cellCount = a.length * b.length;

  if (cellCount > MAX_DIFF_CELLS) {
    lastDiff = null;
    elements.copyBtn.disabled = true;
    elements.resultSummary.textContent = `${modeLabels[options.mode]} 비교 범위가 너무 큽니다. 비교 단위를 바꾸거나 텍스트를 줄여주세요.`;
    setEmptyResult(`현재 ${a.length.toLocaleString('ko-KR')} x ${b.length.toLocaleString('ko-KR')} 토큰입니다.`);
    return;
  }

  const parts = buildDiff(a, b);

  lastDiff = { parts, options };
  elements.copyBtn.disabled = false;
  elements.copyStatus.textContent = '';
  updateSummary(parts, options);
  renderDiff(parts, options);
}

function clearFields() {
  elements.sourceText.value = '';
  elements.targetText.value = '';
  lastDiff = null;
  updateStats();
  elements.resultSummary.textContent = EMPTY_MESSAGE;
  elements.resultArea.innerHTML = '';
  elements.copyStatus.textContent = '';
  elements.copyBtn.disabled = true;
  elements.sourceText.focus();
}

function swapTexts() {
  const sourceText = elements.sourceText.value;
  elements.sourceText.value = elements.targetText.value;
  elements.targetText.value = sourceText;
  updateStats();
  compareTexts();
}

function formatLineResult(parts) {
  return buildAlignedRows(parts).flatMap(row => {
    if (row.type === 'equal') {
      return [`  ${row.sourceText}`];
    }

    if (row.type === 'delete') {
      return [`- ${row.sourceText}`];
    }

    if (row.type === 'insert') {
      return [`+ ${row.targetText}`];
    }

    return [`- ${row.sourceText}`, `+ ${row.targetText}`];
  }).join('\n');
}

function formatInlineResult(parts) {
  return mergeParts(parts)
    .filter(part => part.type !== 'equal' || part.text.trim())
    .map(part => {
      const prefix = part.type === 'insert' ? '+' : part.type === 'delete' ? '-' : ' ';
      return `${prefix} ${part.text}`;
    })
    .join('\n');
}

function formatResultForCopy() {
  if (!lastDiff) {
    return '';
  }

  if (lastDiff.options.mode === 'line') {
    return formatLineResult(lastDiff.parts);
  }

  return formatInlineResult(lastDiff.parts);
}

function fallbackCopy(text) {
  const textarea = document.createElement('textarea');
  textarea.value = text;
  textarea.setAttribute('readonly', '');
  textarea.style.position = 'fixed';
  textarea.style.top = '-1000px';
  document.body.appendChild(textarea);
  textarea.select();
  document.execCommand('copy');
  document.body.removeChild(textarea);
}

function copyResult() {
  const textContent = formatResultForCopy();

  if (!textContent) {
    return;
  }

  if (navigator.clipboard && window.isSecureContext) {
    navigator.clipboard.writeText(textContent).then(() => {
      elements.copyStatus.textContent = '복사됨';
    });
  } else {
    fallbackCopy(textContent);
    elements.copyStatus.textContent = '복사됨';
  }
}

function compareIfReady() {
  if (elements.sourceText.value || elements.targetText.value || lastDiff) {
    compareTexts();
  }
}

elements.sourceText.addEventListener('input', updateStats);
elements.targetText.addEventListener('input', updateStats);
elements.compareMode.addEventListener('change', compareIfReady);
elements.ignoreWhitespace.addEventListener('change', compareIfReady);
elements.ignoreCase.addEventListener('change', compareIfReady);
elements.showOnlyChanges.addEventListener('change', compareIfReady);
elements.compareBtn.addEventListener('click', compareTexts);
elements.clearBtn.addEventListener('click', clearFields);
elements.copyBtn.addEventListener('click', copyResult);
elements.swapBtn.addEventListener('click', swapTexts);
updateStats();
</script>
