---
title: Como registrar eventos em log nos hubs de eventos do Azure no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como registrar eventos em log nos hubs de eventos do Azure no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 646d9206ec82d5f35ccab9365e76276ff779d225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073490"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Como registrar eventos em log nos hubs de eventos do Azure no gerenciamento de API do Azure
Os Event Hubs do Azure são um serviço de entrada de dados altamente dimensionável, que pode ingerir milhões de eventos por segundo para que possa processar e analisar os quantidades enormes de dados produzidos pelos dispositivos e aplicações ligados. Os hubs de eventos atuam como "porta frontal" para um pipeline de eventos e, depois que os dados são coletados em um hub de eventos, podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Os Event Hubs desacoplam a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam aceder aos eventos de acordo com seu próprio agendamento.

Este artigo é um complemento para [integrar o gerenciamento de API do Azure com os hubs de eventos](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descreve como registrar em log os eventos de gerenciamento de API usando os hubs de eventos do Azure.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

Para obter etapas detalhadas sobre como criar um hub de eventos e obter cadeias de conexão que você precisa para enviar e receber eventos de e para o Hub de eventos, consulte [criar um namespace de hubs de eventos e um hub de eventos usando o portal do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Criar um agente de gerenciamento de API
Agora que você tem um hub de eventos, a próxima etapa é configurar um [agente](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) no serviço de gerenciamento de API para que ele possa registrar eventos no Hub de eventos.

Os agentes de gerenciamento de API são configurados usando a [API REST de gerenciamento de API](https://aka.ms/smapi). Antes de usar a API REST pela primeira vez, examine os [pré-requisitos](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) e verifique se você habilitou o [acesso à API REST](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Para criar um agente de log, faça uma solicitação HTTP PUT usando o seguinte modelo de URL:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Substitua `{your service}` pelo nome da instância do serviço de gerenciamento de API.
* Substitua `{new logger name}` pelo nome desejado para o novo agente. Você faz referência a esse nome ao configurar a política de [log para eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub)

Adicione os seguintes cabeçalhos à solicitação:

* Content-Type : application/json
* Nesse SharedAccessSignature 58...
  * Para obter instruções sobre como `SharedAccessSignature` gerar o, consulte [autenticação da API REST do gerenciamento de API do Azure](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Especifique o corpo da solicitação usando o seguinte modelo:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType`deve ser definido como `AzureEventHub`.
* `description`fornece uma descrição opcional do agente e pode ser uma cadeia de caracteres de comprimento zero, se desejado.
* `credentials`contém o `name` e `connectionString` do seu hub de eventos do Azure.

Quando você fizer a solicitação, se o agente de log for criado, um código `201 Created` de status será retornado. Uma resposta de exemplo baseada na solicitação de exemplo acima é mostrada abaixo.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> Para outros códigos de retorno possíveis e seus motivos, consulte [criar um agente de log](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Para ver como executar outras operações, como listar, atualizar e excluir, consulte a documentação da entidade de [agente](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) .
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Configurar políticas de log para Eventhubs

Depois que o agente de log estiver configurado no gerenciamento de API, você poderá configurar suas políticas de Eventhubs para registrar os eventos desejados. A política de log para Eventhubs pode ser usada na seção política de entrada ou na seção política de saída.

1. Navegue até à sua instância APIM.
2. Selecione a guia API.
3. Selecione a API à qual você deseja adicionar a política. Neste exemplo, estamos adicionando uma política à **API de eco** no produto **ilimitado** .
4. Selecione **Todas as operações**.
5. Na parte superior da tela, selecione a guia Design.
6. Na janela de processamento de entrada ou saída, clique no triângulo (ao lado do lápis).
7. Selecione o editor de código. Para obter mais informações, consulte [como definir ou editar políticas](set-edit-policies.md).
8. Posicione o cursor na `inbound` seção de política ou. `outbound`
9. Na janela à direita, selecione **políticas** > avançadas**log no EventHub**. Isso insere o `log-to-eventhub` modelo de declaração de política.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Substitua `logger-id` pelo valor usado para `{new logger name}` na URL para criar o agente de log na etapa anterior.

Você pode usar qualquer expressão que retorne uma cadeia de caracteres como o `log-to-eventhub` valor para o elemento. Neste exemplo, uma cadeia de caracteres que contém a data e hora, o nome do serviço, a ID da solicitação, o endereço IP da solicitação e o nome da operação é registrada.

Clique em **salvar** para salvar a configuração de política atualizada. Assim que for salva, a política estará ativa e os eventos serão registrados no Hub de eventos designado.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os hubs de eventos do Azure
  * [Introdução aos hubs de eventos do Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guia de programação dos Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração de gerenciamento de API e hubs de eventos
  * [Referência de entidade de agente](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referência de política de log para eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitore suas APIs com o gerenciamento de API do Azure, os hubs de eventos e o Moesif](api-management-log-to-eventhub-sample.md)  
* Saiba mais sobre [a integração com o aplicativo Azure](api-management-howto-app-insights.md) insights

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
