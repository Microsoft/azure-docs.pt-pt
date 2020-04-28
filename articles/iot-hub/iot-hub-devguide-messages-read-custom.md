---
title: Compreenda os pontos finais personalizados do Azure IoT Hub [ Microsoft Docs
description: Guia de desenvolvedores - usando consultas de encaminhamento para direcionar mensagens dispositivo-cloud para pontos finais personalizados.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "61244349"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Utilize rotas de mensagens e pontos finais personalizados para mensagens dispositivo-cloud

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O [Encaminhamento](iot-hub-devguide-routing-query-syntax.md) de Mensagens IoT Hub permite que os utilizadores encaminhem mensagens de dispositivo-nuvem para pontos finais virados para o serviço. O encaminhamento também fornece uma capacidade de consulta para filtrar os dados antes de encaminha-lo para os pontos finais. Cada consulta de encaminhamento que configura tem as seguintes propriedades:

| Propriedade      | Descrição |
| ------------- | ----------- |
| **Nome**      | O nome único que identifica a consulta. |
| **Origem**    | A origem do fluxo de dados a ser atotado. Por exemplo, telemetria do dispositivo. |
| **Condição** | A expressão de consulta para a consulta de encaminhamento que é executada contra as propriedades da aplicação de mensagens, propriedades do sistema, corpo de mensagem, etiquetas gémeas do dispositivo e propriedades gémeas do dispositivo para determinar se é compatível com o ponto final. Para mais informações sobre a construção de uma consulta, consulte a [mensagem de encaminhamento de sintaxe](iot-hub-devguide-routing-query-syntax.md) |
| **Ponto Final**  | O nome do ponto final onde o IoT Hub envia mensagens que correspondem à consulta. Recomendamos que escolha um ponto final na mesma região que o seu hub IoT. |

Uma única mensagem pode corresponder à condição em múltiplas consultas de encaminhamento, caso em que o IoT Hub entrega a mensagem ao ponto final associado a cada consulta compatível. O IoT Hub também desduplica automaticamente a entrega de mensagens, por isso, se uma mensagem corresponder a múltiplas consultas que têm o mesmo destino, só é escrita uma vez para esse destino.

## <a name="endpoints-and-routing"></a>Pontos finais e encaminhamento

Um hub IoT tem um [ponto final incorporado](iot-hub-devguide-messages-read-builtin.md)predefinido. Pode criar pontos finais personalizados para encaminhar mensagens, ligando outros serviços na sua subscrição ao hub. O IoT Hub atualmente suporta contentores de armazenamento Azure, Hubs de Eventos, filas de ônibus de serviço e tópicos de ônibus de serviço como pontos finais personalizados.

Quando utiliza o encaminhamento e os pontos finais personalizados, as mensagens só são entregues no ponto final incorporado se não corresponderem a qualquer consulta. Para entregar mensagens ao ponto final incorporado, bem como a um ponto final personalizado, adicione uma rota que envia mensagens para o ponto final dos **eventos incorporados.**

> [!NOTE]
> * O IoT Hub apenas suporta a escrita de dados para os recipientes de armazenamento Azure como bolhas.
> * As filas e tópicos de ônibus de serviço com **Sessões** ou **Deteção duplicada** ativadas não são suportados como pontos finais personalizados.

Para obter mais informações sobre a criação de pontos finais personalizados no IoT Hub, consulte [os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md).

Para mais informações sobre a leitura a partir de pontos finais personalizados, consulte:

* Leitura a partir de recipientes de [armazenamento Azure](../storage/blobs/storage-blobs-introduction.md).

* Leitura de Centros de [Eventos.](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* Leitura das filas de [ônibus de serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Leitura dos tópicos do [Ônibus de serviço.](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre os pontos finais do Hub IoT, consulte os [pontos finais do Hub IoT](iot-hub-devguide-endpoints.md).

* Para obter mais informações sobre a linguagem de consulta que utiliza para definir consultas de encaminhamento, consulte a [sintaxe](iot-hub-devguide-routing-query-syntax.md)de consulta de Message Routing .

* As mensagens de [dispositivo-para-nuvem do Process IoT Hub utilizando o](tutorial-routing.md) tutorial de rotas mostram-lhe como usar consultas de encaminhamento e pontos finais personalizados.