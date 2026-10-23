<!DOCTYPE html>
<html lang="sq">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>PETRO59 Live Wallpaper</title>

<style>

*{
margin:0;
padding:0;
box-sizing:border-box;
}

body{
overflow:hidden;
background:black;
font-family:Arial,sans-serif;
}

/* Sfondi */
.room{
position:fixed;
inset:0;
background:url("room.jpg") center center/cover no-repeat;
}

/* Logo */
.logo{
position:absolute;
top:25px;
left:30px;
font-size:50px;
font-weight:bold;
color:#00ffff;
text-shadow:
0 0 10px #00ffff,
0 0 20px #00ffff,
0 0 40px #00ffff,
0 0 80px #00ffff;
animation:pulse 1s infinite alternate;
}

@keyframes pulse{
from{
transform:scale(1);
}
to{
transform:scale(1.08);
}
}

/* Ora */

#clock{
position:absolute;
top:30px;
right:30px;

font-size:40px;
font-weight:bold;

color:white;

text-shadow:
0 0 10px cyan,
0 0 20px cyan,
0 0 40px cyan;

z-index:100;
}

/* Visualizer */

.visualizer{
position:absolute;

right:14%;
top:30%;

width:350px;
height:120px;

display:flex;
align-items:flex-end;
gap:3px;
}

.bar{
width:5px;
height:5px;
border-radius:3px 3px 0 0;
}

/* Flash */

#flash{
position:fixed;
inset:0;

background:#00aaff;

opacity:0;

pointer-events:none;

transition:.1s;
}

/* Shi */

.rain{
position:absolute;
width:2px;
height:20px;

background:rgba(255,255,255,.7);

animation:fall linear infinite;
}

@keyframes fall{
from{
transform:translateY(-100px);
}

to{
transform:translateY(110vh);
}
}

/* Audio */

audio{
position:fixed;
bottom:20px;
left:20px;
z-index:1000;
}

</style>
</head>

<body>

<div id="flash"></div>

<div class="room">

<div class="logo">PETRO59</div>

<div id="clock"></div>

<div class="visualizer" id="visualizer"></div>

</div>

<audio id="audio" controls>
<source src="music.mp3" type="audio/mpeg">
</audio>

<script>

/* ORA */

function updateClock(){

const now=new Date();

const h=String(now.getHours()).padStart(2,"0");
const m=String(now.getMinutes()).padStart(2,"0");
const s=String(now.getSeconds()).padStart(2,"0");

document.getElementById("clock").innerHTML=
`${h}:${m}:${s}`;

}

setInterval(updateClock,1000);
updateClock();

/* SHIU */

for(let i=0;i<300;i++){

const rain=document.createElement("div");

rain.classList.add("rain");

rain.style.left=Math.random()*100+"vw";

rain.style.animationDuration=
(0.5+Math.random())+"s";

rain.style.opacity=Math.random();

document.body.appendChild(rain);

}

/* VISUALIZER */

const visualizer =
document.getElementById("visualizer");

for(let i=0;i<64;i++){

let bar=document.createElement("div");

bar.classList.add("bar");

visualizer.appendChild(bar);

}

const bars=document.querySelectorAll(".bar");

const audio =
document.getElementById("audio");

const flash =
document.getElementById("flash");

const audioCtx =
new AudioContext();

const source =
audioCtx.createMediaElementSource(audio);

const analyser =
audioCtx.createAnalyser();

analyser.fftSize=128;

source.connect(analyser);
analyser.connect(audioCtx.destination);

const dataArray =
new Uint8Array(analyser.frequencyBinCount);

audio.addEventListener("play",()=>{

audioCtx.resume();

animate();

});

function animate(){

requestAnimationFrame(animate);

analyser.getByteFrequencyData(dataArray);

let bass=0;

bars.forEach((bar,index)=>{

const value=dataArray[index];

bass+=value;

const height=(value/255)*120;

const hue=180+value;

bar.style.height=height+"px";

bar.style.background=
`hsl(${hue},100%,50%)`;

bar.style.boxShadow=
`0 0 15px hsl(${hue},100%,50%)`;

});

bass/=dataArray.length;

/* FLASH NE BEAT */

if(bass>120){

flash.style.opacity="0.5";

document.querySelector(".room")
.style.filter="brightness(1.5)";

}else{

flash.style.opacity="0";

document.querySelector(".room")
.style.filter="brightness(1)";

}

}

</script>
<img src="gam.jpg"
</body>
</html>
