---
title: Gerenciar mensagens do dispositivo de nuvem do Hub IoT do Azure com o Cloud Explorer para Visual Studio | Microsoft Docs
description: Saiba como usar o Cloud Explorer para Visual Studio para monitorar mensagens de dispositivo em nuvem e enviar mensagens de nuvem para dispositivo no Hub IoT do Azure.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: b8ea705b55c1485cab2e1478d2d455f1d4a427d3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050243"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usar o Cloud Explorer para Visual Studio para enviar e receber mensagens entre o dispositivo e o Hub IoT

![Diagrama de ponta a ponta](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

O [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) é uma extensão útil do Visual Studio que permite exibir os recursos do Azure, inspecionar suas propriedades e executar ações de desenvolvedor de chave no Visual Studio. Este artigo se concentra em como usar o Cloud Explorer para enviar e receber mensagens entre o dispositivo e o Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>O que irá aprender

Neste artigo, você aprenderá a usar o Cloud Explorer para Visual Studio para monitorar mensagens do dispositivo para a nuvem e para enviar mensagens da nuvem para o dispositivo. As mensagens do dispositivo para a nuvem podem ser dados de sensor que seu dispositivo coleta e envia para o Hub IoT. Mensagens da nuvem para o dispositivo podem ser comandos que o Hub IoT envia para seu dispositivo. Por exemplo, pisque um LED que está conectado ao seu dispositivo.

## <a name="what-you-do"></a>O que você faz

Neste artigo, você executa as seguintes tarefas:

- Use o Cloud Explorer para Visual Studio para monitorar mensagens do dispositivo para a nuvem.

- Use o Cloud Explorer para Visual Studio para enviar mensagens da nuvem para o dispositivo.

## <a name="what-you-need"></a>Do que precisa

Você precisa dos seguintes pré-requisitos:

- Uma subscrição ativa do Azure.

- Um hub IoT do Azure em sua assinatura.

- Microsoft Visual Studio 2017 atualização 9 ou posterior. Este artigo usa o [Visual Studio 2019](https://www.visualstudio.com/vs/).

- O componente Gerenciador de nuvem da Instalador do Visual Studio, que é selecionado por padrão com a carga de trabalho do Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Atualizar o Cloud Explorer para a versão mais recente

O componente Cloud Explorer da Instalador do Visual Studio para Visual Studio 2017 dá suporte apenas ao monitoramento de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo. Para usar o Visual Studio 2017, baixe e instale o [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)mais recente.

## <a name="sign-in-to-access-your-hub"></a>Entre para acessar seu hub

Para acessar o Hub, siga estas etapas:

1. No Visual Studio, selecione **Exibir** > **Cloud Explorer** para abrir o Cloud Explorer.

1. Selecione o ícone gerenciamento de contas para mostrar suas assinaturas.

    ![Ícone de gerenciamento de conta](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se você estiver conectado ao Azure, suas contas serão exibidas. Para entrar no Azure pela primeira vez, escolha **Adicionar uma conta**.

1. Selecione as assinaturas do Azure que você deseja usar e escolha **aplicar**.

1. Expanda sua assinatura e expanda os **hubs IOT**.  Em cada Hub, você pode ver seus dispositivos para esse Hub.

    ![Lista de Dispositivos](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorar mensagens do dispositivo para a nuvem

Para monitorar as mensagens que são enviadas do seu dispositivo para o Hub IoT, siga estas etapas:

1. Clique com o botão direito do mouse no dispositivo ou Hub IoT e selecione **Iniciar Monitoramento de mensagem D2C**.

    ![Iniciar Monitoramento de mensagem D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. As mensagens monitoradas aparecem em **saída**.

    ![D2Cndo o resultado da mensagem de monitoramento](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Para interromper o monitoramento, clique com o botão direito do mouse em qualquer dispositivo ou Hub IoT e selecione **parar monitoramento de mensagem D2C**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do Hub IoT para seu dispositivo, siga estas etapas:

1. Clique com o botão direito do mouse no dispositivo e selecione **Enviar mensagem C2D**.

1. Insira a mensagem na caixa de entrada.

    ![Enviar mensagem C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Os resultados aparecem em **saída**.

    ![Enviar resultado da mensagem C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Passos Seguintes

Você aprendeu como monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo entre o dispositivo IoT e o Hub IoT do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]