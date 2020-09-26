---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376256"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de:

- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Instale [Node.js](https://nodejs.org/en/download/) versões LTS e de manutenção ativas (8.11.1 e 10.14.1 recomendado).
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../create-communication-resource.md). Terá de gravar o seu **ponto final** de recurso para este arranque rápido.
- Um [token de acesso ao utilizador](../../access-tokens.md). Certifique-se de definir o âmbito para "chat", e note a cadeia simbólica, bem como a cadeia userId.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-web-application"></a>Criar uma nova aplicação web

Primeiro, abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
Corra `npm init -y` para criar umapackage.js** no** ficheiro com definições predefinidos.

```console
npm init -y
```

Use um editor de texto para criar um ficheiro chamado **start-chat.js** no diretório de raiz do projeto. Irá adicionar todo o código fonte para este arranque rápido a este ficheiro nas seguintes secções.

### <a name="install-the-packages"></a>Instalar as embalagens

Utilize o `npm install` comando para instalar as bibliotecas de clientes abaixo dos Serviços de Comunicação para o JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Este quickstart usa webpack para agregar os ativos da aplicação. Executar o seguinte comando para instalar os pacotes npm webpack, webpack-cli e webpack-dev-server e listá-los como dependências de desenvolvimento no seu **package.jsem**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Crie um ** ficheiroindex.html** no diretório de raiz do seu projeto. Usaremos este ficheiro como modelo para adicionar capacidade de chat utilizando a biblioteca de clientes Azure Communication Chat para o JavaScript.

Apresentamos o código a seguir:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
Crie um ficheiro no diretório de raiz do seu projeto chamado **client.js** para conter a lógica de aplicação para este arranque rápido. 

### <a name="create-a-chat-client"></a>Criar um cliente de chat

Para criar um cliente de chat na sua aplicação web, utilizará o ponto final do Serviço de Comunicações e o token de acesso que foi gerado como parte das etapas pré-necessárias. Os tokens de acesso ao utilizador permitem-lhe construir aplicações de clientes que autenticam diretamente os Serviços de Comunicação Azure. Assim que gerar estas fichas no seu servidor, passe-as de volta para um dispositivo cliente. Tens de usar a `AzureCommunicationUserCredential` aula do para passar o símbolo ao teu cliente de `Common client library` chat.

Crie um ficheiro **client.js** no diretório de raiz do seu projeto. Usaremos este ficheiro para adicionar capacidade de chat utilizando a biblioteca de clientes Azure Communication Chat para o JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
Substitua **endpoint** por aquele criado antes com base na [documentação de Recurso de comunicação Azure.](../../create-communication-resource.md)
Substitua **USER_ACCESS_TOKEN** por um token emitido com base na documentação do [Token do Acesso ao Utilizador.](../../access-tokens.md)
Adicione este código ao **client.js** ficheiro


### <a name="run-the-code"></a>Executar o código
Use o `webpack-dev-server` para construir e executar a sua aplicação. Executar o seguinte comando para agregar anfitrião de aplicações em um webserver local:
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Abra o seu navegador e navegue para http://localhost:8080/ .
Na consola de ferramentas de desenvolvimento dentro do seu navegador deve ver o seguinte:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Modelo de objeto 
As seguintes classes e interfaces lidam com algumas das principais funcionalidades da biblioteca de clientes Azure Communication Services Chat para o JavaScript.

| Nome                                   | Descrição                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Esta classe é necessária para a funcionalidade Chat. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatThreadClient | Esta classe é necessária para a funcionalidade Chat Thread. Obtém um caso através do ChatClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/receber utilizadores, enviar notificações de dactilografia e ler recibos, subscrever eventos de chat. |


## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Utilize o `createThread` método para criar um fio de chat.

`createThreadRequest` é utilizado para descrever o pedido de linha:

- Use `topic` para dar um tópico a este chat; O tópico pode ser atualizado após a criação do fio de chat utilizando a `UpdateThread` função. 
- Utilizar `members` para listar os membros a adicionar ao fio de conversação;

Quando resolvido, `createChatThread` o método retorna `threadId` o que é usado para executar operações no fio de chat recém-criado, como adicionar membros ao fio de chat, enviar mensagens, eliminar mensagens, etc.

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

Substitua **USER_ID_FOR_JACK** e **USER_ID_FOR_GEETA** pelos ids do utilizador obtidos a partir do passo anterior (Criar utilizadores e emitir [Tokens de Acesso ao Utilizador)](../../access-tokens.md)

Ao atualizar o separador do navegador deve ver o seguinte na consola
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat

O `getChatThreadClient` método devolve um para um fio que já `chatThreadClient` existe. Pode ser utilizado para a realização de operações no fio criado: adicionar membros, enviar mensagem, etc. threadId é o ID único do fio de chat existente.

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
Adicione este código no lugar do `<CREATE CHAT THREAD CLIENT>` comentário em **client.js, **refresque o separador do navegador e verifique a consola, deve ver:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Utilize `sendMessage` o método para enviar uma mensagem de chat para o fio que acabou de criar, identificado pelo threadId.

`sendMessageRequest` descreve os campos necessários do pedido de mensagem de chat:

- Utilizar `content` para fornecer o conteúdo da mensagem de chat;

`sendMessageOptions` descreve os campos opcionais do pedido de mensagem de chat:

- Utilize `priority` para especificar o nível prioritário da mensagem de chat, como 'Normal' ou 'High'; esta propriedade pode ser usada para ter indicador de UI para o utilizador recetor na sua app para chamar a atenção para a mensagem ou executar lógica de negócio personalizada.   
- Utilizar `senderDisplayName` para especificar o nome de visualização do remetente;

A resposta `sendChatMessageResult` contém um "id", que é o ID único dessa mensagem.

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
Adicione este código no lugar do `<SEND MESSAGE TO A CHAT THREAD>` comentário em **client.js, **refresque o separador do navegador e verifique a consola.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receba mensagens de chat de um fio de chat

Com a sinalização em tempo real, pode subscrever para ouvir novas mensagens recebidas e atualizar as mensagens atuais na memória em conformidade. A Azure Communication Services suporta uma [lista de eventos que pode subscrever.](../../../concepts/chat/concepts.md#real-time-signaling)

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Adicione este código em vez de `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` comentarclient.js. ** **
Refresque o separador do seu navegador, deve ver na consola uma `Notification chatMessageReceived` mensagem;

Alternativamente, pode obter mensagens de chat sondando o `listMessages` método em intervalos especificados. 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
Adicione este código no lugar do `<LIST MESSAGES IN A CHAT THREAD>` comentário em **client.js**.
Refresque o separador, na consola deverá encontrar uma lista de mensagens enviadas neste fio de chat.


`listMessages` retorna a versão mais recente da mensagem, incluindo quaisquer edições ou eliminações que aconteceram com a mensagem usando `updateMessage` e `deleteMessage` .
Para mensagens `chatMessage.deletedOn` eliminadas, retorna um valor de hora de data indicando quando essa mensagem foi eliminada. Para mensagens editadas, `chatMessage.editedOn` retorna uma data indicando quando a mensagem foi editada. O tempo original da criação de mensagens pode ser acedido usando `chatMessage.createdOn` o que pode ser usado para encomendar as mensagens.

`listMessages` retorna diferentes tipos de mensagens que podem ser identificadas por `chatMessage.type` . Estes tipos são:

- `Text`: Mensagem de chat regular enviada por um membro do thread.

- `ThreadActivity/TopicUpdate`: Mensagem do sistema que indica que o tópico foi atualizado.

- `ThreadActivity/AddMember`: Mensagem do sistema que indica que um ou mais membros foram adicionados ao fio de conversação.

- `ThreadActivity/RemoveMember`: Mensagem do sistema que indica que um membro foi removido do fio de conversação.

Para mais detalhes, consulte [os Tipos de Mensagens](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Adicione um utilizador como membro ao fio de chat

Uma vez criado um fio de chat, pode adicionar e remover os utilizadores do mesmo. Ao adicionar os utilizadores, dá-lhes acesso para enviar mensagens para o fio de chat e adicionar/remover outros membros. Antes de ligar `addMembers` para o método, certifique-se de que adquiriu um novo token de acesso e identidade para esse utilizador. O utilizador necessitará desse token de acesso para inicializar o seu cliente de chat.

`addMembersRequest` Descreve o objeto de pedido em que `members` lista os membros a adicionar ao fio de chat;
- `user`, necessário, é necessário que o utilizador de comunicação seja adicionado ao fio de chat.
- `displayName`, opcional, é o nome de visualização do membro thread.
- `shareHistoryTime`, opcional, é o momento a partir do qual a história do chat é partilhada com o membro. Para partilhar a história desde o início do fio de chat, coloque esta propriedade em qualquer data igual ou inferior ao tempo de criação de fios. Para não partilhar nenhuma história anterior à data em que o membro foi adicionado, desemafete-a para a data atual. Para partilhar a história parcial, desemafete-a à data da sua escolha.

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
Substitua **NEW_MEMBER_USER_ID** por um [novo ID de utilizador](../../access-tokens.md) Adicione este código no lugar do comentário em `<ADD NEW MEMBER TO THREAD>` **client.js**

## <a name="list-users-in-a-chat-thread"></a>Listar utilizadores em um fio de chat
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
Adicione este código no lugar do `<LIST MEMBERS IN A THREAD>` comentário em **client.js, **atualize o separador do navegador e verifique a consola, deve ver informações sobre os utilizadores numa linha.

## <a name="remove-user-from-a-chat-thread"></a>Remova o utilizador de um fio de chat

Semelhante à adição de um membro, pode remover os membros de um fio de chat. Para ser removido, terá de rastrear as identificações dos membros que adicionou.

Utilize `removeMember` o método onde o utilizador de `member` comunicação deve ser removido da linha.

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
Substitua **MEMBER_ID** por um ID do utilizador utilizado no passo anterior (<NEW_MEMBER_USER_ID>).
Adicione este código no lugar do `<REMOVE MEMBER FROM THREAD>` comentário em **client.js, **
