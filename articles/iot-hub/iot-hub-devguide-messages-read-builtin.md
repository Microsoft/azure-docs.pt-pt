---
title: Entender o ponto de extremidade interno do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – descreve como usar o ponto de extremidade interno, compatível com o Hub de eventos, para ler mensagens do dispositivo para a nuvem.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: e7b8f8a33b741a8dcf2d1a68ae3cf86d6e3687eb
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950419"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para cloud a partir do ponto final incorporado

Por padrão, as mensagens são roteadas para o ponto de extremidade voltado para o serviço interno (**mensagens/eventos**) que é compatível com os [hubs de eventos](https://azure.microsoft.com/documentation/services/event-hubs/). Esse ponto de extremidade atualmente só é exposto usando o protocolo [AMQP](https://www.amqp.org/) na porta 5671. Um hub IoT expõe as propriedades a seguir para permitir que você controle as **mensagens/eventos**do ponto de extremidade de mensagens compatíveis com o Hub de eventos interno.

| Propriedade            | Descrição |
| ------------------- | ----------- |
| **Contagem de partições** | Defina essa propriedade em criação para definir o número de [partições](../event-hubs/event-hubs-features.md#partitions) para ingestão de eventos do dispositivo para a nuvem. |
| **Tempo de retenção**  | Esta propriedade especifica por quanto tempo em dias as mensagens são retidas pelo Hub IoT. O padrão é um dia, mas pode ser aumentado para sete dias. |

O Hub IoT permite a retenção de dados nos hubs de eventos internos por um máximo de 7 dias. Você pode definir o tempo de retenção durante a criação do Hub IoT. O tempo de retenção de dados no Hub IoT depende da camada e do tipo de unidade do Hub IoT. Em termos de tamanho, os hubs de eventos internos podem reter as mensagens do tamanho máximo da mensagem até pelo menos 24 horas de cota. Por exemplo, para um hub IoT de unidade S1 fornece armazenamento suficiente para reter pelo menos mensagens 400 mil de tamanho de 4K cada. Se seus dispositivos estiverem enviando mensagens menores, eles poderão ser retidos por mais tempo (até 7 dias), dependendo da quantidade de armazenamento consumida. Garantimos manter os dados para o tempo de retenção especificado como um mínimo.

O Hub IoT também permite que você gerencie grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem interno. Você pode ter até 20 grupos de consumidores para cada Hub IoT.

Se você estiver usando o [Roteamento de mensagens](iot-hub-devguide-messages-d2c.md) e a rota de [fallback](iot-hub-devguide-messages-d2c.md#fallback-route) estiver habilitada, todas as mensagens que não corresponderem a uma consulta em qualquer rota vão para o ponto de extremidade interno. Se você desabilitar essa rota de fallback, as mensagens que não corresponderem a nenhuma consulta serão descartadas.

Você pode modificar o tempo de retenção, seja de forma programática usando as [APIs REST do provedor de recursos do Hub IOT](/rest/api/iothub/iothubresource)ou com o [portal do Azure](https://portal.azure.com).

O Hub IoT expõe o ponto de extremidade interno de **mensagens/eventos** para seus serviços de back-end para ler as mensagens do dispositivo para a nuvem recebidas pelo Hub. Esse ponto de extremidade é compatível com o Hub de eventos, que permite que você use qualquer um dos mecanismos aos quais o serviço de hubs de eventos dá suporte para ler mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Ler do ponto de extremidade interno

Algumas integrações de produtos e SDKs de hubs de eventos estão cientes do Hub IoT e permitem que você use a cadeia de conexão de serviço do Hub IoT para se conectar ao ponto de extremidade interno.

Ao usar SDKs de hubs de eventos ou integrações de produtos que não reconhecem o Hub IoT, você precisa de um ponto de extremidade compatível com o Hub de eventos e um nome compatível com o Hub de eventos. Você pode recuperar esses valores do portal da seguinte maneira:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

2. Clique em **pontos de extremidade internos**.

3. A seção de **eventos** contém os seguintes valores: **Partições**, **nome compatível**com o Hub de eventos, **ponto de extremidade compatível**com o Hub de eventos, **tempo de retenção**e **grupos de consumidores**.

    ![Definições do dispositivo para a nuvem](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

No portal, o campo ponto de extremidade compatível com o Hub de eventos contém uma cadeia de conexão completa de hubs de eventos semelhante a: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Se o SDK que você está usando exigir outros valores, eles serão:

| Nome | Value |
| ---- | ----- |
| Ponto Final | sb://abcd1234namespace.servicebus.windows.net/ |
| Nome do Anfitrião | abcd1234namespace.servicebus.windows.net |
| Espaço de Nomes | abcd1234namespace |

Você pode usar qualquer política de acesso compartilhado que tenha as permissões de perconnect para se conectar ao Hub de eventos especificado.

Os SDKs que você pode usar para se conectar ao ponto de extremidade compatível com o Hub de eventos interno que o Hub IoT expõe incluem:

| Idioma | SDK | Exemplo | Notas |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Início rápido](quickstart-send-telemetry-dotnet.md) | Usa informações compatíveis com os hubs de eventos |
 Java | https://github.com/Azure/azure-event-hubs-java | [Início rápido](quickstart-send-telemetry-java.md) | Usa informações compatíveis com os hubs de eventos |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Início rápido](quickstart-send-telemetry-node.md) | Usa a cadeia de conexão do Hub IoT |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Usa a cadeia de conexão do Hub IoT |

As integrações de produto que você pode usar com o ponto de extremidade compatível com o Hub de eventos interno que o Hub IoT expõe incluem:

* [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/). Consulte [processando dados do Hub IOT com Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Consulte [transmitir dados como entrada no Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series insights](https://docs.microsoft.com/azure/time-series-insights/). Consulte [Adicionar uma origem de evento do Hub IOT ao seu ambiente de time Series insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [origem do Spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no github.
* [Integração de Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [O Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre pontos de extremidade do Hub IoT, consulte [pontos de extremidade do Hub IOT](iot-hub-devguide-endpoints.md).

* Os [guias de início rápido](quickstart-send-telemetry-node.md) mostram como enviar mensagens do dispositivo para a nuvem de dispositivos simulados e ler as mensagens do ponto de extremidade interno. 

Para obter mais detalhes, consulte o tutorial [processar mensagens do dispositivo para a nuvem do Hub IOT usando rotas](tutorial-routing.md) .

* Se você quiser rotear suas mensagens do dispositivo para a nuvem para pontos de extremidade personalizados, consulte [usar rotas de mensagens e pontos de extremidade personalizados para mensagens do dispositivo para a nuvem](iot-hub-devguide-messages-read-custom.md).
