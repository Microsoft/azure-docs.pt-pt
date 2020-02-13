---
title: 'Quickstart: Criar um hub de eventos usando powerShell - Hubs de eventos Azure'
description: Este início rápido descreve como criar um hub de eventos com o Azure PowerShell e, em seguida, enviar e receber eventos com o SDK .NET Standard.
services: event-hubs
author: spelluru
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 89ec1957e75fa45eef6c7939a77e5cc1b3cf7806
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162128"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Início Rápido: Criar um hub de eventos com o Azure PowerShell

Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Neste início rápido, vai criar um hub de eventos com o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, confirme que tem:

- Subscrição do Azure. Se não tiver uma, [crie uma conta gratuita][] antes de começar.
- [Estúdio Visual 2019.](https://www.visualstudio.com/vs)
- [SDK .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se estiver a utilizar o PowerShell localmente, tem de executar a versão mais recente do PowerShell para concluir este início rápido. Se precisar de instalar ou atualizar, veja [Instalar e Configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Um grupo de recursos é uma coleção lógica dos recursos do Azure e precisa de um grupo de recursos para criar um hub de eventos. 

O exemplo seguinte cria um grupo de recursos na região E.U.A Leste. Substitua `myResourceGroup` pelo nome do grupo de recursos que pretende utilizar:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Assim que o seu grupo de recursos é feito, crie um espaço de nomes dos Hubs de Eventos dentro desse grupo de recursos. Um espaço de nomes dos Hubs de Eventos oferece um nome de domínio completamente qualificado e exclusivo, no qual pode criar o seu hub de eventos. Substitua `namespace_name` por um nome exclusivo para o seu espaço de nomes:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Agora que tem um espaço de nomes dos Hubs de Eventos, crie um hub de eventos nesse espaço de nomes:  
O período permitido para `MessageRetentionInDays` é entre 1 e 7 dias.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Parabéns! Utilizou o Azure PowerShell para criar um espaço de nomes dos Hubs de Eventos e um hub de eventos nesse espaço de nomes. 

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou o espaço de nomes dos Hubs de Eventos e utilizou exemplos de aplicações para enviar e receber eventos do hub de eventos. Para instruções passo a passo para enviar eventos para (ou) receber eventos de um centro de eventos, consulte o **Envio e receba** tutoriais de eventos: 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (apenas enviar)](event-hubs-c-getstarted-send.md)
- [Tempestade Apache (receber apenas)](event-hubs-storm-getstarted-receive.md)


[crie uma conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-az-ps
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-Azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
