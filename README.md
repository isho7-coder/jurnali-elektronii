<!DOCTYPE html>
<html lang="tg">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Журнали электронии омӯзгор</title>
<style>
*{box-sizing:border-box}
body{
 margin:0;
 font-family:Arial,sans-serif;
 background:#eef4fb;
 color:#172033;
}
header{
 background:#1769aa;
 color:white;
 padding:18px 12px;
 text-align:center;
}
h1{margin:0;font-size:24px}
.container{max-width:1200px;margin:auto;padding:12px}
.card{
 background:white;
 border-radius:12px;
 padding:14px;
 margin-bottom:12px;
 box-shadow:0 2px 8px #0002;
}
.controls{
 display:grid;
 grid-template-columns:repeat(auto-fit,minmax(180px,1fr));
 gap:10px;
}
label{font-weight:bold;font-size:14px}
select,input,button{
 width:100%;
 padding:11px;
 margin-top:5px;
 border:1px solid #bbb;
 border-radius:8px;
 font-size:15px;
}
button{
 border:0;
 background:#1769aa;
 color:white;
 font-weight:bold;
 cursor:pointer;
}
button.red{background:#c62828}
button.green{background:#218838}
button.gray{background:#555}
.buttons{
 display:grid;
 grid-template-columns:repeat(auto-fit,minmax(150px,1fr));
 gap:8px;
 margin-top:12px;
}
.table-wrap{overflow-x:auto}
table{
 width:100%;
 border-collapse:collapse;
 min-width:850px;
}
th,td{
 border:1px solid #bbb;
 padding:6px;
 text-align:center;
}
th{
 background:#dcecff;
 position:sticky;
 top:0;
 z-index:2;
}
td.name{
 text-align:left;
 min-width:180px;
 font-weight:bold;
}
.grade{
 width:70px;
 padding:8px;
 text-align:center;
}
.avg{font-weight:bold;background:#f4f4f4}
.info{
 background:#e8f3ff;
 padding:10px;
 border-radius:8px;
 margin-bottom:10px;
}
@media print{
 header,.controls,.buttons,.info{display:none}
 body{background:white}
 .card{box-shadow:none}
 table{font-size:11px}
}
</style>
</head>

<body>

<header>
<h1>📚 Журнали электронии омӯзгор</h1>
<p>Низоми бақайдгирии баҳои хонандагон</p>
</header>

<div class="container">

<div class="card">
<div class="controls">

<div>
<label>Синф</label>
<select id="classSelect" onchange="loadJournal()">
<option>7-А</option><option>7-Б</option><option>7-В</option>
<option>7-Г</option><option>7-Д</option><option>7-Е</option>
<option>10-А</option><option>10-Б</option><option>10-В</option>
<option>10-Г</option><option>10-Д</option><option>10-Е</option>
<option>11-А</option><option>11-Б</option><option>11-В</option>
<option>11-Г</option><option>11-Д</option><option>11-Е</option>
</select>
</div>

<div>
<label>Фан</label>
<select id="subjectSelect" onchange="loadJournal()">
<option>География</option>
<option>Таълими меҳнат</option>
<option>Асосҳои иқтисодиёт</option>
</select>
</div>

<div>
<label>Сана</label>
<input type="date" id="dateSelect" onchange="loadJournal()">
</div>

</div>

<div class="buttons">
<button class="green" onclick="saveGrades()">💾 Сабт кардан</button>
<button onclick="clearGrades()">🧹 Тоза кардани баҳоҳои рӯз</button>
<button class="red" onclick="clearClass()">🗑 Тоза кардани синф</button>
<button class="gray" onclick="window.print()">🖨 Чоп</button>
</div>
</div>

<div class="card">
<div class="info">
<strong id="titleInfo"></strong><br>
Баҳо аз <b>1 то 10</b> ворид карда мешавад. Миёнаи баҳоҳо автоматӣ ҳисоб мешавад.
</div>

<div class="table-wrap">
<table>
<thead>
<tr>
<th>№</th>
<th>Ному насаби хонанда</th>
<th>Баҳо</th>
<th>Миёна</th>
</tr>
</thead>
<tbody id="studentBody"></tbody>
</table>
</div>
</div>

</div>

<script>

const students = [
"Хонандаи 1","Хонандаи 2","Хонандаи 3","Хонандаи 4","Хонандаи 5",
"Хонандаи 6","Хонандаи 7","Хонандаи 8","Хонандаи 9","Хонандаи 10",
"Хонандаи 11","Хонандаи 12","Хонандаи 13","Хонандаи 14","Хонандаи 15",
"Хонандаи 16","Хонандаи 17","Хонандаи 18","Хонандаи 19","Хонандаи 20",
"Хонандаи 21","Хонандаи 22","Хонандаи 23","Хонандаи 24","Хонандаи 25",
"Хонандаи 26","Хонандаи 27","Хонандаи 28","Хонандаи 29","Хонандаи 30",
"Хонандаи 31","Хонандаи 32","Хонандаи 33","Хонандаи 34","Хонандаи 35",
"Хонандаи 36","Хонандаи 37","Хонандаи 38","Хонандаи 39","Хонандаи 40",
"Хонандаи 41","Хонандаи 42","Хонандаи 43","Хонандаи 44","Хонандаи 45"
];

const classSelect=document.getElementById("classSelect");
const subjectSelect=document.getElementById("subjectSelect");
const dateSelect=document.getElementById("dateSelect");

dateSelect.value=new Date().toISOString().slice(0,10);

function key(){
 return "journal_"+classSelect.value+"_"+subjectSelect.value;
}

function getData(){
 return JSON.parse(localStorage.getItem(key())||"{}");
}

function saveData(data){
 localStorage.setItem(key(),JSON.stringify(data));
}

function loadJournal(){

 const data=getData();
 const date=dateSelect.value;

 document.getElementById("titleInfo").textContent =
 classSelect.value+" — "+subjectSelect.value+" — "+date;

 const body=document.getElementById("studentBody");
 body.innerHTML="";

 students.forEach((name,i)=>{

   const tr=document.createElement("tr");

   const tdNo=document.createElement("td");
   tdNo.textContent=i+1;

   const tdName=document.createElement("td");
   tdName.className="name";
   tdName.textContent=name;

   const tdGrade=document.createElement("td");
   const input=document.createElement("input");

   input.type="number";
   input.min="1";
   input.max="10";
   input.className="grade";

   if(data[date] && data[date][i]!==undefined){
      input.value=data[date][i];
   }

   input.oninput=function(){
      calculateAverage(i);
   };

   tdGrade.appendChild(input);

   const tdAvg=document.createElement("td");
   tdAvg.className="avg";
   tdAvg.id="avg_"+i;

   tr.appendChild(tdNo);
   tr.appendChild(tdName);
   tr.appendChild(tdGrade);
   tr.appendChild(tdAvg);

   body.appendChild(tr);

   calculateAverage(i);
 });
}

function calculateAverage(index){

 const data=getData();
 let total=0;
 let count=0;

 Object.keys(data).forEach(date=>{
   const value=data[date][index];
   if(value!==undefined && value!==""){
      total+=Number(value);
      count++;
   }
 });

 const cell=document.getElementById("avg_"+index);

 if(cell){
   cell.textContent=count ? (total/count).toFixed(1) : "—";
 }
}

function saveGrades(){

 const data=getData();
 const date=dateSelect.value;

 data[date]=[];

 document.querySelectorAll(".grade").forEach(input=>{
   data[date].push(input.value);
 });

 saveData(data);

 alert("Баҳоҳо бомуваффақият сабт шуданд ✅");

 loadJournal();
}

function clearGrades(){

 if(!confirm("Баҳоҳои ҳамин рӯз тоза карда шаванд?")) return;

 const data=getData();
 delete data[dateSelect.value];

 saveData(data);
 loadJournal();
}

function clearClass(){

 if(!confirm("ҲАМАИ маълумоти ин синф ва фан тоза карда шавад?")) return;

 localStorage.removeItem(key());
 loadJournal();
}

loadJournal();

</script>

</body>
</html>
