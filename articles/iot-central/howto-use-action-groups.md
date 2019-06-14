---
title: Executar várias ações a partir de uma regra do Azure IoT Central | Documentos da Microsoft
description: Executar várias ações a partir de uma única regra de centro de IoT e criar grupos reutilizáveis de ações que pode executar a partir de várias regras.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60517250"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Várias ações para executar a partir de uma ou mais regras de grupo

*Este artigo aplica-se para criadores e administradores.*

Azure IoT Central, vai criar regras para executar ações quando for cumprida uma condição. Regras baseiam-se a telemetria do dispositivo ou eventos. Por exemplo, pode notificar um operador quando a temperatura num dispositivo excede um limiar. Este artigo descreve como utilizar [do Azure Monitor](../azure-monitor/overview.md) *grupos de ação* para anexar várias ações numa regra de IoT Central. Pode anexar um grupo de ação para várias regras. Uma [grupo de ação](../azure-monitor/platform/action-groups.md) é uma coleção de preferências de notificações definidos pelo proprietário de uma subscrição do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo de pay as you go
- Uma conta do Azure e subscrição para criar e gerir grupos de ação do Azure Monitor

## <a name="create-action-groups"></a>Criar grupos de ações

Pode [criar e gerir grupos de ação no portal do Azure](../azure-monitor/platform/action-groups.md) ou com um [modelo Azure Resource Manager](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Um grupo de ação pode:

- Enviar notificações, como uma mensagem de e-mail, um SMS, ou fazer uma chamada de voz.
- Execute uma ação, como chamar um webhook.

Captura de ecrã seguinte mostra um grupo de ação que envia o e-mail e notificações por SMS e chama um webhook:

![grupo de ação](media/howto-use-action-groups/actiongroup.png)

Para utilizar um grupo de ação numa regra de IoT Central, o grupo de ação tem de ser na mesma subscrição do Azure como o aplicativo de IoT Central.

## <a name="use-an-action-group"></a>Utilizar um grupo de ação

Para utilizar um grupo de ação na sua aplicação do Centro de IoT, crie primeiro uma regra de evento ou de telemetria. Quando adicionar uma ação para a regra, selecione **grupos de ação do Azure Monitor**:

![Escolha a ação](media/howto-use-action-groups/chooseaction.png)

Escolha um grupo de ação da sua subscrição do Azure:

![Escolha o grupo de ação](media/howto-use-action-groups/chooseactiongroup.png)

Selecione **Guardar**. O grupo de ação é apresentada na lista de ações a executar quando a regra é acionada:

![Guardar o grupo de ação](media/howto-use-action-groups/savedactiongroup.png)

A tabela seguinte resume as informações enviadas para os tipos de ação suportados:

| Tipo de ação | Formato de saída |
| ----------- | -------------- |
| Email       | Modelo de e-mail padrão do Centro de IoT |
| SMS         | Alerta do Centro de IoT do Azure: ${applicationName} - "${ruleName}" acionada "${deviceName}" em ${triggerDate} ${triggerTime} |
| Voz       | Alerta da Central de I.O.T do Azure: "${ruleName}" acionada no dispositivo "${deviceName}" em ${triggerDate} ${triggerTime}, a regra na aplicação ${applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](#payload)} } |

O texto seguinte é um exemplo de mensagem SMS de um grupo de ação:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> O JSON seguinte mostra um payload de ação do webhook de exemplo:

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

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar grupos de ação com as regras, a próxima etapa sugerida é saber como [gerir os seus dispositivos](howto-manage-devices.md).
