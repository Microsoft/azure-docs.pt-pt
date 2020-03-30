---
title: Criar webhooks sobre regras no Azure IoT Central [ Microsoft Docs
description: Crie webhooks na Azure IoT Central para notificar automaticamente outras aplicações quando as regras disparam.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158100"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook sobre regras na Azure IoT Central

*Este tópico aplica-se a construtores e administradores.*

Os Webhooks permitem-lhe ligar a sua aplicação IoT Central a outras aplicações e serviços para monitorização remota e notificações. Os Webhooks notificam automaticamente outras aplicações e serviços que liga sempre que uma regra é desencadeada na sua aplicação IoT Central. A sua aplicação IoT Central envia um pedido post para o ponto final http da outra aplicação sempre que uma regra é desencadeada. A carga útil contém detalhes do dispositivo e detalhes do gatilho da regra.

## <a name="set-up-the-webhook"></a>Configurar o webhook

Neste exemplo, liga-se ao RequestBin para ser notificado quando as regras disparam utilizando webhooks.

1. Open [RequestBin](https://requestbin.net/).

1. Crie um novo RequestBin e copie o URL do **bin**.

1. Criar uma regra de [telemetria.](tutorial-create-telemetry-rules.md) Salve a regra e adicione uma nova ação.

    ![Tela de criação webhook](media/howto-create-webhooks/webhookcreate.png)

1. Escolha a ação webhook e forneça um nome de exibição e colhe o URL do Bin como URL de Callback.

1. Salve a regra.

Agora, quando a regra é desencadeada, vê-se um novo pedido aparecer no RequestBin.

## <a name="payload"></a>Carga útil

Quando uma regra é desencadeada, é feito um pedido HTTP POST para o URL de callback contendo uma carga útil json com os dados de telemetria, dispositivo, regra e aplicação. A carga pode parecer a seguinte:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente não existe uma forma programática de subscrever/cancelar estes webhooks através de uma API.

Se tiver ideias para melhorar esta funcionalidade, publique as suas sugestões no nosso fórum de [voz do Utilizador](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a configurar e usar webhooks, o próximo passo sugerido é explorar a configuração dos Grupos de [Ação do Monitor do Azure](howto-use-action-groups.md).
