---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 24f64e19077488223e13d01e110b5b5118231673
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603344"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de:

- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale [Node.js](https://nodejs.org/en/download/) versões LTS e de manutenção ativas (8.11.1 e 10.14.1 recomendado).
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../create-communication-resource.md). Terá de **gravar o seu ponto final de recurso** para este arranque rápido.
- Crie *três* Utilizadores ACS e emita-lhes um token de acesso ao [utilizador Token](../../access-tokens.md). Certifique-se de definir o âmbito para **o chat**, e note a **cadeia simbólica, bem como a cadeia userId**. A demonstração completa cria um fio com dois participantes iniciais e, em seguida, adiciona um terceiro participante ao fio.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-web-application"></a>Criar uma nova aplicação web

Primeiro, abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Corra `npm init -y` para criar umapackage.js **no** ficheiro com definições predefinidos.

```console
npm init -y
```

### <a name="install-the-packages"></a>Instalar as embalagens

Utilize o `npm install` comando para instalar as bibliotecas de clientes abaixo dos Serviços de Comunicação para o JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Este quickstart usa webpack para agregar os ativos da aplicação. Executar o seguinte comando para instalar os pacotes npm webpack, webpack-cli e webpack-dev-server e listá-los como dependências de desenvolvimento no seu **package.jsem**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Crie um **ficheiroindex.html** no diretório de raiz do seu projeto. Usaremos este ficheiro como modelo para adicionar capacidade de chat utilizando a biblioteca de clientes Azure Communication Chat para o JavaScript.

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

Para criar um cliente de chat na sua aplicação web, utilizará o **ponto final** do Serviço de Comunicações e o **token** de acesso que foi gerado como parte dos passos pré-requisitos.

Os tokens de acesso ao utilizador permitem-lhe construir aplicações de clientes que autenticam diretamente os Serviços de Comunicação Azure. Este quickstart não cobre a criação de um nível de serviço para gerir fichas para a sua aplicação de chat. Consulte [os conceitos de chat](../../../concepts/chat/concepts.md) para obter mais informações sobre a arquitetura de chat e os [tokens de acesso ao utilizador](../../access-tokens.md) para obter mais informações sobre os tokens de acesso.

No interior **client.js** utilizar o ponto final e o token de acesso no código abaixo para adicionar a capacidade de chat utilizando a biblioteca de clientes Azure Communication Chat para o JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Substitua **o ponto finalUral** pelo ponto final do recurso Serviços de Comunicação, consulte [Criar um Recurso de Comunicação Azure](../../create-communication-resource.md) se ainda não o tiver feito.
- Substitua **o utilizadorAccessToken** pelo token que emitiu.


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

- Use `topic` para dar um tópico a esta conversa. Os tópicos podem ser atualizados após a criação do fio de chat utilizando a `UpdateThread` função.
- Utilize `participants` para listar os participantes a adicionar ao fio de chat.

Quando resolvido, `createChatThread` o método devolve a `CreateChatThreadResult` . Este modelo contém uma `chatThread` propriedade onde pode aceder ao fio `id` recém-criado. Em seguida, pode usar `id` o para obter um exemplo de um `ChatThreadClient` . `ChatThreadClient`Em seguida, pode ser usado para executar o funcionamento dentro do fio, como enviar mensagens ou listar participantes.

```JavaScript
async function createChatThread() {
    let createThreadRequest = {
        topic: 'Preparation for London conference',
        participants: [{
                    id: { communicationUserId: '<USER_ID_FOR_JACK>' },
                    displayName: 'Jack'
                }, {
                    id: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                    displayName: 'Geeta'
                }]
    };
    let createChatThreadResult = await chatClient.createChatThread(createThreadRequest);
    let threadId = createChatThreadResult.chatThread.id;
    return threadId;
    }

createChatThread().then(async threadId => {
    console.log(`Thread created:${threadId}`);
    // PLACEHOLDERS
    // <CREATE CHAT THREAD CLIENT>
    // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
    // <SEND MESSAGE TO A CHAT THREAD>
    // <LIST MESSAGES IN A CHAT THREAD>
    // <ADD NEW PARTICIPANT TO THREAD>
    // <LIST PARTICIPANTS IN A THREAD>
    // <REMOVE PARTICIPANT FROM THREAD>
    });
```

Substitua **USER_ID_FOR_JACK** e **USER_ID_FOR_GEETA** pelos IDs do utilizador obtidos com a criação de utilizadores e fichas [(Tokens de acesso ao utilizador)](../../access-tokens.md)

Ao atualizar o separador do navegador deverá ver o seguinte na consola:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat

O `getChatThreadClient` método devolve um para um fio que já `chatThreadClient` existe. Pode ser usado para realizar operações no fio criado: adicionar participantes, enviar mensagem, etc. threadId é o ID único do fio de chat existente.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Adicione este código no lugar do `<CREATE CHAT THREAD CLIENT>` comentário em **client.js,** refresque o separador do navegador e verifique a consola, deve ver:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Utilize `sendMessage` o método para enviar uma mensagem para um fio identificado pelo threadId.

`sendMessageRequest` é usado para descrever o pedido de mensagem:

- Utilizar `content` para fornecer o conteúdo da mensagem de chat;

`sendMessageOptions` é utilizado para descrever os params opcionais da operação:

- Utilizar `senderDisplayName` para especificar o nome de visualização do remetente;
- Utilizar `type` para especificar o tipo de mensagem, como 'texto' ou 'html';

`SendChatMessageResult` é a resposta devolvida do envio de uma mensagem, contém um ID, que é o ID único da mensagem.

```JavaScript
let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    senderDisplayName : 'Jack',
    type: 'text'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
```

Adicione este código no lugar do `<SEND MESSAGE TO A CHAT THREAD>` comentário em **client.js,** refresque o separador do navegador e verifique a consola.
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
Adicione este código em vez de `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` comentarclient.js. ****
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
Refresque o separador, na consola deverá encontrar a lista de mensagens enviadas neste fio de chat.


`listMessages` retorna a versão mais recente da mensagem, incluindo quaisquer edições ou eliminações que aconteceram com a mensagem usando `updateMessage` e `deleteMessage` .
Para mensagens `chatMessage.deletedOn` eliminadas, retorna um valor de hora de data indicando quando essa mensagem foi eliminada. Para mensagens editadas, `chatMessage.editedOn` retorna uma data indicando quando a mensagem foi editada. O tempo original da criação de mensagens pode ser acedido usando `chatMessage.createdOn` o que pode ser usado para encomendar as mensagens.

`listMessages` retorna diferentes tipos de mensagens que podem ser identificadas por `chatMessage.type` . Estes tipos são:

- `Text`: Mensagem de chat regular enviada por um participante de thread.

- `ThreadActivity/TopicUpdate`: Mensagem do sistema que indica que o tópico foi atualizado.

- `ThreadActivity/AddParticipant`: Mensagem do sistema que indica que um ou mais participantes foram adicionados ao fio de conversação.

- `ThreadActivity/RemoveParticipant`: Mensagem do sistema que indica que um participante foi removido do fio de conversação.

Para mais detalhes, consulte [os Tipos de Mensagens](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicione um utilizador como participante ao fio de chat

Uma vez criado um fio de chat, pode adicionar e remover os utilizadores do mesmo. Ao adicionar os utilizadores, dá-lhes acesso para enviar mensagens para o fio de chat e adicionar/remover outros participantes.

Antes de ligar para o `addParticipants` método, certifique-se de que adquiriu um novo token de acesso e identidade para esse utilizador. O utilizador necessitará desse token de acesso para inicializar o seu cliente de chat.

`addParticipantsRequest` Descreve o objeto de pedido em que `participants` lista os participantes a adicionar ao fio de chat;
- `id`, necessário, é o identificador de comunicação a ser adicionado ao fio de conversação.
- `displayName`, opcional, é o nome de exibição do participante do thread.
- `shareHistoryTime`, opcional, é o momento a partir do qual a história do chat é partilhada com o participante. Para partilhar a história desde o início do fio de chat, coloque esta propriedade em qualquer data igual ou inferior ao tempo de criação de fios. Para não partilhar nenhuma história anterior à data da adição do participante, desafete-a para a data atual. Para partilhar a história parcial, desemafete-a à data da sua escolha.

```JavaScript

let addParticipantsRequest =
{
    participants: [
        {
            id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
Substitua **NEW_PARTICIPANT_USER_ID** por um [novo ID do utilizador](../../access-tokens.md) Adicione este código no lugar do comentário em `<ADD NEW PARTICIPANT TO THREAD>` **client.js**

## <a name="list-users-in-a-chat-thread"></a>Listar utilizadores em um fio de chat
```JavaScript
async function listParticipants() {
   let pagedAsyncIterableIterator = await chatThreadClient.listParticipants();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listParticipants();
```
Adicione este código no lugar do `<LIST PARTICIPANTS IN A THREAD>` comentário em **client.js,** atualize o separador do navegador e verifique a consola, deve ver informações sobre os utilizadores numa linha.

## <a name="remove-user-from-a-chat-thread"></a>Remova o utilizador de um fio de chat

Semelhante à adição de um participante, pode remover os participantes de um fio de chat. Para remover, terá de rastrear as identificações dos participantes que adicionou.

Utilize `removeParticipant` o método onde o utilizador de `participant` comunicação deve ser removido da linha.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Substitua **PARTICIPANT_ID** por um ID do utilizador utilizado no passo anterior (<NEW_PARTICIPANT_USER_ID>).
Adicione este código no lugar do `<REMOVE PARTICIPANT FROM THREAD>` comentário em **client.js,**
