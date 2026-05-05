<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Carpo AI Chatbot - Sign In</title>
<style>
  body {
    font-family: Arial, sans-serif;
    background: linear-gradient(to bottom, #a0e7e5, #ffffff);
    margin: 0;
    padding: 0;
  }
  header {
    background-color: #0077be;
    color: #fff;
    padding: 1em;
    text-align: center;
  }
  #signin-container {
    max-width: 400px;
    margin: 50px auto;
    background: #fff;
    padding: 30px;
    border-radius: 12px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.2);
    text-align: center;
  }
  #email-input {
    width: 80%;
    padding: 10px;
    font-size: 1em;
    margin-bottom: 15px;
    border-radius: 20px;
    border: 2px solid #ccc;
  }
  #signin-btn {
    padding: 10px 20px;
    font-size: 1em;
    border-radius: 20px;
    background-color: #0077be;
    color: #fff;
    border: none;
    cursor: pointer;
  }
  #signin-btn:hover {
    background-color: #005f8d;
  }

  #chat-container {
    display: none; /* Hidden until sign-in */
    max-width: 700px;
    margin: 20px auto;
    background: #fff;
    border-radius: 12px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.2);
    padding: 20px;
    height: 75vh;
    display: flex;
    flex-direction: column;
  }
  #messages {
    flex: 1;
    overflow-y: auto;
    padding: 10px;
  }
  .message {
    display: flex;
    align-items: flex-start;
    margin: 10px 0;
  }
  .fish {
    width: 40px;
    height: 40px;
    margin-right: 10px;
  }
  .fish img {
    width: 100%;
    height: auto;
  }
  .bubble {
    max-width: 70%;
    padding: 10px 15px;
    border-radius: 20px;
    position: relative;
    font-family: 'Comic Sans MS', cursive, sans-serif;
    font-size: 14px;
  }
  .user {
    justify-content: flex-end;
  }
  .user .bubble {
    background-color: #ffd700;
    border: 2px solid #e6b800;
  }
  .bot {
    justify-content: flex-start;
  }
  .bot .bubble {
    background-color: #91e6f2;
    border: 2px solid #6fc0d9;
  }
  #input-area {
    display: flex;
    margin-top: 10px;
  }
  #user-input {
    flex: 1;
    padding: 10px;
    font-size: 1em;
    border-radius: 20px;
    border: 2px solid #ccc;
  }
  #send-btn {
    padding: 10px 20px;
    margin-left: 10px;
    font-size: 1em;
    border-radius: 20px;
    background-color: #0077be;
    color: #fff;
    border: none;
    cursor: pointer;
  }
  #send-btn:hover {
    background-color: #005f8d;
  }
</style>
</head>
<body>
<header>
  <h1>Welcome to Carpo AI Chatbot</h1>
</header>

<!-- Sign In Container -->
<div id="signin-container">
  <h2>Sign In / Sign Up</h2>
  <input type="email" id="email-input" placeholder="Enter your email" />
  <br />
  <button id="signin-btn">Start Chatting</button>
</div>

<!-- Chat Container (hidden until sign-in) -->
<div id="chat-container">
  <div id="messages"></div>
  <div id="input-area">
    <input type="text" id="user-input" placeholder="Type your message..." />
    <button id="send-btn">Send</button>
  </div>
</div>

<script>
  let userEmail = '';

  const signinContainer = document.getElementById('signin-container');
  const chatContainer = document.getElementById('chat-container');

  document.getElementById('signin-btn').onclick = () => {
    const email = document.getElementById('email-input').value.trim();
    if (email) {
      userEmail = email;
      signinContainer.style.display = 'none';
      chatContainer.style.display = 'flex';

      // Optionally, display a welcome message with email
      appendMessage('Hi! You are signed in as ' + userEmail, 'bot');
    } else {
      alert('Please enter a valid email.');
    }
  };

  const messagesDiv = document.getElementById('messages');
  const userInput = document.getElementById('user-input');
  const sendBtn = document.getElementById('send-btn');

  function appendMessage(text, sender) {
    const messageDiv = document.createElement('div');
    messageDiv.className = 'message ' + sender;

    const fishIcon = document.createElement('div');
    fishIcon.className = 'fish';
    fishIcon.innerHTML = '<img src="https://i.imgur.com/4AiXzf8.png" alt="fish">';

    const bubble = document.createElement('div');
    bubble.className = 'bubble';
    bubble.innerText = text;

    messageDiv.appendChild(fishIcon);
    messageDiv.appendChild(bubble);
    messagesDiv.appendChild(messageDiv);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
  }

  async function sendMessage() {
    const message = userInput.value;
    if (!message) return;

    appendMessage(message, 'user');
    userInput.value = '';

    // Send to your backend API
    const response = await fetch('/api/chat', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ message, email: userEmail }),
    });
    const data = await response.json();
    appendMessage(data.reply, 'bot');
  }

  document.getElementById('send-btn').onclick = sendMessage;
  userInput.addEventListener('keydown', (e) => {
    if (e.key === 'Enter') sendMessage();
  });
</script>
</body>
</html>

