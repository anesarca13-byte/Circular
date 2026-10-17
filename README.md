# Circular
Bot
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Chatbot Accesible</title>
  <style>
    /* === ESTILOS GENERALES === */
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      background-color: #f9f9f9;
    }

    /* === BURBUJA FLOTANTE === */
    .chat-bubble {
      position: fixed;
      bottom: 20px;
      right: 20px;
      width: 60px;
      height: 60px;
      border-radius: 50%;
      background: #1bea4c;
      color: #fff;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
      transition: background 0.3s;
      z-index: 1000;
    }

    .chat-bubble:hover {
      background: #0b3722; /* Contraste mayor al pasar el mouse */
    }

    /* === CONTENEDOR DEL CHATBOT (oculto inicialmente) === */
    .chat-container {
      position: fixed;
      bottom: 90px;
      right: 20px;
      width: 100%;
      max-width: 380px;
      height: 500px;
      background: #c78c7c;
      border-radius: 12px;
      box-shadow: 0 6px 16px rgba(0, 0, 0, 0.2);
      display: flex;
      flex-direction: column;
      opacity: 0;
      pointer-events: none;
      transform: translateY(20px);
      transition: opacity 0.3s ease, transform 0.3s ease;
      overflow: hidden;
    }

    /* Cuando está activo se muestra con animación */
    .chat-container.active {
      opacity: 1;
      pointer-events: auto;
      transform: translateY(0);
    }

    /* === CABECERA DEL CHATBOT === */
    .chat-header {
      background: #ee4f06;
      color: #ddd9c8;
      padding: 12px;
      text-align: center;
      font-size: 16px;
      font-weight: bold;
      position: relative;
    }

    /* Botón para cerrar el chat */
    .close-btn {
      position: absolute;
      right: 12px;
      top: 12px;
      background: transparent;
      border: none;
      color: #fff;
      font-size: 18px;
      cursor: pointer;
    }

    /* === ÁREA DE MENSAJES === */
    .messages {
      flex: 1;
      padding: 12px;
      overflow-y: auto;
      background: #f4f4f4;
    }

    .message {
      margin: 8px 0;
      padding: 10px;
      border-radius: 10px;
      max-width: 75%;
      font-size: 14px;
      line-height: 1.4;
    }

    .user {
      background-color: #adb881;
      align-self: flex-end;
      margin-left: auto;
    }

    .bot {
      background-color: #b2a33e;
      align-self: flex-start;
      margin-right: auto;
    }

    /* === ÁREA DE INPUT === */
    .input-area {
      display: flex;
      border-top: 1px solid #ccb2b2;
      background: #fff;
    }

    .input-area input {
      flex: 1;
      padding: 12px;
      border: none;
      outline: none;
      font-size: 14px;
    }

    .input-area button {
      padding: 12px 16px;
      background: #cf400c;
      color: white;
      border: none;
      cursor: pointer;
      transition: background 0.3s;
    }

    .input-area button:hover {
      background: #783937;
    }

    /* === ACCESIBILIDAD Y RESPONSIVIDAD === */
    @media (max-width: 480px) {
      .chat-container {
        right: 10px;
        left: 10px;
        height: 70vh; /* altura dinámica para móviles */
      }
    }
  </style>
</head>
<body>
  <!-- BURBUJA FLOTANTE -->
  <button class="chat-bubble" id="chatToggle" aria-label="Abrir chat con el bot">💬</button>

  <!-- CONTENEDOR PRINCIPAL DEL CHAT -->
  <div class="chat-container" id="chatContainer" role="dialog" aria-modal="true" aria-labelledby="chatTitle">
    <!-- CABECERA -->
    <div class="chat-header">
      <span id="chatTitle">Chatbot Educativo</span>
      <button class="close-btn" id="closeChat" aria-label="Cerrar chat">✖</button>
    </div>

    <!-- ÁREA DE MENSAJES -->
    <div class="messages" id="messages" role="log" aria-live="polite"></div>

    <!-- ÁREA DE INPUT -->
    <div class="input-area">
      <input type="text" id="userInput" aria-label="Escribe tu mensaje" placeholder="Escribe tu pregunta..." />
      <button id="sendBtn">Enviar</button>
    </div>
  </div>

  <script>
    // === REFERENCIAS A ELEMENTOS DEL DOM ===
    const chatToggle = document.getElementById("chatToggle");
    const chatContainer = document.getElementById("chatContainer");
    const closeChat = document.getElementById("closeChat");
    const messages = document.getElementById("messages");
    const userInput = document.getElementById("userInput");
    const sendBtn = document.getElementById("sendBtn");

    /* === FUNCIÓN: agregar mensaje al área === */
    function addMessage(text, sender) {
      const div = document.createElement("div");
      div.classList.add("message", sender);
      div.textContent = text;
      messages.appendChild(div);
      messages.scrollTop = messages.scrollHeight; // siempre baja al último mensaje
    }

    /* === RESPUESTAS SIMPLES DEL BOT === */
    function botResponse(input) {
      const pregunta = input.toLowerCase();
      if (pregunta.includes("hola")) {
        return "Buscando... Por favor si encuentras o no lo que necesitas, te recomiento usar las siguientes opciones:  Pintor, Pegador, Albañil, Terminador; y al terminar digita, solicitud y despues salir. :)";
      } else if  (pregunta.includes("Pintor")) {
        return "Tenemos lo que buscas: Santiago esta disponible mañana por 16 Dolares el dia.";
      } else if  (pregunta.includes("Pegador")) {
        return "Tenemos lo que buscas: Duvan esta disponible mañana por 18 Dolares el dia.";
      } else if  (pregunta.includes("Albañil")) {
        return "Tenemos lo que buscas: Rogger esta disponible mañana por 17 Dolares el dia.";
      } else if  (pregunta.includes("Terminador")) {
        return "Tenemos lo que buscas: Rogger esta disponible mañana por 21 Dolares el dia.";
      } else if  (pregunta.includes("salir")) {
        return "Gracias por conversar conmigo. ¡Éxitos en tu proyecto! 👋";
      } else {
        return "Lo siento, aún no tengo respuesta para esa pregunta.";
      }
    }

    /* === FUNCIÓN: enviar mensaje del usuario === */
    function sendMessage() {
      const text = userInput.value.trim();
      if (!text) return;

      addMessage(text, "user"); // añade mensaje de usuario
      userInput.value = ""; // limpia input

      // pequeña pausa para simular procesamiento
      const respuesta = botResponse(text);
      setTimeout(() => addMessage(respuesta, "bot"), 400);
    }

    /* === EVENTOS === */

    // Abrir / cerrar chatbot al hacer clic en la burbuja
    chatToggle.addEventListener("click", () => {
      chatContainer.classList.add("active");
      userInput.focus();
    });

    // Cerrar chatbot y limpiar historial de mensajes
    closeChat.addEventListener("click", () => {
      chatContainer.classList.remove("active");
      messages.innerHTML = ""; // limpia mensajes automáticamente
    });

    // Botón enviar
    sendBtn.addEventListener("click", sendMessage);

    // Enviar con tecla Enter
    userInput.addEventListener("keypress", (e) => {
      if (e.key === "Enter") sendMessage();
    });
    /*************************************************************************
     * Inicialización: añadir mensaje de bienvenida (opcional)
     *************************************************************************/
    (function init() {
      // Mensaje inicial del bot para orientar al aprendiz
      addMessage('Hola — soy el asistente del proyecto. Puedes preguntar por "Pintor", "Albañil", "Pegador" o "Terminador".', 'bot');
    })();
  </script>
</body>
</html>
