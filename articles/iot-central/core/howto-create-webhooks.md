---
title: Criar webhooks sobre regras no Azure IoT Central Microsoft Docs
description: Crie webhooks no Azure IoT Central para notificar automaticamente outras aplicações quando as regras dispararem.
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337233"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook em regras no Azure IoT Central

*Este tópico aplica-se a construtores e administradores.*

Os webhooks permitem-lhe ligar a sua aplicação IoT Central a outras aplicações e serviços para monitorização remota e notificações. Os Webhooks notificam automaticamente outras aplicações e serviços que liga sempre que uma regra é desencadeada na sua aplicação IoT Central. A sua aplicação IoT Central envia um pedido DE POST para o ponto final HTTP da outra aplicação sempre que uma regra é desencadeada. A carga útil contém detalhes do dispositivo e detalhes do gatilho de regras.

## <a name="set-up-the-webhook"></a>Configurar o webhook

Neste exemplo, você conecta-se ao RequestBin para ser notificado quando as regras disparam usando webhooks.

1. Open [RequestBin](https://requestbin.net/).

1. Crie um novo RequestBin e copie o **URL do Bin.**

1. Criar uma regra de [telemetria](tutorial-create-telemetry-rules.md). Salve a regra e adicione uma nova ação.

    ![Tela de criação webhook](media/howto-create-webhooks/webhookcreate.png)

1. Escolha a ação webhook e forneça um nome de exibição e cole o URL do Bin como URL de callback.

1. Salve a regra.

Agora, quando a regra é desencadeada, vê-se um novo pedido aparecer no RequestBin.

## <a name="payload"></a>Carga útil

Quando uma regra é desencadeada, é feito um pedido HTTP POST para o URL de retorno que contém uma carga de json com a telemetria, o dispositivo, a regra e os detalhes da aplicação. A carga útil pode parecer o seguinte:

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
Se a regra monitorizar a telemetria agregada durante um período de tempo, a carga útil conterá uma secção de telemetria diferente.

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>Aviso de alteração do formato de dados

Se tiver um ou mais webhooks criados e guardados antes **de 3 de abril de 2020,** terá de eliminar o webhook e criar um novo webhook. Isto porque os webhooks mais antigos usam um formato de carga útil mais antigo que será depreciado no futuro.

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>Carga útil webhook (formato preifrido a partir de 3 de abril de 2020)

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
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente não existe uma forma programática de subscrever/cancelar a subscrição destes webhooks através de uma API.

Se tiver ideias para melhorar esta funcionalidade, publique as suas sugestões no nosso [fórum de voz do Utilizador.](https://feedback.azure.com/forums/911455-azure-iot-central)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a configurar e usar webhooks, o próximo passo sugerido é explorar os [grupos de ação do Monitor Azure.](howto-use-action-groups.md)
