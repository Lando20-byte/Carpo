<script>
  const ownerEmail = 'Landenshegstad@gmail.com';
  let userEmail = '';

  // Initialize Google Sign-In
  window.onload = function() {
    google.accounts.id.initialize({
      client_id: 'YOUR_GOOGLE_CLIENT_ID', // Replace with your Google Client ID
      callback: handleCredentialResponse
    });
    google.accounts.id.renderButton(
      document.getElementById("signInDiv"),
      { theme: "outline", size: "large" }
    );
    google.accounts.id.prompt();
  };

  function handleCredentialResponse(response) {
    const data = parseJwt(response.credential);
    userEmail = data.email;

    // Check if user is owner
    if (userEmail.toLowerCase() === ownerEmail.toLowerCase()) {
      alert('Welcome, Owner!');
      // You could also display owner-specific UI here
    }

    document.getElementById('signInDiv').style.display = 'none';
    document.getElementById('chat-container').style.display = 'flex';

    // Optionally, display owner badge somewhere on the page
    if (userEmail.toLowerCase() === ownerEmail.toLowerCase()) {
      const ownerBadge = document.createElement('div');
      ownerBadge.innerText = 'Owner Access Granted';
      ownerBadge.style.color = 'red';
      ownerBadge.style.fontWeight = 'bold';
      document.body.insertBefore(ownerBadge, document.body.firstChild);
    }
  }

  function parseJwt(token) {
    const base64Url = token.split('.')[1];
    const base64 = base64Url.replace(/-/g, '+').replace(/_/g, '/');
    const jsonPayload = decodeURIComponent(atob(base64).split('').map(function(c) {
      return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2);
    }).join(''));
    return JSON.parse(jsonPayload);
  }

  // Rest of your chat code...
</script> 

