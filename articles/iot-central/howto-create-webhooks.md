---
title: Criar webhooks em regras do Azure IoT Central | Documentos da Microsoft
description: Crie webhooks no Azure IoT Central para notificar automaticamente a outros aplicativos quando as regras são acionados.
author: viv-liu
ms.author: viviali
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 896d4e9c775fa0b0c8eb062d11d141901daa7242
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295990"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook em regras do Azure IoT Central

*Este tópico aplica-se para criadores e administradores.*

Os Webhooks permitem-lhe ligar a sua aplicação do Centro de IoT para outras aplicações e serviços para monitorização remota e notificações. Os Webhooks notificam automaticamente outras aplicações e serviços que ligar sempre que é acionada uma regra na sua aplicação IoT Central. Centro de IoT irá enviar um pedido POST ao ponto final HTTP de outro aplicativo sempre que é acionada uma regra. O payload irá conter os detalhes do dispositivo e detalhes do acionador de regra. 

## <a name="how-to-set-up-the-webhook"></a>Como configurar o webhook
Neste exemplo, irá ligar ao RequestBin para ser notificado quando as regras são acionados através de webhooks. 

1. Open [RequestBin](http://requestbin.net/). 
1. Criar um novo RequestBin e copiar o **URL do Bin**. 
1. Criar uma [regra telemetria](howto-create-telemetry-rules.md) ou uma [regra de evento](howto-create-event-rules.md). Guardar a regra e adicione uma nova ação.
![Ecrã de criação do Webhook](media/howto-create-webhooks/webhookcreate.PNG)
1. Escolha a ação de webhook e fornecer um nome a apresentar e cole o URL do Bin como o URL de retorno de chamada. 
1. Guarde a regra.

Agora, quando a regra é acionada, verá um novo pedido de aparecer no RequestBin.

## <a name="payload"></a>Payload
Quando é acionada uma regra, um pedido POST de HTTP é feito para o URL de retorno de chamada, que contém um payload json com o medidas, dispositivo, regras e detalhes da aplicação. Para uma regra de telemetria, o payload pode ter um aspeto semelhante ao seguinte:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Limitações conhecidas
Atualmente não é possível programática de subscrever/anular a subscrição destes webhooks através de uma API.

Se tiver ideias sobre como melhorar esta funcionalidade, publique suas sugestões à nossa [fórum do Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu a configurar e utilizar webhooks, a próxima etapa sugerida é explorar [criação de fluxos de trabalho no Microsoft Flow](howto-add-microsoft-flow.md).
