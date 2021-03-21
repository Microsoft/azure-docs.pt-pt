---
title: Descrição Gera de integração do Azure Service Bus para o Event Grid | Microsoft Docs
description: Este artigo fornece uma descrição de como as mensagens do Azure Service Bus se integram com a Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369667"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Descrição geral de integração do Azure Service Bus para o Event Grid
O Service Bus agora pode emitir eventos para o Event Grid quando existem mensagens numa fila ou subscrição quando não existem recetores. Pode criar subscrições do Event Grid para os seus espaços de nomes do Service Bus, escutar estes eventos e, em seguida, reagir aos eventos ao iniciar um recetor. Com esta funcionalidade, pode utilizar o Service Bus em modelos de programação reativa. O cenário principal desta funcionalidade é as filas ou subscrições do Service Bus que têm um volume baixo de mensagens não precisarem de ter continuamente uma consulta de recetor para mensagens. 

Para ativar a funcionalidade, precisa dos seguintes itens:

* Um espaço de nomes Premium do Service Bus com, pelo menos, uma fila ou um tópico do Service Bus com, pelo menos, uma subscrição.
* Acesso de contribuidor ao espaço de nomes do Service Bus. Navegue para o seu espaço de nomes de Service Bus no portal Azure e, em seguida, selecione **Access control (IAM)** e selecione **o separador atribuições de funções.** Verifique se tem acesso ao espaço de nomes. 
* Além disso, precisa de uma subscrição do Event Grid para o espaço de nomes do Service Bus. Esta subscrição recebe uma notificação do Event Grid a indicar que existem mensagens a ser escolhidas. Os subscritores típicos podem ser a funcionalidade Logic Apps do Serviço de Aplicações do Azure, as Funções do Azure ou um webhook que contacta uma aplicação Web. Em seguida, o subscritor processa as mensagens. 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Subscrições de Grelha de Eventos para espaços de nomes de Service Bus
Pode criar subscrições do Event Grid para espaços de nomes do Service Bus de três formas diferentes:

- Portal Azure. Consulte os seguintes tutoriais para aprender a usar o portal Azure para criar subscrições de Grade de Eventos para eventos de Service Bus com Apps Azure Logic e Azure Functions como manipuladores. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Funções do Azure](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* CLI do Azure. O exemplo cli seguinte mostra como criar uma subscrição de Azure Functions para um [tópico de sistema](../event-grid/system-topics.md) criado por um espaço de nomes de Service Bus.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell. Eis um exemplo:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>Qual o número e a frequência de eventos emitidos?

Se tiver várias filas e tópicos ou subscrições no espaço de nomes, obtém, pelo menos, um evento por fila e um por subscrição. Os eventos são emitidos imediatamente se não existirem mensagens na entidade do Service Bus e é entregue uma nova mensagem. Em alternativa, os eventos são emitidos a cada dois minutos, a menos que o Service Bus detete um recetor ativo. A procura de mensagem não interrompe os eventos.

Por predefinição, o Service Bus emite eventos para todas as entidades no espaço de nomes. Se quiser receber eventos apenas para entidades específicas, veja a secção seguinte.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Utilizar filtros para limitar a origem dos eventos

Se quiser receber eventos apenas de, por exemplo, uma fila ou uma subscrição no seu espaço de nomes, pode utilizar os filtros *Começa com* ou *Termina com* fornecidos pelo Event Grid. Em algumas interfaces, os filtros são denominados *Pre* e *Sufixo*. Se quiser receber eventos para várias filas e subscrições, mas não todas, pode criar várias subscrições do Event Grid e fornecer um filtro para cada uma.

## <a name="next-steps"></a>Passos seguintes
Veja os tutoriais seguintes: 
- [Azure Logic Apps para lidar com mensagens de bus de serviço recebidas via Grade de Eventos](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Azure Funciona para lidar com mensagens de ônibus de serviço recebidas através da Grade de Eventos](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
