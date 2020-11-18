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
ms.openlocfilehash: 69b3e900973b1be484b76764847767b9e74d9998
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816779"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) versão 8 ou superior.
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

### <a name="add-the-package-references-for-the-chat-client-library"></a>Adicione as referências do pacote para a biblioteca do cliente do chat

No seu ficheiro POM, faça referência ao `azure-communication-chat` pacote com as APIs do Chat:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>
```

Para a autenticação, o seu cliente necessita de fazer referência ao `azure-communication-common` pacote:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>

```

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas e interfaces lidam com algumas das principais características da biblioteca de clientes Azure Communication Services Chat para Java.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Esta classe é necessária para a funcionalidade Chat. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatAsyncClient | Esta classe é necessária para a funcionalidade chat assíncronos. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatThreadClient | Esta classe é necessária para a funcionalidade Chat Thread. Obtém um caso através do ChatClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/obter utilizadores, enviar notificações de dactilografia e ler recibos. |
| ChatThreadAsyncClient | Esta classe é necessária para a funcionalidade chat thread assíncronos. Obtém um caso através do ChatAsyncClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/obter utilizadores, enviar notificações de dactilografia e ler recibos. |

## <a name="create-a-chat-client"></a>Criar um cliente de chat
Para criar um cliente de chat, utilizará o ponto final do Serviço de Comunicações e o token de acesso que foi gerado como parte das etapas pré-necessárias. Os tokens de acesso ao utilizador permitem-lhe construir aplicações de clientes que autenticam diretamente os Serviços de Comunicação Azure. Assim que gerar estas fichas no seu servidor, passe-as de volta para um dispositivo cliente. Você precisa usar a classe CommunicationUserCredential da biblioteca de clientes Comuns para passar o símbolo para o seu cliente de chat. 

Ao adicionar as declarações de importação, certifique-se de adicionar apenas importações do com.azure.communication.chat e com.azure.communication.chat.models namespaces, e não do com.azure.communication.chat.implementation namespace. No ficheiro .java App que foi gerado via Maven, pode utilizar o seguinte código para começar com:

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationUserCredential with the given access token, which is only valid until the token is valid
        CommunicationUserCredential userCredential = new CommunicationUserCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Utilize o `createChatThread` método para criar um fio de chat.
`createChatThreadOptions` é usado para descrever o pedido de linha.

- Use `topic` para dar um tópico a este chat; O tópico pode ser atualizado após a criação do fio de chat utilizando a `UpdateThread` função.
- Utilize `members` para listar os membros do fio a serem adicionados à linha. `ChatThreadMember`leva o utilizador criado no quickstart [do User Access Token.](../../access-tokens.md)

A resposta `chatThreadClient` é utilizada para executar operações no fio de chat criado: adicionar membros ao fio de chat, enviar uma mensagem, apagar uma mensagem, etc. Contém uma `chatThreadId` propriedade que é o ID único do fio de chat. A propriedade é acessível pelo método público .getChatThreadId().

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(firstUser)
    .setDisplayName("Member Display Name 1");
    
ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(secondUser)
    .setDisplayName("Member Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setMembers(members);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Utilize o `sendMessage` método para enviar uma mensagem para o fio que acabou de criar, identificado pelo chatThreadId.
`sendChatMessageOptions` é usado para descrever o pedido de mensagem de chat.

- Utilize `content` para fornecer o conteúdo da mensagem de chat.
- Utilize `priority` para especificar o nível prioritário da mensagem de chat, como 'Normal' ou 'High'; esta propriedade pode ser usada para ter um indicador de UI para o utilizador recetor na sua app, para chamar a atenção para a mensagem ou executar a lógica de negócio personalizada.
- Utilize `senderDisplayName` para especificar o nome de visualização do remetente.

A resposta `sendChatMessageResult` contém um , que é o `id` ID único da mensagem.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setPriority(ChatMessagePriority.NORMAL)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat

O `getChatThreadClient` método devolve um cliente de linha para um fio que já existe. Pode ser utilizado para a realização de operações no fio criado: adicionar membros, enviar mensagem, etc. `chatThreadId` é o ID único do fio de chat existente.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receba mensagens de chat de um fio de chat

Pode recuperar mensagens de chat sondando o `listMessages` método no cliente do fio de chat em intervalos especificados.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` retorna a versão mais recente da mensagem, incluindo quaisquer edições ou eliminações que aconteceram com a mensagem usando .editMessage() e .deleteMessage(). Para mensagens eliminadas, `chatMessage.getDeletedOn()` de retorna um valor de hora de data indicando quando essa mensagem foi eliminada. Para mensagens editadas, `chatMessage.getEditedOn()` retorna uma data indicando quando a mensagem foi editada. A hora original da criação de mensagens pode ser acedida através da `chatMessage.getCreatedOn()` utilização , podendo ser utilizada para encomendar as mensagens.

`listMessages` retorna diferentes tipos de mensagens que podem ser identificadas por `chatMessage.getType()` . Estes tipos são:

- `Text`: Mensagem de chat regular enviada por um membro do thread.

- `ThreadActivity/TopicUpdate`: Mensagem do sistema que indica que o tópico foi atualizado.

- `ThreadActivity/AddMember`: Mensagem do sistema que indica que um ou mais membros foram adicionados ao fio de conversação.

- `ThreadActivity/DeleteMember`: Mensagem do sistema que indica que um membro foi removido do fio de conversação.

Para mais detalhes, consulte [os Tipos de Mensagens](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Adicione um utilizador como membro ao fio de chat

Uma vez criado um fio de chat, pode adicionar e remover os utilizadores do mesmo. Ao adicionar os utilizadores, dá-lhes acesso para enviar mensagens para o fio de chat e adicionar/remover outros membros. Terá de começar por obter um novo token de acesso e identidade para esse utilizador. Antes de ligar para o método addMembers, certifique-se de que adquiriu um novo token de acesso e identidade para esse utilizador. O utilizador necessitará desse token de acesso para inicializar o seu cliente de chat.

Utilize `addMembers` o método para adicionar os membros do fio ao fio identificado pelo threadId.

- Utilize `members` para listar os membros a adicionar ao fio de conversação.
- `user`, necessário, é o ComunicadoR que criou pela Entidade ComunicaçãoClient no quickstart [do User Access Token.](../../access-tokens.md)
- `display_name`, opcional, é o nome de visualização do membro thread.
- `share_history_time`, opcional, é o momento a partir do qual a história do chat é partilhada com o membro. Para partilhar a história desde o início do fio de chat, coloque esta propriedade em qualquer data igual ou inferior ao tempo de criação de fios. Para não partilhar nenhuma história anterior à data em que o membro foi adicionado, desemafete-a para a data atual. Para partilhar o histórico parcial, desemafete-o para a data exigida.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(user2)
    .setDisplayName("Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

AddChatThreadMembersOptions addChatThreadMembersOptions = new AddChatThreadMembersOptions()
    .setMembers(members);
chatThreadClient.addMembers(addChatThreadMembersOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>Remova o utilizador de um fio de chat

Similar a adicionar um utilizador a um fio, pode remover os utilizadores de um fio de chat. Para isso, é necessário rastrear as identidades dos membros que adicionou.

Use, `removeMember` onde `user` está o Comunicador que criou.

```Java
chatThreadClient.removeMember(user);
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
