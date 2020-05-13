---
title: Conexões Híbridas Azure Relay - WebSockets no Nó
description: Escreva uma aplicação de consola Node.js para Websockets de Ligações Híbridas do Azure Relay
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 818db4db082a441877b573fd52361e63becce374
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211895"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Inicie com Ligações Híbridas Relay WebSockets em Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste arranque rápido, cria aplicações de remetente e recetor noNode.js que enviam e recebem mensagens utilizando WebSockets de Ligações Híbridas em Relé Azure. Para saber mais sobre o Azure Relay em geral, consulte [O Relé Azure.](relay-what-is-it.md) 

Neste arranque rápido, tome os seguintes passos: 

1. Crie um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida nesse espaço de nomes, com o portal do Azure.
3. Escrever uma aplicação de consola (serviço de escuta) de servidor para receber mensagens.
4. Escrever uma aplicação de consola (remetente) de cliente para enviar mensagens.
5. Executar aplicações. 

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js.](https://nodejs.org/en/)
- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-namespace"></a>Criar um espaço de nomes
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Criar uma ligação híbrida
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Criar uma aplicação de servidor (serviço de escuta)
Para escutar e receber mensagens do Reencaminhamento, escreva uma aplicação de consola Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Criar uma aplicação cliente (remetente)
Para enviar mensagens para o Reencaminhamento, escreva uma aplicação de consola Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>Executar as aplicações

1. Execute a aplicação de servidor: a partir de um tipo de linha de comandos `node listener.js` do Node.js.
2. Execute a aplicação de cliente: a partir de um tipo de linha de comandos `node sender.js` do Node.js e introduza algum texto.
3. Certifique-se de que a consola da aplicação de servidor produz o texto que foi introduzido na aplicação cliente.

    ![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Parabéns, criou uma aplicação de Ligações Híbridas ponto a ponto com o Node.js!

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, criou aplicações de clientes e servidores Node.js que utilizavam WebSockets para enviar e receber mensagens. A funcionalidade Conexões Híbridas do Azure Relay também suporta a utilização do HTTP para enviar e receber mensagens. Para aprender a utilizar http com ligações híbridas De retransmissão Azure, consulte o início rápido do [Nó.js HTTP](relay-hybrid-connections-http-requests-node-get-started.md).

Neste arranque rápido, usou o Node.js para criar aplicações de clientes e servidores. Para aprender a escrever aplicações de cliente e servidor utilizando .NET Framework, consulte o [.NET WebSockets quickstart](relay-hybrid-connections-dotnet-get-started.md) ou o [.NET HTTP quickstart](relay-hybrid-connections-http-requests-dotnet-get-started.md).


