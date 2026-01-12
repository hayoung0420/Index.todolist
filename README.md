<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>가계부</title>
<style>
:root {
  --bg: #f7f2e9;
  --card: #fff8f0;
  --text: #3b2f2f;
  --sub: #6b5e4f;
  --muted: #d9cfc2;
  --brand: #f4d7b2;
  --brand-dark: #c39f7a;
  --danger: #ef4444;
  --ok: #16a34a;
  --radius: 12px;
  --hover: #f1ebe0;
  --fx-bg: #f4d7b2;   /* 외화 가을톤 */
  --krw-bg: #c39f7a;  /* 원화 가을톤 */
}
* { box-sizing: border-box; }
body { margin:0; font-family:'Segoe UI', Roboto, 'Noto Sans KR', sans-serif; background: var(--bg); color: var(--text); }

.container { max-width:1200px; margin:24px auto; padding:0 16px; position: relative; }

/* 합계 카드 */
#summaryCard { position:absolute; top:16px; right:16px; display:flex; gap:12px; flex-wrap:wrap; z-index: 10; }
#summaryCard .box { border-radius: var(--radius); padding: 12px 16px; text-align: center; display: flex; align-items: center; justify-content: center; gap: 8px; font-weight:bold; box-shadow:0 4px 12px rgba(0,0,0,0.05); font-size:16px; }
#summaryCard .fx { background: var(--fx-bg); border:2px solid var(--brand-dark); }
#summaryCard .krw { background: var(--krw-bg); border:2px solid var(--brand-dark); }

/* 카드 */
.card { background: var(--card); border-radius: var(--radius); box-shadow:0 6px 18px rgba(0,0,0,0.08); margin-bottom: 80px; overflow:hidden; }
.card-header { padding:24px; background: var(--brand); color: var(--text); border-bottom:2px solid var(--brand-dark); }
.title { margin:0; font-size:26px; font-weight:900; }
.subtitle { margin:6px 0 0; font-size:14px; color: var(--sub); }

/* 폼 */
.form { display:grid; grid-template-columns: repeat(auto-fill, minmax(120px, 1fr)); gap:12px; padding:20px; background:#f8f5f0; }
.form input, .form select { padding:10px; border:1px solid var(--muted); border-radius: var(--radius); font-size:14px; }
.form input:focus, .form select:focus { outline:none; border-color: var(--brand-dark); box-shadow:0 0 5px rgba(195,159,122,0.3); }
.form button { grid-column:1 / -1; padding:12px; background: var(--brand); color: var(--text); border:none; border-radius: var(--radius); cursor:pointer; font-weight:bold; font-size:16px; transition:0.2s; }
.form button:hover { background: var(--brand-dark); color:#fff; }

/* 아코디언 */
.accordion-item { border-radius: var(--radius); overflow: hidden; margin-bottom: 12px; box-shadow:0 2px 6px rgba(0,0,0,0.06); background: var(--card); }
.accordion-header { background: var(--hover); padding: 14px 18px; font-weight: bold; display: flex; justify-content: space-between; align-items: center; cursor: pointer; transition:0.2s; }
.accordion-header:hover { background: #e8dfd0; }
.accordion-header span.total { font-weight: normal; color: var(--sub); font-size: 13px; }
.accordion-header span.trip { font-weight: normal; font-size: 12px; color: var(--sub); margin-left: 10px; }
.accordion-content { display: none; padding: 10px 18px 18px; }
.accordion-content table { width:100%; border-collapse: collapse; }
.accordion-content th, .accordion-content td { padding:8px 10px; border-bottom:1px solid var(--muted); font-size:14px; text-align:center; }
.accordion-content th { background:#f1ece4; font-size:12px; color: var(--sub); }

/* 칩 */
.chip { display:inline-block; padding:2px 8px; border-radius:999px; font-size:12px; font-weight:600; }
.chip-exp { background:#fee2e2; color: var(--danger); }
.chip-inc { background:#dcfce7; color: var(--ok); }

/* 버튼 */
.del-btn, .edit-btn { border:none; background:transparent; cursor:pointer; font-size:12px; margin-left:4px; }
.del-btn { color: var(--danger); }
.edit-btn { color: var(--brand-dark); }
.save-btn { padding:8px 14px; border-radius:10px; background: var(--brand); color: var(--text); border:none; cursor:pointer; font-weight:bold; font-size:14px; transition:0.2s; }
.save-btn:hover { background: var(--brand-dark); color:#fff; }

/* 카테고리 요약 */
#categorySummary { margin-top:24px; border-radius: var(--radius); background: var(--card); padding:16px; box-shadow:0 2px 8px rgba(0,0,0,0.06); }
#categorySummary table { width:100%; border-collapse:collapse; }
#categorySummary th, #categorySummary td { padding:8px 10px; border-bottom:1px solid var(--muted); text-align:center; font-size:14px; }
#categorySummary th { background:#f1ece4; font-size:12px; color: var(--sub); }

.month-filter { margin-bottom:16px; text-align:center; }
.month-filter button { margin:0 4px; padding:6px 12px; border-radius:8px; border:1px solid var(--muted); background: var(--brand); cursor:pointer; transition:0.2s; }
.month-filter button:hover { background: var(--brand-dark); color:#fff; }
.month-filter button.active { background: var(--brand-dark); color:#fff; }
</style>
</head>
<body>
<div class="container">
  <div class="card">
    <div class="card-header">
      <h1 class="title">가계부</h1>
      <p class="subtitle">내 지출/입금 기록 관리</p>
    </div>
    <form class="form" id="form">
      <input type="text" id="date" placeholder="날짜 YYYY-MM-DD or 여행" required>
      <input type="text" id="category" placeholder="항목" required>
      <input type="text" id="desc" placeholder="내용">
      <input type="number" step="any" id="fx" placeholder="외화">
      <input type="number" step="any" id="krw" placeholder="KRW">
      <select id="type">
        <option value="지출">지출</option>
        <option value="입금">입금</option>
      </select>
      <input type="text" id="trip" placeholder="여행 국가 (선택)">
      <input type="text" id="note" placeholder="비고">
      <button type="submit">추가/수정</button>
    </form>
  </div>

  <div class="month-filter" id="monthFilter"></div>
  <div id="summaryCard"></div>
  <div id="accordion"></div>
  <div id="categorySummary"></div>
</div>

<script>
const STORAGE_KEY="my_ledger";
let rows=JSON.parse(localStorage.getItem(STORAGE_KEY)||"[]");
rows.forEach(r=>{ if(!r.fxRaw) r.fxRaw=r.fx??0; if(!r.krwRaw) r.krwRaw=r.krw??0; if(!r.trip) r.trip=""; });

const form=document.getElementById("form");
const accordion=document.getElementById("accordion");
const summaryCard=document.getElementById("summaryCard");
const categorySummary=document.getElementById("categorySummary");
const monthFilter=document.getElementById("monthFilter");
let editIndex=-1; 
let currentMonthFilter="all";

form.addEventListener("submit",(e)=>{
  e.preventDefault();
  const fxVal=document.getElementById("fx").value;
  const krwVal=document.getElementById("krw").value;
  const row={
    date:document.getElementById("date").value,
    category:document.getElementById("category").value,
    desc:document.getElementById("desc").value,
    fx:parseFloat(fxVal)||0,
    krw:parseInt(krwVal)||0,
    fxRaw:fxVal,
    krwRaw:krwVal,
    type:document.getElementById("type").value,
    trip:document.getElementById("trip").value,
    note:document.getElementById("note").value,
  };
  if(editIndex>=0){ rows[editIndex]=row; editIndex=-1; } else{ rows.push(row); }
  renderAll();
  form.reset();
});

function saveData(){ localStorage.setItem(STORAGE_KEY,JSON.stringify(rows)); downloadJSON(); alert("저장이 완료되었습니다."); }
function downloadJSON(){ const blob=new Blob([JSON.stringify(rows,null,2)],{type:"application/json"}); const a=document.createElement("a"); a.href=URL.createObjectURL(blob); a.download=`ledger_backup_${new Date().toISOString().slice(0,10)}.json`; document.body.appendChild(a); a.click(); document.body.removeChild(a); URL.revokeObjectURL(a.href); }
setInterval(()=>{ localStorage.setItem(STORAGE_KEY,JSON.stringify(rows)); },5*60*1000);

function groupByMonth(data){
  const map={};
  data.forEach(r=>{
    let month=r.date.includes("-")?r.date.slice(0,7):"trip";
    if(!map[month]) map[month]=[];
    map[month].push(r);
  });
  return map;
}

function renderMonthFilter(){
  const months=Object.keys(groupByMonth(rows)).filter(m=>m!=="trip").sort();
  let html=`<button class="${currentMonthFilter==='all'?'active':''}" onclick="filterMonth('all')">전체</button>`;
  months.forEach(m=>{
    html+=`<button class="${currentMonthFilter===m?'active':''}" onclick="filterMonth('${m}')">${m}</button>`;
  });
  monthFilter.innerHTML=html;
}

function filterMonth(month){ currentMonthFilter=month; renderAll(); }

function renderSummary(){
  let fxExp=0,fxInc=0,krwExp=0,krwInc=0;
  rows.forEach(r=>{ 
    if(currentMonthFilter!=="all" && !r.date.startsWith(currentMonthFilter)) return;
    if(r.type==="입금"){ fxInc+=r.fx; krwInc+=r.krw; } 
    else{ fxExp+=r.fx; krwExp+=r.krw; } 
  });

  function formatFX(val){ return Number.isInteger(val)?val:val; } // 소수점은 입력대로
  function formatKRW(val){ return val.toLocaleString(); }

  summaryCard.innerHTML=`
  <div class="box fx">${formatFX(fxInc-fxExp)} FX</div>
  <button class="save-btn" onclick="saveData()">저장</button>
  <div class="box krw">${formatKRW(krwInc-krwExp)} 원</div>`;
}

function groupByCategory(data){ const map={}; data.forEach(r=>{ if(currentMonthFilter!=="all" && !r.date.startsWith(currentMonthFilter)) return; if(!map[r.category]) map[r.category]=[]; map[r.category].push(r); }); return map; }

function renderCategorySummary(){
  const grouped=groupByCategory(rows);
  let html=`<table><thead><tr><th>항목</th><th>총 외화</th><th>총 KRW</th><th>합계</th></tr></thead><tbody>`;
  Object.keys(grouped).forEach(cat=>{
    const items=grouped[cat];
    const totalFX=items.reduce((a,b)=>a+(b.type==="입금"?b.fx:-b.fx),0);
    const totalKRW=items.reduce((a,b)=>a+(b.type==="입금"?b.krw:-b.krw),0);
    html+=`<tr><td>${cat}</td><td>${totalFX}</td><td>${totalKRW.toLocaleString()}</td><td>${totalKRW.toLocaleString()} 원</td></tr>`;
  });
  html+="</tbody></table>";
  categorySummary.innerHTML=html;
}

function renderAccordion(){
  accordion.innerHTML="";
  const filteredRows=currentMonthFilter==="all"?rows:rows.filter(r=>r.date.startsWith(currentMonthFilter));
  const grouped={};
  filteredRows.forEach(r=>{ if(!grouped[r.date]) grouped[r.date]=[]; grouped[r.date].push(r); });
  Object.keys(grouped).sort().forEach(date=>{
    const items=grouped[date];
    const totalKRW=items.reduce((a,b)=>a+(b.type==="입금"?b.krw:-b.krw),0);
    const header=document.createElement("div");
    header.className="accordion-header";
    const trips=items.map(r=>r.trip).filter(t=>t).join(", ");
    header.innerHTML=`<span>${date}</span>${trips?'<span class="trip">'+trips+'</span>':''}<span class="total">총액: ${totalKRW.toLocaleString()} 원</span><span>+</span>`;
    const content=document.createElement("div");
    content.className="accordion-content";
    let html=`<table><thead><tr><th>항목</th><th>내용</th><th>외화</th><th>KRW</th><th>구분</th><th>여행</th><th>비고</th><th></th></tr></thead><tbody>`;
    items.forEach(r=>{
      html+=`<tr>
        <td>${r.category}</td>
        <td>${r.desc}</td>
        <td>${r.fxRaw}</td>
        <td>${r.krwRaw}</td>
        <td>${r.type==="입금"?'<span class="chip chip-inc">입금</span>':'<span class="chip chip-exp">지출</span>'}</td>
        <td>${r.trip}</td>
        <td>${r.note}</td>
        <td><button class="edit-btn" onclick="editRow(${rows.indexOf(r)})">수정</button><button class="del-btn" onclick="deleteRow(${rows.indexOf(r)})">삭제</button></td>
      </tr>`;
    });
    html+="</tbody></table>";
    content.innerHTML=html;
    const item=document.createElement("div");
    item.className="accordion-item";
    item.appendChild(header);
    item.appendChild(content);
    accordion.appendChild(item);
    header.addEventListener("click",()=>{ content.style.display=content.style.display==="none"||content.style.display===""?"block":"none"; });
  });
}

function deleteRow(idx){ if(!confirm("삭제하시겠습니까?")) return; rows.splice(idx,1); renderAll(); }
function editRow(idx){
  const r=rows[idx];
  document.getElementById("date").value=r.date;
  document.getElementById("category").value=r.category;
  document.getElementById("desc").value=r.desc; 
  document.getElementById("fx").value=r.fxRaw;
  document.getElementById("krw").value=r.krwRaw;
  document.getElementById("type").value=r.type;
  document.getElementById("trip").value=r.trip;
  document.getElementById("note").value=r.note;
  editIndex=idx;
}

function renderAll(){ renderMonthFilter(); renderSummary(); renderAccordion(); renderCategorySummary(); }
renderAll();
</script>
</body>
</html>
