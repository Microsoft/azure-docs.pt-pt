---
title: Compreender o ponto de final incorporado do IoT Hub do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – descreve como utilizar o ponto final compatível com o Event Hub incorporado, para ler mensagens do dispositivo para a cloud.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: dobett
ms.openlocfilehash: 81cdd53769cc33daaed70ba824a0a3bbf68f8134
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877236"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para cloud a partir do ponto final incorporado

Por predefinição, as mensagens são roteadas para o ponto de final de serviço com acesso à incorporado (**mensagens/eventos**) que é compatível com [dos Hubs de eventos](https://azure.microsoft.com/documentation/services/event-hubs/). Este ponto final está apenas exposto a utilizar atualmente o [AMQP](https://www.amqp.org/) protocolo na porta 5671. Um IoT hub expõe as propriedades seguintes para que possa controlar compatível com o Event Hub mensagens ponto final incorporado **mensagens/eventos**.

| Propriedade            | Descrição |
| ------------------- | ----------- |
| **Contagem de partições** | Defina esta propriedade durante a criação para definir o número de [partições](../event-hubs/event-hubs-features.md#partitions) para ingestão de eventos do dispositivo para a cloud. |
| **Período de retenção**  | Esta propriedade especifica o período de tempo em dias as mensagens são mantidas pelo IoT Hub. A predefinição é um dia, mas ele pode ser aumentado para sete dias. |

IoT Hub também permite-lhe gerir grupos de consumidores sobre o dispositivo para cloud incorporado recebem o ponto final.

Se estiver a utilizar [roteamento de mensagens](iot-hub-devguide-messages-d2c.md) e o [rota contingência](iot-hub-devguide-messages-d2c.md#fallback-route) é ativada, todas as mensagens que não correspondem uma consulta em qualquer rota Ir para o ponto final incorporado. Se desativar esta rota de contingência, as mensagens que não correspondam a qualquer consulta são ignoradas.

Pode modificar o período de retenção, por meio de programação utilizando o [fornecedor de recursos do IoT Hub REST APIs](/rest/api/iothub/iothubresource), ou com o [portal do Azure](https://portal.azure.com).

IoT Hub expõe os **mensagens/eventos** ponto final incorporado para os seus serviços de back-end ler as mensagens de dispositivo-para-cloud recebidas pelo hub. Este ponto final é o evento compatível com o Hub, que permite-lhe utilizar qualquer um dos mecanismos do serviço de Hubs de eventos oferece suporte para a leitura de mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Ler a partir do ponto final incorporado

Algumas integrações de produtos e SDKs de Hubs de eventos estão cientes do IoT Hub e permitem-lhe utilizar a cadeia de ligação de serviço do IoT hub para ligar ao ponto final incorporado.

Quando utilizar Event Hubs SDKs ou integrações de produtos que não estão cientes do IoT Hub, tem um ponto de final compatível com o Event Hub e o nome compatível com o Event Hub. Pode obter estes valores no portal da seguinte forma:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

2. Clique em **pontos finais incorporados**.

3. O **eventos** secção contém os seguintes valores: **Partições**, **nome compatível com o Event Hub**, **endpoint compatível com o Event Hub**, **período de retenção**, e **degruposdeconsumidores**.

    ![Definições do dispositivo para a nuvem](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

No portal, o campo de ponto final compatível com o Event Hub contém uma cadeia de ligação dos Hubs de eventos completa semelhante a: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Se estiver a utilizar o SDK requer outros valores, em seguida, eles seria:

| Name | Value |
| ---- | ----- |
| Ponto Final | sb://abcd1234namespace.servicebus.windows.net/ |
| Nome de anfitrião | abcd1234namespace.servicebus.windows.net |
| Espaço de nomes | abcd1234namespace |

Em seguida, pode utilizar qualquer política de acesso partilhado que tenha as **ServiceConnect** permissões para ligar ao Hub de eventos especificado.

Os SDKs que pode utilizar para ligar ao ponto final compatível com o Event Hub incorporado que o IoT Hub expõe incluem:

| Idioma | SDK | Exemplo | Notas |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Início rápido](quickstart-send-telemetry-dotnet.md) | Utiliza informações compatível com o Event Hubs |
 Java | https://github.com/Azure/azure-event-hubs-java | [Início rápido](quickstart-send-telemetry-java.md) | Utiliza informações compatível com o Event Hubs |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Início rápido](quickstart-send-telemetry-node.md) | Utiliza a cadeia de ligação do IoT Hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Utiliza a cadeia de ligação do IoT Hub |

Integrações de produtos que pode utilizar com o ponto final compatível com o Event Hub incorporado que o IoT Hub expõe incluem:

* [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/). Ver [processamento de dados do IoT Hub com o Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Ver [Stream dados como entrada para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Ver [adicionar uma origem de evento do hub IoT ao seu ambiente do Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Spout do Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Pode ver o [spout origem](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [O Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre pontos finais do IoT Hub, veja [pontos finais do IoT Hub](iot-hub-devguide-endpoints.md).

* O [inícios Rápidos](quickstart-send-telemetry-node.md) mostram-lhe como enviar mensagens dispositivo-para-cloud a partir de dispositivos simulados e ler as mensagens a partir do ponto final incorporado. 

Para obter mais detalhes, consulte a [mensagens de dispositivo-para-cloud do Hub de IoT de processo utilizar rotas](tutorial-routing.md) tutorial.

* Se quiser encaminhe suas mensagens do dispositivo para a cloud para os pontos finais personalizados, veja [utilizar rotas de mensagens e os pontos finais personalizados para mensagens de dispositivo-para-cloud](iot-hub-devguide-messages-read-custom.md).
