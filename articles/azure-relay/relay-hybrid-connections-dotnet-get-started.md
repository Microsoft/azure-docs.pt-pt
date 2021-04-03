---
title: Conexões híbridas Azure Relay - WebSockets em .NET
description: Escreva uma aplicação de consola C# para WebSockets de conexões híbridas Azure Relay.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: bf22b8b11dc386644803b43ee4e3a51d04b70419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90527433"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Introdução aos Websockets das Ligações Híbridas do Azure Relay em .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste arranque rápido, cria aplicações de remetente .NET e recetor que enviam e recebem mensagens utilizando WebSockets de Conexões Híbridas no Azure Relay. Para saber mais sobre a Azure Relay em geral, consulte [a Azure Relay](relay-what-is-it.md). 

Neste arranque rápido, você dá os seguintes passos:

1. Crie um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida nesse espaço de nomes, com o portal do Azure.
3. Escrever uma aplicação de consola (serviço de escuta) de servidor para receber mensagens.
4. Escrever uma aplicação de consola (remetente) de cliente para enviar mensagens.
5. Executar candidaturas. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* [Visual Studio 2015 ou mais tarde](https://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017.
* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-namespace"></a>Criar um espaço de nomes
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Criar uma ligação híbrida
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Criar uma aplicação de servidor (serviço de escuta)
No Visual Studio, crie uma aplicação de consola C# para ouvir e receber mensagens do reencaminhamento.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Criar uma aplicação cliente (remetente)
No Visual Studio, crie uma aplicação de consola C# para enviar mensagens para o reencaminhamento.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Executar as aplicações
1. Execute a aplicação de servidor.
2. Execute a aplicação cliente e introduza algum texto.
3. Certifique-se de que a consola da aplicação de servidor mostra o texto que foi introduzido na aplicação cliente.

    ![Janelas de consola testando as aplicações do servidor e do cliente.](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Parabéns, criaste uma aplicação completa da Hybrid Connections!

## <a name="next-steps"></a>Passos seguintes
Neste quickstart, criou aplicações de clientes .NET e servidor que utilizavam WebSockets para enviar e receber mensagens. A funcionalidade Ligações Híbridas do Azure Relay também suporta a utilização de HTTP para enviar e receber mensagens. Para aprender a utilizar HTTP com Ligações Híbridas Azure Relay, consulte o [quickstart HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

Neste arranque rápido, utilizou o Quadro .NET para criar aplicações de clientes e servidores. Para aprender a escrever aplicações de clientes e servidor usando Node.js, consulte o [ arranque rápido doNode.js WebSockets](relay-hybrid-connections-node-get-started.md) ou o arranque rápidoNode.js [ HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

