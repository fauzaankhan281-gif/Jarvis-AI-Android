# Jarvis-AI-Android
My personal JARVIS AI assistant

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>JARVIS AI</title>
<style>
*{box-sizing:border-box}
body{
  margin:0;background:#06101d;color:#e7faff;
  font-family:Arial,sans-serif;text-align:center;
}
header{padding:22px 10px;color:#00e5ff}
h1{font-size:30px;letter-spacing:4px;margin:8px}
#orb{
  width:160px;height:160px;margin:25px auto;
  border:3px solid #00d9ff;border-radius:50%;
  box-shadow:0 0 25px #00bfff,inset 0 0 30px #007bff;
  display:flex;align-items:center;justify-content:center;
  font-size:48px;animation:pulse 2s infinite;
}
@keyframes pulse{
  50%{box-shadow:0 0 45px #00e5ff,inset 0 0 45px #007bff}
}
button,input{
  border:1px solid #00cfff;border-radius:12px;
  padding:13px;background:#10263a;color:white;
  font-size:16px;
}
button{cursor:pointer;margin:5px}
button:active{background:#006b8b}
#status{color:#8eeeff;min-height:25px}
#chat{
  margin:18px auto;padding:12px;width:92%;
  max-width:500px;height:260px;overflow:auto;
  border:1px solid #12617c;border-radius:14px;
  text-align:left;background:#091827;
}
.msg{padding:9px;margin:6px 0;border-radius:9px;
  overflow-wrap:anywhere}
.jarvis{background:#10334a}
.user{background:#164e43;text-align:right}
#entry{width:65%;max-width:340px}
.small{font-size:12px;color:#91aab9;padding:15px}
</style>
</head>
<body>
<header>
  <h1>J.A.R.V.I.S</h1>
  <div>PERSONAL AI ASSISTANT</div>
</header>

<div id="orb">◉</div>
<div id="status">SYSTEM READY</div>

<div>
  <button onclick="listen()">🎙️ Speak</button>
  <button onclick="stopListen()">⏹ Stop</button>
</div>
<br>
<input id="entry" placeholder="Type a command...">
<button onclick="send()">Send</button>

<div id="chat">
  <div class="msg jarvis">
    JARVIS: Hello! How can I help you?
  </div>
</div>

<div class="small">
  Internet search and voice features need a compatible
  browser and an internet connection.
</div>

<script>
const chat=document.getElementById("chat");
const statusBox=document.getElementById("status");
const entry=document.getElementById("entry");

function addMessage(text,who){
  const d=document.createElement("div");
  d.className="msg "+who;
  d.textContent=(who==="user"?"YOU: ":"JARVIS: ")+text;
  chat.appendChild(d);
  chat.scrollTop=chat.scrollHeight;
}

function speak(text){
  if(!("speechSynthesis" in window))return;
  speechSynthesis.cancel();
  const u=new SpeechSynthesisUtterance(text);
  u.lang="hi-IN";
  u.rate=1;
  speechSynthesis.speak(u);
}

function reply(text){
  addMessage(text,"jarvis");
  speak(text);
}

function searchWeb(query){
  reply("Searching the internet for "+query);
  window.open(
    "https://www.google.com/search?q="+encodeURIComponent(query),
    "_blank"
  );
}

function handleCommand(raw){
  const cmd=raw.toLowerCase().trim();
  addMessage(raw,"user");

  if(!cmd)return;

  if(cmd.includes("next reel") || cmd.includes("scroll")){
    reply("Phone scrolling needs Android accessibility permission. This web version cannot scroll reels directly.");
  }
  else if(cmd.includes("open youtube")){
    reply("Opening YouTube");
    window.open("https://m.youtube.com","_blank");
  }
  else if(cmd.includes("open instagram")){
    reply("Opening Instagram website");
    window.open("https://www.instagram.com","_blank");
  }
  else if(cmd.includes("time") || cmd.includes("samay")){
    reply("The time is "+new Date().toLocaleTimeString());
  }
  else if(cmd.includes("date") || cmd.includes("tarikh")){
    reply("Today is "+new Date().toLocaleDateString());
  }
  else if(cmd.includes("search") || cmd.includes("who is") ||
          cmd.includes("what is") || cmd.includes("kya hai") ||
          cmd.includes("kaun hai")){
    searchWeb(raw);
  }
  else if(cmd.includes("hello") || cmd.includes("salam") ||
          cmd.includes("namaste")){
    reply("Hello! I am JARVIS. How can I help you?");
  }
  else if(cmd.includes("stop")){
    stopListen();
    reply("Voice recognition stopped.");
  }
  else{
    reply("I can search the internet for that. Please wait.");
    searchWeb(raw);
  }
}

function send(){
  const text=entry.value.trim();
  if(!text)return;
  entry.value="";
  handleCommand(text);
}

entry.addEventListener("keydown",e=>{
  if(e.key==="Enter")send();
});

let recognition=null;

function listen(){
  const SR=window.SpeechRecognition ||
           window.webkitSpeechRecognition;
  if(!SR){
    reply("Speech recognition is not supported in this browser.");
    return;
  }

  recognition=new SR();
  recognition.lang="hi-IN";
  recognition.continuous=false;
  recognition.interimResults=false;

  recognition.onstart=()=>{
    statusBox.textContent="LISTENING...";
  };
  recognition.onresult=e=>{
    handleCommand(e.results[0][0].transcript);
  };
  recognition.onerror=e=>{
    statusBox.textContent="Voice error: "+e.error;
  };
  recognition.onend=()=>{
    statusBox.textContent="SYSTEM READY";
  };
  recognition.start();
}

function stopListen(){
  if(recognition)recognition.stop();
  if("speechSynthesis" in window)speechSynthesis.cancel();
  statusBox.textContent="SYSTEM READY";
}
</script>
</body>
</html>
