---
title: Compreender o encaminhamento de mensagens do IoT Hub do Azure | Documentos da Microsoft
description: Guia para programadores - como utilizar o encaminhamento de mensagens para enviar mensagens dispositivo-para-cloud. Inclui informações sobre o envio de telemetria e de dados sem ser de telemetria.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: dd811a48d6f3f1061bad49a81b7e833dcb40e1e3
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241294"
---
# <a name="use-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Utilizar o encaminhamento de mensagens para enviar mensagens do dispositivo para a cloud para diferentes pontos de extremidade

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Roteamento de mensagens permite-lhe enviar mensagens a partir dos seus dispositivos para serviços cloud de forma automatizada, escalável e fiável. Roteamento de mensagens pode ser utilizado para: 

* **Enviar mensagens de telemetria do dispositivo, bem como eventos** ou seja, os eventos de ciclo de vida do dispositivo e os eventos de alteração para os pontos finais integrados em endpoint e personalizados do dispositivo duplo. Saiba mais sobre [pontos finais de encaminhamento](#routing-endpoints).

* **Filtrar os dados antes de encaminhamento para vários pontos de extremidade** através da aplicação de consultas. Roteamento de mensagens permite-lhe consultar sobre as propriedades da mensagem e corpo da mensagem, bem como as etiquetas do dispositivo duplo e propriedades dos dispositivos duplos. Saiba mais sobre como utilizar [consultas de encaminhamento de mensagens](iot-hub-devguide-routing-query-syntax.md).

IoT Hub precisa de acesso de escrita para estes pontos finais de serviço para encaminhamento de mensagens funcione. Se configurar os pontos finais através do portal do Azure, são adicionadas as permissões necessárias para. Certifique-se de que configura seus serviços para suportar o débito esperado. Ao configurar a sua solução de IoT em primeiro lugar, terá de monitorizar os pontos finais adicionais e faça qualquer ajuste necessário para a carga real.

O IoT Hub define um [formato comum](iot-hub-devguide-messages-construct.md) para todo dispositivo-para-cloud messaging para interoperabilidade entre protocolos. Se uma mensagem corresponde a várias rotas que apontam para o mesmo ponto final, IoT Hub entregar mensagens para esse ponto final apenas uma vez. Portanto, não precisa de configurar a eliminação de duplicados no tópico ou fila do Service Bus. Em filas particionadas, a afinidade de partição garante a ordenação de mensagens. Utilize este tutorial para saber como [configurar o encaminhamento de mensagens](tutorial-routing.md).

## <a name="routing-endpoints"></a>Pontos finais de encaminhamento

Um hub IoT tem uma predefinição interno-em-ponto final (**mensagens/eventos**) que é compatível com os Hubs de eventos. Pode criar [os pontos finais personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) para rotear mensagens ao ligar a outros serviços na sua subscrição para o IoT Hub. IoT Hub suporta atualmente os seguintes serviços como pontos finais personalizados:

### <a name="built-in-endpoint"></a>Ponto final incorporado

Pode usar o padrão [integração dos Hubs de eventos e SDKs](iot-hub-devguide-messages-read-builtin.md) para receber mensagens do dispositivo para a cloud de ponto final incorporado (**mensagens/eventos**). Tenha em atenção que depois de criar uma rota, dados deixa de fluir para o interno-em-ponto final, a menos que uma rota é criada para esse ponto final.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

IoT Hub suporta apenas a escrita de dados no armazenamento de Blobs do Azure no [Apache Avro](http://avro.apache.org/) formato. IoT Hub mensagens dos lotes e escreve dados para um blob, sempre que o batch atinge um determinado tamanho ou um determinado período de tempo decorrido.

IoT Hub é predefinido para a seguinte convenção de nomenclatura de ficheiro:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Pode usar qualquer convenção de nomenclatura de ficheiros, no entanto, tem de utilizar tokens de todas as listadas. IoT Hub irá escrever para um blob vazio se não houver dados escrever.

### <a name="service-bus-queues-and-service-bus-topics"></a>Filas do Service Bus e tópicos do Service Bus

Filas do Service Bus e tópicos usados como pontos finais do IoT Hub não tem de ter **sessões** ou **duplicar deteção** ativada. Se qualquer uma dessas opções estiverem ativada, o ponto final é apresentado como **inacessível** no portal do Azure.

### <a name="event-hubs"></a>Hubs de Eventos

Para além do integrados em Event Hubs ponto final compatível com, também pode encaminhar dados para pontos finais personalizados do tipo Hubs de eventos. 

Quando utiliza pontos de extremidade personalizados e encaminhamento, as mensagens são entregues apenas para o ponto final incorporado se eles não correspondam a quaisquer regras. Para entregar mensagens para o ponto final incorporado e os pontos finais personalizados, adicione uma rota que envia mensagens para o ponto de extremidade de eventos.

## <a name="reading-data-that-has-been-routed"></a>Leitura de dados que foi encaminhados

Pode configurar uma rota ao seguir este [tutorial](tutorial-routing.md).

Utilize os seguintes tutoriais para saber como ler a mensagem de um ponto final.

* Ler a partir do [integrados em endpoint](quickstart-send-telemetry-node.md)

* Ler a partir do [armazenamento de BLOBs](../storage/blobs/storage-blob-event-quickstart.md)

* Ler a partir do [dos Hubs de eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Ler a partir do [filas do barramento de serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Ler a partir do [tópicos do barramento de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Rota de contingência

A contingência rota envia todas as mensagens que não satisfazem as condições de consulta em qualquer uma das rotas para os Hubs de eventos em integrados existentes (**mensagens/eventos**), que é compatível com [dos Hubs de eventos](/azure/event-hubs/). Se o roteamento de mensagens estiver ativado, pode ativar a capacidade de rota de contingência. Tenha em atenção que depois de criar uma rota, dados interrompe a fluir para o interno-em-ponto final, a menos que uma rota é criada para esse ponto final. Se não há nenhum rotas para o interno-em-ponto de extremidade e uma rota de contingência está ativada, apenas as mensagens que não correspondam a quaisquer condições de consulta em rotas serão enviadas para o interno-em-ponto final. Além disso, se todas as rotas existentes são eliminadas, a rota de contingência tem de estar ativada para receber todos os dados no interno-em-ponto final. 

Pode ativar/desativar a contingência rota do Azure Portal -> Painel de encaminhamento de mensagens. Também pode utilizar para o Azure Resource Manager [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) para utilizar um ponto final personalizado para a rota de contingência.

## <a name="non-telemetry-events"></a>Eventos de telemetria não

Além de telemetria do dispositivo, roteamento de mensagens também permite enviar eventos de alteração de dispositivo duplo e eventos de ciclo de vida do dispositivo. Por exemplo, se uma rota é criada com a origem de dados definida como **eventos de alteração do dispositivo duplo**, IoT Hub envia mensagens para o ponto final que contêm a alteração no dispositivo duplo. Da mesma forma, se uma rota é criada com a origem de dados definida como **eventos de ciclo de vida do dispositivo**, IoT Hub irá enviar uma mensagem a indicar se o dispositivo foi eliminado ou criado. 

[IoT Hub também se integra com o Azure Event Grid](iot-hub-event-grid.md) para publicar eventos de dispositivo para dar suporte em tempo real de integrações e automatização de fluxos de trabalho com base nestes eventos. Ver a chave [diferenças entre o roteamento de mensagens e o Event Grid](iot-hub-event-grid-routing-comparison.md) para saber o que funciona melhor para seu cenário.

## <a name="testing-routes"></a>Teste de rotas

Quando cria uma nova rota ou editar uma rota existente, deve testar a consulta de rota com uma mensagem de exemplo. Pode testar rotas individuais ou testar todas as rotas de uma só vez e não as mensagens são encaminhadas para os pontos de extremidade durante o teste. Azure Portal, do Azure Resource Manager, do Azure PowerShell e CLI do Azure podem ser usados para teste. Os resultados de ajudar a identificar se a mensagem de exemplo correspondentes a consulta, a mensagem não correspondeu a consulta ou teste não foi possível executar uma vez que a sintaxe de mensagem ou consulta de exemplo estão incorretas. Para obter mais informações, consulte [teste rota](/rest/api/iothub/iothubresource/testroute) e [testar todas as rotas](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Latência

Ao encaminhar mensagens de telemetria do dispositivo para a cloud utilizando pontos finais incorporados, existe um pequeno aumento na latência ponto a ponto depois da criar a primeira rota.

Na maioria dos casos, o aumento de média de latência é inferior a 500 MS. Pode monitorizar a utilização da latência **encaminhamento: latência para mensagens/eventos de mensagens** ou **d2c.endpoints.latency.builtIn.events** métrica do IoT Hub. Criar ou eliminar quaisquer rotas após o primeiro não afeta a latência de ponto-a-ponto.

## <a name="monitoring-and-troubleshooting"></a>Monitorização e resolução de problemas

O IoT Hub fornece encaminhamento várias e métricas para uma visão geral do Estado de funcionamento do seu hub e as mensagens enviadas relacionados com o ponto final. Pode combinar informações a partir de várias métricas para identificar a causa de raiz para problemas. Por exemplo, utilize métrica **encaminhamento: mensagens de telemetria removidas** ou **d2c.telemetry.egress.dropped** para identificar o número de mensagens que foram removidos quando eles não correspondeu consultas em qualquer uma das rotas e a rota de contingência foi desativada. [Métricas do IoT Hub](iot-hub-metrics.md) apresenta uma lista de todas as métricas que estão ativadas por predefinição para o seu IoT Hub.

Com o **rotas** registos de diagnóstico no Azure Monitor [das definições de diagnóstico](../iot-hub/iot-hub-monitor-resource-health.md), pode erros de roteiros que ocorrem durante a avaliação de um Estado de funcionamento encaminhamento de consulta e o ponto final como percebido pelo IoT Hub, por exemplo Quando um ponto final está inativo. Estes registos de diagnóstico podem ser enviados para os registos do Azure Monitor, os Hubs de eventos ou armazenamento do Azure para processamento personalizado.

## <a name="next-steps"></a>Passos Seguintes

* Para saber como criar rotas de mensagens, veja [mensagens de dispositivo-para-cloud do Hub de IoT de processo utilizar rotas](tutorial-routing.md).

* [Como enviar mensagens dispositivo-para-cloud](quickstart-send-telemetry-node.md)

* Para informações sobre os SDKs que pode utilizar para enviar mensagens do dispositivo para a nuvem, consulte [do Azure IoT SDKs](iot-hub-devguide-sdks.md).
