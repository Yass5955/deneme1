<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Bibliothèque de cours</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
:root{--primary:#4f46e5;--secondary:#1e293b;--bg:#f8fafc;--card:#fff;--text:#0f172a}
*{box-sizing:border-box}
body{margin:0;font-family:Segoe UI,system-ui;background:var(--bg);color:var(--text)}
header{background:linear-gradient(135deg,var(--primary),#6366f1);color:#fff;padding:30px;text-align:center}
nav{display:flex;justify-content:center;gap:10px;padding:15px;background:var(--secondary);flex-wrap:wrap}
nav button{background:transparent;border:none;color:#fff;padding:10px 18px;border-radius:8px;cursor:pointer}
nav button.active,nav button:hover{background:#fff;color:var(--secondary)}
.container{max-width:1200px;margin:auto;padding:25px}
.login-box,.admin-panel{background:#fff;padding:25px;border-radius:12px;box-shadow:0 10px 25px rgba(0,0,0,.1);margin-bottom:25px}
.login-box input,.admin-panel input,.admin-panel select,.admin-panel button{width:100%;padding:10px;margin-bottom:10px;border-radius:6px;border:1px solid #c7d2fe}
.admin-panel{display:none;background:#eef2ff}
.admin-panel button{background:var(--primary);color:#fff;border:none;cursor:pointer}
.level{display:none}
.level.active{display:block}
.subject{background:var(--card);border-radius:12px;padding:20px;margin-bottom:20px;box-shadow:0 8px 20px rgba(0,0,0,.05)}
.subject h3{margin:0}
.chapter{margin-top:15px}
.chapter h4{margin:0;color:var(--primary)}
.subchapter{margin-left:20px;margin-top:8px}
.subchapter button{margin-left:10px;background:red;color:#fff;border:none;border-radius:4px;cursor:pointer}
footer{text-align:center;padding:20px;color:#64748b}
</style>
</head>
<body>
<header>
<h1>Bibliothèque de cours</h1>
<p>Données persistantes (GitHub Pages compatible)</p>
</header>
<nav>
<button class="active" onclick="showLevel('seconde',this)">Seconde</button>
<button onclick="showLevel('premiere',this)">Première</button>
<button onclick="showLevel('terminale',this)">Terminale</button>
<button onclick="toggleLogin()">Admin</button>
</nav>
<div class="container">
<div id="loginBox" class="login-box" style="display:none">
<h2>Connexion admin</h2>
<input id="login" placeholder="Identifiant">
<input id="password" type="password" placeholder="Mot de passe">
<button onclick="loginAdmin()">Connexion</button>
</div>
<div id="adminPanel" class="admin-panel">
<h2>Gestion des cours</h2>
<select id="levelSelect"></select>
<select id="subjectSelect"></select>
<input id="chapterInput" placeholder="Chapitre">
<input id="subchapterInput" placeholder="Sous-chapitre">
<input id="fileInput" type="file" accept="application/pdf">
<button onclick="addSubChapter()">Ajouter</button>
</div>
<div id="seconde" class="level active"></div>
<div id="premiere" class="level"></div>
<div id="terminale" class="level"></div>
</div>
<footer>© 2025 – Plateforme scolaire</footer>
<script>
const ADMIN_LOGIN="admin",ADMIN_PASS="1234";
let isAdmin=false;

const defaultData={
  seconde:{Mathématiques:{},Français:{},"Histoire-Géo":{},"Physique-Chimie":{},SVT:{},SES:{},SNT:{}},
  premiere:{Mathématiques:{},Français:{},"Histoire-Géo":{},"Physique-Chimie":{},SVT:{},SES:{}},
  terminale:{Mathématiques:{},Philosophie:{},"Histoire-Géo":{},"Physique-Chimie":{},SVT:{},SES:{}}
};

let data=JSON.parse(localStorage.getItem('coursData'))||defaultData;

function save(){localStorage.setItem('coursData',JSON.stringify(data))}

function render(){['seconde','premiere','terminale'].forEach(level=>{const container=document.getElementById(level);container.innerHTML='';for(const subject in data[level]){const sDiv=document.createElement('div');sDiv.className='subject';sDiv.innerHTML=`<h3>${subject}</h3>`;for(const chap in data[level][subject]){const cDiv=document.createElement('div');cDiv.className='chapter';cDiv.innerHTML=`<h4>${chap}</h4>`;for(const sub in data[level][subject][chap]){const sc=document.createElement('div');sc.className='subchapter';sc.innerHTML=`<a href='${data[level][subject][chap][sub]}' target='_blank'>${sub}</a>${isAdmin?" <button onclick=\"deleteSub('${level}','${subject}','${chap}','${sub}')\">X</button>":""}`;cDiv.appendChild(sc)}sDiv.appendChild(cDiv)}container.appendChild(sDiv)}})}

function showLevel(id,btn){document.querySelectorAll('.level').forEach(l=>l.classList.remove('active'));document.getElementById(id).classList.add('active');document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));btn.classList.add('active')}

function toggleLogin(){loginBox.style.display='block'}
function loginAdmin(){if(login.value===ADMIN_LOGIN&&password.value===ADMIN_PASS){isAdmin=true;loginBox.style.display='none';adminPanel.style.display='block';initAdmin();render()}else alert('Erreur')}

function initAdmin(){levelSelect.innerHTML='';Object.keys(data).forEach(l=>levelSelect.innerHTML+=`<option value='${l}'>${l}</option>`);updateSubjects();levelSelect.onchange=updateSubjects}
function updateSubjects(){subjectSelect.innerHTML='';Object.keys(data[levelSelect.value]).forEach(s=>subjectSelect.innerHTML+=`<option>${s}</option>`)}

function addSubChapter(){const l=levelSelect.value,s=subjectSelect.value,c=chapterInput.value.trim(),sc=subchapterInput.value.trim(),f=fileInput.files[0];if(!c||!sc||!f)return alert('Champs manquants');const url=URL.createObjectURL(f);if(!data[l][s][c])data[l][s][c]={};data[l][s][c][sc]=url;save();render();chapterInput.value=subchapterInput.value=fileInput.value=''}

function deleteSub(l,s,c,sc){delete data[l][s][c][sc];if(Object.keys(data[l][s][c]).length===0)delete data[l][s][c];save();render()}

render();
</script>
</body>
</html>
