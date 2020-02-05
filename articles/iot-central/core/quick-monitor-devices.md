---
title: Início rápido-monitorar seus dispositivos no Azure IoT Central
description: Como operador, aprenda a utilizar a sua aplicação Azure IoT Central para monitorizar os seus dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e5185d78b0ffc17e861f49d294af65ddcd200d07
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027749"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Quickstart: Use Azure IoT Central para monitorizar os seus dispositivos

*Este artigo aplica-se a operadores, construtores e administradores.*

Este guia de início rápido mostra você, como um operador, como usar o aplicativo Microsoft Azure IoT Central para monitorar seus dispositivos e alterar as configurações.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os três guias de início rápido anteriores [criar um aplicativo de IOT central do Azure](./quick-deploy-iot-central.md), [Adicionar um dispositivo simulado ao aplicativo IOT central](./quick-create-pnp-device.md) e [configurar regras e ações para seu dispositivo](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Receber uma notificação

O Azure IoT Central envia notificações sobre os dispositivos como mensagens de e-mail. O construtor adicionou uma regra para enviar uma notificação quando a temperatura num sensor de dispositivo conectado excedia um limiar. Verifique os e-mails enviados para a conta que o construtor escolheu para receber notificações.

Abra a mensagem de email recebida no final das [regras e ações de configuração para o](quick-configure-rules.md) início rápido do seu dispositivo. No email, selecione o link para o dispositivo:

![Email de notificação de alerta](media/quick-monitor-devices/email.png)

A visão **geral** do dispositivo simulado que criou nos quickstarts anteriores abre no seu navegador:

![Dispositivo que acionou a mensagem de e-mail de notificação](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Investigar um problema

Como operador, pode visualizar informações sobre o dispositivo na **visão geral**, **sobre**e **comandos.** O construtor criou uma visão do **dispositivo Manage** para que editasse informações do dispositivo e definisse as propriedades do dispositivo.

Escolha o modo de exibição **visão geral** para ver informações sobre o dispositivo.

O gráfico no dashboard mostra um desenho da temperatura do dispositivo. Você decide que a temperatura do dispositivo é muito alta.

## <a name="remediate-an-issue"></a>Resolver um problema

Para alterar o dispositivo, utilize a página **do dispositivo Gerir.**

Mude a **velocidade do ventilador** para 500 para arrefecer o dispositivo. Escolha **salvar** para atualizar o dispositivo. Quando o dispositivo confirma a alteração das configurações, o status da propriedade é alterado para **sincronizado**:

![Atualizar definições](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Receber uma notificação
* Investigar um problema
* Resolver um problema

Agora que você já conhece agora para monitorar seu dispositivo, a próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Crie e gerencie um modelo de dispositivo](howto-set-up-template.md).
