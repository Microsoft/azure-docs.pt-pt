---
title: Conexões Híbridas Azure Relay - HTTP solicita em .NET
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75355157"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Introdução aos pedidos HTTP das Ligações Híbridas do Azure Relay em .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste arranque rápido, cria aplicações de remetente .NET e recetores que enviam e recebem mensagens utilizando o protocolo HTTP. As aplicações utilizam a funcionalidade Ligações Híbridas do Relé Azure. Para saber mais sobre o Azure Relay em geral, consulte [O Relé Azure.](relay-what-is-it.md) 

Neste arranque rápido, tome os seguintes passos:

1. Crie um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida nesse espaço de nomes, com o portal do Azure.
3. Escrever uma aplicação de consola (serviço de escuta) de servidor para receber mensagens.
4. Escrever uma aplicação de consola (remetente) de cliente para enviar mensagens.
5. Executar aplicações. 

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
1. Execute a aplicação cliente. Verá `hello!` na janela do cliente. O cliente enviou um pedido HTTP para o `hello!`servidor, e o servidor respondeu com um . 
3. Agora, para fechar as janelas da consola, prima **ENTER** em ambas as janelas da consola. 

Parabéns, criou uma aplicação completa de Ligações Híbridas!

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou aplicações de cliente e servidor .NET que utilizavam http para enviar e receber mensagens. A funcionalidade Ligações Híbridas do Azure Relay também suporta a utilização de WebSockets para enviar e receber mensagens. Para aprender a utilizar webSockets com ligações híbridas De retransmissão Azure, consulte o arranque rápido das [WebSockets](relay-hybrid-connections-dotnet-get-started.md).

Neste arranque rápido, utilizou a .NET Framework para criar aplicações de clientes e servidores. Para aprender a escrever aplicações de cliente e servidor utilizando o Node.js, consulte o Início rápido do [Node.js WebSockets](relay-hybrid-connections-node-get-started.md) ou o [Quickstart Node.js HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).
