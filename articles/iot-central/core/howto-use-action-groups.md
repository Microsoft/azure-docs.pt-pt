---
title: Executar várias ações de uma regra de IoT Central do Azure | Microsoft Docs
description: Execute várias ações de uma única regra de IoT Central e crie grupos reutilizáveis de ações que podem ser executadas a partir de várias regras.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: cc89033cedcdf185ce7e18c3981045e2a20569f5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952176"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Agrupar várias ações para executar a partir de uma ou mais regras

*Este artigo se aplica a construtores e administradores.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

No Azure IoT Central, você cria regras para executar ações quando uma condição é atendida. As regras são baseadas em eventos ou telemetria do dispositivo. Por exemplo, você pode notificar um operador quando a temperatura em um dispositivo exceder um limite. Este artigo descreve como usar [Azure monitor](../../azure-monitor/overview.md) *grupos de ação* para anexar várias ações a uma regra de IOT central. Você pode anexar um grupo de ações a várias regras. Um [grupo de ações](../../azure-monitor/platform/action-groups.md) é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo pago conforme o uso
- Uma conta do Azure e uma assinatura para criar e gerenciar Azure Monitor grupos de ação

## <a name="create-action-groups"></a>Criar grupos de ações

Você pode [criar e gerenciar grupos de ações no portal do Azure](../../azure-monitor/platform/action-groups.md) ou com um [modelo de Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Um grupo de ações pode:

- Envie notificações como um email, um SMS ou faça uma chamada de voz.
- Execute uma ação como chamar um webhook.

A captura de tela a seguir mostra um grupo de ações que envia email e notificações de SMS e chama um webhook:

![Grupo de ação](media/howto-use-action-groups/actiongroup.png)

Para usar um grupo de ações em uma regra de IoT Central, o grupo de ações deve estar na mesma assinatura do Azure que o aplicativo IoT Central.

## <a name="use-an-action-group"></a>Usar um grupo de ação

Para usar um grupo de ações em seu aplicativo IoT Central, primeiro crie uma telemetria ou regra de evento. Ao adicionar uma ação à regra, selecione **Azure monitor grupos de ação**:

![Escolher ação](media/howto-use-action-groups/chooseaction.png)

Escolha um grupo de ação da sua assinatura do Azure:

![Escolher grupo de ação](media/howto-use-action-groups/chooseactiongroup.png)

Selecione **Guardar**. O grupo de ações agora aparece na lista de ações a serem executadas quando a regra é disparada:

![Grupo de ações salvo](media/howto-use-action-groups/savedactiongroup.png)

A tabela a seguir resume as informações enviadas para os tipos de ação com suporte:

| Tipo de ação | Formato de saída |
| ----------- | -------------- |
| E-mail       | Modelo de email de IoT Central padrão |
| SMS         | Alerta do Azure IoT Central: $ {applicationName}-"$ {ruleName}" disparado em "$ {DeviceName}" em $ {triggerDate} $ {triggertime} |
| Voz       | Alerta central do Azure I. O. T: regra "$ {ruleName}" disparada no dispositivo "$ {DeviceName}" em $ {triggerDate} $ {triggertime}, no aplicativo $ {applicationName} |
| Webhook     | {"SchemaId": "AzureIoTCentralRuleWebhook", "data": {[carga regular do webhook](#payload)}} |

O texto a seguir é um exemplo de mensagem de SMS de um grupo de ação:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>O JSON a seguir mostra um exemplo de carga de ação de webhook:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar grupos de ação com regras, a próxima etapa sugerida é aprender a [gerenciar seus dispositivos](howto-manage-devices.md).
