# pyszneizdrowe.io
<!doctype html>
<html lang="ru">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Insta Slides — PyszneIZdrowe</title>
<link href="https://fonts.googleapis.com/css2?family=Lora:wght@400;700&family=Montserrat:wght@400;700&display=swap" rel="stylesheet">
<style>
body {margin:0; font-family:Montserrat,Arial,sans-serif; background:#07192a; color:#fff; display:flex; flex-direction:column; align-items:center; padding:20px;}
h1 {text-align:center; margin-bottom:20px;}
textarea {width:90%; height:100px; margin-bottom:10px; font-size:16px; padding:6px; border-radius:6px;}
button {padding:8px 12px; border:none; border-radius:6px; background:#f59e0b; color:#071022; cursor:pointer; margin-bottom:10px;}
#slidesContainer {display:flex; flex-wrap:wrap; justify-content:center;}
.slide {width:300px; height:400px; background:#0f1724; margin:10px; display:flex; align-items:center; justify-content:center; text-align:center; padding:12px; border-radius:12px; box-shadow:0 4px 12px rgba(0,0,0,0.6); position:relative; font-family:Lora,serif; font-size:20px; line-height:1.3;}
.downloadBtn {position:absolute; bottom:10px; right:10px; padding:4px 6px; background:#f59e0b; color:#071022; font-size:12px; border:none; border-radius:4px; cursor:pointer;}
</style>
</head>
<body>

<h1>Insta Slides — Pyszne i Zdrowe</h1>

<textarea id="inputText" placeholder="Напишите текст для слайдов…"></textarea>
<input type="file" id="bgImage" accept="image/*">
<button id="generate">Создать слайды</button>

<div id="slidesContainer"></div>

<script>
const inputText = document.getElementById('inputText');
const generateBtn = document.getElementById('generate');
const slidesContainer = document.getElementById('slidesContainer');
const bgImageInput = document.getElementById('bgImage');
let bgImageURL = null;

bgImageInput.addEventListener('change', e => {
  const file = e.target.files[0];
  if(file){
    bgImageURL = URL.createObjectURL(file);
  }
});

function downloadSlide(div, index){
  const canvas = document.createElement('canvas');
  canvas.width = 1080;
  canvas.height = 1350;
  const ctx = canvas.getContext('2d');

  // фон
  if(bgImageURL){
    const img = new Image();
    img.onload = ()=> {
      ctx.drawImage(img,0,0,canvas.width,canvas.height);
      drawText();
    };
    img.src = bgImageURL;
  } else {
    ctx.fillStyle = '#0f1724';
    ctx.fillRect(0,0,canvas.width,canvas.height);
    drawText();
  }

  function drawText(){
    ctx.fillStyle = '#fff';
    ctx.font = '48px Lora';
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    const text = div.dataset.text.split(' ');
    let line = '';
    const lines = [];
    text.forEach((word,i)=>{
      const testLine = line + word + ' ';
      const metrics = ctx.measureText(testLine);
      if(metrics.width > canvas.width - 100){
        lines.push(line);
        line = word + ' ';
      } else {
        line = testLine;
      }
      if(i === text.length - 1) lines.push(line);
    });
    lines.forEach((l,i)=>{
      ctx.fillText(l, canvas.width/2, canvas.height/2 + i*50 - (lines.length-1)*25);
    });

    // скачать
    const link = document.createElement('a');
    link.download = 'slide-' + (index+1) + '.png';
    link.href = canvas.toDataURL('image/png');
    link.click();
  }
}

function renderSlides(){
  const text = inputText.value.trim();
  if(!text) return alert('Введите текст!');
  slidesContainer.innerHTML = '';
  const slides = text.split(/\n\n/);
  slides.forEach((txt,i)=>{
    const div = document.createElement('div');
    div.className = 'slide';
    div.dataset.text = txt;
    div.innerText = txt;

    const btn = document.createElement('button');
    btn.className = 'downloadBtn';
    btn.innerText = 'Скачать';
    btn.addEventListener('click', ()=> downloadSlide(div,i));
    div.appendChild(btn);

    slidesContainer.appendChild(div);
  });
}

generateBtn.addEventListener('click', renderSlides);
</script>

</body>
</html>
