---
title: Entidades de mensagens de suspender o Azure Service Bus | Documentos da Microsoft
description: Suspender e reativar as entidades de mensagens do Service bus do Azure.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 5d1b2718b360a55d9b1510bcfcb2ddb6492e2830
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436753"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspender e reativar as entidades de mensagens (desativar)

Filas, tópicos e subscrições, podem ser temporariamente suspensa. A suspensão coloca a entidade no estado desativado em que todas as mensagens são mantidas no armazenamento. No entanto, as mensagens não podem ser removidas ou adicionadas e as operações de protocolo respectivos resultar em erros.

A suspensão de uma entidade é geralmente feita por motivos de administrativos urgentes. Um cenário é tendo implantado um recetor com falhas que recebe as mensagens da fila, ocorre uma falha de processamento e ainda incorretamente conclui as mensagens e remove-os. Se esse comportamento é diagnosticado, a fila pode ser desabilitada para receber até que o código corrigido é implementado e ainda mais a perda de dados causada pelo código com falhas pode ser impedida.

Uma suspensão ou reativação pode ser efetuada pelo utilizador ou pelo sistema. O sistema apenas suspende entidades devido a razões administrativas graves como atingir a limite de gastos de subscrição. Entidades de sistema desativado não podem ser reativadas pelo utilizador, mas são restauradas quando estar resolvida a causa da suspensão.

No portal, o **propriedades** secção da entidade respectivos permite alterar o estado; a captura de ecrã seguinte mostra o botão de alternar para uma fila:

![][1]

O portal permite apenas desativar completamente as filas. Também pode desativar a enviar e receber operações separadamente com o Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) APIs no SDK do .NET Framework, ou com um modelo do Azure Resource Manager através da CLI do Azure ou do Azure PowerShell.

## <a name="suspension-states"></a>Estados de suspensão

Os Estados que podem ser definidos para uma fila são:

-   **Active Directory**: A fila está ativa.
-   **Desativado**: A fila está suspensa.
-   **SendDisabled**: A fila está parcialmente suspenso, com receive que está a ser permitido.
-   **ReceiveDisabled**: A fila parcialmente está suspenso, com o envio a ser permitido.

Para as subscrições e tópicos, apenas **Active Directory** e **desativado** pode ser definido.

O [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) enumeração também define um conjunto de Estados de transição que só pode ser definido pelo sistema. O comando do PowerShell para desativar uma fila é mostrado no exemplo a seguir. O comando de reativação é equivalente, definindo `Status` para **Active**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

