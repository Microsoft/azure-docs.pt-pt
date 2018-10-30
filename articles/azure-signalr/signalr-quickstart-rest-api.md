---
title: Início rápido - API REST do Azure SignalR Service | Microsoft Docs
description: Um início rápido para utilizar a API REST do Azure SignalR Service.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 36fb87d3255149c041c4288d13c54eaff8425e06
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024361"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Início rápido: Transmitir mensagens em tempo real a partir da aplicação de consola

O Azure SignalR Service apresenta a [API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) para suportar o servidor para cenários de comunicação de cliente, como a difusão. Pode escolher qualquer linguagem de programação que possa fazer a chamada à API REST. Pode publicar mensagens para todos os clientes ligados, um cliente específico por nome ou um grupo de clientes.

Neste início rápido, irá aprender a enviar mensagens a partir de uma aplicação de linha de comandos para aplicações de cliente ligadas em C#.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pode ser executado no macOS, Windows ou Linux.
* [SDK .NET Core](https://www.microsoft.com/net/download/core)
* Um editor de texto ou editor de código da sua preferência.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com/> com a sua conta do Azure.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Enquanto o serviço está a ser implementado, vamos passar para preparar o código. Clone a [aplicação de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples.git), defina a cadeia de ligação do Serviço SignalR e execute a aplicação localmente.

1. Abra uma janela de terminal do git. Alterar para uma pasta em que quer clonar o projeto de exemplo.

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

Este exemplo é uma aplicação de consola que mostra a utilização do Azure SignalR Service. Ele oferece dois modos:

- Modo de Servidor: utilize comandos simples para chamar a API REST do Azure SignalR Service.
- Modo de Cliente: ligar ao Azure SignalR Service e receber mensagens do servidor.

Também pode encontrar como gerar um token de acesso para autenticar com o Azure SignalR Service.

### <a name="build-the-executable-file"></a>Criar o ficheiro executável

Utilizamos o macOS osx.10.13-x64 como exemplo. Pode encontrar a [referência](https://docs.microsoft.com/dotnet/core/rid-catalog) sobre como criar noutras plataformas.
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

## <a name="usage"></a>Utilização

Depois de o servidor ser iniciado, utilize o comando para enviar mensagem

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Pode iniciar vários clientes com nomes de cliente diferentes.

## <a name="usage"> </a> Integração com serviços de terceiros
O serviço Azure SignalR permite que os serviços de terceiros sejam integrados no sistema.
### <a name="usage"> </a> Definição das especificações técnicas
A tabela seguinte mostra todas as versões das APIs REST suportadas até à data. Também pode encontrar o ficheiro de definição para cada versão específica

Versão | Estado da API | Porta | Específico
--- | --- | --- | ---
`1.0-preview` | Disponível | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Disponível | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

A lista de APIs disponíveis para cada versão específica está disponível na lista seguinte.

API | `1.0-preview` | `1.0`
--- | --- | ---
[Difundir para todos](#broadcast) | :heavy_check_mark: | :heavy_check_mark:
[Difundir para um grupo](#broadcast-group) | :heavy_check_mark: | :heavy_check_mark:
Difundir para alguns grupos | :heavy_check_mark: (Preterido) | `N / A`
[Enviar para utilizadores específicos](#send-user) | :heavy_check_mark: | :heavy_check_mark:
Enviar para alguns utilizadores | :heavy_check_mark: (Preterido) | `N / A`
[A adicionar um utilizador a um grupo](#add-user-to-group) | `N / A` | :heavy_check_mark:
[A remover um utilizador de um grupo](#remove-user-from-group) | `N / A` | :heavy_check_mark:

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
### <a name="sending-to-specific-users"></a>A enviar para utilizadores específicos
Versão | Método HTTP da API | URL do Pedido | Corpo do pedido
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Mesmo que acima

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>A adicionar um utilizador a um grupo
Versão | Método HTTP da API | URL do Pedido
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>A remover um utilizador de um grupo
Versão | Método HTTP da API | URL do Pedido
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
