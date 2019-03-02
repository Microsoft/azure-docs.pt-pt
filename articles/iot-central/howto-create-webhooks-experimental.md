---
title: Criar webhooks em regras do Azure IoT Central | Documentos da Microsoft
description: Crie webhooks no Azure IoT Central para notificar automaticamente a outros aplicativos quando as regras são acionados.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 291307fe8d8911a969e11a8d0e9d5b9078132eda
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213794"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook em regras do Azure IoT Central

*Este tópico aplica-se para criadores e administradores.*

Os Webhooks permitem-lhe ligar a sua aplicação do Centro de IoT para outras aplicações e serviços para monitorização remota e notificações. Os Webhooks notificam automaticamente outras aplicações e serviços que ligar sempre que é acionada uma regra na sua aplicação IoT Central. A aplicação de centro de IoT envia um pedido POST ao ponto final HTTP de outro aplicativo sempre que é acionada uma regra. O payload contém detalhes do dispositivo e detalhes do acionador de regra.

## <a name="set-up-the-webhook"></a>Configurar o webhook

Neste exemplo, vai ligar a RequestBin para ser notificado quando as regras são acionados através de webhooks.

1. Open [RequestBin](http://requestbin.net/).

1. Criar um novo RequestBin e copiar o **URL do Bin**.

1. Criar uma [regra telemetria](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ou uma [regra de evento](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Guardar a regra e adicione uma nova ação.

    ![Ecrã de criação do Webhook](media/howto-create-webhooks-experimental/webhookcreate.png)

1. Escolha a ação de webhook e fornecer um nome a apresentar e cole o URL do Bin como o URL de retorno de chamada.

1. Guarde a regra.

Agora quando a regra é acionada, verá um novo pedido de aparecer no RequestBin.

## <a name="payload"></a>Payload

Quando é acionada uma regra, um pedido POST de HTTP é feito para o URL de retorno de chamada, que contém um payload json com o medidas, dispositivo, regras e detalhes da aplicação. Para uma regra de telemetria, o payload é semelhante ao seguinte:

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

Agora que aprendeu a configurar e utilizar webhooks, a próxima etapa sugerida é explorar [criação de fluxos de trabalho no Microsoft Flow](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
