---
title: Quickstart - Monitorize os seus dispositivos na Azure IoT Central
description: Quickstart - Como operador, aprenda a utilizar a sua aplicação Azure IoT Central para monitorizar os seus dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c63a9833e6b9a9b243d289d79428ddef1468253
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833886"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Quickstart: Utilize a Azure IoT Central para monitorizar os seus dispositivos

*Este artigo aplica-se a operadores, construtores e administradores.*

Este quickstart mostra-lhe, como operador, como utilizar a sua aplicação Microsoft Azure IoT Central para monitorizar os seus dispositivos e alterar as definições.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deverá completar os três quickstarts anteriores [Criar uma aplicação Azure IoT Central](./quick-deploy-iot-central.md), Adicione um dispositivo [simulado à sua aplicação IoT Central](./quick-create-simulated-device.md) e [configuure regras e ações para o seu dispositivo.](quick-configure-rules.md)

## <a name="receive-a-notification"></a>Receber uma notificação

O Azure IoT Central envia notificações sobre os dispositivos como mensagens de e-mail. Como construtor, adicionou uma regra para enviar uma notificação a um operador quando a humidade de um sensor de dispositivo ligado excedeu um limiar. Como operador, verifique os seus e-mails para obter notificações.

Abra a mensagem de e-mail que recebeu no final das [regras e ações do Configure para o seu dispositivo](quick-configure-rules.md) de arranque rápido. No e-mail, selecione o link para o dispositivo:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Screenshot que mostra e-mail de notificação":::

A **vista geral** para o dispositivo simulado que criou nos quickstarts anteriores abre no seu navegador:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Screenshot que mostra visão geral do dispositivo que desencadeou a notificação":::

## <a name="investigate-an-issue"></a>Investigar um problema

Como operador, pode visualizar informações sobre o dispositivo no **resumo**, **sobre** e **visualizações de comandos.** O construtor criou uma visão **do dispositivo Manage** para que possa editar informações do dispositivo e definir as propriedades do dispositivo.

O gráfico no painel mostra um enredo da humidade do dispositivo. Você decide que a humidade do dispositivo é muito alta.

## <a name="remediate-an-issue"></a>Resolver um problema

Para escoar uma alteração no dispositivo, utilize a página **do dispositivo Manage.**

Mude **a temperatura do alvo** para 80 para aquecer o dispositivo e reduzir a humidade. Escolha **Guardar** para atualizar o dispositivo. Quando o dispositivo confirma a alteração das definições, o estado da propriedade muda para **sincronizado:**

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Screenshot que mostra a definição atualizada da temperatura do alvo para o dispositivo":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Receber uma notificação
* Investigar um problema
* Resolver um problema

Agora que sabe agora para monitorizar o seu dispositivo, o próximo passo sugerido é:

> [!div class="nextstepaction"]
> [Construa e gerencie um modelo de dispositivo.](howto-set-up-template.md)
