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
ms.openlocfilehash: dedea2a622cb0eece92bb8b57871c76daa05fb68
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495468"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de:

- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Instale [Xcode](https://developer.apple.com/xcode/) e [Cacau,](https://cocoapods.org/)estaremos a usar o Xcode para criar uma aplicação iOS para o arranque rápido e cacau para instalar dependências.
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../create-communication-resource.md). Terá de **gravar o seu ponto final de recurso** para este arranque rápido.
- Crie **dois** Utilizadores ACS e emita-lhes um token de acesso ao [utilizador Token](../../access-tokens.md). Certifique-se de definir o âmbito para **o chat**, e note a **cadeia simbólica, bem como a cadeia userId**. Neste quickstart vamos criar um fio com um participante inicial e, em seguida, adicionar um segundo participante ao fio.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-ios-application"></a>Criar uma nova aplicação iOS

Abra o Xcode e selecione `Create a new Xcode project` .

Na janela seguinte, selecione `iOS` como plataforma e para o `App` modelo.

Ao escolher opções insira `ChatQuickstart` como o nome do projeto. Selecione `Storyboard` como interface, `UIKit App Delegate` como ciclo de vida, e como o `Swift` idioma.

Clique em seguida e escolha o diretório onde deseja que o projeto seja criado.

### <a name="install-the-libraries"></a>Instalar as bibliotecas

Usaremos cacau para instalar as dependências necessárias dos Serviços de Comunicação.

A partir da linha de comando navegue dentro do diretório de raiz do `ChatQuickstart` projeto iOS.

Criar um Podfile: `pod init`

Abra o Podfile e adicione as seguintes dependências ao `ChatQuickstart` alvo:
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Instale as dependências, isto também criará um espaço de trabalho Xcode: `pod install`

**Depois de executar a instalação do pod, reabrimos o projeto em Xcode selecionando o recém-criado `.xcworkspace` .**

### <a name="setup-the-placeholders"></a>Configurar os espaços reservados

Abra o espaço de trabalho `ChatQuickstart.xcworkspace` em Xcode e, em seguida, abra `ViewController.swift` .

Neste Quickstart, adicionaremos o nosso código para `viewController` , e visualizaremos a saída na consola Xcode. Este quickstart não aborda a construção de um UI no iOS. 

No topo da `viewController.swift` importação das `AzureCommunication` `AzureCommunicatonChat` bibliotecas:

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

Usaremos um semáforo para sincronizar o nosso código para fins de demonstração. Nos passos seguintes, substituiremos os espaços reservados por um código de amostra utilizando a biblioteca Azure Communication Services Chat.


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

`<ACS_RESOURCE_ENDPOINT>`Substitua-o pelo ponto final do seu recurso ACS.
Substitua `<ACCESS_TOKEN>` por um token de acesso ACS válido.

Este quickstart não cobre a criação de um nível de serviço para gerir fichas para a sua aplicação de chat, embora seja recomendado. Consulte a seguinte documentação para obter mais detalhes [Sobre a Arquitetura chat](../../../concepts/chat/concepts.md)

Saiba mais sobre [tokens de acesso ao utilizador.](../../access-tokens.md)

## <a name="object-model"></a>Modelo de objeto 
As seguintes classes e interfaces lidam com algumas das principais funcionalidades da biblioteca de clientes Azure Communication Services Chat para o JavaScript.

| Nome                                   | Descrição                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Esta classe é necessária para a funcionalidade Chat. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatThreadClient | Esta classe é necessária para a funcionalidade Chat Thread. Obtém um caso através do ChatClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/receber utilizadores, enviar notificações de dactilografia e ler recibos, subscrever eventos de chat. |

## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Agora vamos usar o nosso `ChatClient` para criar um novo fio com um utilizador inicial.

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

Estamos a usar um semáforo para esperar pelo manipulador de conclusão antes de continuar. Usaremos a `threadId` resposta devolvida ao manipulador de conclusão em etapas posteriores.

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat

Agora que criámos um fio chat, vamos obter um `ChatThreadClient` para realizar operações dentro do fio.

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

Primeiro construímos o `SendChatMessageRequest` que contém o nome de exibição de conteúdos e remetentes (também opcionalmente pode conter o tempo de histórico de partilha). A resposta devolvida ao manipulador de conclusão contém a identificação da mensagem que foi enviada.

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

Substitua-o pelo `<USER_ID>` ID do utilizador ACS do utilizador a adicionar.

Ao adicionar um participante a um fio, a resposta devolvida pode conter erros. Estes erros representam a falta de adição de participantes específicos.

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

`<USER ID>`Substitua-o pelo ID do utilizador dos Serviços de Comunicação do participante que está a ser removido.

## <a name="run-the-code"></a>Executar o código

No Xcode, premir o botão Executar para construir e executar o projeto. Na consola pode ver a saída a partir do código e a saída do madeireiro a partir do ChatClient.


