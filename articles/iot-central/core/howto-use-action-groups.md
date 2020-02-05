---
title: Executar múltiplas ações a partir de uma regra Central Azure IoT  Microsoft Docs
description: Execute múltiplas ações a partir de uma única regra IoT Central e crie grupos reutilizáveis de ações que você pode executar a partir de várias regras.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 7c60728ab501d03e9c40928e730225575e76efbc
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023825"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Grupo múltiplas ações para executar a partir de uma ou mais regras

*Este artigo aplica-se a construtores e administradores.*

Na Azure IoT Central, cria-se regras para executar ações quando uma condição é satisfeita. As regras baseiam-se na telemetria do dispositivo ou em eventos. Por exemplo, pode notificar um operador quando a temperatura de um dispositivo exceder um limiar. Este artigo descreve como usar *grupos* de ação [do Monitor Azure](../../azure-monitor/overview.md) para anexar múltiplas ações a uma regra IoT Central. Pode anexar um grupo de ação a várias regras. Um grupo de [ação](../../azure-monitor/platform/action-groups.md) é uma coleção de preferências de notificação definidas pelo proprietário de uma subscrição Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação criada usando um plano de preços padrão
- Uma conta Azure e uma subscrição para criar e gerir grupos de ação do Azure Monitor

## <a name="create-action-groups"></a>Criar grupos de ações

Pode [criar e gerir grupos](../../azure-monitor/platform/action-groups.md) de ação no portal Azure ou com um modelo de Gestor de [Recursos Azure](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Um grupo de ação pode:

- Envie notificações como um e-mail, um SMS ou faça uma chamada de voz.
- Execute uma ação como chamar um webhook.

A imagem seguinte mostra um grupo de ação que envia notificações de e-mail e SMS e chama um webhook:

![Grupo de ação](media/howto-use-action-groups/actiongroup.png)

Para utilizar um grupo de ação numa regra IoT Central, o grupo de ação deve estar na mesma subscrição azure que a aplicação IoT Central.

## <a name="use-an-action-group"></a>Use um grupo de ação

Para utilizar um grupo de ação na sua aplicação IoT Central, primeiro crie uma regra. Quando adicionar uma ação à regra, selecione Grupos de **Ação do Monitor Azure:**

![Escolha ação](media/howto-use-action-groups/chooseaction.png)

Escolha um grupo de ação a partir da sua subscrição Azure:

![Escolha grupo de ação](media/howto-use-action-groups/chooseactiongroup.png)

Selecione **Guardar**. O grupo de ação aparece agora na lista de ações a executar quando a regra é desencadeada:

![Grupo de ação salvo](media/howto-use-action-groups/savedactiongroup.png)

O quadro seguinte resume as informações enviadas aos tipos de ação suportados:

| Tipo de ação | Formato de saída |
| ----------- | -------------- |
| E-mail       | Modelo padrão de e-mail IoT Central |
| SMS         | Alerta central Azure IoT: ${applicationName} - "${ruleName}" disparado em "${deviceName}" em ${triggerDate} ${triggerTime} |
| Voz       | Alerta central Azure I.O.T: regra "${ruleName}" ativado no dispositivo "${deviceName}" a ${triggerDate} ${triggerTime}, na aplicação ${applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[carga útil regular do webhook](howto-create-webhooks.md#payload)}} |

O seguinte texto é um exemplo de mensagem SMS de um grupo de ação:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar grupos de ação com regras, o próximo passo sugerido é aprender a [gerir os seus dispositivos.](howto-manage-devices.md)
