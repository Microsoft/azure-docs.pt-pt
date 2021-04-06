---
title: Quickstart - Azure SignalR Service REST API
description: Aprenda a utilizar a API REST com o Serviço Azure SignalR seguindo amostras. Encontre detalhes da especificação da API REST.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 2613f91e8c7f1ad3a05792a9a165f4560c09a637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874530"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Início rápido: Transmitir mensagens em tempo real a partir da aplicação de consola

O Azure SignalR Service apresenta a [API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) para suportar o servidor para cenários de comunicação de cliente, como a difusão. Pode escolher qualquer linguagem de programação que possa fazer a chamada à API REST. Pode publicar mensagens para todos os clientes ligados, um cliente específico por nome ou um grupo de clientes.

Neste início rápido, irá aprender a enviar mensagens a partir de uma aplicação de linha de comandos para aplicações de cliente ligadas em C#.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pode ser executado no macOS, Windows ou Linux.

* [SDK .NET Core](https://www.microsoft.com/net/download/core)
* Um editor de texto ou editor de código da sua preferência.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsapi)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com/> com a sua conta do Azure.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsapi)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsapi)

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Enquanto o serviço está a ser implementado, vamos passar para preparar o código. Clone a [aplicação de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples.git), defina a cadeia de ligação do Serviço SignalR e execute a aplicação localmente.

1. Abra uma janela de terminal do git. Alterar para uma pasta em que quer clonar o projeto de exemplo.

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsapi)

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

Este exemplo é uma aplicação de consola que mostra a utilização do Azure SignalR Service. Ele oferece dois modos:

- Modo de Servidor: utilize comandos simples para chamar a API REST do Azure SignalR Service.
- Modo de Cliente: ligar ao Azure SignalR Service e receber mensagens do servidor.

Também pode encontrar como gerar um token de acesso para autenticar com o Azure SignalR Service.

### <a name="build-the-executable-file"></a>Criar o ficheiro executável

Utilizamos o macOS osx.10.13-x64 como exemplo. Pode encontrar a [referência](/dotnet/core/rid-catalog) sobre como criar noutras plataformas.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Iniciar um cliente

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Iniciar um servidor

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsapi)

## <a name="run-the-sample-without-publishing"></a>Executar o exemplo sem publicar

Também pode executar o comando abaixo para iniciar um servidor ou cliente

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Utilize segredos do utilizador para especificar a Cadeia de Ligação

Pode executar `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` no diretório de raiz do exemplo. Depois disso, deixará de precisar da opção `-c "<ConnectionString>"`.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsapi)

## <a name="usage"></a>Utilização

Depois de o servidor ter começado, utilize o comando para enviar mensagem:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Pode iniciar vários clientes com nomes de cliente diferentes.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsapi)

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Integração com serviços de terceiros

O serviço Azure SignalR permite que os serviços de terceiros sejam integrados no sistema.

### <a name="definition-of-technical-specifications"></a> Definição das especificações técnicas

A tabela seguinte mostra todas as versões das APIs REST suportadas até à data. Também pode encontrar o ficheiro de definição para cada versão específica

Versão | Estado da API | Porta | Específico
--- | --- | --- | ---
`1.0-preview` | Disponível | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Disponível | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

A lista de APIs disponíveis para cada versão específica está disponível na lista seguinte.

API | 1.0 pré-visualização | 1.0
--- | --- | ---
[Difundir para todos](#broadcast) | **&#x2713;** | **&#x2713;**
[Difundir para um grupo](#broadcast-group) | **&#x2713;** | **&#x2713;**
Difundir para alguns grupos | **&#x2713;** (Depreciado) | `N / A`
[Enviar a um utilizador](#send-user) | **&#x2713;** | **&#x2713;**
Enviar para alguns utilizadores | **&#x2713;** (Depreciado) | `N / A`
[A adicionar um utilizador a um grupo](#add-user-to-group) | `N / A` | **&#x2713;**
[A remover um utilizador de um grupo](#remove-user-from-group) | `N / A` | **&#x2713;**
[Verifique a existência do utilizador](#check-user-existence) | `N / A` | **&#x2713;**
[Remover um utilizador de todos os grupos](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Enviar para uma ligação](#send-connection) | `N / A` | **&#x2713;**
[Adicionar uma ligação a um grupo](#add-connection-to-group) | `N / A` | **&#x2713;**
[Remover uma ligação de um grupo](#remove-connection-from-group) | `N / A` | **&#x2713;**
[Fechar uma ligação com o cliente](#close-connection) | `N / A` | **&#x2713;**
[Estado de funcionamento dos serviços](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Difundir para todos

Versão | Método HTTP da API | URL do Pedido | Corpo do pedido
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Mesmo que acima

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Difundir para um grupo

Versão | Método HTTP da API | URL do Pedido | Corpo do pedido
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Mesmo que acima

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>Envio para um utilizador

Versão | Método HTTP da API | URL do Pedido | Corpo do pedido
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Mesmo que acima

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>A adicionar um utilizador a um grupo

Versão | Método HTTP da API | URL do Pedido
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>A remover um utilizador de um grupo

Versão | Método HTTP da API | URL do Pedido
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Verifique a existência do utilizador em um grupo

Versão da API | Método HTTP da API | URL do Pedido
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Código de Estado da Resposta | Description
---|---
`200` | O utilizador existe
`404` | O utilizador não existe

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Remover um utilizador de todos os grupos

Versão da API | Método HTTP da API | URL do Pedido
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Enviar mensagem para uma ligação

Versão da API | Método HTTP da API | URL do Pedido | Corpo do Pedido
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Adicionar uma ligação a um grupo

Versão da API | Método HTTP da API | URL do Pedido
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Remover uma ligação de um grupo

Versão da API | Método HTTP da API | URL do Pedido
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>Fechar uma ligação com o cliente

Versão da API | Método HTTP da API | URL do Pedido
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Service Health

Versão da API | Método HTTP da API | URL do Pedido
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Código de Estado da Resposta | Description
---|---
`200` | Bom serviço
`5xx` | Erro de serviço

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsapi)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsapi)

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a usar a API REST para transmitir mensagem em tempo real do Serviço SignalR aos clientes. Em seguida, saiba mais sobre como desenvolver e implementar Funções Azure com ligação SignalR Service, que é construída em cima da API REST.

> [!div class="nextstepaction"]
> [Desenvolver funções Azure utilizando encadernações do Serviço Azure SignalR](signalr-quickstart-azure-functions-csharp.md)
