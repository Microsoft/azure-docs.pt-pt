---
title: Compreenda o Hub Azure IoT embutido no ponto final [ Microsoft Docs
description: Guia de desenvolvimento - descreve como usar o ponto final compatível com o Event Hub para ler mensagens dispositivo-a-nuvem.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: e7b8f8a33b741a8dcf2d1a68ae3cf86d6e3687eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284606"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para cloud a partir do ponto final incorporado

Por predefinição, as mensagens são encaminhadas para o ponto final virado para o serviço incorporado **(mensagens/eventos**) que é compatível com [os Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)de Eventos . Este ponto final só está atualmente exposto utilizando o protocolo [AMQP](https://www.amqp.org/) na porta 5671. Um hub IoT expõe as seguintes propriedades para permitir controlar as **mensagens/eventos**compatíveis com o Event Hub .

| Propriedade            | Descrição |
| ------------------- | ----------- |
| **Contagem de partição** | Defina esta propriedade na criação para definir o número de [divisórias](../event-hubs/event-hubs-features.md#partitions) para a ingestão de eventos dispositivo-nuvem. |
| **Tempo de retenção**  | Esta propriedade especifica quanto tempo em dias as mensagens são retidas pelo IoT Hub. O incumprimento é um dia, mas pode ser aumentado para sete dias. |

O IoT Hub permite a retenção de dados nos Centros de Eventos incorporados por um máximo de 7 dias. Pode definir o tempo de retenção durante a criação do seu Hub IoT. O tempo de retenção de dados no IoT Hub depende do seu nível de hub IoT e do tipo de unidade. Em termos de tamanho, os Centros de Eventos incorporados podem reter mensagens do tamanho máximo da mensagem até pelo menos 24 horas de quota. Por exemplo, para 1 Unidade S1 IoT Hub fornece armazenamento suficiente para reter pelo menos 400k mensagens de tamanho 4k cada. Se os seus dispositivos estiverem a enviar mensagens menores, podem ser retidas por mais tempo (até 7 dias), dependendo da quantidade de armazenamento consumida. Garantimos a conservação dos dados para o tempo de retenção especificado como mínimo.

O IoT Hub também permite gerir os grupos de consumidores no ponto final incorporado do dispositivo-para-nuvem. Pode ter até 20 grupos de consumidores por cada Hub IoT.

Se estiver a usar [o encaminhamento](iot-hub-devguide-messages-d2c.md) de mensagens e a rota de [recuo](iot-hub-devguide-messages-d2c.md#fallback-route) estiver ativada, todas as mensagens que não correspondem a uma consulta em qualquer rota vão para o ponto final incorporado. Se desativar esta rota de recuo, as mensagens que não correspondem a qualquer consulta são retiradas.

Pode modificar o tempo de retenção, quer programáticamente utilizando o fornecedor de [recursos Do IoT Hub REST APIs,](/rest/api/iothub/iothubresource)ou com o [portal Azure](https://portal.azure.com).

O IoT Hub expõe as **mensagens/eventos incorporados** para os seus serviços de back-end para ler as mensagens dispositivo-to-cloud recebidas pelo seu hub. Este ponto final é compatível com o Event Hub, que lhe permite utilizar qualquer um dos mecanismos que o serviço De Evento Hubs suporta para ler mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Leia a partir do ponto final incorporado

Algumas integrações de produtos e SDKs de Hubs de Eventos estão cientes do IoT Hub e permitem-lhe usar a sua cadeia de ligação de ligação de hub IoT para se ligar ao ponto final incorporado.

Quando utiliza SDKs de Hubs de Eventos ou integrações de produtos que desconhecem o IoT Hub, precisa de um ponto final compatível com o Event Hub e um nome compatível com o Event Hub. Pode recuperar estes valores do portal da seguinte forma:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

2. Clique **em pontos finais incorporados**.

3. A secção **Eventos** contém os **seguintes valores: Partições,** nome compatível com o Hub de **Eventos,** **ponto final compatível com o Hub de Eventos,** tempo de **retenção**e **grupos de consumidores.**

    ![Definições de dispositivo-para-nuvem](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

No portal, o campo final compatível com o Event Hub contém uma cadeia completa de ligação De Event Hubs que se parece com: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/; SharedAccessKeyName=iothubowner; SharedAccessKey=keykeykeykeykeykey=; EntidadePath=iothub-ehub-abcd-1234-123456**. Se o SDK que está a usar requer outros valores, então seriam:

| Nome | Valor |
| ---- | ----- |
| Ponto Final | sb://abcd1234namespace.servicebus.windows.net/ |
| Nome de anfitrião | abcd1234namespace.servicebus.windows.net |
| Espaço de nomes | espaço abcd1234nome |

Em seguida, pode utilizar qualquer política de acesso partilhado que tenha as permissões **ServiceConnect** para se ligar ao Centro de Eventos especificado.

Os SDKs que pode utilizar para ligar ao ponto final compatível com o Hub de Eventos incorporado que o IoT Hub expõe incluem:

| Idioma | SDK | Exemplo | Notas |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Início rápido](quickstart-send-telemetry-dotnet.md) | Utiliza informações compatíveis com hubs de eventos |
 Java | https://github.com/Azure/azure-event-hubs-java | [Início rápido](quickstart-send-telemetry-java.md) | Utiliza informações compatíveis com hubs de eventos |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Início rápido](quickstart-send-telemetry-node.md) | Usa a cadeia de ligação IoT Hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Usa a cadeia de ligação IoT Hub |

As integrações do produto que pode utilizar com o ponto final compatível com o Event Hub que o IoT Hub expõe incluem:

* [Funções Azure.](https://docs.microsoft.com/azure/azure-functions/) Consulte [o Processamento de dados do IoT Hub com funções Azure](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics.](https://docs.microsoft.com/azure/stream-analytics/) Consulte [os dados do Stream como entrada no Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Insights da Série Time.](https://docs.microsoft.com/azure/time-series-insights/) Consulte Adicionar uma fonte de evento de [hub IoT ao seu ambiente Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Storm bico.](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md) Pode ver a fonte de [bico](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração Apache Spark.](../hdinsight/spark/apache-spark-eventhub-streaming.md)
* [Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/)

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre os pontos finais do Hub IoT, consulte os [pontos finais do Hub IoT](iot-hub-devguide-endpoints.md).

* Os [Quickstarts](quickstart-send-telemetry-node.md) mostram-lhe como enviar mensagens dispositivo-nuvem a partir de dispositivos simulados e ler as mensagens a partir do ponto final incorporado. 

Para mais detalhes, consulte as mensagens de [dispositivo-para-nuvem do Process IoT Hub utilizando o](tutorial-routing.md) tutorial de rotas.

* Se pretender encaminhar as mensagens de dispositivo para nuvem para pontos finais personalizados, consulte [as rotas de mensagens e os pontos finais personalizados para mensagens dispositivo-cloud](iot-hub-devguide-messages-read-custom.md).
