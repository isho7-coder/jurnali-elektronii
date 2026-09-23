# jurnali-elektronii<!DOCTYPE html>
<html lang="tg">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Журнали электронии омӯзгор</title>
<style>
*{box-sizing:border-box}
body{
 margin:0;font-family:Arial,sans-serif;background:#f3f6fa;color:#17202a;
}
header{
 background:#1769aa;color:white;padding:18px 12px;text-align:center;
 position:sticky;top:0;z-index:5;
}
h1{font-size:24px;margin:0 0 8px}
.subtitle{font-size:14px}
main{padding:12px;max-width:1200px;margin:auto}
.card{
 background:white;border-radius:14px;padding:14px;margin-bottom:12px;
 box-shadow:0 2px 8px #0001;
}
label{font-weight:bold;display:block;margin-bottom:6px}
select,input,button{
 font-size:16px;border:1px solid #ccd3da;border-radius:9px;
 padding:10px;background:white;
}
select,input{width:100%}
button{
 background:#1769aa;color:white;border:0;font-weight:bold;
 cursor:pointer;
}
button.secondary{background:#68737d}
button.danger{background:#c62828}
.grid{
 display:grid;grid-template-columns:1fr 1fr;gap:10px;
}
.info{
 background:#e8f3ff;padding:10px;border-radius:10px;
 margin-top:10px;
}
.table-wrap{overflow:auto}
table{
 border-collapse:collapse;width:100%;min-width:760px;
}
th,td{
 border:1px solid #d5dbe0;padding:7px;text-align:center;
}
th{
 background:#1769aa;color:white;position:sticky;top:76px;z-index:2;
}
td:first-child,th:first-child{
 position:sticky;left:0;background:#eef4f8;z-index:1;
}
th:first-child{background:#1769aa}
.name{
 min-width:220px;text-align:left;
}
.grade{
 width:75px;text-align:center;
}
.avg{font-weight:bold;background:#f4f8fb}
.small{font-size:13px;color:#667}
.actions{
 display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-top:10px;
}
@media(max-width:600px){
 h1{font-size:20px}
 .grid{grid-template-columns:1fr}
 .actions{grid-template-columns:1fr}
}
</style>
</head>

<body>

<header>
<h1>📚 ЖУРНАЛИ ЭЛЕКТРОНИИ</h1>
<div class="subtitle">Журнали 10-баллаи омӯзгор</div>
</header>

<main>

<div class="card">
<div class="grid">

<div>
<label>Синф</label>
<select id="classSelect" onchange="loadJournal()">
<option value="">Синфро интихоб кунед</option>
<option>7-А</option><option>7-Б</option><option>7-В</option>
<option>7-Г</option><option>7-Ғ</option><option>7-Д</option><option>7-Е</option>
<option>10-А</option><option>10-Б</option><option>10-В</option>
<option>10-Г</option><option>10-Ғ</option><option>10-Д</option><option>10-Е</option>
<option>11-А</option><option>11-Б</option><option>11-В</option>
<option>11-Г</option><option>11-Ғ</option><option>11-Д</option><option>11-Е</option>
</select>
</div>

<div>
<label>Сана</label>
<input type="date" id="dateInput">
</div>

</div>

<div class="info">
<b>Фан:</b> <span id="subject">—</span><br>
<b>Шумораи хонандагон:</b> 45
</div>
</div>

<div class="card">

<label>Баҳо барои санаи интихобшуда</label>

<div class="small">
Баҳои аз 1 то 10 гузоред. Номи хонандагонро худатон ворид кунед.
</div>

<div class="table-wrap">
<table>
<thead>
<tr>
<th>№</th>
<th>Номи хонанда</th>
<th>Баҳо</th>
<th>Миёна</th>
</tr>
</thead>
<tbody id="students"></tbody>
</table>
</div>

<div class="actions">
<button onclick="saveData()">💾 Захира кардан</button>
<button class="secondary" onclick="clearGrades()">Тоза кардани баҳоҳои ин рӯз</button>
<button onclick="window.print()">🖨 Чоп кардан</button>
<button class="danger" onclick="clearAll()">⚠️ Тоза кардани маълумоти ин синф</button>
</div>

</div>

<div class="card">
<b>📊 Маълумоти журнал</b>
<p class="small">
Номҳо ва баҳоҳо дар худи браузери ҳамин телефон нигоҳ дошта мешаванд.
Онҳо ба GitHub фиристода намешаванд.
</p>
</div>

</main>

<script>

const subjects={
 "7":"География",
 "10":"Таълими меҳнат",
 "11":"Асосҳои иқтисодиёт"
};

let currentClass="";

function key(){
 return "journal_"+currentClass;
}

function getData(){
 return JSON.parse(localStorage.getItem(key())||"{}");
}

function saveData(){
 if(!currentClass){
  alert("Аввал синфро интихоб кунед.");
  return;
 }

 const data=getData();
 const date=document.getElementById("dateInput").value;

 if(!date){
  alert("Аввал санаро интихоб кунед.");
  return;
 }

 data.students=[];

 for(let i=1;i<=45;i++){
  const name=document.getElementById("name_"+i).value.trim();
  data.students.push({name:name});
 }

 if(!data.grades)data.grades={};
 if(!data.grades[date])data.grades[date]={};

 for(let i=1;i<=45;i++){
  const value=document.getElementById("grade_"+i).value;
  data.grades[date][i]=value;
 }

 localStorage.setItem(key(),JSON.stringify(data));
 updateAverages(data);
 alert("Маълумот захира шуд ✅");
}

function loadJournal(){
 currentClass=document.getElementById("classSelect").value;

 if(!currentClass){
  document.getElementById("subject").textContent="—";
  document.getElementById("students").innerHTML="";
  return;
 }

 const grade=currentClass.split("-")[0];
 document.getElementById("subject").textContent=subjects[grade];

 const data=getData();

 let html="";

 for(let i=1;i<=45;i++){
  const student=data.students?.[i-1]||{};
  html+=`
  <tr>
   <td>${i}</td>
   <td>
    <input class="name" id="name_${i}"
    value="${escapeHtml(student.name||"")}"
    placeholder="Номи хонанда">
   </td>
   <td>
    <input class="grade" id="grade_${i}"
    type="number" min="1" max="10" step="1"
    placeholder="—">
   </td>
   <td class="avg" id="avg_${i}">—</td>
  </tr>`;
 }

 document.getElementById("students").innerHTML=html;

 const today=new Date().toISOString().split("T")[0];
 if(!document.getElementById("dateInput").value)
  document.getElementById("dateInput").value=today;

 loadGrades();
 updateAverages(data);
}

function loadGrades(){
 if(!currentClass)return;

 const date=document.getElementById("dateInput").value;
 const data=getData();

 for(let i=1;i<=45;i++){
  const el=document.getElementById("grade_"+i);
  if(el)el.value=data.grades?.[date]?.[i]||"";
 }

 updateAverages(data);
}

document.getElementById("dateInput").addEventListener("change",loadGrades);

function updateAverages(data){
 if(!currentClass)return;

 for(let i=1;i<=45;i++){
  let grades=[];

  if(data.grades){
   for(const date in data.grades){
    const g=Number(data.grades[date][i]);
    if(g>=1 && g<=10)grades.push(g);
   }
  }

  const el=document.getElementById("avg_"+i);

  if(el){
   if(grades.length){
    const avg=grades.reduce((a,b)=>a+b,0)/grades.length;
    el.textContent=avg.toFixed(2);
   }else{
    el.textContent="—";
   }
  }
 }
}

function clearGrades(){
 if(!currentClass)return;

 const date=document.getElementById("dateInput").value;
 if(!date)return;

 if(!confirm("Баҳои ҳамин рӯз тоза карда шавад?"))return;

 const data=getData();

 if(data.grades)data.grades[date]={};

 localStorage.setItem(key(),JSON.stringify(data));
 loadGrades();
}

function clearAll(){
 if(!currentClass)return;

 if(!confirm("ҲАМАИ маълумоти ин синф тоза карда шавад?"))return;

 localStorage.removeItem(key());
 loadJournal();
}

function escapeHtml(text){
 return text
 .replace(/&/g,"&amp;")
 .replace(/</g,"&lt;")
 .replace(/>/g,"&gt;")
 .replace(/"/g,"&quot;")
 .replace(/'/g,"&#039;");
}

</script>

</body>
</html>