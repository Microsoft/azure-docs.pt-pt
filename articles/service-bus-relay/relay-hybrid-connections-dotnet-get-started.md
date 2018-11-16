---
title: Introdução aos Websockets de Ligações Híbridas do Azure Relay em .NET | Microsoft Docs
description: Escreva uma aplicação de consola C# para Websockets de Ligações Híbridas do Azure Relay.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 63961e978056a20e697872c0c1d892efaadcc627
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612079"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Introdução ao reencaminhamento WebSockets de ligações híbridas no .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste início rápido, vai criar aplicativos de remetente e o receptor de .NET que enviar e recebem mensagens através de WebSockets de ligações híbridas de reencaminhamento do Azure. Para saber mais sobre o reencaminhamento do Azure em geral, veja [reencaminhamento do Azure](relay-what-is-it.md). 

Neste início rápido, siga os passos seguintes:

1. Crie um espaço de nomes de Reencaminhamento com o Portal do Azure.
2. Crie uma ligação híbrida nesse espaço de nomes, com o portal do Azure.
3. Escrever uma aplicação de consola (serviço de escuta) de servidor para receber mensagens.
4. Escrever uma aplicação de consola (remetente) de cliente para enviar mensagens.
5. Execute aplicações. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* [Visual Studio 2015 ou posterior](http://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017.
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

    ![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Parabéns, criou uma aplicação de Ligações Híbridas ponto a ponto.

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, criou aplicativos de cliente e servidor de .NET que utilizado WebSockets para enviar e receber mensagens. A funcionalidade ligações híbridas do reencaminhamento do Azure também suporta a utilização de HTTP para enviar e receber mensagens. Para saber como utilizar HTTP com ligações híbridas do reencaminhamento do Azure, veja a [guia de introdução do HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

Neste início rápido, utilizou .NET Framework para criar aplicações de cliente e servidor. Para saber como escrever aplicativos de cliente e servidor com node. js, veja a [guia de introdução de WebSockets node. js](relay-hybrid-connections-node-get-started.md) ou o [guia de introdução do node. js HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

