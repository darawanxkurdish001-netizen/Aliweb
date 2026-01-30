AI Web
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AI English Tutor (For Kurdish Sorani Speakers)</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f6f9;
      margin: 0;
      padding: 0;
    }

    header {
      background: #2563eb;
      color: white;
      padding: 20px;
      text-align: center;
    }

    main {
      max-width: 900px;
      margin: 20px auto;
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }

    .api-key-box {AIzaSyBeajRvb8hEChm7eS4QZvZJ1VpurO_Wbiw
      margin-bottom: 20px;
    }

    input[type="text"] {
      width: 100%;
      padding: 10px;
      margin-top: 5px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }

    button {
      background: #2563eb;
      color: white;
      border: none;
      padding: 10px 20px;
      border-radius: 5px;
      cursor: pointer;
      margin-top: 10px;
    }

    button:hover {
      background: #1d4ed8;
    }

    .chat-box {
      height: 400px;
      overflow-y: auto;
      border: 1px solid #ddd;
      padding: 10px;
      border-radius: 5px;
      margin-bottom: 15px;
      background: #fafafa;
    }

    .message {
      margin-bottom: 10px;
    }

    .ai {
      color: #1d4ed8;
      font-weight: bold;
    }

    .user {
      color: #047857;
      font-weight: bold;
    }

    .input-area {
      display: flex;
      gap: 10px;
    }

    .input-area input {
      flex: 1;
    }

    .role-box {
      margin-bottom: 15px;
    }

    select {
      width: 100%;
      padding: 10px;
      border-radius: 5px;
    }

    footer {
      text-align: center;
      margin-top: 20px;
      color: #555;
    }
  </style>
</head>
<body>

<header>
  <h1>🤖 AI English Tutor</h1>
  <p>For Kurdish Sorani Speakers (فێربوونی ئینگلیزی)</p>
</header>

<main>

  <!-- API KEY -->AIzaSyBeajRvb8hEChm7eS4QZvZJ1VpurO_Wbiw
  <div class="api-key-box">
    <label>Your Gemini API Key:</label>
    <input type="text" id="apiKey" placeholder="Paste your Gemini 2.5 Flash API Key here">
  </div>

  <!-- ROLE PLAY -->
  <div class="role-box">
    <label>Select Role Play Situation:</label>
    <select id="roleSelect">
      <option value="restaurant">🍽️ Restaurant</option>
      <option value="airport">✈️ Airport</option>
      <option value="job">💼 Job Interview</option>
      <option value="hotel">🏨 Hotel</option>
      <option value="shopping">🛒 Shopping</option>
      <option value="doctor">🏥 Doctor Visit</option>
    </select>
    <button onclick="startRolePlay()">Start Role Play</button>
  </div>

  <!-- CHAT -->
  <div class="chat-box" id="chatBox"></div>

  <!-- INPUT -->
  <div class="input-area">
    <input type="text" id="userInput" placeholder="Type your English answer here...">
    <button onclick="sendMessage()">Send</button>
  </div>

</main>

<footer>
  <p>Made for Kurdish Sorani Learners ❤️</p>
</footer>

<script>

const chatBox = document.getElementById("chatBox");
let conversation = [];

const systemPrompt = `You are an English tutor for Kurdish Sorani speakers.
Your job:
- Create role play situations
- Speak simple English
- Correct mistakes politely
- Explain errors briefly in Kurdish Sorani when needed
- Encourage the learner
- Ask questions in English
`;

function addMessage(sender, text) {
  const div = document.createElement("div");
  div.classList.add("message");

  if (sender === "AI") {
    div.innerHTML = `<span class='ai'>AI:</span> ${text}`;
  } else {
    div.innerHTML = `<span class='user'>You:</span> ${text}`;
  }

  chatBox.appendChild(div);
  chatBox.scrollTop = chatBox.scrollHeight;
}

function getScenario(role) {
  const scenarios = {
    restaurant: "You are in a restaurant. You want to order food.",
    airport: "You are at the airport. You are checking in.",
    job: "You are in a job interview.",
    hotel: "You are booking a hotel room.",
    shopping: "You are shopping for clothes.",
    doctor: "You are visiting a doctor."
  };

  return scenarios[role];
}

async function startRolePlay() {
  const apiKey = document.getElementById("apiKey").value.trim();
  const role = document.getElementById("roleSelect").value;

  if (!apiKey) {
    alert("Please enter your Gemini API Key first.");
    return;
  }

  chatBox.innerHTML = "";
  conversation = [];

  const scenario = getScenario(role);

  const prompt = `Start a role play.
Situation: ${scenario}
First, describe the situation.
Then ask the learner a simple English question.`;

  conversation.push({ role: "system", content: systemPrompt });
  conversation.push({ role: "user", content: prompt });

  addMessage("AI", "Loading...");

  const response = await callGemini(apiKey, conversation);

  chatBox.lastChild.remove();
  addMessage("AI", response);

  conversation.push({ role: "assistant", content: response });
}

async function sendMessage() {
  const apiKey = document.getElementById("apiKey").value.trim();
  const input = document.getElementById("userInput");
  const text = input.value.trim();

  if (!text) return;

  if (!apiKey) {
    alert("Please enter your Gemini API Key first.");
    return;
  }

  addMessage("You", text);
  conversation.push({ role: "user", content: text });

  input.value = "";

  addMessage("AI", "Thinking...");

  const response = await callGemini(apiKey, conversation);

  chatBox.lastChild.remove();
  addMessage("AI", response);

  conversation.push({ role: "assistant", content: response });
}

async function callGemini(apiKey, messages) {

  const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${apiKey}`;

  const contents = messages.map(m => ({
    role: m.role === "assistant" ? "model" : "user",
    parts: [{ text: m.content }]
  }));

  const body = {
    contents: contents,
    generationConfig: {
      temperature: 0.6,
      maxOutputTokens: 400
    }
  };

  try {
    const res = await fetch(url, {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify(body)
    });

    const data = await res.json();

    if (!data.candidates) {
      return "Error: Invalid API response.";
    }

    return data.candidates[0].content.parts[0].text;

  } catch (err) {
    console.error(err);
    return "Connection error. Please try again.";
  }
}

</script>

</body>
</html>
