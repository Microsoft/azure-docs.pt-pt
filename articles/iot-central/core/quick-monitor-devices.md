---
title: Quickstart - Monitorize os seus dispositivos em Azure IoT Central
description: Como operador, aprenda a utilizar a sua aplicação Azure IoT Central para monitorizar os seus dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e56b733a567c706c2a15f2c30ed93a47c244db11
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168701"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Quickstart: Use Azure IoT Central para monitorizar os seus dispositivos

*Este artigo aplica-se a operadores, construtores e administradores.*

Este quickstart mostra-lhe, como operador, como utilizar a sua aplicação Microsoft Azure IoT Central para monitorizar os seus dispositivos e alterar as definições.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deverá completar os três quickstarts anteriores [Criar uma aplicação Central Azure IoT,](./quick-deploy-iot-central.md) [adicionar um dispositivo simulado à sua aplicação IoT Central](./quick-create-pnp-device.md) e [configurar regras e ações para o seu dispositivo](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Receber uma notificação

O Azure IoT Central envia notificações sobre os dispositivos como mensagens de e-mail. O construtor adicionou uma regra para enviar uma notificação quando a temperatura num sensor de dispositivo conectado excedia um limiar. Verifique os e-mails enviados para a conta que o construtor escolheu para receber notificações.

Abra a mensagem de e-mail recebida no final das regras e ações do [Configure para o seu dispositivo](quick-configure-rules.md) de arranque rápido. No e-mail, selecione o link para o dispositivo:

![E-mail de notificação de alerta](media/quick-monitor-devices/email.png)

A visão **geral** do dispositivo simulado que criou nos quickstarts anteriores abre no seu navegador:

![Dispositivo que acionou a mensagem de e-mail de notificação](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Investigar um problema

Como operador, pode visualizar informações sobre o dispositivo na **visão geral**, **sobre**e **comandos.** O construtor criou uma visão do **dispositivo Manage** para que editasse informações do dispositivo e definisse as propriedades do dispositivo.

O gráfico no dashboard mostra um desenho da temperatura do dispositivo. Você decide que a temperatura do dispositivo é muito alta.

## <a name="remediate-an-issue"></a>Resolver um problema

Para alterar o dispositivo, utilize a página **do dispositivo Gerir.**

Mude a **velocidade do ventilador** para 500 para arrefecer o dispositivo. Escolha **Guardar** para atualizar o dispositivo. Quando o dispositivo confirma a alteração das definições, o estado da propriedade muda para **sincronizado:**

![Atualizar definições](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Receber uma notificação
* Investigar um problema
* Resolver um problema

Agora que sabe agora para monitorizar o seu dispositivo, o próximo passo sugerido é:

> [!div class="nextstepaction"]
> [Construir e gerir um modelo](howto-set-up-template.md)de dispositivo.
