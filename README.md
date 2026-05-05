# Carpo
Ai website
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Carpo AI Chatbot</title>
<style>
  body {
    font-family: Arial, sans-serif;
    background-color: #f0f0f0;
    margin: 0;
    padding: 0;
  }
  header {
    background-color: #333;
    color: #fff;
    padding: 1em;
    text-align: center;
  }
  #chat-container {
    max-width: 600px;
    margin: 20px auto;
    background: #fff;
    border-radius: 8px;
    padding: 20px;
    display: flex;
    flex-direction: column;
    height: 70vh;
  }
  #messages {
    flex: 1;
    overflow-y: auto;
    margin-bottom: 10px;
  }
  .message {
    margin: 10px 0;
  }
  .user {
    text-align: right;
    color: blue;
  }
  .bot {
    text-align: left;
    color: green;
  }
  #input-area {
    display: flex;
  }
  #user-input {
    flex: 1;
    padding: 10px;
    font-size: 1em;
  }
  #send-btn {
    padding: 10px 20px;
    font-size: 1em;
    background-color: #333;
    color: #fff;
    border: none;
    cursor: pointer;
  }
  #send-btn:hover {
    background-color: #555;
  }
</style>
</head>
<body>
<header>
  <h1>Welcome to Carpo AI Chatbot</h1>
</header>

<div id="chat-container">
  <div id="messages"></div>
  <div id="input-area">
    <input type="text" id="user-input" placeholder="Type your message..." />
    <button id="send-btn">Send</button>
  </div>
</div>

<script>
  const messagesDiv = document.getElementById('messages');
  const userInput = document.getElementById('user-input');
  const sendBtn = document.getElementById('send-btn');

  // Function to append message
  function appendMessage(text, sender) {
    const msgDiv = document.createElement('div');
    msgDiv.className = 'message ' + sender;
    msgDiv.innerText = text;
    messagesDiv.appendChild(msgDiv);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
  }

  // Function to handle sending message
  async function sendMessage() {
    const message = userInput.value;
    if (!message) return;

    appendMessage(message, 'user');
    userInput.value = '';

    // Call your AI backend here
    const response = await fetch('/api/chat', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ message }),
    });
    const data = await response.json();
    appendMessage(data.reply, 'bot');
  }

  sendBtn.onclick = sendMessage;
  userInput.addEventListener('keydown', (e) => {
    if (e.key === 'Enter') sendMessage();
  });
</script>
</body>
</html>
