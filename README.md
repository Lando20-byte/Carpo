<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Carpo AI Chatbot</title>
<!-- Load Google Sign-In SDK -->
<script src="https://accounts.google.com/gsi/client" async defer></script>
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
  #g_id_onload, #g_id_signin {
    margin: 10px auto;
    display: none;
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
  #signInDiv {
    text-align: center;
    margin: 20px;
  }
</style>
</head>
<body>
<header>
  <h1>Welcome to Carpo AI Chatbot</h1>
</header>

<!-- Google Sign-In Button -->
<div id="signInDiv"></div>

<!-- Chat Container (hidden until signed in) -->
<div id="chat-container" style="display:none;">
  <div id="messages"></div>
  <div id="input-area">
    <input type="text" id="user-input" placeholder="Type your message..." />
    <button id="send-btn">Send</button>
  </div>
</div>

<script>
  let userEmail = '';

  // Initialize Google Sign-In
  window.onload = function() {
    google.accounts.id.initialize({
      client_id: 'YOUR_GOOGLE_CLIENT_ID', // Replace with your Google Client ID
      callback: handleCredentialResponse
    });
    google.accounts.id.renderButton(
      document.getElementById("signInDiv"),
      { theme: "outline", size: "large" } // customization attributes
    );
    google.accounts.id.prompt(); // Also display the One Tap prompt
  };

  function handleCredentialResponse(response) {
    // Decode JWT token to get user info
    const data = parseJwt(response.credential);
    userEmail = data.email;
    document.getElementById('signInDiv').style.display = 'none';
    document.getElementById('chat-container').style.display = 'flex';
  }

  function parseJwt(token) {
    const base64Url = token.split('.')[1];
    const base64 = base64Url.replace(/-/g, '+').replace(/_/g, '/');
    const jsonPayload = decodeURIComponent(atob(base64).split('').map(function(c) {
      return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2);
    }).join(''));
    return JSON.parse(jsonPayload);
  }

  const messagesDiv = document.getElementById('messages');
  const userInput = document.getElementById('user-input');
  const sendBtn = document.getElementById('send-btn');

  function appendMessage(text, sender) {
    const msgDiv = document.createElement('div');
    msgDiv.className = 'message ' + sender;
    msgDiv.innerText = text;
    messagesDiv.appendChild(msgDiv);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
  }

  async function sendMessage() {
    const message = userInput.value;
    if (!message) return;

    appendMessage(message, 'user');
    userInput.value = '';

    // Call your AI backend here
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
