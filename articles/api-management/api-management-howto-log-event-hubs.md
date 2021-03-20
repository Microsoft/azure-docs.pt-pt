---
title: Como registar eventos para Azure Event Hubs na Azure API Management | Microsoft Docs
description: Saiba como registar eventos no Azure Event Hubs na Azure API Management. O Event Hubs é um serviço de entrada de dados altamente escalável.
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
ms.openlocfilehash: 5066169951409fa86aa75a64e8fc6d4189947f27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072412"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Como registar eventos para Azure Event Hubs na Azure API Management
Os Event Hubs do Azure são um serviço de entrada de dados altamente dimensionável, que pode ingerir milhões de eventos por segundo para que possa processar e analisar os quantidades enormes de dados produzidos pelos dispositivos e aplicações ligados. O Event Hubs funciona como a "porta da frente" para um oleoduto de eventos, e uma vez recolhidos dados num centro de eventos, pode ser transformado e armazenado usando qualquer fornecedor de análise em tempo real ou adaptadores de lote/armazenamento. Os Event Hubs desacoplam a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam aceder aos eventos de acordo com seu próprio agendamento.

Este artigo é um companheiro da [Integração Azure API Management com](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) vídeo de Event Hubs e descreve como registar eventos de Gestão API usando Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

Para etapas detalhadas sobre como criar um centro de eventos e obter cordas de conexão que você precisa enviar e receber eventos de e para o Centro de [Eventos, consulte Criar um espaço de nomes de Event Hubs e um centro de eventos usando o portal Azure.](../event-hubs/event-hubs-create.md)

## <a name="create-an-api-management-logger"></a>Criar um madeireiro de gestão da API
Agora que tem um Centro de Eventos, o próximo passo é configurar um [Logger](/rest/api/apimanagement/2019-12-01/logger) no seu serviço de Gestão API para que possa registar eventos no Centro de Eventos.

Os madeireiros de gestão da API são configurados utilizando a [API Management REST API](/rest/api/apimanagement/ApiManagementREST/API-Management-REST). Para exemplos de pedidos detalhados, consulte [como criar madeireiros.](/rest/api/apimanagement/2019-12-01/logger/createorupdate)

## <a name="configure-log-to-eventhub-policies"></a>Configure as políticas de log-to-eventhub

Uma vez configurado o seu madeireiro na Gestão da API, pode configurar a sua política de log-to-eventhub para registar os eventos pretendidos. A política de log-to-eventhub pode ser utilizada na secção política de entrada ou na secção política de saída.

1. Navegue até à sua instância APIM.
2. Selecione o separador API.
3. Selecione a API à qual pretende adicionar a apólice. Neste exemplo, estamos adicionando uma política à **API Echo** no produto **Ilimitado.**
4. Selecione **Todas as operações**.
5. Na parte superior do ecrã, selecione o separador Design.
6. Na janela de processamento de entrada ou saída, clique no triângulo (junto ao lápis).
7. Selecione o editor de código. Para obter mais informações, consulte [como definir ou editar políticas.](set-edit-policies.md)
8. Posicione o cursor na `inbound` secção ou na secção `outbound` política.
9. Na janela à direita, selecione **Advanced policies** Log  >  **to EventHub**. Isto insere o `log-to-eventhub` modelo de declaração de política.

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
`logger-id`Substitua-o pelo valor utilizado `{loggerId}` no URL de pedido para criar o madeireiro no passo anterior.

Pode utilizar qualquer expressão que devolva uma corda como o valor do `log-to-eventhub` elemento. Neste exemplo, um string no formato JSON contendo a data e hora, nome de serviço, ID de pedido, endereço IP de pedido e nome de operação é registado.

Clique em **Guardar** para guardar a configuração de política atualizada. Assim que for salva, a política está ativa e os eventos são registados no Centro de Eventos designado.

> [!NOTE]
> O tamanho máximo de mensagem suportado que pode ser enviado para um centro de eventos desta política de Gestão da API é de 200 quilobytes (KB). Se uma mensagem enviada para um centro de eventos for superior a 200 KB, será automaticamente truncada e a mensagem truncada será transferida para centros de eventos.

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>Pré-visualizar o registo em Centros de Eventos utilizando a Azure Stream Analytics

Pode visualizar o registo em Centros de Eventos utilizando [consultas Azure Stream Analytics](../event-hubs/process-data-azure-stream-analytics.md). 

1. No portal Azure, navegue pelo centro de eventos para onde o madeireiro envia eventos. 
2. Em **Funcionalidades**, selecione o separador **de dados do Processo.**
3. No **Enable introspeções em tempo real a partir do** cartão de eventos, selecione **Explore**.
4. Deverá ser capaz de pré-visualizar o registo no **separador de pré-visualização de Entrada.** Se os dados apresentados não estiverem atuais, selecione **Refresh** para ver os eventos mais recentes.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os Azure Event Hubs
  * [Começa com os Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com o EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração da API Management e Do Event Hubs
  * [Referência da entidade logger](/rest/api/apimanagement/2019-12-01/logger)
  * [referência política log-to-eventhub](./api-management-advanced-policies.md#log-to-eventhub)
  * [Monitorize as suas APIs com Azure API Management, Event Hubs e Moesif](api-management-log-to-eventhub-sample.md)  
* Saiba mais sobre [a integração com a Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png