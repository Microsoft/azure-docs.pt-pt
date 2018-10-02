---
title: Compreender os pontos finais personalizados do IoT Hub do Azure | Documentos da Microsoft
description: Guia do programador - através de consultas de encaminhamento para encaminhar mensagens do dispositivo para a cloud para os pontos finais personalizados.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: bbd5058be502839f83db484136d1c97bac4a3d79
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585956"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Utilizar rotas de mensagens e os pontos finais personalizados para mensagens de dispositivo para a cloud

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [roteamento de mensagens](iot-hub-devguide-routing-query-syntax.md) permite aos utilizadores encaminhar mensagens do dispositivo para a cloud para pontos finais de serviço com acesso à. Também encaminhamento fornece um recurso de consulta para filtrar os dados antes de encaminhamento para os pontos de extremidade. Cada consulta de encaminhamento que configurar tem as seguintes propriedades:

| Propriedade      | Descrição |
| ------------- | ----------- |
| **Nome**      | O nome exclusivo que identifica a consulta. |
| **Origem**    | A origem do fluxo de dados a ser alterados. Por exemplo, telemetria do dispositivo. |
| **condição** | A expressão de consulta para a consulta de encaminhamento que é executada em relação a propriedades da mensagem de aplicação, as propriedades do sistema, corpo da mensagem, etiquetas de twin do dispositivo e propriedades dos dispositivos duplos para determinar se é uma correspondência para o ponto final. Para obter mais informações sobre a construção de uma consulta, consulte a [sintaxe de consulta de encaminhamento de mensagens](iot-hub-devguide-routing-query-syntax.md) |
| **Endpoint**  | O nome do ponto de extremidade em que o IoT Hub envia as mensagens que correspondem à consulta. Recomendamos que escolha um ponto de extremidade na mesma região que o seu hub IoT. |

Uma única mensagem pode correspondem à condição em várias consultas de encaminhamento, nesse caso o IoT Hub entrega a mensagem para o ponto final associado a cada consulta correspondente. IoT Hub também automaticamente elimina duplicados entrega de mensagens, portanto, se uma mensagem corresponde a várias consultas que têm o mesmo destino, só é escrito uma vez para esse destino.

## <a name="endpoints-and-routing"></a>Pontos de extremidade e roteamento

Um hub IoT tem uma predefinição [ponto final incorporado](iot-hub-devguide-messages-read-builtin.md). Pode criar pontos finais personalizados para rotear mensagens ao ligar a outros serviços na sua subscrição para o hub. IoT Hub suporta atualmente os contentores de armazenamento do Azure, os Hubs de eventos, filas do Service Bus e tópicos do Service Bus como pontos finais personalizados.

Quando utiliza pontos de extremidade personalizados e encaminhamento, as mensagens são entregues apenas para o ponto final incorporado se eles não correspondem a qualquer consulta. Para entregar mensagens para o ponto final incorporado, bem como para um ponto final personalizado, adicione uma rota que envia mensagens para o **eventos** ponto final.

> [!NOTE]
> * IoT Hub suporta apenas a escrita de dados para os contentores de armazenamento do Azure como blobs.
> * Filas do Service Bus e tópicos com **sessões** ou **duplicar deteção** ativada não são suportados como pontos finais personalizados.

Para obter mais informações sobre a criação de pontos finais personalizados no IoT Hub, veja [pontos finais do IoT Hub](iot-hub-devguide-endpoints.md).

Para obter mais informações sobre a leitura a partir de pontos finais personalizados, consulte:

* Ler a partir [contentores de armazenamento do Azure](../storage/blobs/storage-blobs-introduction.md).

* Ler a partir [os Hubs de eventos](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Ler a partir [filas do Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Ler a partir [tópicos do Service Bus](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre pontos finais do IoT Hub, veja [pontos finais do IoT Hub](iot-hub-devguide-endpoints.md).

* Para obter mais informações sobre a linguagem de consulta que utilizar para definir o encaminhamento de consultas, consulte [sintaxe de consulta de mensagem encaminhamento](iot-hub-devguide-routing-query-syntax.md).

* O [mensagens de dispositivo-para-cloud do Hub de IoT de processo utilizar rotas](tutorial-routing.md) tutorial mostra-lhe como utilizar o encaminhamento de consultas e os pontos finais personalizados.