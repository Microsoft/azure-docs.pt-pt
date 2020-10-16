---
title: Conexões híbridas Azure Relay - pedidos HTTP em Nó
description: Escreva uma aplicação de consola Node.js para pedidos HTTP das Ligações Híbridas do Azure Relay em Node.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 249b4fa231cd54a1a8054b32985ed0e48fcc16f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263764"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Introdução aos pedidos HTTP das Ligações Híbridas do Azure Relay em Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste arranque rápido, cria-se Node.js aplicações de remetente e recetor que enviam e recebem mensagens utilizando o protocolo HTTP. As aplicações utilizam a função Ligações Híbridas do Azure Relay. Para saber mais sobre a Azure Relay em geral, consulte [a Azure Relay](relay-what-is-it.md). 

Neste arranque rápido, você dá os seguintes passos:

1. Crie um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida nesse espaço de nomes, com o portal do Azure.
3. Escrever uma aplicação de consola (serviço de escuta) de servidor para receber mensagens.
4. Escrever uma aplicação de consola (remetente) de cliente para enviar mensagens.
5. Executar candidaturas.

## <a name="prerequisites"></a>Pré-requisitos
- [Node.js. ](https://nodejs.org/en/)
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
Neste arranque rápido, criou Node.js aplicações de clientes e servidores que utilizavam HTTP para enviar e receber mensagens. A funcionalidade Conexões Híbridas do Azure Relay também suporta a utilização de WebSockets para enviar e receber mensagens. Para aprender a utilizar WebSockets com conexões híbridas Azure Relay, consulte o arranque rápido dos [WebSockets](relay-hybrid-connections-node-get-started.md).

Neste arranque rápido, usou Node.js para criar aplicações de clientes e servidores. Para aprender a escrever aplicações de clientes e servidores utilizando o [.NET WebSockets quickstart](relay-hybrid-connections-dotnet-get-started.md) ou o [quickstart .NET HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).
