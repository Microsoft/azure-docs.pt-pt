---
title: Executar várias ações a partir de uma regra Azure IoT Central / Microsoft Docs
description: Executar várias ações a partir de uma única regra IoT Central e criar grupos reutilizáveis de ações que você pode executar a partir de várias regras.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80157692"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Agrupar múltiplas ações para executar a partir de uma ou mais regras

*Este artigo aplica-se a construtores e administradores.*

Na Azure IoT Central, cria-se regras para executar ações quando uma condição é cumprida. As regras baseiam-se na telemetria do dispositivo ou em eventos. Por exemplo, pode notificar um operador quando a temperatura de um dispositivo exceder um limiar. Este artigo descreve como usar *grupos de ação* [do Azure Monitor](../../azure-monitor/overview.md) para anexar múltiplas ações a uma regra IoT Central. Pode anexar um grupo de ação a várias regras. Um [grupo de ação](../../azure-monitor/platform/action-groups.md) é uma coleção de preferências de notificação definidas pelo proprietário de uma subscrição Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação criada usando um plano de preços padrão
- Uma conta Azure e subscrição para criar e gerir grupos de ação do Azure Monitor

## <a name="create-action-groups"></a>Criar grupos de ações

Pode [criar e gerir grupos de ação no portal Azure](../../azure-monitor/platform/action-groups.md) ou com um [modelo de Gestor de Recursos Azure.](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)

Um grupo de ação pode:

- Envie notificações como um e-mail, um SMS ou faça uma chamada de voz.
- Executar uma ação como chamar um webhook.

A imagem que se segue mostra um grupo de ação que envia notificações de e-mail e SMS e chama um webhook:

![Grupo de ações](media/howto-use-action-groups/actiongroup.png)

Para utilizar um grupo de ação numa regra IoT Central, o grupo de ação deve estar na mesma subscrição Azure que a aplicação IoT Central.

## <a name="use-an-action-group"></a>Use um grupo de ação

Para utilizar um grupo de ação na sua aplicação IoT Central, crie primeiro uma regra. Ao adicionar uma ação à regra, selecione **Grupos de Ação do Monitor Azure**:

![Escolha ação](media/howto-use-action-groups/chooseaction.png)

Escolha um grupo de ação da sua assinatura Azure:

![Escolha grupo de ação](media/howto-use-action-groups/chooseactiongroup.png)

Selecione **Guardar**. O grupo de ação aparece agora na lista de ações a executar quando a regra é desencadeada:

![Grupo de ação salvo](media/howto-use-action-groups/savedactiongroup.png)

O quadro que se segue resume as informações enviadas aos tipos de ação suportados:

| Tipo de ação | Formato de saída |
| ----------- | -------------- |
| E-mail       | Modelo de e-mail padrão IoT Central |
| SMS         | Alerta Azure IoT Central: ${applicationName} - "${ruleName}" desencadeado em "${deviceName}" a ${triggerDate} ${triggerTime} |
| Voz       | Alerta central Azure I.O.T: regra "${ruleName}" ativada no dispositivo "${deviceName}" a ${triggerDate} ${triggerTime}, na aplicação ${applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "dados": {[carga útil regular do webhook](howto-create-webhooks.md#payload)}} |

O texto a seguir é um exemplo de mensagem SMS de um grupo de ação:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar grupos de ação com regras, o próximo passo sugerido é aprender a [gerir os seus dispositivos.](howto-manage-devices.md)
