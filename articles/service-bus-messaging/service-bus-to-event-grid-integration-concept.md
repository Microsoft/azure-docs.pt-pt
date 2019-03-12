---
title: Descrição Gera de integração do Azure Service Bus para o Event Grid | Microsoft Docs
description: Descrição de mensagens do Service Bus e integração do Event Grid
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: aschhab
ms.openlocfilehash: 93c4fdd778f1dc3689de74c0144c513744f438c5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762421"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Descrição geral de integração do Azure Service Bus para o Event Grid

O Azure Service Bus lançou uma nova integração para o Azure Event Grid. O cenário principal desta funcionalidade é as filas ou subscrições do Service Bus que têm um volume baixo de mensagens não precisarem de ter continuamente uma consulta de recetor para mensagens. 

O Service Bus agora pode emitir eventos para o Event Grid quando existem mensagens numa fila ou subscrição quando não existem recetores. Pode criar subscrições do Event Grid para os seus espaços de nomes do Service Bus, escutar estes eventos e, em seguida, reagir aos eventos ao iniciar um recetor. Com esta funcionalidade, pode utilizar o Service Bus em modelos de programação reativa.

Para ativar a funcionalidade, precisa dos seguintes itens:

* Um espaço de nomes Premium do Service Bus com, pelo menos, uma fila ou um tópico do Service Bus com, pelo menos, uma subscrição.
* Acesso de contribuidor ao espaço de nomes do Service Bus.
* Além disso, precisa de uma subscrição do Event Grid para o espaço de nomes do Service Bus. Esta subscrição recebe uma notificação do Event Grid a indicar que existem mensagens a ser escolhidas. Os subscritores típicos podem ser a funcionalidade Logic Apps do Serviço de Aplicações do Azure, as Funções do Azure ou um webhook que contacta uma aplicação Web. Em seguida, o subscritor processa as mensagens. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Verifique se tem acesso de contribuinte
Aceda ao seu espaço de nomes do Service Bus e, em seguida, selecione **controlo de acesso (IAM)** e selecione **atribuições de funções** separador. Certifique-se de que tem o acesso de contribuinte ao espaço de nomes. 

### <a name="events-and-event-schemas"></a>Eventos e esquemas de eventos

O Service Bus envia atualmente eventos para dois cenários:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* DeadletterMessagesAvailable

Além disso, utiliza a segurança padrão do Event Grid e [mecanismos de autenticação](https://docs.microsoft.com/azure/event-grid/security-authentication).

Para obter mais informações, veja [Esquemas de eventos do Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema).

#### <a name="active-messages-available-event"></a>Evento Disponível de Mensagens Ativas

Este evento é gerado se tiver mensagens ativas numa fila ou subscrição, sem recetores à escuta.

O esquema para este evento é o seguinte:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-letter-messages-available-event"></a>Evento Disponível de Mensagens Não Entregues

Obtém, pelo menos, um evento por fila de Mensagens Não Entregues, que tem mensagens e recetores não ativos.

O esquema para este evento é o seguinte:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>Qual o número e a frequência de eventos emitidos?

Se tiver várias filas e tópicos ou subscrições no espaço de nomes, obtém, pelo menos, um evento por fila e um por subscrição. Os eventos são emitidos imediatamente se não existirem mensagens na entidade do Service Bus e é entregue uma nova mensagem. Em alternativa, os eventos são emitidos a cada dois minutos, a menos que o Service Bus detete um recetor ativo. A procura de mensagem não interrompe os eventos.

Por predefinição, o Service Bus emite eventos para todas as entidades no espaço de nomes. Se quiser receber eventos apenas para entidades específicas, veja a secção seguinte.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Utilizar filtros para limitar a origem dos eventos

Se quiser receber eventos apenas de, por exemplo, uma fila ou uma subscrição no seu espaço de nomes, pode utilizar os filtros *Começa com* ou *Termina com* fornecidos pelo Event Grid. Em algumas interfaces, os filtros são denominados *Pre* e *Sufixo*. Se quiser receber eventos para várias filas e subscrições, mas não todas, pode criar várias subscrições do Event Grid e fornecer um filtro para cada uma.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Criar subscrições do Event Grid para espaços de nomes do Service Bus

Pode criar subscrições do Event Grid para espaços de nomes do Service Bus de três formas diferentes:

* No portal do Azure
* Na [CLI do Azure](#azure-cli-instructions)
* No [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Instruções do portal do Azure

Para criar uma nova subscrição do Event Grid, efetue o seguinte:
1. No portal do Azure, aceda ao seu espaço de nomes.
2. No painel esquerdo, selecione **Event Grid**. 
3. Selecione **Subscrição de Eventos**.  

   A imagem seguinte mostra um espaço de nomes que tem uma subscrição do Event Grid:

   ![Subscrições do Event Grid](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   A imagem seguinte mostra como subscrever uma função ou um webhook sem filtragem específica:

   ![21][]

## <a name="azure-cli-instructions"></a>Instruções da CLI do Azure

Primeiro, certifique-se de que tem a versão 2.0 da CLI do Azure ou posterior instalada. [Transfira o instalador](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Selecione **Windows + X** e, em seguida, abra uma nova consola do PowerShell com permissões de administrador. Em alternativa, pode utilizar uma shell de comandos no portal do Azure.

Execute o seguinte código:

 ```azurecli-interactive
az login

az account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>Instruções do PowerShell

Confirme que instalou o Azure PowerShell. [Transfira o instalador](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.4.0). Selecione **Windows + X** e, em seguida, abra uma nova consola do PowerShell com permissões de Administrador. Em alternativa, pode utilizar uma shell de comandos no portal do Azure.

```PowerShell-interactive
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

A partir daqui, pode explorar as outras opções de configuração ou testar que eventos estão a fluir.

## <a name="next-steps"></a>Passos Seguintes

* Obtenha [exemplos](service-bus-to-event-grid-integration-example.md) do Service Bus e Event Grid.
* Saiba mais sobre o [Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Saiba mais sobre as [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/).
* Saiba mais sobre o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/).
* Saiba mais sobre o [Service Bus](https://docs.microsoft.com/azure/service-bus/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
