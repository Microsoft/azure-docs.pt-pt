---
title: Retransmissão do Azure Conexões Híbridas-solicitações HTTP no nó
description: Escreva uma aplicação de consola Node.js para pedidos HTTP das Ligações Híbridas do Azure Relay em Node.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: clemensv
ms.openlocfilehash: d71386b86bf7133bb73ddce2e65c3b88743009ad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462019"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Introdução aos pedidos HTTP das Ligações Híbridas do Azure Relay em Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste guia de início rápido, você cria aplicativos de remetente e receptor do node. js que enviam e recebem mensagens usando o protocolo HTTP. Os aplicativos usam Conexões Híbridas recurso da retransmissão do Azure. Para saber mais sobre a retransmissão do Azure em geral, consulte [retransmissão do Azure](relay-what-is-it.md). 

Neste guia de início rápido, você executará as seguintes etapas:

1. Crie um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida nesse espaço de nomes, com o portal do Azure.
3. Escrever uma aplicação de consola (serviço de escuta) de servidor para receber mensagens.
4. Escrever uma aplicação de consola (remetente) de cliente para enviar mensagens.
5. Executar aplicativos.

## <a name="prerequisites"></a>Pré-requisitos
- [Node.js](https://nodejs.org/en/).
- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-namespace-using-the-azure-portal"></a>Criar um espaço de nomes com o Portal do Azure
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Criar uma ligação híbrida com o Portal do Azure
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Criar uma aplicação de servidor (serviço de escuta)
Para escutar e receber mensagens do Reencaminhamento, escreva uma aplicação de consola Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Criar uma aplicação cliente (remetente)

Para enviar mensagens para o Reencaminhamento, pode utilizar qualquer cliente HTTP ou escrever uma aplicação de consola Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>Executar as aplicações

1. Execute a aplicação de servidor: a partir de um tipo de linha de comandos `node listener.js` do Node.js.
2. Execute a aplicação de cliente: a partir de um tipo de linha de comandos `node sender.js` do Node.js e introduza algum texto.
3. Certifique-se de que a consola da aplicação de servidor produz o texto que foi introduzido na aplicação cliente.

Parabéns, criou uma aplicação de Ligações Híbridas ponto a ponto com o Node.js!

## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você criou aplicativos de cliente e servidor do node. js que usavam HTTP para enviar e receber mensagens. O recurso Conexões Híbridas da retransmissão do Azure também dá suporte ao uso de Websockets para enviar e receber mensagens. Para saber como usar Websockets com o Conexões Híbridas de retransmissão do Azure, consulte o guia de [início rápido do WebSockets](relay-hybrid-connections-node-get-started.md).

Neste guia de início rápido, você usou o Node. js para criar aplicativos de cliente e servidor. Para saber como gravar aplicativos de cliente e servidor usando .NET Framework, consulte o [início rápido do .net WebSockets](relay-hybrid-connections-dotnet-get-started.md) ou o [início rápido do .net http](relay-hybrid-connections-http-requests-dotnet-get-started.md).
