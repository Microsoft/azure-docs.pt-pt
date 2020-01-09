---
title: Retransmissão do Azure Conexões Híbridas-solicitações HTTP no .NET
description: Escreva uma aplicação de consola C# para pedidos HTTP das Ligações Híbridas do Azure Relay em .NET.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 7c984876c4338b4f6802ba55752c8f612c390e94
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355157"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Introdução aos pedidos HTTP das Ligações Híbridas do Azure Relay em .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste guia de início rápido, você cria aplicativos de remetente e receptor do .NET que enviam e recebem mensagens usando o protocolo HTTP. Os aplicativos usam Conexões Híbridas recurso da retransmissão do Azure. Para saber mais sobre a retransmissão do Azure em geral, consulte [retransmissão do Azure](relay-what-is-it.md). 

Neste guia de início rápido, você executará as seguintes etapas:

1. Crie um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida nesse espaço de nomes, com o portal do Azure.
3. Escrever uma aplicação de consola (serviço de escuta) de servidor para receber mensagens.
4. Escrever uma aplicação de consola (remetente) de cliente para enviar mensagens.
5. Executar aplicativos. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017.
* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-namespace"></a>Criar um espaço de nomes
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Criar uma ligação híbrida
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Criar uma aplicação de servidor (serviço de escuta)
No Visual Studio, crie uma aplicação de consola C# para ouvir e receber mensagens do reencaminhamento.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Criar uma aplicação cliente (remetente)
No Visual Studio, crie uma aplicação de consola C# para enviar mensagens para o reencaminhamento.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Executar as aplicações
1. Execute a aplicação de servidor. Verá o seguinte texto na janela da consola:

    ```
    Online
    Server listening
    ```
1. Execute a aplicação cliente. Verá `hello!` na janela do cliente. O cliente enviou uma solicitação HTTP para o servidor e o servidor respondeu com um `hello!`. 
3. Agora, para fechar as janelas da consola, prima **ENTER** em ambas as janelas da consola. 

Parabéns, você criou um aplicativo de Conexões Híbridas completo!

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou aplicativos de cliente e servidor .NET que usavam HTTP para enviar e receber mensagens. O recurso Conexões Híbridas da retransmissão do Azure também dá suporte ao uso de Websockets para enviar e receber mensagens. Para saber como usar Websockets com o Conexões Híbridas de retransmissão do Azure, consulte o guia de [início rápido do WebSockets](relay-hybrid-connections-dotnet-get-started.md).

Neste guia de início rápido, você usou .NET Framework para criar aplicativos de cliente e servidor. Para saber como gravar aplicativos de cliente e de servidor usando o Node. js, consulte o guia de [início rápido do nó. js WebSockets](relay-hybrid-connections-node-get-started.md) ou o [início rápido do http do node. js](relay-hybrid-connections-http-requests-dotnet-get-started.md).
