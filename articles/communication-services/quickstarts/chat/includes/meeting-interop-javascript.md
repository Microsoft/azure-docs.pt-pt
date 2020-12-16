---
title: Quickstart - Junte-se a um encontro de equipas
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c41771af81989ff965098a762338216db54fd27
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578146"
---
## <a name="join-the-meeting-chat"></a>Junte-se ao chat da reunião 

Uma vez ativada a interoperabilidade das Equipas, um utilizador dos Serviços de Comunicação pode juntar-se à chamada teams como utilizador convidado utilizando a biblioteca do cliente chamador. Juntando-se à chamada irá adicioná-los como participantes no chat de reunião também, onde podem enviar e receber mensagens com outros utilizadores na chamada. O utilizador não terá acesso a mensagens de chat que foram enviadas antes de se juntarem à chamada. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Obtenha um fio de chat de reuniões de equipas para um utilizador de Serviços de Comunicação

Primeiro, instantâneo a `ChatThreadClient` para o fio de conversação da reunião. Analise o link de reunião ou use as APIs do gráfico com o ID da reunião para obter o ID do fio. 

- Um link de encontro de equipas é o seguinte: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . O ID do fio será onde `meeting_chat_thread_id` está nesse link. 
- Se tiver o ID da reunião, pode utilizar [a API do Gráfico](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) para obter o ID do fio. A resposta [get API](https://docs.microsoft.com/graph/api/onlinemeeting-get?view=graph-rest-beta&tabs=http%22%20%5C) terá um `chatInfo` objeto que contém o `threadID` . 

Assim que tiver o ID do fio de chat, pode obter cliente de linha de chat usando a biblioteca do cliente de chat JavaScript: 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
Pode `chatThreadClient` ser usado para listar membros na linha de chat, obter histórico de chat e enviar mensagens.  

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens  

Use `SendMessage` o para enviar uma mensagem para o chat da reunião. Para receber mensagens recebidas, a capacidade de subscrever eventos como não é suportado, uma vez que `chatMessageReceived` a sinalização em tempo real ainda não está ativada para este cenário. Para obter as últimas mensagens, pode sondar a `ListMessages` API. Para o cenário de interoperabilidade, a `ListMessages` API suporta agora a devolução de três novos tipos de mensagens:
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

Para obter mais informações sobre os tipos de mensagens, consulte [aqui.](../../../concepts/chat/concepts.md) 

**Nota** - Atualmente apenas o envio e receção de mensagens é suportado para cenários de interoperabilidade com equipas. Outras funcionalidades, como indicadores de dactilografia e serviços de comunicação que adicionam ou retiram outros utilizadores da reunião das Equipas ainda não são suportadas.  

 
