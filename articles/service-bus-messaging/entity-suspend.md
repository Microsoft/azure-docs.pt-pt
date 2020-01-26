---
title: Azure Service Bus - suspender entidades de mensagens
description: Este artigo explica como suspender e reativar temporariamente as entidades de mensagens azure service bus (filas, tópicos e subscrições).
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7386932f19eee064926184eb17f5e92e30add98e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760390"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspender e reativar entidades de mensagens (desativar)

Filas, tópicos e subscrições podem ser temporariamente suspensas. A suspensão coloca a entidade num estado de desativação em que todas as mensagens são mantidas no armazenamento. No entanto, as mensagens não podem ser removidas ou adicionadas e as respetivas operações protocolares produzem erros.

A suspensão de uma entidade é normalmente feita por razões administrativas urgentes. Um dos cenários é ter implementado um recetor defeituoso que retira mensagens da fila, falha no processamento e, no entanto, completa incorretamente as mensagens e as remove. Se esse comportamento for diagnosticado, a fila pode ser desativada para receber até que o código corrigido seja implementado e pode ser evitada uma nova perda de dados causada pelo código defeituoso.

Uma suspensão ou reativação pode ser realizada pelo utilizador ou pelo sistema. O sistema só suspende entidades por razões administrativas graves, como o de atingir o limite de despesas por subscrição. As entidades com deficiência do sistema não podem ser reativadas pelo utilizador, mas são restauradas quando a causa da suspensão tiver sido abordada.

No portal, a secção **Propriedades** da respetiva entidade permite alterar o Estado; a seguinte imagem mostra o alternância para uma fila:

![][1]

O portal só permite filas completamente incapacitantes. Também pode desativar as operações de envio e receção separadamente utilizando as APIs do Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) no .NET Framework SDK, ou com um modelo de Gestor de Recursos Azure através do Azure CLI ou do Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Estados de suspensão

Os estados que podem ser definidos para uma fila são:

-   **Ativo**: A fila está ativa.
-   **Desativado**: A fila está suspensa.
-   **EnvioDeficiente :** A fila está parcialmente suspensa, sendo permitida a receção.
-   **RecepçãoDeficiente**: A fila está parcialmente suspensa, sendo o envio permitido.

Para subscrições e tópicos, apenas o **Ative** and **Disabled** pode ser definido.

A enumeração [do Status da Entidade](/dotnet/api/microsoft.servicebus.messaging.entitystatus) também define um conjunto de estados transitórios que só podem ser definidos pelo sistema. O comando PowerShell para desativar uma fila é mostrado no seguinte exemplo. O comando de reativação é equivalente, definindo `Status` a **Ativo**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do barramento de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

