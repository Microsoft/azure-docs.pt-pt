---
title: Azure Service Bus - suspenda as entidades de mensagens
description: Este artigo explica como suspender e reativar temporariamente as entidades de mensagens Azure Service Bus (filas, tópicos e subscrições).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543056"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspender e reativar entidades de mensagens (desativar)

Filas, tópicos e subscrições podem ser temporariamente suspensos. A suspensão coloca a entidade num estado desativado no qual todas as mensagens são mantidas no armazenamento. No entanto, as mensagens não podem ser removidas ou adicionadas, e as respetivas operações de protocolo produzem erros.

Pode querer suspender uma entidade por razões administrativas urgentes. Por exemplo, um recetor defeituoso retira mensagens da fila, falha no processamento e, no entanto, completa incorretamente as mensagens e remove-as. Neste caso, pode querer desativar a fila para receber até corrigir e implementar o código. 

Uma suspensão ou reativação pode ser realizada pelo utilizador ou pelo sistema. O sistema só suspende as entidades por razões administrativas graves, como bater o limite de gastos de subscrição. As entidades com deficiência do sistema não podem ser reativadas pelo utilizador, mas são restauradas quando a causa da suspensão foi abordada.

## <a name="queue-status"></a>Estado da fila 
Os estados que podem ser definidos para uma **fila** são:

-   **Ativo** : A fila está ativa. Pode enviar mensagens e receber mensagens da fila. 
-   **Desativado** : A fila está suspensa. É equivalente a definir **o SendDisabled** e **o ReceiveDisabled**. 
-   **SendDisabled** : Não é possível enviar mensagens para a fila, mas pode receber mensagens daí enviadas. Receberá uma exceção se tentar enviar mensagens para a fila. 
-   **Receber :Pode** enviar mensagens para a fila, mas não pode receber mensagens daí enviadas. Receberá uma exceção se tentar receber mensagens para a fila.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Alterar o estado da fila no portal Azure: 

1. No portal Azure, navegue para o seu espaço de nomes de Service Bus. 
1. Selecione a fila para a qual pretende alterar o estado. Vê-se filas no painel inferior no meio. 
1. Na página **de Fila de Autocarros** de Serviço, consulte o estado atual da fila como uma hiperligação. Se a **visão geral** não for selecionada no menu esquerdo, selecione-a para ver o estado da fila. Selecione o estado atual da fila para alterá-la. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Selecione o estado da fila":::
4. Selecione o novo estado para a fila e selecione **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Definir estado da fila":::
    
Também pode desativar as operações de envio e receção utilizando as APIs do Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) no .NET SDK, ou utilizando um modelo de Gestor de Recursos Azure através do Azure CLI ou da Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Alterar o estado da fila utilizando o Azure PowerShell
O comando PowerShell para desativar uma fila é mostrado no exemplo seguinte. O comando de reativação é equivalente, definindo `Status` para **Ative**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Estado do tópico
Pode alterar o estado de tópico no portal Azure. Selecione o estado atual do tópico para ver a página seguinte, que lhe permite alterar o estado. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Alterar o estado do tópico":::

Os estados que podem ser definidos para um **tópico** são:
- **Ativo** : O tópico está ativo. Pode enviar mensagens para o tema. 
- **Desativado** : O tópico está suspenso. Não pode enviar mensagens para o assunto. 
- **SendDisabled** : Mesmo efeito **que desativado**. Não pode enviar mensagens para o assunto. Receberá uma exceção se tentar enviar mensagens para o tema. 

## <a name="subscription-status"></a>Estado de subscrição
Pode alterar o estado de subscrição no portal Azure. Selecione o estado atual da subscrição para ver a página seguinte, que lhe permite alterar o estado. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Alterar o estado de subscrição":::

Os estados que podem ser definidos para uma **subscrição** são:
- **Ativo** : A subscrição está ativa. Pode receber mensagens frm da subscrição.
- **Desativado** : A subscrição está suspensa. Não pode receber mensagens da subscrição. 
- **Receber Indisabled** : O mesmo efeito **que o deficiente**. Não pode receber mensagens da subscrição. Receberá uma exceção se tentar receber mensagens para a subscrição.

| Estado do tópico | Estado de subscrição | Comportamento | 
| ------------ | ------------------- | -------- | 
| Ativo | Ativo | Pode enviar mensagens para o tema e receber mensagens da subscrição. | 
| Ativo | Desativado ou receber desativado | Pode enviar mensagens para o tema, mas não pode receber mensagens da subscrição | 
| Desativado ou enviar desativado | Ativo | Não é possível enviar mensagens para o tema, mas pode receber mensagens que já estão na subscrição. | 
| Desativado ou enviar desativado | Desativado ou receber desativado | Não pode enviar mensagens para o tema e também não pode receber da subscrição. | 

## <a name="other-statuses"></a>Outros estatutos
A enumeração [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) também define um conjunto de estados transitórios que só podem ser definidos pelo sistema. 


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

