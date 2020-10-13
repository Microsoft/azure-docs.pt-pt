---
title: Azure Service Bus - suspenda as entidades de mensagens
description: Este artigo explica como suspender e reativar temporariamente as entidades de mensagens Azure Service Bus (filas, tópicos e subscrições).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575263"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspender e reativar entidades de mensagens (desativar)

Filas, tópicos e subscrições podem ser temporariamente suspensos. A suspensão coloca a entidade num estado desativado no qual todas as mensagens são mantidas no armazenamento. No entanto, as mensagens não podem ser removidas ou adicionadas, e as respetivas operações de protocolo produzem erros.

A suspensão de uma entidade é normalmente feita por razões administrativas urgentes. Um dos cenários é ter implantado um recetor defeituoso que retira mensagens da fila, falha no processamento e, no entanto, completa incorretamente as mensagens e as remove. Se esse comportamento for diagnosticado, a fila pode ser desativada para receber até que o código corrigido seja implementado e a perda de dados causada pelo código defeituoso pode ser prevenida.

Uma suspensão ou reativação pode ser realizada pelo utilizador ou pelo sistema. O sistema só suspende as entidades por razões administrativas graves, como bater o limite de gastos de subscrição. As entidades com deficiência do sistema não podem ser reativadas pelo utilizador, mas são restauradas quando a causa da suspensão foi abordada.

## <a name="queue-status"></a>Estado da fila 
Os estados que podem ser definidos para uma fila são:

-   **Ativo**: A fila está ativa.
-   **Desativado**: A fila está suspensa. É equivalente a definir **o SendDisabled** e **o ReceiveDisabled**. 
-   **SendDisabled**: A fila está parcialmente suspensa, sendo permitida a receção.
-   **Receber :** A fila está parcialmente suspensa, sendo permitido o envio.

### <a name="change-the-queue-status-in-the-azure-portal"></a>Alterar o estado da fila no portal Azure: 

1. No portal Azure, navegue para o seu espaço de nomes de Service Bus. 
1. Selecione a fila para a qual pretende alterar o estado. Vê-se filas no painel inferior no meio. 
1. Na página **de Fila de Autocarros** de Serviço, consulte o estado atual da fila como uma hiperligação. Se a **visão geral** não for selecionada no menu esquerdo, selecione-a para ver o estado da fila. Selecione o estado atual da fila para alterá-la. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Selecione o estado da fila":::
4. Selecione o novo estado para a fila e selecione **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Selecione o estado da fila":::
    
O portal só permite filas completamente incapacitantes. Também pode desativar as operações de envio e receção separadamente utilizando as APIs de [Nome de](/dotnet/api/microsoft.servicebus.namespacemanager) Serviços de ônibus no SDK.NET Framework, ou com um modelo de Gestor de Recursos Azure através do Azure CLI ou da Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Alterar o estado da fila utilizando o Azure PowerShell
O comando PowerShell para desativar uma fila é mostrado no exemplo seguinte. O comando de reativação é equivalente, definindo `Status` para **Ative**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Estado do tópico
Alterar o estado do tópico no portal Azure é semelhante à alteração do estado de uma fila. Quando seleciona o estado atual do tópico, vê a página seguinte que lhe permite alterar o estado. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Selecione o estado da fila":::

Os estados que podem ser definidos para um tópico são:
- **Ativo**: O tópico está ativo.
- **Desativado**: O tópico está suspenso.
- **SendDisabled**: Mesmo efeito **que desativado**.

## <a name="subscription-status"></a>Estado de subscrição
Alterar o estado de subscrição no portal Azure é semelhante à alteração do estado de um tópico ou de uma fila. Quando seleciona o estado atual da subscrição, vê a página seguinte que lhe permite alterar o estado. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Selecione o estado da fila":::

Os estados que podem ser definidos para um tópico são:
- **Ativo**: O tópico está ativo.
- **Desativado**: O tópico está suspenso.
- **Receber Indisabled**: O mesmo efeito **que o deficiente**.

## <a name="other-statuses"></a>Outros estatutos
A enumeração [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) também define um conjunto de estados transitórios que só podem ser definidos pelo sistema. 


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

