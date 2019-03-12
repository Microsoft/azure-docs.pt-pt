---
title: Introdução aos Websockets de Ligações Híbridas do Azure Relay em Node | Microsoft Docs
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
ms.openlocfilehash: b4864673e25ba4f5a1f2e8629e0889863051bc07
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764868"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Introdução ao reencaminhamento WebSockets de ligações híbridas no node. js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste início rápido, vai criar aplicações de remetente e o receptor de node. js que enviarem e recebem mensagens através de WebSockets de ligações híbridas de reencaminhamento do Azure. Para saber mais sobre o reencaminhamento do Azure em geral, veja [reencaminhamento do Azure](relay-what-is-it.md). 

Neste início rápido, siga os passos seguintes: 

1. Crie um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida nesse espaço de nomes, com o portal do Azure.
3. Escrever uma aplicação de consola (serviço de escuta) de servidor para receber mensagens.
4. Escrever uma aplicação de consola (remetente) de cliente para enviar mensagens.
5. Execute aplicações. 

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/).
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

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, criou aplicações de cliente e servidor node. js que utilizado WebSockets para enviar e receber mensagens. A funcionalidade ligações híbridas do reencaminhamento do Azure também suporta a utilização de HTTP para enviar e receber mensagens. Para saber como utilizar HTTP com ligações híbridas do reencaminhamento do Azure, veja a [guia de introdução do node. js HTTP](relay-hybrid-connections-http-requests-node-get-started.md).

Neste início rápido, utilizou node. js para criar aplicações de cliente e servidor. Para saber como escrever aplicativos de cliente e servidor usando o .NET Framework, consulte a [guia de introdução do .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md) ou o [guia de introdução do .NET HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).


