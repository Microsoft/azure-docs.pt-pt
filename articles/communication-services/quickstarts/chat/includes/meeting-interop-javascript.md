---
title: Quickstart - Junte-se a um encontro de equipas
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 773bca81694534346019e30e9d55190af6f51e74
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106803"
---
## <a name="joining-the-meeting-chat"></a>Juntando-se ao chat da reunião 

Uma vez ativada a interoperabilidade das Equipas, um utilizador dos Serviços de Comunicação pode juntar-se à chamada teams como utilizador externo utilizando o Call SDK. Juntando-se à chamada irá adicioná-los como participantes no chat de reunião também, onde podem enviar e receber mensagens com outros utilizadores na chamada. O utilizador não terá acesso a mensagens de chat que foram enviadas antes de se juntarem à chamada. Para se juntar à reunião e começar a conversar, pode seguir os próximos passos.

## <a name="install-the-chat-packages"></a>Instale os pacotes de chat

Utilize o `npm install` comando para instalar os SDKs de serviços de comunicação necessários para o JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

## <a name="add-the-teams-ui-controls"></a>Adicione os controlos de UI das equipas

Substitua o código em index.html com o seguinte corte.
As caixas de texto no topo da página serão usadas para entrar no contexto de reunião das Equipas e no iD do fio de encontro. O botão 'Join Teams Meeting' será utilizado para se juntar à reunião especificada.
Aparecerá um pop-up de chat na parte inferior da página. Pode ser utilizado para enviar mensagens na linha de reunião, e apresentará em tempo real quaisquer mensagens enviadas na linha enquanto o utilizador ACS é membro.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Ativar os controlos de UI das equipas

Substitua o conteúdo do ficheiro client.js pelo seguinte corte.

Dentro do corte, substitua 
- `SECRET CONNECTION STRING` com a cadeia de ligação do seu Serviço de Comunicação 
- `ENDPOINT URL` com o url de ponto final do seu Serviço de Comunicação

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Obtenha um fio de chat de reuniões de equipas para um utilizador de Serviços de Comunicação

O link de reunião e chat das Equipas pode ser recuperado usando APIs gráficos, detalhados na [documentação do Gráfico](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta). Os Serviços de Comunicação Que Ligam a SDK aceitam uma ligação completa de reuniões de equipas. Este link é devolvido como parte do `onlineMeeting` recurso, acessível sob a [ `joinWebUrl` propriedade](/graph/api/resources/onlinemeeting?view=graph-rest-beta) Com as [APIs do Gráfico,](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta)também pode obter o `threadId` . A resposta terá um `chatInfo` objeto que contém o `threadID` . 

Também pode obter as informações de reunião necessárias e o ID de linha da URL de **reunião** de reuniões de junta na reunião das equipas convidam-se a si próprios.
Um link de encontro de equipas é o seguinte: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . O `threadId` será onde está no `meeting_chat_thread_id` link. Certifique-se de que `meeting_chat_thread_id` o não é desajustado antes da utilização. Deve estar no seguinte formato: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Executar o código

Os utilizadores da Webpack podem usar `webpack-dev-server` a para construir e executar a sua aplicação. Executar o seguinte comando para agregar o seu anfitrião de aplicações num webserver local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o seu navegador e navegue para http://localhost:8080/ . Deverá ver o seguinte:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Screenshot da aplicação JavaScript concluída.":::

Insira o link de encontro das equipas e introduza o ID nas caixas de texto. Press *Join Teams Meeting* para se juntar ao encontro de equipas. Depois de o utilizador ACS ter sido admitido na reunião, pode conversar a partir da sua aplicação de Serviços de Comunicação. Navegue para a caixa na parte inferior da página para começar a conversar.

> [!NOTE] 
> Atualmente apenas o envio, receção e edição de mensagens é suportado para cenários de interoperabilidade com equipas. Outras funcionalidades, como indicadores de dactilografia e serviços de comunicação que adicionam ou retiram outros utilizadores da reunião das Equipas ainda não são suportadas.  
