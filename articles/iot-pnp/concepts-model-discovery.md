---
title: Utilize os modelos IoT Plug e Play numa solução | Microsoft Docs
description: Como construtor de soluções, saiba como pode utilizar os modelos IoT Plug e Play na sua solução IoT.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7e0d93d3fd179d672b49967c575bbfb9c0fc5de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213696"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>Use modelos IoT Plug e Play numa solução IoT

Este artigo descreve como, numa solução IoT, pode identificar o ID do modelo de um dispositivo IoT Plug and Play e, em seguida, recuperar a definição do seu modelo.

Existem duas grandes categorias de uma solução IoT:

- Uma *solução construída de propósito* funciona com um conjunto conhecido de modelos para os dispositivos IoT Plug e Play que se ligarão à solução. Utiliza-se estes modelos quando desenvolve a solução.

- Uma solução *orientada para o modelo* pode funcionar com o modelo de qualquer dispositivo IoT Plug and Play. Construir uma solução orientada para modelos é mais complexo, mas o benefício é que a sua solução funciona com quaisquer dispositivos que possam ser adicionados no futuro. Uma solução IoT orientada por modelo recupera um modelo e utiliza-o para determinar a telemetria, propriedades e comandos que o dispositivo implementa.

Para utilizar um modelo IoT Plug and Play, uma solução IoT:

1. Identifica o ID do modelo implementado pelo dispositivo IoT Plug and Play, módulo ou módulo IoT Edge ligado à solução.

1. Utiliza o ID do modelo para recuperar a definição de modelo do dispositivo conectado a partir de um repositório de modelo ou loja personalizada.

## <a name="identify-model-id"></a>Identificar iD do modelo

Quando um dispositivo IoT Plug and Play se conecta ao IoT Hub, regista o ID do modelo que implementa com o IoT Hub.

O IoT Hub notifica a solução com o ID do modelo do dispositivo como parte do fluxo de ligação do dispositivo.

Uma solução pode obter o ID do modelo do dispositivo IoT Plug and Play utilizando um dos três métodos seguintes:

### <a name="get-device-twin-api"></a>Obter Dispositivo Twin API

A solução pode utilizar o [Get Device Twin](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) API para recuperar o ID do modelo do dispositivo IoT Plug and Play.

> [!TIP]
> Para módulos e módulos IoT Edge, utilize [MóduloClient.getTwin](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin).

No seguinte corte de resposta dupla do dispositivo, `modelId` contém o ID do modelo de um dispositivo IoT Plug and Play:

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

### <a name="get-digital-twin-api"></a>Obtenha API Digital Twin

A solução pode utilizar a [API Get Digital Twin](/rest/api/iothub/service/digitaltwin/getdigitaltwin) para recuperar o ID do modelo implementado pelo dispositivo IoT Plug and Play.

No seguinte corte de resposta digital twin, `$metadata.$model` contém o ID do modelo de um dispositivo IoT Plug and Play:

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

### <a name="digital-twin-change-event-notification"></a>Notificação digital de evento de mudança de gémeos

Uma ligação do dispositivo resulta numa notificação [de evento de alteração Digital Twin.](concepts-digital-twin.md#digital-twin-change-events) Uma solução precisa subscrever esta notificação do evento. Para aprender a permitir o encaminhamento para eventos digitais gémeos, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

A solução pode utilizar o evento mostrado no seguinte corte para aprender sobre o dispositivo IoT Plug and Play que está a ligar e obter o seu ID modelo:

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

## <a name="retrieve-a-model-definition"></a>Recuperar uma definição de modelo

Uma solução utiliza o ID do modelo acima identificado para recuperar a definição do modelo correspondente.

Uma solução pode obter a definição do modelo utilizando uma das seguintes opções:

### <a name="model-repository"></a>Repositório de modelos

As soluções podem usar o [repositório do modelo](concepts-model-repository.md) para recuperar modelos. Quer os construtores de dispositivos quer os construtores de soluções devem enviar previamente os seus modelos para o repositório para que a solução os possa recuperar.

Depois de identificar o ID do modelo para uma nova ligação ao dispositivo, siga estes passos:

1. Recupere a definição do modelo utilizando o ID do modelo a partir do repositório do modelo. Para obter mais informações, consulte [o Repositório do Modelo do Dispositivo.](concepts-model-repository.md)

1. Utilizando a definição de modelo do dispositivo conectado, pode enumerar as capacidades do dispositivo.

1. Utilizando as capacidades enumeradas do dispositivo, pode permitir que os utilizadores [interajam com o dispositivo](quickstart-service.md).

### <a name="custom-store"></a>Loja personalizada

As soluções podem armazenar estas definições de modelos num sistema de ficheiros local, numa loja de ficheiros públicos, ou usar uma implementação personalizada.

Depois de identificar o ID do modelo para uma nova ligação ao dispositivo, siga estes passos:

1. Recupere a definição do modelo utilizando o ID do modelo na sua loja personalizada.

1. Utilizando a definição de modelo do dispositivo conectado, pode enumerar as capacidades do dispositivo. 

1. Utilizando as capacidades enumeradas do dispositivo, pode permitir que os utilizadores [interajam com o dispositivo](quickstart-service.md).  

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a integrar os modelos IoT Plug e Play numa solução IoT, alguns dos próximos passos sugeridos são:

- [Interagir com um dispositivo a partir da sua solução](quickstart-service.md)
- [IoT Digital Twin REST API](/rest/api/iothub/service/digitaltwin)
- [Explorador de Azure IoT](howto-use-iot-explorer.md)
