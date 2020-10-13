---
title: Compreenda o Azure IoT Hub embutido no ponto final Microsoft Docs
description: Developer guide - descreve como usar o ponto final incorporado, compatível com o Event Hub para ler mensagens dispositivo-a-nuvem.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 941953c75c516a9eceff526a0ced0ec0910f1f1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327706"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para cloud a partir do ponto final incorporado

Por predefinição, as mensagens são encaminhadas para o ponto final incorporado **(mensagens/eventos)** que é compatível com [os Centros de Eventos.](https://azure.microsoft.com/documentation/services/event-hubs/) Este ponto final só está atualmente exposto utilizando o protocolo [AMQP](https://www.amqp.org/) na porta 5671. Um hub IoT expõe as seguintes propriedades para permitir controlar as **mensagens/eventos**de mensagens compatíveis com o Event Hub.

| Propriedade            | Descrição |
| ------------------- | ----------- |
| **Contagem de divisórias** | Defina esta propriedade na criação para definir o número de [divisórias](../event-hubs/event-hubs-features.md#partitions) para ingestão de eventos dispositivo-a-nuvem. |
| **Tempo de retenção**  | Esta propriedade especifica quanto tempo em dias as mensagens são retidas pelo IoT Hub. O incumprimento é um dia, mas pode ser aumentado para sete dias. |

O IoT Hub permite a retenção de dados nos Centros de Eventos incorporados por um máximo de 7 dias. Pode definir o tempo de retenção durante a criação do seu Hub IoT. O tempo de retenção de dados no IoT Hub depende do nível do hub IoT e do tipo de unidade. Em termos de tamanho, os Centros de Eventos incorporados podem reter mensagens do tamanho máximo da mensagem até pelo menos 24 horas de quota. Por exemplo, para 1 unidade S1, o IoT Hub fornece armazenamento suficiente para reter pelo menos 400k mensagens de tamanho 4k cada. Se os seus dispositivos estiverem a enviar mensagens mais pequenas, podem ser conservadas por mais tempo (até 7 dias) dependendo da quantidade de armazenamento consumida. Garantimos a retenção dos dados para o tempo de retenção especificado como mínimo. As mensagens caducarão e não serão acessíveis após o tempo de retenção. 

O IoT Hub também permite gerir grupos de consumidores no ponto final de receção de dispositivos a nuvem incorporado. Você pode ter até 20 grupos de consumidores para cada IoT Hub.

Se estiver a utilizar o [encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md) e a rota de [retorno](iot-hub-devguide-messages-d2c.md#fallback-route) estiver ativada, todas as mensagens que não correspondam a uma consulta em qualquer rota vão para o ponto final incorporado. Se desativar esta rota de retorno, as mensagens que não correspondem a qualquer consulta são deixadas.

Pode modificar o tempo de retenção, quer programáticamente utilizando o [fornecedor de recursos IoT Hub REST APIs,](/rest/api/iothub/iothubresource)quer com o [portal Azure](https://portal.azure.com).

O IoT Hub expõe as **mensagens/eventos incorporados** no ponto final para os seus serviços de back-end para ler as mensagens de dispositivo para nuvem recebidas pelo seu hub. Este ponto final é compatível com o Event Hub, que lhe permite utilizar qualquer um dos mecanismos que o serviço Event Hubs suporta para ler mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Leia do ponto final incorporado

Algumas integrações de produtos e SDKs de Centros de Eventos estão cientes do IoT Hub e permitem-lhe usar o seu fio de serviço de serviço IoT para ligar ao ponto final incorporado.

Quando utilizar SDKs de Centros de Eventos ou integrações de produtos que não sabem do IoT Hub, precisa de um ponto final compatível com o Event Hub e um nome compatível com o Event Hub. Pode recuperar estes valores do portal da seguinte forma:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

2. Clique **em pontos finais incorporados.**

3. A secção **Eventos** contém os **seguintes valores: Partições,** **nome compatível com o Event Hub,** **ponto final compatível com o Event Hub,** Tempo de **Retenção**e **Grupos de Consumidores**.

    ![Configurações dispositivo-a-nuvem](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

No portal, o campo de ponto final compatível com o Event Hub contém uma cadeia completa de conexão Event Hubs que se parece **com: Endpoint=sb://abcd1234namespace.servicebus.windows.net/; SharedAccessKeyName=iothubowner; SharedAccessKey=keykeykeykeykeykey=; EntityPath=iothub-ehub-abcd-1234-123456**. Se o SDK que está a usar requer outros valores, então eles seriam:

| Nome | Valor |
| ---- | ----- |
| Ponto final | sb://abcd1234namespace.servicebus.windows.net/ |
| Hostname (Nome do anfitrião) | abcd1234namespace.servicebus.windows.net |
| Espaço de Nomes | abcd1234namespace |

Em seguida, pode escolher qualquer política de acesso partilhado a partir do drop-down, como mostrado na imagem acima. Só mostra políticas que têm as permissões **ServiceConnect** para ligar ao Centro de Eventos especificado.

Os SDKs que pode utilizar para ligar ao ponto final compatível com o Event Hub que o IoT Hub expõe incluem:

| Idioma | SDK | Exemplo |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [Início rápido](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [Início rápido](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [Início rápido](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | [Início rápido](quickstart-send-telemetry-python.md) |

As integrações do produto que pode utilizar com o ponto final compatível com o Event Hub que o IoT Hub expõe incluem:

* [Funções Azure](https://docs.microsoft.com/azure/azure-functions/). Consulte [os dados de processamento do IoT Hub com Funções Azure](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Consulte os [dados do Stream como entrada no Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Insights da Série de Tempo](https://docs.microsoft.com/azure/time-series-insights/). Consulte [uma fonte de evento do hub IoT ao ambiente time series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Bico de tempestade Apache.](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md) Pode ver a [fonte do bico](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração apache Spark.](../hdinsight/spark/apache-spark-eventhub-streaming.md)
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre os pontos finais do [IoT Hub, consulte os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md).

* Os [Quickstarts](quickstart-send-telemetry-node.md) mostram-lhe como enviar mensagens dispositivo-a-nuvem a partir de dispositivos simulados e ler as mensagens do ponto final incorporado. 

Para obter mais detalhes, consulte as [mensagens process IoT Hub dispositivo-nuvem usando](tutorial-routing.md) o tutorial de rotas.

* Se pretender encaminhar as mensagens do dispositivo para a nuvem para pontos finais personalizados, consulte [as rotas de mensagens e pontos finais personalizados para mensagens dispositivo-nuvem](iot-hub-devguide-messages-read-custom.md).
