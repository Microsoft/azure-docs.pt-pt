---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 7fe50a6236cf67f1048dddecbf46fea836ec05c5
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106126046"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Um recurso de serviços de comunicação implantado e uma cadeia de conexão. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um [token de acesso ao utilizador](../../access-tokens.md). Certifique-se de definir o âmbito para "chat", e note a cadeia simbólica, bem como a cadeia userId.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-java-application"></a>Criar uma nova aplicação Java

Abra a sua janela de terminal ou comando e navegue para o diretório onde pretende criar a sua aplicação Java. Executar o comando abaixo para gerar o projeto Java a partir do modelo maven-arqueotype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Você vai notar que o objetivo 'gerar' criou um diretório com o mesmo nome que o artefactoId. Sob este diretório, contém `src/main/java directory` o código fonte do projeto, o `src/test/java` diretório contém a fonte de teste, e o ficheiro pom.xml é o Project Object Model do projeto, ou [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Atualize o ficheiro POM da sua aplicação para utilizar Java 8 ou superior:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Adicione as referências do pacote para o Chat SDK

No seu ficheiro POM, faça referência ao `azure-communication-chat` pacote com as APIs do Chat:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

Para a autenticação, o seu cliente necessita de fazer referência ao `azure-communication-common` pacote:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Azure Communication Services Chat SDK para Java.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Esta classe é necessária para a funcionalidade Chat. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatAsyncClient | Esta classe é necessária para a funcionalidade chat assíncronos. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatThreadClient | Esta classe é necessária para a funcionalidade Chat Thread. Obtém um caso através do ChatClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/obter utilizadores, enviar notificações de dactilografia e ler recibos. |
| ChatThreadAsyncClient | Esta classe é necessária para a funcionalidade chat thread assíncronos. Obtém um caso através do ChatAsyncClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/obter utilizadores, enviar notificações de dactilografia e ler recibos. |

## <a name="create-a-chat-client"></a>Criar um cliente de chat
Para criar um cliente de chat, utilizará o ponto final do Serviço de Comunicações e o token de acesso que foi gerado como parte das etapas pré-necessárias. Os tokens de acesso ao utilizador permitem-lhe construir aplicações de clientes que autenticam diretamente os Serviços de Comunicação Azure. Assim que gerar estas fichas no seu servidor, passe-as de volta para um dispositivo cliente. Você precisa usar a classe CommunicationTokenCredential do Common SDK para passar o símbolo para o seu cliente de chat.

Saiba mais sobre [a Arquitetura chat](../../../concepts/chat/concepts.md)

Ao adicionar as declarações de importação, certifique-se de adicionar apenas importações do com.azure.communication.chat e com.azure.communication.chat.models namespaces, e não do com.azure.communication.chat.implementation namespace. No ficheiro .java App que foi gerado via Maven, pode utilizar o seguinte código para começar com:

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Utilize o `createChatThread` método para criar um fio de chat.
`createChatThreadOptions` é usado para descrever o pedido de linha.

- Utilize o `topic` parâmetro do construtor para dar um tópico a este chat; O tópico pode ser atualizado após a criação do fio de chat utilizando a `UpdateThread` função.
- Utilize `participants` para listar os participantes do fio a adicionar ao fio. `ChatParticipant`leva o utilizador criado no quickstart [do User Access Token.](../../access-tokens.md)

`CreateChatThreadResult` é a resposta devolvida da criação de um fio de chat.
Contém um `getChatThread()` método que devolve o objeto que pode ser usado para obter o cliente thread a partir do qual `ChatThread` você pode obter o para executar `ChatThreadClient` operações no fio criado: adicionar participantes, enviar mensagem, etc. O `ChatThread` objeto também contém o método que recupera o `getId()` ID único do fio.

```Java
ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(firstUser)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(secondUser)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Linhas de chat de lista

Utilize o `listChatThreads` método para recuperar uma lista de fios de chat existentes.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat

O `getChatThreadClient` método devolve um cliente de linha para um fio que já existe. Pode ser utilizado para a realização de operações no fio criado: adicionar participantes, enviar mensagem, etc. `chatThreadId` é o ID único do fio de chat existente.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Utilize o `sendMessage` método para enviar uma mensagem para o fio que acabou de criar, identificado pelo chatThreadId.
`sendChatMessageOptions` é usado para descrever o pedido de mensagem de chat.

- Utilize `content` para fornecer o conteúdo da mensagem de chat.
- Utilize `type` para especificar o tipo de conteúdo de mensagem de chat, TEXTO ou HTML.
- Utilize `senderDisplayName` para especificar o nome de visualização do remetente.

A resposta `sendChatMessageResult` contém um , que é o `id` ID único da mensagem.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receba mensagens de chat de um fio de chat

Pode recuperar mensagens de chat sondando o `listMessages` método no cliente do fio de chat em intervalos especificados.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` retorna a versão mais recente da mensagem, incluindo quaisquer edições ou eliminações que aconteceram com a mensagem usando .editMessage() e .deleteMessage(). Para mensagens eliminadas, `chatMessage.getDeletedOn()` de retorna um valor de hora de data indicando quando essa mensagem foi eliminada. Para mensagens editadas, `chatMessage.getEditedOn()` retorna uma data indicando quando a mensagem foi editada. A hora original da criação de mensagens pode ser acedida através da `chatMessage.getCreatedOn()` utilização , podendo ser utilizada para encomendar as mensagens.

Leia mais sobre os tipos de mensagens aqui: [Tipos de mensagens](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Enviar recibo de leitura

Utilize o `sendReadReceipt` método para publicar um evento de recibo de leitura num fio de chat, em nome de um utilizador.
`chatMessageId` é o ID único da mensagem de chat que foi lido.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Listam participantes do chat

Utilize `listParticipants` para recuperar uma coleção paged contendo os participantes do fio de chat identificado pelo chatThreadId.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Adicione um utilizador como participante ao fio de chat

Uma vez criado um fio de chat, pode adicionar e remover os utilizadores do mesmo. Ao adicionar os utilizadores, dá-lhes acesso para enviar mensagens para o fio de chat e adicionar/remover outros participantes. Terá de começar por obter um novo token de acesso e identidade para esse utilizador. Antes de ligar para o método addParticipants, certifique-se de que adquiriu um novo token de acesso e identidade para esse utilizador. O utilizador necessitará desse token de acesso para inicializar o seu cliente de chat.

Utilize o `addParticipants` método para adicionar os participantes ao fio.

- `communicationIdentifier`, é necessário, é o Identificador de Comunicação que criou pela Entidade ComunicaçãoClient no quickstart [do User Access Token.](../../access-tokens.md)
- `displayName`, opcional, é o nome de exibição do participante do thread.
- `shareHistoryTime`, opcional, é o momento a partir do qual a história do chat é partilhada com o participante. Para partilhar a história desde o início do fio de chat, coloque esta propriedade em qualquer data igual ou inferior ao tempo de criação de fios. Para não partilhar nenhuma história anterior à data da adição do participante, desafete-a para a data atual. Para partilhar o histórico parcial, desemafete-o para a data exigida.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>Executar o código

Navegue para o diretório que contém o ficheiro *pom.xml* e compile o projeto utilizando o seguinte `mvn` comando.

```console
mvn compile
```

Então, construa o pacote.

```console
mvn package
```

Execute o seguinte `mvn` comando para executar a aplicação.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
