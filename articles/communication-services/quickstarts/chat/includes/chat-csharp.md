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
ms.openlocfilehash: a76c6467dac69fd3d21aa659c52227046c166938
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816790"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de:
- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Instalar [Estúdio Visual](https://visualstudio.microsoft.com/downloads/) 
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../create-communication-resource.md). Terá de gravar o seu **ponto final** de recurso para este arranque rápido.
- Um [token de acesso ao utilizador](../../access-tokens.md). Certifique-se de definir o âmbito para "chat", e note a cadeia simbólica, bem como a cadeia userId.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `ChatQuickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Mude o seu diretório para a pasta de aplicação recém-criada e use o `dotnet build` comando para compilar a sua aplicação.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instale o pacote

Instale a biblioteca de clientes Azure Communication Chat para .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.3
``` 

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas lidam com algumas das principais características da biblioteca de clientes Azure Communication Services Chat para C#.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Esta classe é necessária para a funcionalidade Chat. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatThreadClient | Esta classe é necessária para a funcionalidade Chat Thread. Obtém um caso através do ChatClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/obter utilizadores, enviar notificações de dactilografia e ler recibos. |

## <a name="create-a-chat-client"></a>Criar um cliente de chat

Para criar um cliente de chat, utilizará o seu ponto final dos Serviços de Comunicação e o token de acesso que foi gerado como parte das etapas pré-requisitos. Você precisa usar a `CommunicationIdentityClient` classe da biblioteca do cliente para criar um utilizador e emitir um símbolo para passar para o seu `Administration` cliente de chat. Saiba mais sobre [tokens de acesso ao utilizador.](../../access-tokens.md)

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Utilize o `createChatThread` método para criar um fio de chat.
- Use `topic` para dar um tópico a este chat; O tópico pode ser atualizado após a criação do fio de chat utilizando a `UpdateThread` função.
- Utilize `members` propriedade para passar uma lista de `ChatThreadMember` objetos a adicionar ao fio de chat. O `ChatThreadMember` objeto é inicializado com um `CommunicationUser` objeto. Para obter um `CommunicationUser` objeto, terá de passar um ID de acesso que criou seguindo instruções para [criar um utilizador](../../access-tokens.md#create-an-identity)

A resposta `chatThreadClient` é utilizada para executar operações no fio de chat criado: adicionar membros ao fio de chat, enviar uma mensagem, apagar uma mensagem, etc. Contém o `Id` atributo que é o ID único do fio de chat. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat
O `GetChatThreadClient` método devolve um cliente de linha para um fio que já existe. Pode ser utilizado para a realização de operações no fio criado: adicionar membros, enviar mensagem, etc. threadId é o ID único do fio de chat existente.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Utilize `SendMessage` o método para enviar uma mensagem a um fio identificado pelo threadId.

- Utilize `content` para fornecer o conteúdo da mensagem de chat, é necessário.
- Utilizar `priority` para especificar o nível de prioridade da mensagem, como 'Normal' ou 'Alto', se não for especificado, será utilizado 'Normal'.
- Utilizar `senderDisplayName` para especificar o nome de visualização do remetente, se não for especificado, será utilizado o nome vazio.

`SendChatMessageResult` é a resposta devolvida do envio de uma mensagem, contém um id, que é o ID único da mensagem.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receba mensagens de chat de um fio de chat

Pode recuperar mensagens de chat sondando o `GetMessages` método no cliente do fio de chat em intervalos especificados.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` toma um `DateTimeOffset` parâmetro opcional. Se essa compensação for especificada, receberá mensagens que foram recebidas, atualizadas ou eliminadas após a sua aprovação. Note que as mensagens recebidas antes do tempo de compensação, mas editadas ou removidas depois de serem devolvidas também serão devolvidas.

`GetMessages` retorna a versão mais recente da mensagem, incluindo quaisquer edições ou eliminações que aconteceram com a mensagem usando `UpdateMessage` e `DeleteMessage` . Para mensagens eliminadas, `chatMessage.DeletedOn` de retorna um valor de hora de data indicando quando essa mensagem foi eliminada. Para mensagens editadas, `chatMessage.EditedOn` retorna uma data indicando quando a mensagem foi editada. A hora original da criação de mensagens pode ser acedida através da `chatMessage.CreatedOn` utilização , podendo ser utilizada para encomendar as mensagens.

`GetMessages` retorna diferentes tipos de mensagens que podem ser identificadas por `chatMessage.Type` . Estes tipos são:

- `Text`: Mensagem de chat regular enviada por um membro do thread.

- `ThreadActivity/TopicUpdate`: Mensagem do sistema que indica que o tópico foi atualizado.

- `ThreadActivity/AddMember`: Mensagem do sistema que indica que um ou mais membros foram adicionados ao fio de conversação.

- `ThreadActivity/DeleteMember`: Mensagem do sistema que indica que um membro foi removido do fio de conversação.

Para mais detalhes, consulte [os Tipos de Mensagens](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Atualizar uma mensagem

Pode atualizar uma mensagem que já foi enviada invocando `UpdateMessage` `ChatThreadClient` .

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Apagar uma mensagem

Pode apagar uma mensagem invocando `DeleteMessage` `ChatThreadClient` .

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Adicione um utilizador como membro ao fio de chat

Uma vez criado um fio, pode adicionar e remover os utilizadores do mesmo. Ao adicionar os utilizadores, dá-lhes acesso para poderem enviar mensagens para o fio e adicionar/remover outros membros. Antes de `AddMembers` ligar, certifique-se de que adquiriu um novo token de acesso e identidade para esse utilizador. O utilizador necessitará desse token de acesso para inicializar o seu cliente de chat.

Utilize `AddMembers` o método para adicionar os membros do fio ao fio identificado pelo threadId.

 - Utilizar `members` para listar os membros a adicionar ao fio de conversação;
 - `User`, necessário, é a identidade que obtém para este novo utilizador.
 - `DisplayName`, opcional, é o nome de visualização do membro thread.
 - `ShareHistoryTime`, opcional, tempo a partir do qual a história do chat é partilhada com o membro. Para partilhar a história desde o início do chat thread, desemote-o para DateTime.MinValue. Para não partilhar nenhuma história, antes de quando o membro foi adicionado, defini-lo para o momento atual. Para partilhar a história parcial, defini-la a um ponto no tempo entre a criação do fio e o tempo atual.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Remova o utilizador de um fio de chat

Similar a adicionar um utilizador a um fio, pode remover os utilizadores de um fio de chat. Para isso, é necessário rastrear a identidade (CommunicationUser) dos membros que adicionou.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```console
dotnet run
```
