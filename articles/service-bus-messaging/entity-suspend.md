---
title: Azure Service Bus - suspenda as entidades de mensagens
description: Este artigo explica como suspender e reativar temporariamente as entidades de mensagens Azure Service Bus (filas, tópicos e subscrições).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2dad0b774f271ed719ca09b1e749559d5e1868bd
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078870"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspender e reativar entidades de mensagens (desativar)

Filas, tópicos e subscrições podem ser temporariamente suspensos. A suspensão coloca a entidade num estado desativado no qual todas as mensagens são mantidas no armazenamento. No entanto, as mensagens não podem ser removidas ou adicionadas, e as respetivas operações de protocolo produzem erros.

A suspensão de uma entidade é normalmente feita por razões administrativas urgentes. Um dos cenários é ter implantado um recetor defeituoso que retira mensagens da fila, falha no processamento e, no entanto, completa incorretamente as mensagens e as remove. Se esse comportamento for diagnosticado, a fila pode ser desativada para receber até que o código corrigido seja implementado e a perda de dados causada pelo código defeituoso pode ser prevenida.

Uma suspensão ou reativação pode ser realizada pelo utilizador ou pelo sistema. O sistema só suspende as entidades por razões administrativas graves, como bater o limite de gastos de subscrição. As entidades com deficiência do sistema não podem ser reativadas pelo utilizador, mas são restauradas quando a causa da suspensão foi abordada.

No portal, a secção **De Visão** Geral da respetiva entidade permite alterar o estado; o estado atual é apresentado sob **o Estado** como uma hiperligação.

A imagem que se segue mostra os estados disponíveis para os quais a entidade pode ser alterada selecionando a hiperligação: 

![Screenshot da funcionalidade Service Bus dentro do Overview para alterar a opção estado da entidade.][1]

O portal só permite filas completamente incapacitantes. Também pode desativar as operações de envio e receção separadamente utilizando as APIs de [Nome de](/dotnet/api/microsoft.servicebus.namespacemanager) Serviços de ônibus no SDK.NET Framework, ou com um modelo de Gestor de Recursos Azure através do Azure CLI ou da Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Estados suspensos

Os estados que podem ser definidos para uma fila são:

-   **Ativo**: A fila está ativa.
-   **Desativado**: A fila está suspensa.
-   **SendDisabled**: A fila está parcialmente suspensa, sendo permitida a receção.
-   **Receber :** A fila está parcialmente suspensa, sendo permitido o envio.

Para subscrições e tópicos, apenas **o Ative** e o Disabled podem ser **definidos.**

A enumeração [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) também define um conjunto de estados transitórios que só podem ser definidos pelo sistema. O comando PowerShell para desativar uma fila é mostrado no exemplo seguinte. O comando de reativação é equivalente, definindo `Status` para **Ative**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

