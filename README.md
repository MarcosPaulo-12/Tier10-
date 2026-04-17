# Tier10-


<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Tier10 - HEAT</title>

<style>
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:Arial;background:url(https://raw.githubusercontent.com/MarcosPaulo-12/Background-/main/1776443927152.jpg);color:#fff}
.topo,.box,.container,.pool{padding:12px}
.topo{text-align:center}
.logo{height:95px}

.card,.box{
background:#B22222;
border:1px solid #2b2b2b;
border-radius:12px;
margin:10px 12px;
padding:12px
}

.background
{color:orange}
;
.temaTitle,.small{
font-size:13px;
color:#999;
margin-bottom:8px;
text-transform:uppercase
}

#temaTexto{
font-size:22px;
font-weight:bold;
color:orange;
margin-bottom:10px;
text-align:center;
direction:ltr
}

.row,.grid{
display:flex;
gap:8px
}

.grid{flex-wrap:wrap}
.grid>*{flex:1 1 48%}

input,button{
padding:11px;
border:none;
border-radius:8px;
font-size:14px;
width:100%
}

input{
background:#252525;
color:#fff;
text-align:left;
direction:ltr
}

button{
background:orange;
font-weight:bold
}

.sec{
background:#2a2a2a;
color:#fff
}

.tier{
display:flex;
border:2px solid #333;
margin-bottom:6px
}

.label{
width:95px;
display:flex;
align-items:center;
justify-content:center;
font-weight:bold;
text-align:center;
padding:8px
}

.items{
flex:1;
min-height:72px;
padding:8px;
display:flex;
gap:8px;
flex-wrap:wrap
}

.pool h2{
margin-bottom:10px;
font-size:26px
}

.item{
width:62px;
height:62px;
background:#222;
border-radius:8px;
overflow:hidden;
position:relative
}

.item img{
width:100%;
height:100%;
object-fit:cover
}

.play{
position:absolute;
right:3px;
bottom:3px;
background:#000;
padding:2px 4px;
font-size:11px;
border-radius:4px
}

#modal{
display:none;
position:fixed;
inset:0;
background:rgba(0,0,0,.94);
justify-content:center;
align-items:center;
z-index:99
}

#modal iframe,#modal video{
width:92%;
height:62%;
border:none;
border-radius:10px
}

@media(max-width:700px){
.grid>*{flex:1 1 100%}
.logo{height:170px}
.label{width:85px;font-size:14px}
.item{width:58px;height:58px}
#temaTexto{font-size:19px}
}
</style>
</head>
<body>

<div class="topo">
<img src="/storage/emulated/0/Pictures/PS%20CC%202019/1776208406766.png" class="logo">
</div>

<div class="card">
<div class="temaTitle">Tema Atual</div>
<div id="temaTexto">Nenhum definido</div>

<div class="row">
<input id="temaInput" placeholder="Digite o tema">
<button onclick="setTema()">OK</button>
</div>
</div>

<div class="container" id="tiers"></div>

<div class="pool">
<h2>Itens</h2>
<div class="items" id="poolArea" style="border:2px solid #333"></div>
</div>

<div class="box">
<div class="small">Adicionar Item</div>

<div class="grid">
<input id="imgInput" placeholder="Link da imagem">
<input type="file" id="imgFile" accept="image/*">

<input id="vidInput" placeholder="Link do vídeo">
<input type="file" id="vidFile" accept="video/*">
</div>

<div style="margin-top:10px">
<button onclick="addItem()">+ Adicionar Item</button>
</div>

<div class="grid" style="margin-top:8px">
<button class="sec" onclick="location.reload()">Resetar</button>
<button class="sec" onclick="printScreen()">📸 Print</button>
</div>
</div>

<div id="modal" onclick="closeVideo()"></div>

<script src="https://html2canvas.hertzen.com/dist/html2canvas.min.js"></script>

<script>
const $=id=>document.getElementById(id),
tiers=$("tiers"),
names=["HEAT+","9","8","7","6","5","4","3","2","1","TÁ MALUCO"];

let drag=null;

/* tema */

function setTema(){

  const campo = $("temaInput");
  const texto = campo.value.trim();

  $("temaTexto").textContent =
    texto || "Nenhum definido";

  campo.value = "";
  campo.blur();

  setTimeout(()=>{
    campo.type = "text";
    campo.focus();
    campo.blur();
  },50);
}

/* tiers */
names.forEach((n,i)=>{
let t=div("tier"),
l=div("label",n),
it=div("items");

it.dataset.i=i;
it.ondragover=e=>{
e.preventDefault();
scrollAuto(e.clientY);
};
it.ondrop=()=>drop(it);

t.append(l,it);
tiers.append(t);
});

/* add */
function addItem(){

let img=$("imgFile").files[0]?
URL.createObjectURL($("imgFile").files[0]):
$("imgInput").value.trim();

let vid=$("vidFile").files[0],
link=$("vidInput").value.trim();

if(vid){
vid=URL.createObjectURL(vid);
thumbVideo(vid,t=>put(t,vid));
clear();
return;
}

if(link){
vid=yt(link);
img=img||thumb(link)||fallback();
}

if(!img&&!vid)return alert("Adicione imagem ou vídeo");

put(img||fallback(),vid);
clear();
}

function put(img,vid){
$("poolArea").append(item(img,vid));
}

/* item */
function item(img,vid){

let d=div("item");
d.draggable=true;
d.ondragstart=()=>drag=d;

let im=new Image();
im.src=img;
im.onerror=()=>im.src=fallback();

im.onclick = e=>{
  e.stopPropagation();
  openImage(img);
};
d.append(im);

if(vid){
let p=div("play","▶");
p.onclick=e=>{
e.stopPropagation();
openVideo(vid);
};
d.append(p);
}

return d;
}

/* drop */
function drop(target){

let i=+target.dataset.i;

if((i==0||i==10)&&target.children[0]){
let old=target.children[0];
document.querySelector(`[data-i="${i==0?1:9}"]`)
.append(old);
}

target.append(drag);
}

/* auto scroll */
function scrollAuto(y){
if(y<120)scrollBy(0,-18);
if(y>innerHeight-120)scrollBy(0,18);
}

/* video */
function openVideo(u){
$("modal").style.display="flex";
$("modal").innerHTML=u.startsWith("blob:")
?`<video controls autoplay src="${u}"></video>`
:`<iframe src="${u}" allowfullscreen></iframe>`;
}

function closeVideo(){
$("modal").style.display="none";
$("modal").innerHTML="";
}

/* print */
function printScreen(){
html2canvas(document.body).then(c=>{
let a=document.createElement("a");
a.download="tier10.png";
a.href=c.toDataURL();
a.click();
});
}

/* helpers */
function div(c,t=""){
let e=document.createElement("div");
e.className=c;
e.textContent=t;
return e;
}

function clear(){
["imgInput","imgFile","vidInput","vidFile"]
.forEach(i=>$(i).value="");
}

function fallback(){
return "https://via.placeholder.com/65?text=VIDEO";
}

function yt(u){

let id = getYoutubeId(u);

return id
? `https://www.youtube.com/embed/${id}`
: u;

}

function thumb(u){

let id = getYoutubeId(u);

return id
? `https://img.youtube.com/vi/${id}/mqdefault.jpg`
: null;

}
function getYoutubeId(url){

if(url.includes("watch?v="))
return url.split("watch?v=")[1].split("&")[0];

if(url.includes("youtu.be/"))
return url.split("youtu.be/")[1].split("?")[0];

if(url.includes("/shorts/"))
return url.split("/shorts/")[1].split("?")[0];

if(url.includes("/live/"))
return url.split("/live/")[1].split("?")[0];

return null;
}

function thumbVideo(src,cb){
let v=document.createElement("video");
v.src=src;
v.onloadeddata=()=>v.currentTime=1;
v.onseeked=()=>{
let c=document.createElement("canvas");
c.width=v.videoWidth;
c.height=v.videoHeight;
c.getContext("2d").drawImage(v,0,0);
cb(c.toDataURL());
};
}

function openImage(url){

  $("modal").style.display = "flex";

  $("modal").innerHTML =
  `<img src="${url}"
   style="
   max-width:95%;
   max-height:90%;
   border-radius:10px;
   object-fit:contain;
  ">`;
}
</script>
</body>
</html>