---
title: Usar as ferramentas do Azure IoT para VSCode para gerente mensagens do hub de ti
description: Saiba como usar as ferramentas de IoT do Azure para Visual Studio Code para monitorar mensagens de dispositivo em nuvem e enviar mensagens de nuvem para dispositivo no Hub IoT do Azure.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 785c6f2b36396558cc21ce6c025be59c456ff32d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083262"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usar as ferramentas de IoT do Azure para Visual Studio Code para enviar e receber mensagens entre o dispositivo e o Hub IoT

![Diagrama de ponta a ponta](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

As [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) são uma extensão útil Visual Studio Code que facilita o gerenciamento do Hub IOT e o desenvolvimento de aplicativos IOT. Este artigo se concentra em como usar as ferramentas do Azure IoT para Visual Studio Code para enviar e receber mensagens entre o dispositivo e o Hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>O que você aprenderá

Você aprende a usar as ferramentas de IoT do Azure para Visual Studio Code monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo. As mensagens do dispositivo para a nuvem podem ser dados de sensor que seu dispositivo coleta e envia para o Hub IoT. Mensagens da nuvem para o dispositivo podem ser comandos que seu hub IoT envia para seu dispositivo para piscar um LED que está conectado ao seu dispositivo.

## <a name="what-you-will-do"></a>O que você fará

* Use as ferramentas do Azure IoT para Visual Studio Code para monitorar mensagens do dispositivo para a nuvem.

* Use as ferramentas do Azure IoT para Visual Studio Code para enviar mensagens da nuvem para o dispositivo.

## <a name="what-you-need"></a>Do que precisa

* Uma subscrição ativa do Azure.

* Um hub IoT do Azure em sua assinatura.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Ferramentas do Azure IOT para vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou [abrir este link no Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu hub IoT

1. Na exibição do **Gerenciador** de vs Code, expanda a seção **dispositivos do Hub IOT do Azure** no canto inferior esquerdo.

2. Clique em **selecionar Hub IOT** no menu de contexto.

3. Um pop-up será exibido no canto inferior direito para permitir que você entre no Azure pela primeira vez.

4. Depois de entrar, sua lista de assinaturas do Azure será mostrada e, em seguida, selecione assinatura do Azure e Hub IoT.

5. A lista de dispositivos será mostrada na guia **dispositivos do Hub IOT do Azure** em alguns segundos.

   > [!Note]
   > Também pode concluir a configuração ao escolher **Definir Cadeia de Ligação do Hub IoT**. Insira a cadeia de conexão da política **iothubowner** para o Hub IOT ao qual seu dispositivo IOT se conecta na janela pop-up.

## <a name="monitor-device-to-cloud-messages"></a>Monitorar mensagens do dispositivo para a nuvem

Para monitorar as mensagens que são enviadas do seu dispositivo para o Hub IoT, siga estas etapas:

1. Clique com o botão direito do mouse no dispositivo e selecione **Iniciar Monitoramento do ponto de extremidade do evento interno**.

2. As mensagens monitoradas serão mostradas na **saída** > exibição **do kit de ferramentas do Hub IOT do Azure** .

3. Para interromper o monitoramento, clique com o botão direito do mouse na exibição de **saída** e selecione **parar monitoramento de ponto de extremidade de evento interno**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do Hub IoT para seu dispositivo, siga estas etapas:

1. Clique com o botão direito do mouse no dispositivo e selecione **Enviar mensagem C2D para o dispositivo**.

2. Insira a mensagem na caixa de entrada.

3. Os resultados serão mostrados na **saída** > exibição **do kit de ferramentas do Hub IOT do Azure** .

## <a name="next-steps"></a>Passos seguintes

Você aprendeu como monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo entre o dispositivo IoT e o Hub IoT do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]