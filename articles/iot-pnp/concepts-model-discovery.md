---
title: Implementar IoT Plug e Play Preview modelo de descoberta / Microsoft Docs
description: Como construtor de soluções, saiba como pode implementar a descoberta do modelo IoT Plug e Play na sua solução.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337386"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementar ioT plug e reproduzir a descoberta do modelo de pré-visualização numa solução IoT

Este artigo descreve como, como construtor de soluções, pode implementar a descoberta do modelo IoT Plug e Play Preview numa solução IoT. A descoberta do modelo descreve como:

- Os dispositivos IoT Plug e Play registam o seu ID de modelo.
- Uma solução IoT recupera as interfaces implementadas pelo dispositivo.

Existem duas grandes categorias de solução IoT:

- Uma *solução IoT construída de propósito* funciona com um conjunto conhecido de modelos ioT plug e play device.

- Uma *solução IoT orientada por modelos* pode funcionar com qualquer dispositivo IoT Plug e Play. Construir uma solução orientada para o modelo é mais complexo, mas o benefício é que a sua solução funciona com quaisquer dispositivos adicionados no futuro.

    Para construir uma solução IoT orientada por modelos, é necessário criar lógica contra os primitivos da interface IoT Plug e Play: telemetria, propriedades e comandos. A lógica da sua solução representa um dispositivo combinando múltiplas capacidades de telemetria, propriedade e comando.

Este artigo descreve como implementar a descoberta de modelos em ambos os tipos de solução.

## <a name="model-discovery"></a>Deteção de modelos

Para descobrir o modelo que um dispositivo implementa, uma solução pode obter o ID do modelo usando a descoberta baseada em eventos ou descobertas de duas bases:

### <a name="event-based-discovery"></a>Descoberta baseada em eventos

Quando um dispositivo IoT Plug and Play se conecta ao IoT Hub, regista o modelo que implementa. Este registo resulta numa notificação [de evento de alteração Digital Twin.](concepts-digital-twin.md#digital-twin-change-events) Para aprender a permitir o encaminhamento para eventos digitais gémeos, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

A solução pode utilizar o evento mostrado no seguinte corte para aprender sobre o dispositivo IoT Plug e Play que está a ligar e obter o seu ID modelo:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

Este evento é acionado quando o ID do modelo do dispositivo é adicionado ou atualizado.

### <a name="twin-based-discovery"></a>Descoberta de duas bases

Se a solução quiser saber sobre as capacidades de um determinado dispositivo, pode utilizar a [API Get Digital Twin](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) para recuperar a informação.

No seguinte snippet digital twin, `$metadata.$model` contém o ID do modelo de um dispositivo IoT Plug and Play:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

A solução também pode usar **a Get Twin** para recuperar o ID do modelo gémeo do dispositivo, como mostrado no seguinte corte:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>Resolução de modelos

Uma solução utiliza a resolução do modelo para ter acesso às interfaces que compõem um modelo a partir do ID do modelo. 

- As soluções podem optar por armazenar estas interfaces como ficheiros numa pasta local. 
- As soluções podem utilizar o [repositório do modelo.](concepts-model-repository.md)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre a descoberta de modelos uma solução IoT, saiba mais sobre a [plataforma Azure IoT](overview-iot-plug-and-play.md) para usar outras capacidades para a sua solução.

- [Interaja com um dispositivo a partir da sua solução](quickstart-service-node.md)
- [IoT Digital Twin REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Explorador de Azure IoT](howto-use-iot-explorer.md)
