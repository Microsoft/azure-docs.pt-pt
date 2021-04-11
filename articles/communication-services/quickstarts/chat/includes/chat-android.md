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
ms.openlocfilehash: 447747fbf378c090f9dd09cde3b0452780039180
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957954"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de:

- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale [o Android Studio](https://developer.android.com/studio), vamos usar o Android Studio para criar uma aplicação Android para o quickstart para instalar dependências.
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../create-communication-resource.md). Terá de **gravar o seu ponto final de recurso** para este arranque rápido.
- Crie **dois** Utilizadores de Serviços de Comunicação e emita-lhes um token de acesso ao [utilizador Token](../../access-tokens.md). Certifique-se de definir o âmbito para **o chat**, e note a cadeia simbólica e a **cadeia userId**. Neste arranque rápido, criaremos um fio com um participante inicial e, em seguida, adicionaremos um segundo participante ao fio.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-android-application"></a>Criar uma nova aplicação android

1. Abra o Android Studio e selecione `Create a new project` . 
2. Na janela seguinte, selecione `Empty Activity` como modelo de projeto.
3. Ao escolher opções, insira `ChatQuickstart` como o nome do projeto.
4. Clique em seguida e escolha o diretório onde deseja que o projeto seja criado.

### <a name="install-the-libraries"></a>Instalar as bibliotecas

Usaremos Gradle para instalar as dependências necessárias dos Serviços de Comunicação. A partir da linha de comando, navegue dentro do diretório de raiz do `ChatQuickstart` projeto. Abra o ficheiro build.gradle da aplicação e adicione as seguintes dependências ao `ChatQuickstart` alvo:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Excluir os ficheiros de meta nas opções de embalagem em root build.gradle
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>(Alternativa) Para instalar bibliotecas através de Maven
Para importar a biblioteca para o seu projeto utilizando o sistema de construção [Maven,](https://maven.apache.org/) adicione-a à `dependencies` secção do ficheiro da sua aplicação, `pom.xml` especificando o seu ID de artefacto e a versão que pretende utilizar:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.8</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Configurar os espaços reservados

Abra e edite o `MainActivity.java` ficheiro. Neste Quickstart, adicionaremos o nosso código para `MainActivity` , e visualizaremos a saída na consola. Este quickstart não se refere à construção de uma UI. No topo do ficheiro, importa as `Communication common` `Communication chat` bibliotecas e as bibliotecas:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Copie o seguinte código no `MainActivity` ficheiro:

```java
    private String secondUserId = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

Nos passos seguintes, substituiremos os espaços reservados por um código de amostra utilizando a biblioteca Azure Communication Services Chat.


### <a name="create-a-chat-client"></a>Criar um cliente de chat

Substitua o comentário `<CREATE A CHAT CLIENT>` pelo seguinte código (coloque as declarações de importação no topo do ficheiro):

```java
import com.azure.android.communication.chat.ChatAsyncClient;
import com.azure.android.communication.chat.ChatClientBuilder;
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.HttpHeader;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

1. Utilize o `ChatClientBuilder` para configurar e criar um exemplo de `ChatAsyncClient` .
2. `<resource>`Substitua-o pelo recurso serviços de comunicação.
3. Substitua `<user_access_token>` por um token de acesso válido dos Serviços de Comunicação.

## <a name="object-model"></a>Modelo de objeto
As seguintes classes e interfaces lidam com algumas das principais características do Azure Communication Services Chat SDK para JavaScript.

| Nome                                   | Descrição                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Esta classe é necessária para a funcionalidade Chat. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatThreadClient/ChatThreadAsyncClient | Esta classe é necessária para a funcionalidade Chat Thread. Obtém um caso através do ChatClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/receber utilizadores, enviar notificações de dactilografia e ler recibos, subscrever eventos de chat. |

## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Usaremos o nosso `ChatAsyncClient` para criar um novo fio com um utilizador inicial.

Substitua o comentário `<CREATE A CHAT THREAD>` pelo código seguinte:

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(id))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

Substitua `<user_id>` por um ID de utilizador válido dos Serviços de Comunicação. Vamos usar a `threadId` resposta devolvida ao manipulador de conclusão em etapas posteriores, por isso substitua a classe com a que `<thread_id>` `threadId` obtemos deste pedido e reencando a app.

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat

Agora que criámos um fio chat, vamos obter um `ChatThreadAsyncClient` para realizar operações dentro do fio. Substitua o comentário `<CREATE A CHAT THREAD CLIENT>` pelo código seguinte:

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Certifique-se de que substituímos `<thread_id>` por um ID de fio válido, enviaremos mensagem para esse fio agora.

Substitua o comentário `<SEND A MESSAGE>` pelo código seguinte:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

Depois de `chatMessageId` obtermos, podemos substituir `<chat_message_id>` por `chatMessageId` posterior utilização do método em quickstart e re-executar a app.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicione um utilizador como participante ao fio de chat

Substitua o comentário `<ADD A USER>` pelo código seguinte:

```java
// The display name for the thread participant.
displayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```

Substitua `<second_user_id>` na classe pelo ID do utilizador dos Serviços de Comunicação do utilizador a adicionar. 

## <a name="list-users-in-a-thread"></a>Listar utilizadores em um fio

Substitua o comentário `<LIST USERS>` pelo código seguinte:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> firstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant participant : firstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Coloque o seguinte método de ajuda na classe:

```java
void listParticipantsNextPage(String continuationToken, int pageNumber) {
if (continuationToken != null) {
    PagedResponse<ChatParticipant> nextPageWithResponse =
        chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
        for (ChatParticipant participant : nextPageWithResponse.getValue()) {
            // You code to handle participant
        }
            
        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>Remova o utilizador de um fio de chat

Certifique-se de que `<second_user_id>` substitui um ID de utilizador válido, removemos agora o segundo utilizador da linha.

Substitua o comentário `<REMOVE A USER>` pelo código seguinte:

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Enviar uma notificação de dactilografia

Substitua o comentário `<SEND A TYPING NOTIFICATION>` pelo código seguinte:

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Enviar um recibo de leitura

Certifique-se de substituir `<chat_message_id>` por um ID de mensagem de chat válido, enviaremos o recibo de leitura para esta mensagem agora.

Substitua o comentário `<SEND A READ RECEIPT>` pelo código seguinte:

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Lista ler recibos

Substitua o comentário `<READ RECEIPTS>` pelo código seguinte:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatMessageReadReceipt> firstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : firstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Coloque o seguinte método de ajuda na classe:
```java
void listReadReceiptsNextPage(String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
            chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();

        for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
            // You code to handle readReceipt
        }

        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="run-the-code"></a>Executar o código

No Android Studio, premir o botão Run para construir e executar o projeto. Na consola, pode ver a saída a partir do código e a saída do madeireiro a partir do ChatClient.
