---
title: 문서 비교
layout: page
permalink: /tools/diff/
---

문서의 두 텍스트를 나란히 비교하고 차이를 시각적으로 확인할 수 있는 도구입니다. 아래에 원본 텍스트와 비교할 텍스트를 붙여넣고 `비교하기`를 눌러 결과를 확인하세요.

<div class="diff-tool">
  <div class="diff-columns">
    <section class="diff-panel">
      <h2>원본 문서</h2>
      <textarea id="sourceText" placeholder="비교할 첫 번째 텍스트를 여기에 붙여넣기"></textarea>
    </section>
    <section class="diff-panel">
      <h2>비교 문서</h2>
      <textarea id="targetText" placeholder="비교할 두 번째 텍스트를 여기에 붙여넣기"></textarea>
    </section>
  </div>

  <div class="diff-meta">
    <label>
      <span>비교 단위</span>
      <select id="compareMode">
        <option value="line">행 단위</option>
        <option value="word">단어 단위</option>
      </select>
    </label>
    <button id="compareBtn" class="button">비교하기</button>
    <button id="clearBtn" class="button muted">초기화</button>
    <button id="copyBtn" class="button tertiary">결과 복사</button>
  </div>

  <section class="result">
    <h2>비교 결과</h2>
    <div id="resultSummary" class="result-summary">텍스트를 입력하고 비교하기를 누르면 차이가 표시됩니다.</div>
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
  border: 1px solid rgba(55, 65, 81, 0.12);
  border-radius: 1rem;
  padding: 1rem;
  background: var(--surface);
}
.diff-panel textarea {
  width: 100%;
  min-height: 280px;
  border: 1px solid rgba(55, 65, 81, 0.12);
  border-radius: 0.75rem;
  padding: 0.85rem;
  resize: vertical;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  line-height: 1.6;
}
.diff-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
  align-items: center;
}
.diff-meta label {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}
.result {
  border: 1px solid rgba(55, 65, 81, 0.12);
  border-radius: 1rem;
  padding: 1rem;
  background: var(--surface);
}
.result-summary {
  margin-bottom: 1rem;
  color: var(--muted);
}
.result-area {
  display: grid;
  gap: 0.25rem;
}
.diff-row {
  display: block;
  width: 100%;
  padding: 0.65rem 0.85rem;
  border-radius: 0.75rem;
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
.diff-token {
  border-radius: 0.35rem;
  padding: 0.08rem 0.25rem;
}
.diff-token.inserted {
  background: rgba(16, 185, 129, 0.18);
  color: #065f46;
}
.diff-token.deleted {
  background: rgba(239, 68, 68, 0.18);
  color: #831010;
}
</style>

<script>
function escapeHTML(text) {
  return text.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;');
}

function splitLineTokens(text) {
  return text.split(/\r?\n/);
}

function splitWordTokens(text) {
  return text.split(/(\s+)/).filter(Boolean);
}

function buildDiff(a, b) {
  const m = a.length;
  const n = b.length;
  const dp = Array.from({ length: m + 1 }, () => Array(n + 1).fill(0));

  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (a[i - 1] === b[j - 1]) {
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
    if (i > 0 && j > 0 && a[i - 1] === b[j - 1]) {
      parts.unshift({ type: 'equal', text: a[i - 1] });
      i -= 1;
      j -= 1;
    } else if (j > 0 && (i === 0 || dp[i][j - 1] >= dp[i - 1][j])) {
      parts.unshift({ type: 'insert', text: b[j - 1] });
      j -= 1;
    } else {
      parts.unshift({ type: 'delete', text: a[i - 1] });
      i -= 1;
    }
  }

  return parts;
}

function renderDiff(parts, mode) {
  const resultArea = document.getElementById('resultArea');
  resultArea.innerHTML = '';

  if (mode === 'line') {
    parts.forEach(part => {
      const row = document.createElement('div');
      row.className = 'diff-row diff-' + part.type;
      row.innerHTML = escapeHTML(part.text);
      resultArea.appendChild(row);
    });
  } else {
    const line = document.createElement('div');
    line.className = 'diff-row diff-equal';
    parts.forEach(part => {
      const token = document.createElement('span');
      token.className = 'diff-token ' + (part.type === 'insert' ? 'inserted' : part.type === 'delete' ? 'deleted' : '');
      token.innerHTML = escapeHTML(part.text);
      line.appendChild(token);
    });
    resultArea.appendChild(line);
  }
}

function compareTexts() {
  const sourceText = document.getElementById('sourceText').value;
  const targetText = document.getElementById('targetText').value;
  const mode = document.getElementById('compareMode').value;
  const summary = document.getElementById('resultSummary');

  if (!sourceText && !targetText) {
    summary.textContent = '텍스트를 입력하고 비교하기를 누르면 차이가 표시됩니다.';
    document.getElementById('resultArea').innerHTML = '';
    return;
  }

  const a = mode === 'line' ? splitLineTokens(sourceText) : splitWordTokens(sourceText);
  const b = mode === 'line' ? splitLineTokens(targetText) : splitWordTokens(targetText);
  const parts = buildDiff(a, b);

  const inserted = parts.filter(part => part.type === 'insert').length;
  const deleted = parts.filter(part => part.type === 'delete').length;
  summary.textContent = `${mode === 'line' ? '행' : '단어'} 비교: +${inserted} 삽입, -${deleted} 삭제, ${parts.filter(part => part.type === 'equal').length} 동일`;

  renderDiff(parts, mode);
}

function clearFields() {
  document.getElementById('sourceText').value = '';
  document.getElementById('targetText').value = '';
  document.getElementById('resultSummary').textContent = '텍스트를 입력하고 비교하기를 누르면 차이가 표시됩니다.';
  document.getElementById('resultArea').innerHTML = '';
}

function copyResult() {
  const resultArea = document.getElementById('resultArea');
  const textContent = resultArea.textContent;
  if (!textContent) {
    return;
  }
  navigator.clipboard.writeText(textContent).then(() => {
    alert('비교 결과가 클립보드에 복사되었습니다.');
  });
}

document.getElementById('compareBtn').addEventListener('click', compareTexts);
document.getElementById('clearBtn').addEventListener('click', clearFields);
document.getElementById('copyBtn').addEventListener('click', copyResult);
</script>
