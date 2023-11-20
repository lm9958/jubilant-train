
```html
<!DOCTYPE html>
<html>
<head>
  <title>私人聊天机器人</title>
  <style>
    .chatbox {
      width: 300px;
      height: 400px;
      border: 1px solid #ccc;
      padding: 10px;
      overflow-y: scroll;
    }
    .user-message {
      color: blue;
    }
    .bot-message {
      color: green;
    }
    input[type="text"] {
      width: 100%;
      padding: 5px;
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <div id="chat" class="chatbox"></div>
  <input id="user-input" type="text" placeholder="输入消息" autofocus="true" />
  
  <script>
    // 创建聊天框架
    function createChatFrame() {
      const chatbox = document.getElementById('chat');
      const userInput = document.getElementById('user-input');
      userInput.addEventListener('keydown', handleUserInput);

      function handleUserInput(event) {
        if (event.key === 'Enter') {
          const userMessage = userInput.value;
          addMessage(userMessage, 'user-message');
          processUserInput(userMessage);
          userInput.value = '';
        }
      }

      function processUserInput(userMessage) {
        // 发送用户输入到后端服务器
        sendUserMessageToBackend(userMessage)
          .then(botMessage => {
            addMessage(botMessage, 'bot-message');
          })
          .catch(error => {
            console.error('发送用户消息时出错：', error);
            addMessage('抱歉，发送消息时出现了错误', 'bot-message');
          });
      }

      function sendUserMessageToBackend(userMessage) {
        return new Promise((resolve, reject) => {
          const xhr = new XMLHttpRequest();
          const endpointUrl = 'YOUR_BACKEND_ENDPOINT_URL';  // 替换为你的后端服务器的URL

          xhr.open('POST', endpointUrl);
          xhr.setRequestHeader('Content-Type', 'application/json');

          xhr.onload = function() {
            if (xhr.status === 200) {
              const response = JSON.parse(xhr.responseText);
              resolve(response.botMessage);
            } else {
              reject('无法从后端服务器获取响应');
            }
          };

          xhr.onerror = function() {
            reject('请求发送失败');
          };

          const requestBody = JSON.stringify({ userMessage });
          xhr.send(requestBody);
        });
      }

      function addMessage(message, className) {
        const messageElement = document.createElement('div');
        messageElement.textContent = message;
        messageElement.classList.add(className);
        chatbox.appendChild(messageElement);
        chatbox.scrollTop = chatbox.scrollHeight;
      }
    }
    
    createChatFrame();
  </script>
</body>
</html>
```

