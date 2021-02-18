---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/16/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 7e62bbc5929eaf23a9b7be12de222105bc2529cd
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653588"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de:

- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale [o Android Studio](https://developer.android.com/studio), vamos usar o Android Studio para criar uma aplicação Android para o quickstart para instalar dependências.
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../create-communication-resource.md). Terá de **gravar o seu ponto final de recurso** para este arranque rápido.
- Crie **dois** Utilizadores de Serviços de Comunicação e emita-lhes um token de acesso ao [utilizador Token](../../access-tokens.md). Certifique-se de definir o âmbito para **o chat**, e note a **cadeia simbólica, bem como a cadeia userId**. Neste quickstart vamos criar um fio com um participante inicial e, em seguida, adicionar um segundo participante ao fio.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-android-application"></a>Criar uma nova aplicação android

1. Abra o Android Studio e selecione `Create a new project` . 
2. Na janela seguinte, selecione `Empty Activity` como modelo de projeto.
3. Ao escolher opções insira `ChatQuickstart` como o nome do projeto.
4. Clique em seguida e escolha o diretório onde deseja que o projeto seja criado.

### <a name="install-the-libraries"></a>Instalar as bibliotecas

Usaremos Gradle para instalar as dependências necessárias dos Serviços de Comunicação. A partir da linha de comando navegue dentro do diretório de raiz do `ChatQuickstart` projeto. Abra o ficheiro build.gradle da aplicação e adicione as seguintes dependências ao `ChatQuickstart` alvo:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.5'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.5'
```

Clique em 'sync now' no Android Studio.

#### <a name="alternative-to-install-libraries-through-maven"></a>(Alternativa) Para instalar bibliotecas através de Maven
Para importar a biblioteca para o seu projeto utilizando o sistema de construção [Maven,](https://maven.apache.org/) adicione-a à `dependencies` secção do ficheiro da sua aplicação, `pom.xml` especificando o seu ID de artefacto e a versão que pretende utilizar:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.5</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Configurar os espaços reservados

Abra e edite o `MainActivity.java` ficheiro. Neste Quickstart, adicionaremos o nosso código para `MainActivity` , e visualizaremos a saída na consola. Este quickstart não se refere à construção de uma UI. No topo do ficheiro, importa as `Communication common` `Communication chat` bibliotecas e as bibliotecas:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Copie o seguinte código no `MainActivity` ficheiro:

```
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
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

Nos passos seguintes, substituiremos os espaços reservados por um código de amostra utilizando a biblioteca Azure Communication Services Chat.


### <a name="create-a-chat-client"></a>Criar um cliente de chat

Substitua o comentário `<CREATE A CHAT CLIENT>` pelo seguinte código (coloque as declarações de importação no topo do ficheiro):

```java
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatClient client = new ChatClient.Builder()
    .endpoint(endpoint)
    .credentialInterceptor(chain -> chain.proceed(chain.request()
        .newBuilder()
        .header(HttpHeader.AUTHORIZATION, userAccessToken)
        .build());
```

1. Utilize o `AzureCommunicationChatServiceAsyncClient.Builder` para configurar e criar um exemplo de `AzureCommunicationChatClient` .
2. `<resource>`Substitua-o pelo recurso serviços de comunicação.
3. Substitua `<user_access_token>` por um token de acesso válido dos Serviços de Comunicação.

## <a name="object-model"></a>Modelo de objeto
As seguintes classes e interfaces lidam com algumas das principais funcionalidades da biblioteca de clientes Azure Communication Services Chat para o JavaScript.

| Nome                                   | Descrição                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Esta classe é necessária para a funcionalidade Chat. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatThreadClient | Esta classe é necessária para a funcionalidade Chat Thread. Obtém um caso através do ChatClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/receber utilizadores, enviar notificações de dactilografia e ler recibos, subscrever eventos de chat. |

## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Usaremos o nosso `ChatClient` para criar um novo fio com um utilizador inicial.

Substitua o comentário `<CREATE A CHAT THREAD>` pelo código seguinte:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setId(id)
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
    .setTopic(topic)
    .setParticipants(participants);

// optional, set a repeat request ID 
final String repeatabilityRequestID = '123';

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
    public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
        // MultiStatusResponse is the result returned from creating a thread.
        // It has a 'multipleStatus' property which represents a list of IndividualStatusResponse.
        String threadId;
        List<IndividualStatusResponse> statusList = result.getMultipleStatus();
        for (IndividualStatusResponse status : statusList) {
            if (status.getId().endsWith("@thread.v2")
                && status.getType().contentEquals("Thread")) {
                threadId = status.getId();
                break;
            }
        }
        // Take further action.
    }

    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
    }
});
```

Substitua `<user_id>` por um ID de utilizador válido dos Serviços de Comunicação. Usaremos a `threadId` resposta devolvida ao manipulador de conclusão em passos posteriores.

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat

Agora que criámos um fio chat, vamos obter um `ChatThreadClient` para realizar operações dentro do fio. Substitua o comentário `<CREATE A CHAT THREAD CLIENT>` pelo código seguinte:

```
ChatThreadClient threadClient =
        new ChatThreadClient.Builder()
            .endpoint(<endpoint>))
            .build();
```

`<endpoint>`Substitua-o pelo ponto de terminação dos serviços de comunicação.

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Substitua o comentário `<SEND A MESSAGE>` pelo código seguinte:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.sendChatMessage(threadId, message, new Callback<String>() {
    @Override
    public void onSuccess(String messageId, Response response) {
        // A string is the response returned from sending a message, it is an id, 
        // which is the unique ID of the message.
        final String chatMessageId = messageId;
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

`<thread_id>`Substitua-o pelo id de linha para o que envia a mensagem.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicione um utilizador como participante ao fio de chat

Substitua o comentário `<ADD A USER>` pelo código seguinte:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The CommunicationUser.identifier you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "a new participant";
participants.add(new ChatParticipant().setId(id).setDisplayName(displayName));
// The model to pass to the add method.
AddChatParticipantsRequest participants = new AddChatParticipantsRequest()
    .setParticipants(participants);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.addChatParticipants(threadId, participants, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. Substitua-se pelo `<user_id>` ID do utilizador dos Serviços de Comunicação do utilizador a adicionar. 
2. `<thread_id>`Substitua-o pelo ID do fio a que o utilizador está a adicionar.

## <a name="list-users-in-a-thread"></a>Listar utilizadores em um fio

Substitua o comentário `<LIST USERS>` pelo código seguinte:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";

// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
    @Override
    public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> firstPage,
        Response response) {
        // pageCollection enables enumerating list of chat participants.
        pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> firstPage, Response response) {
                for (ChatParticipant participant : firstPage.getItems()) {
                    // Take further action.
                }
                retrieveNextParticipantsPages(firstPage.getPageId(), pageCollection);
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                // Handle error.
            }
         }
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});

void listChatParticipantsNext(String nextLink,
    AsyncPagedDataCollection<Page<ChatParticipant>> pageCollection) {
        @Override
        public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
            for (ChatParticipant participant : nextPage.getItems()) {
                // Take further action.
            }
            if (nextPage.getPageId() != null) {
                retrieveNextParticipantsPages(nextPage.getPageId(), pageCollection);
            }
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            // Handle error.
        }
}
```

`<thread_id>`Substitua-o pelo ID do fio para o qual está a listar os utilizadores.

## <a name="remove-user-from-a-chat-thread"></a>Remova o utilizador de um fio de chat

Substitua o comentário `<REMOVE A USER>` pelo código seguinte:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";
// The unique ID of the participant.
final String participantId = "<participant_id>";
threadClient.removeChatParticipant(threadId, participantId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. `<thread_id>`Substitua-o pelo id de linha de que o utilizador remove.
1. `<participant_id>`Substitua-se pelo ID do utilizador dos Serviços de Comunicação do participante que está a ser removido.

## <a name="run-the-code"></a>Executar o código

No Android Studio, premiu o botão Run para construir e executar o projeto. Na consola pode ver a saída a partir do código e a saída do madeireiro a partir do ChatClient.
