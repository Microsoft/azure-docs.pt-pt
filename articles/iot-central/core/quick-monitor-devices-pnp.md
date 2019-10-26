---
title: Monitorizar os seus dispositivos no Azure IoT Central | Microsoft Docs
description: Como operador, utilize a aplicação Azure IoT Central para monitorizar os seus dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: db229a6c6d8fded8dc2434653f34ec0c727cc761
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958078"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Início rápido: usar o Azure IoT Central para monitorar seus dispositivos (recursos de visualização)

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este guia de início rápido mostra você, como um operador, como usar o aplicativo Microsoft Azure IoT Central para monitorar seus dispositivos e alterar as configurações.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os três guias de início rápido anteriores [criar um aplicativo de IOT central do Azure](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), [Adicionar um dispositivo simulado ao aplicativo IOT central](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) e [configurar regras e ações para seu dispositivo](quick-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="receive-a-notification"></a>Receber uma notificação

O Azure IoT Central envia notificações sobre os dispositivos como mensagens de e-mail. O Construtor adicionou uma regra para enviar uma notificação quando a temperatura em um dispositivo de sensor ambiental conectado excedeu um limite. Verifique os e-mails enviados para a conta que o construtor escolheu para receber notificações.

Abra a mensagem de email recebida no final das [regras e ações de configuração para o](quick-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) início rápido do seu dispositivo. No email, selecione o link para o dispositivo:

![Email de notificação de alerta](media/quick-monitor-devices-pnp/email.png)

A exibição de **painel** do dispositivo simulado do sensor ambiental criado nos guias de início rápido anteriores é aberta no navegador:

![Dispositivo que acionou a mensagem de e-mail de notificação](media/quick-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Investigar um problema

Como um operador, você pode exibir informações sobre o dispositivo na **visão geral**, **Propriedades do sensor ambiental**e páginas de **comandos** . O Construtor personalizou as páginas de propriedades do **painel** e do **sensor ambiental** para exibir informações importantes sobre um dispositivo de sensor ambiental conectado.

Escolha o modo de exibição **visão geral** para ver informações sobre o dispositivo.

O gráfico no dashboard mostra um desenho da temperatura do dispositivo. Você decide que a temperatura do dispositivo é muito alta.

## <a name="remediate-an-issue"></a>Resolver um problema

Para fazer uma alteração no dispositivo, use a página **Propriedades do sensor ambiental** .

Escolha **Propriedades do sensor ambiental**. Altere o **nível de brilho** para 10. Escolha **salvar** para atualizar o dispositivo. Quando o dispositivo confirma a alteração das configurações, o status da propriedade é alterado para **sincronizado**:

![Atualizar definições](media/quick-monitor-devices-pnp/change-settings.png)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Receber uma notificação
* Investigar um problema
* Resolver um problema

Agora que você já conhece agora para monitorar seu dispositivo, a próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Crie e gerencie um modelo de dispositivo](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
