---
title: Criar WebHooks em regras no Azure IoT Central | Microsoft Docs
description: Crie WebHooks no Azure IoT Central para notificar automaticamente outros aplicativos quando as regras forem acionadas.
author: viv-liu
ms.author: viviali
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 77c57b8c20b3fd181cdf6ade58e478845304da8d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950551"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook em regras no Azure IoT Central

*Este tópico se aplica a construtores e administradores.*

Os WebHooks permitem que você conecte seu aplicativo IoT Central a outros aplicativos e serviços para monitoramento e notificações remotas. WebHooks notificam automaticamente outros aplicativos e serviços que você conecta sempre que uma regra é disparada em seu aplicativo IoT Central. Seu aplicativo IoT Central envia uma solicitação POST para o ponto de extremidade HTTP do outro aplicativo sempre que uma regra é disparada. A carga contém detalhes do dispositivo e detalhes do gatilho da regra.

## <a name="set-up-the-webhook"></a>Configurar o webhook

Neste exemplo, você se conecta ao RequestBin para ser notificado quando as regras forem acionadas usando WebHooks.

1. Abra [RequestBin](https://requestbin.net/).

1. Crie um novo RequestBin e copie a **URL bin**.

1. Crie uma [regra de telemetria](tutorial-create-telemetry-rules-pnp.md). Salve a regra e adicione uma nova ação.

    ![Tela de criação do webhook](media/howto-create-webhooks-pnp/webhookcreate.png)

1. Escolha a ação de webhook e forneça um nome de exibição e cole a URL do compartimento como a URL de retorno de chamada.

1. Salve a regra.

Agora, quando a regra for disparada, você verá que uma nova solicitação aparecerá em RequestBin.

## <a name="payload"></a>Carga

Quando uma regra é disparada, uma solicitação HTTP POST é feita à URL de retorno de chamada que contém uma carga JSON com os detalhes de telemetria, dispositivo, regra e aplicativo. O conteúdo pode ser semelhante ao seguinte:

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

Atualmente, não há nenhuma maneira programática de assinar/cancelar a assinatura desses WebHooks por meio de uma API.

Se você tiver ideias sobre como melhorar esse recurso, poste suas sugestões em nosso [Fórum UserVoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como configurar e usar WebHooks, a próxima etapa sugerida é explorar a [configuração Azure monitor grupos de ação](howto-use-action-groups-pnp.md).
