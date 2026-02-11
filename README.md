# mi-aitana
mi univers
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Universo Luna Aitana</title>
<style>
body{margin:0;overflow:hidden;background:black;font-family:Arial;}
#inicio{
position:fixed;
width:100%;
height:100%;
display:flex;
flex-direction:column;
justify-content:center;
align-items:center;
background:black;
color:white;
z-index:10;
text-align:center;
}
button{
padding:15px 25px;
font-size:18px;
border:none;
border-radius:30px;
background:#ff4da6;
color:white;
}
#fade{
position:fixed;
width:100%;
height:100%;
background:black;
pointer-events:none;
opacity:1;
transition:opacity 3s ease;
z-index:9;
}
</style>
</head>
<body>

<div id="inicio">
<h2>¿Quieres conocer mi universo?</h2>
<button id="abrirBtn">Tocar para abrir</button>
</div>

<div id="fade"></div>
<audio id="musica" src="musica.mp3"></audio>

<script src="https://cdn.jsdelivr.net/npm/three@0.158.0/build/three.min.js"></script>

<script>

let scene, camera, renderer;
let stars, planets=[];
let clock = new THREE.Clock();

document.getElementById("abrirBtn").onclick = async function(){

document.getElementById("inicio").style.display="none";

/* música */
let musica = document.getElementById("musica");
musica.volume = 0;
await musica.play().catch(()=>{});

/* fade in música */
let fadeMusic = setInterval(()=>{
if(musica.volume < 1){
musica.volume += 0.02;
}else{
clearInterval(fadeMusic);
}
},100);

init();
cinematica();

};

function init(){

scene = new THREE.Scene();

camera = new THREE.PerspectiveCamera(
75,
window.innerWidth/window.innerHeight,
0.1,
2000
);

camera.position.z = 200;

renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.body.appendChild(renderer.domElement);

/* ESTRELLAS PROFUNDAS */
const starGeo = new THREE.BufferGeometry();
const starCount = 6000;
let positions = [];

for(let i=0;i<starCount;i++){
positions.push(
(Math.random()-0.5)*2000,
(Math.random()-0.5)*2000,
(Math.random()-0.5)*2000
);
}

starGeo.setAttribute(
'position',
new THREE.Float32BufferAttribute(positions,3)
);

const starMat = new THREE.PointsMaterial({
color:0xffffff,
size:1
});

stars = new THREE.Points(starGeo,starMat);
scene.add(stars);

/* SOL CENTRAL */
const sunGeo = new THREE.SphereGeometry(10,64,64);
const sunMat = new THREE.MeshBasicMaterial({color:0xff66cc});
const sun = new THREE.Mesh(sunGeo,sunMat);
scene.add(sun);

/* FOTOS PLANETAS */
const loader = new THREE.TextureLoader();

const fotos=[
"foto1.jpg","foto2.jpg","foto3.jpg",
"foto4.jpg","foto5.jpg","foto6.jpg",
"foto7.jpg","foto8.jpg","foto9.jpg"
];

fotos.forEach((src,i)=>{

const texture = loader.load(src);
const geo = new THREE.SphereGeometry(4,32,32);
const mat = new THREE.MeshBasicMaterial({map:texture});
const planet = new THREE.Mesh(geo,mat);

planet.userData={
angle:Math.random()*Math.PI*2,
distance:30 + i*12,
speed:0.3 + i*0.05
};

planets.push(planet);
scene.add(planet);

});

window.addEventListener("resize",()=>{
camera.aspect = window.innerWidth/window.innerHeight;
camera.updateProjectionMatrix();
renderer.setSize(window.innerWidth,window.innerHeight);
});

}

/* CINEMÁTICA DE VIAJE */
function cinematica(){

let fade = document.getElementById("fade");
setTimeout(()=>{fade.style.opacity=0;},100);

animate();

}

function animate(){
requestAnimationFrame(animate);

let delta = clock.getDelta();

/* estrellas girando */
stars.rotation.y += 0.0008;

/* órbitas */
planets.forEach(p=>{
p.userData.angle += p.userData.speed * delta;
p.position.x = Math.cos(p.userData.angle)*p.userData.distance;
p.position.z = Math.sin(p.userData.angle)*p.userData.distance;
});

/* movimiento cámara cinematográfico */
camera.position.z -= 0.3;
if(camera.position.z < 80){
camera.position.z = 80 + Math.sin(Date.now()*0.0005)*5;
}

camera.position.x = Math.sin(Date.now()*0.0003)*10;
camera.lookAt(0,0,0);

renderer.render(scene,camera);
}

</script>

</body>
</html>
