---
title: Como registar eventos nos Hubs de eventos do Azure na gestão de API do Azure | Documentos da Microsoft
description: Saiba como registar eventos nos Hubs de eventos do Azure na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 14f84b5380a1c106114cdab425de7f69f4e19825
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60657548"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Como registar eventos nos Hubs de eventos do Azure na gestão de API do Azure
Os Event Hubs do Azure são um serviço de entrada de dados altamente dimensionável, que pode ingerir milhões de eventos por segundo para que possa processar e analisar os quantidades enormes de dados produzidos pelos dispositivos e aplicações ligados. Os Event Hubs atuam como a "porta de entrada" para um pipeline de eventos, e quando dados são recolhidos para um hub de eventos, podem ser transformado e armazenados em qualquer fornecedor de análises em tempo real ou adaptadores de criação de batches/armazenamento. Os Event Hubs desacoplam a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam aceder aos eventos de acordo com seu próprio agendamento.

Este artigo é um complemento para o [integrar a gestão de API do Azure com os Hubs de eventos](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) vídeo e descreve como registar eventos de gestão de API com os Hubs de eventos do Azure.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

Para obter passos detalhados sobre como criar um hub de eventos e obter cadeias de ligação que tem de enviar e receber eventos para e do Hub de eventos, consulte [criar um espaço de nomes de Hubs de eventos e um hub de eventos com o portal do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Criar um agente de gestão de API
Agora que tiver um Hub de eventos, a próxima etapa é configurar uma [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) na sua gestão de API de serviço para que possa registar eventos para o Hub de eventos.

Agentes de gestão de API são configurados utilizando o [API do REST de gestão de API](https://aka.ms/smapi). Antes de utilizar a API REST pela primeira vez, reveja os [pré-requisitos](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) e certifique-se de que tenha [ativado o acesso à API REST do](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Para criar um agente de log, fazer um pedido HTTP PUT enviado utilizando o modelo de URL seguinte:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Substitua `{your service}` com o nome da sua instância do serviço de gestão de API.
* Substitua `{new logger name}` com o nome pretendido para o seu novo logger. Referenciar este nome quando configurar o [log-para-eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub) política

Adicione os seguintes cabeçalhos à solicitação:

* Content-Type : application/json
* Autorização: SharedAccessSignature 58...
  * Para obter instruções sobre a gerar a `SharedAccessSignature` veja [autenticação de API do REST de gestão do Azure API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Especifique o corpo do pedido utilizando o modelo seguinte:

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

* `loggerType` tem de ser definido como `AzureEventHub`.
* `description` Fornece uma descrição opcional de agente e pode ser uma cadeia de comprimento zero se assim o desejar.
* `credentials` contém os `name` e `connectionString` do seu Hub de eventos do Azure.

Quando fizer a solicitação, se o agente de log é criado, um código de estado `201 Created` é devolvido. Uma resposta de exemplo baseada no pedido de exemplo acima é mostrada abaixo.

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
> Para os motivos e outros códigos de retorno possíveis, consulte [criar um agente de log](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Para ver como realizar outras operações, tais como a lista, update e delete, consulte a [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) documentação de entidade.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Configurar políticas de registo para eventhubs

Uma vez que o agente de log é configurado na gestão de API, pode configurar as políticas de registo para eventhubs para registar os eventos pretendidos. A política de registo para eventhubs pode ser utilizada na secção de política de entrada ou a seção de política de saída.

1. Navegue até à sua instância APIM.
2. Selecione o separador de API.
3. Selecione a API à qual pretende adicionar a política. Neste exemplo, estamos a adicionar uma política para o **API eco** no **ilimitado** produto.
4. Selecione **Todas as operações**.
5. Na parte superior do ecrã, selecione o separador de Design.
6. Na janela de processamento de entrada ou saída, clique no triângulo (junto ao lápis).
7. Selecione o editor de código. Para obter mais informações, consulte [como definir ou editar políticas](set-edit-policies.md).
8. Posicione o cursor no `inbound` ou `outbound` secção política.
9. Na janela à direita, selecione **políticas avançadas** > **registo para EventHub**. Isso insere o `log-to-eventhub` modelo de política de instrução.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Substitua `logger-id` com o valor utilizado para `{new logger name}` no URL para criar o agente de log no passo anterior.

Pode utilizar qualquer expressão que devolve uma cadeia de caracteres como o valor para o `log-to-eventhub` elemento. Neste exemplo, uma cadeia de caracteres que contém a data e hora, nome do serviço, id do pedido, endereço de ip do pedido e nome da operação é registada.

Clique em **guardar** para guardar a configuração de política atualizada. Assim que guardar a política estar ativa e os eventos são registados para o Hub de eventos designado.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre os Hubs de eventos do Azure
  * [Introdução aos Hubs de eventos do Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com o EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guia de programação dos Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração de gestão de API e os Hubs de eventos
  * [Referência de entidade Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referência de política de registo para eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitorizar as suas APIs com a gestão de API do Azure, os Hubs de eventos e Moesif](api-management-log-to-eventhub-sample.md)  
* Saiba mais sobre [integração com o Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
