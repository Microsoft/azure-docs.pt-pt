---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224489"
---
The following table lists the limits associated with the different service tiers S1, S2, S3, and F1. For information about the cost of each *unit* in each tier, see [Azure IoT Hub pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Standard | S2 Standard | S3 Standard | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Mensagens/dia |400,000 |6,000,000 |300,000,000 |8,000 |
| Máximo de unidades |200 |200 |10 |1 |

> [!NOTE]
> If you anticipate using more than 200 units with an S1 or S2 tier hub or 10 units with an S3 tier hub, contact Microsoft Support.
> 
> 

The following table lists the limits that apply to IoT Hub resources.

| Recurso | Limite |
| --- | --- |
| Máximo de hubs IoT pagos por subscrição do Azure |100 |
| Máximo de hubs IoT gratuitos por subscrição do Azure |1 |
| Maximum number of characters in a device ID | 128 |
| Número máximo de identidades de dispositivos<br/> devolvidas numa única chamada |1,000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para a cloud |7 dias |
| Tamanho máximo da mensagem do dispositivo para a cloud |256 KB |
| Tamanho máximo do batch do dispositivo para a cloud |AMQP and HTTP: 256 KB for the entire batch <br/>MQTT: 256 KB for each message |
| Máximo de mensagens no batch do dispositivo para a cloud |500 |
| Tamanho máximo da mensagem da cloud para o dispositivo |64 KB |
| TTL máximo das mensagens da cloud para o dispositivo |2 dias |
| Contagem máxima de entrega da cloud para o dispositivo <br/> mensagens |100 |
| Maximum cloud-to-device queue depth per device |50 |
| Contagem máxima de entrega para mensagens de comentários <br/> em resposta a uma mensagem da cloud para o dispositivo |100 |
| TTL máximo para mensagens de comentários em <br/> resposta a uma mensagem da cloud para o dispositivo |2 dias |
| [Maximum size of device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB for tags section, and 32 KB for desired and reported properties sections each |
| Maximum length of device twin string key | 1 KB |
| Maximum length of device twin string value | 4 KB |
| [Maximum depth of object in device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Tamanho máximo do payload de método direto | 128 KB |
| Retenção máxima de histórico de tarefas | 30 dias |
| Máximo de tarefas simultâneas | 10 (para S3), 5 (para S2), 1 (para S1) |
| Máximo de pontos finais adicionais | 10 (for S1, S2, and S3) |
| Regras de encaminhamento máximo de mensagens | 100 (for S1, S2, and S3) |
| Maximum number of concurrently connected device streams | 50 (for S1, S2, S3, and F1 only) |
| Maximum device stream data transfer | 300 MB per day (for S1, S2, S3, and F1 only) |

> [!NOTE]
> If you need more than 100 paid IoT hubs in an Azure subscription, contact Microsoft Support.

> [!NOTE]
> Currently, the total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. If you want to increase this limit, contact [Microsoft Support](https://azure.microsoft.com/support/options/).

IoT Hub throttles requests when the following quotas are exceeded.

| Limitação | Valor por hub |
| --- | --- |
| Operações de registo de identidade <br/> (create, retrieve, list, update, and delete), <br/> importar/exportar de forma individual ou em massa |83.33/sec/unit (5,000/min/unit) (for S3). <br/> 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| Ligações do dispositivo |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Mínimo de 100/seg. |
| Envios do dispositivo para a cloud |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Mínimo de 100/seg. |
| Envios da cloud para o dispositivo | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| Receções da cloud para o dispositivo |833.33/sec/unit (50,000/min/unit) (for S3), 16.67/sec/unit (1,000/min/unit) (for S1 and S2). |
| Operações de carregamento de ficheiros |83.33 file upload initiations/sec/unit (5,000/min/unit) (for S3), 1.67 file upload initiations/sec/unit (100/min/unit) (for S1 and S2). <br/> 10,000 SAS URIs can be out for an Azure Storage account at one time.<br/> 10 SAS URIs/dispositivo podem ficar fora de uma só vez. |
| Métodos diretos | 24 MB/sec/unit (for S3), 480 KB/sec/unit (for S2), 160 KB/sec/unit (for S1).<br/> Based on 8-KB throttling meter size. |
| Leituras de dispositivo duplo | 500/sec/unit (for S3), Maximum of 100/sec or 10/sec/unit (for S2), 100/sec (for S1) |
| Atualizações de dispositivo duplo | 250/sec/unit (for S3), Maximum of 50/sec or 5/sec/unit (for S2), 50/sec (for S1) |
| Operações de tarefas <br/> (create, update, list, and delete) | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S2), 1.67/sec/unit (100/min/unit) (for S1). |
| Débito de operação tarefas por dispositivo | 50/sec/unit (for S3), maximum of 10/sec or 1/sec/unit (for S2), 10/sec (for S1). |
| Device stream initiation rate | 5 new streams/sec (for S1, S2, S3, and F1 only). |
