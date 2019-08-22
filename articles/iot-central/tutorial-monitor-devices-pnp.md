---
title: Monitorizar os seus dispositivos no Azure IoT Central | Microsoft Docs
description: Como operador, utilize a aplicação Azure IoT Central para monitorizar os seus dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878118"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Tutorial: Usar o IoT Central do Azure para monitorar seus dispositivos (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra-lhe, como operador, como utilizar a aplicação Microsoft Azure IoT Central para monitorizar os seus dispositivos e alterar as definições.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Receber uma notificação
> * Investigar um problema
> * Resolver um problema

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, o construtor deve concluir os três tutoriais do construtor para criar a aplicação Azure IoT Central:

* [Definir um novo tipo de dispositivo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Adicionar um dispositivo](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Receber uma notificação

O Azure IoT Central envia notificações sobre os dispositivos como mensagens de e-mail. O Construtor adicionou uma regra para enviar uma notificação quando a temperatura em um dispositivo de sensor ambiental conectado excedeu um limite. Verifique os e-mails enviados para a conta que o construtor escolheu para receber notificações.

Abra a mensagem de e-mail que recebeu no final do tutorial [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). No email, selecione o link para o dispositivo:

![Email de notificação de alerta](media/tutorial-monitor-devices-pnp/email.png)

A exibição de **painel** do dispositivo simulado do sensor ambiental criado nos tutoriais anteriores é aberta no navegador:

![Dispositivo que acionou a mensagem de e-mail de notificação](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Investigar um problema

Como um operador, você pode exibir informações sobre o dispositivo no **painel**, **as propriedades do sensor ambiental e as**páginas de **comandos** . O Construtor personalizou as páginas de propriedades do **painel** e do **sensor ambiental** para exibir informações importantes sobre um dispositivo de sensor ambiental conectado.

Escolha a vista **Dashboard** para ver as informações sobre o dispositivo.

O gráfico no dashboard mostra um desenho da temperatura do dispositivo. Você também pode ver a temperatura de destino atual para o dispositivo no bloco de **Propriedades do dispositivo** . Decide que a temperatura ideal é demasiado elevada.

## <a name="remediate-an-issue"></a>Resolver um problema

Para fazer uma alteração no dispositivo, use a página de **Propriedades do sensor ambiental** :

1. Escolha **Propriedades do sensor ambiental**. Altere o **nível de brilho** para 10. Escolha **salvar** para atualizar o dispositivo. Quando o dispositivo confirma a alteração das configurações, o status da propriedade é alteradopara sincronizado:

    ![Atualizar definições](media/tutorial-monitor-devices-pnp/change-settings.png)

2. Escolha **painel** e verifique o novo valor de propriedade:

    ![Dashboard do dispositivo atualizado](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

* Receber uma notificação
* Investigar um problema
* Resolver um problema

Agora que você já conhece agora para monitorar seu dispositivo, a próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).