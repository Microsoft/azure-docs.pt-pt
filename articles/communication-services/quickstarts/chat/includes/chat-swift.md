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
ms.openlocfilehash: 5bf4bbe2c8dc863f67dffb50609f7775a4499e3a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073743"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de:

- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Instale [Xcode](https://developer.apple.com/xcode/) e [Cacau.](https://cocoapods.org/) Utiliza o Xcode para criar uma aplicação iOS para o arranque rápido e os Cacau para instalar dependências.
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Quickstart: Criar e gerir os recursos dos Serviços de Comunicação.](../../create-communication-resource.md) Para este arranque rápido, tem de gravar o seu ponto final de recurso.
- Crie dois utilizadores nos Serviços de Comunicação Azure e emita-lhes um [token de acesso ao utilizador](../../access-tokens.md). Certifique-se de definir o alcance para `chat` , e note a corda assim como a `token` `userId` corda. Neste arranque rápido, cria-se um fio com um participante inicial e, em seguida, adiciona-se um segundo participante ao fio.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-ios-application"></a>Criar uma nova aplicação iOS

Abra o Xcode e **selecione Criar um novo projeto Xcode**. Em seguida, selecione **o iOS** como plataforma e **App** para o modelo.

Para o nome do projeto, **insira ChatQuickstart**. Em seguida, selecione **Storyboard** como interface, **Delegado de Aplicação UIKit** como o ciclo de vida, e **Swift** como o idioma.

Selecione **Next**, e escolha o diretório onde pretende que o projeto seja criado.

### <a name="install-the-libraries"></a>Instalar as bibliotecas

Utilize cacau para instalar as dependências necessárias dos Serviços de Comunicação.

A partir da linha de comando, entre no diretório de raiz do `ChatQuickstart` projeto iOS. Criar um Podfile com o seguinte comando: `pod init` .

Abra o Podfile e adicione as seguintes dependências ao `ChatQuickstart` alvo:

```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Instale as dependências com o seguinte comando: `pod install` . Note que isto também cria um espaço de trabalho Xcode.

Após a `pod install` execução, reabrimos o projeto em Xcode selecionando o recém-criado `.xcworkspace` .

### <a name="set-up-the-placeholders"></a>Configurar os espaços reservados

Abra o espaço de trabalho `ChatQuickstart.xcworkspace` em Xcode e, em seguida, `ViewController.swift` abra.

Neste arranque rápido, adiciona-se o seu código para `viewController` , e vê a saída na consola Xcode. Este quickstart não aborda a construção de uma interface de utilizador no iOS. 

No topo de `viewController.swift` , importar as `AzureCommunication` `AzureCommunicatonChat` bibliotecas e bibliotecas:

```
import AzureCommunication
import AzureCommunicationChat
```

Copie o seguinte código no `viewDidLoad()` método `ViewController` de:

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

Para fins de demonstração, usaremos um semáforo para sincronizar o seu código. Nos passos seguintes, substitui os espaços reservados por um código de amostra utilizando a biblioteca Azure Communication Services Chat.


### <a name="create-a-chat-client"></a>Criar um cliente de chat

Substitua o comentário `<CREATE A CHAT CLIENT>` pelo código seguinte:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
    let credential =
    try CommunicationTokenCredential(
        token: "<ACCESS_TOKEN>"
    )
    let options = AzureCommunicationChatClientOptions()

    let chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: options
    )
```

`<ACS_RESOURCE_ENDPOINT>`Substitua-o pelo ponto final do seu recurso Azure Communication Services. Substitua `<ACCESS_TOKEN>` por um token de acesso válido dos Serviços de Comunicação.

Este quickstart não cobre a criação de um nível de serviço para gerir fichas para a sua aplicação de chat, mas isso é recomendado. Para mais informações, consulte a secção "Chat architecture" dos [conceitos Chat.](../../../concepts/chat/concepts.md)

Para obter mais informações sobre fichas de acesso ao utilizador, consulte [Quickstart: Criar e gerir fichas de acesso.](../../access-tokens.md)

## <a name="object-model"></a>Modelo de objeto 

As seguintes classes e interfaces lidam com algumas das principais características do Azure Communication Services Chat SDK para JavaScript.

| Nome                                   | Descrição                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | Esta classe é necessária para a funcionalidade do chat. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| `ChatThreadClient` | Esta classe é necessária para a funcionalidade do fio de chat. Obtém-se um caso através `ChatClient` de , e utiliza-o para enviar, receber, atualizar e apagar mensagens. Também pode usá-lo para adicionar, remover e obter utilizadores, enviar notificações de dactilografia e ler recibos, e subscrever eventos de chat. |

## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Agora usa o seu `ChatClient` para criar um novo fio com um utilizador inicial.

Substitua o comentário `<CREATE A CHAT THREAD>` pelo código seguinte:

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Substitua `<USER_ID>` por um ID de utilizador válido dos Serviços de Comunicação.

Estás a usar um semáforo para esperar pelo manipulador de conclusão antes de continuares. Em etapas posteriores, você usará a `threadId` resposta devolvida ao manipulador de conclusão.

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat

Agora que criou um fio de chat, pode obter um `ChatThreadClient` para realizar operações dentro do fio.

Substitua o comentário `<CREATE A CHAT THREAD CLIENT>` pelo código seguinte:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Substitua o comentário `<SEND A MESSAGE>` pelo código seguinte:

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Primeiro, constrói-se o `SendChatMessageRequest` , que contém o conteúdo e o nome de visualização do remetente. Este pedido também pode conter o tempo de histórico de partilha, se quiser incluí-lo. A resposta devolvida ao manipulador de conclusão contém a identificação da mensagem que foi enviada.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicione um utilizador como participante ao fio de chat

Substitua o comentário `<ADD A USER>` pelo código seguinte:

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

Substitua-se pelo `<USER_ID>` ID do utilizador dos Serviços de Comunicação do utilizador a adicionar.

Quando se adiciona um participante a um fio, a resposta devolvida pode conter erros. Estes erros representam a falta de adição de participantes específicos.

## <a name="list-users-in-a-thread"></a>Listar utilizadores em um fio

Substitua o comentário `<LIST USERS>` pelo código seguinte:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>Remova o utilizador de um fio de chat

Substitua o comentário `<REMOVE A USER>` pelo código seguinte:

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

`<USER ID>`Substitua-se pelo ID do utilizador dos Serviços de Comunicação do participante que está a ser removido.

## <a name="run-the-code"></a>Executar o código

No Xcode, selecione **Run** para construir e executar o projeto. Na consola, pode ver a saída a partir do código e a saída do madeireiro do cliente do chat.

