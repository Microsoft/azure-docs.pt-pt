---
title: Use ferramentas Azure IoT para VSCode para gerente de mensagens IT Hub
description: Saiba como usar ferramentas Azure IoT para o Código de Estúdio Visual para monitorizar o dispositivo para cloud messages e enviar mensagens de nuvem para dispositivos no Hub Azure IoT.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 31a5d55d1067b9dd946c1667118d0bde5ee3d59e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682505"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Utilize ferramentas Azure IoT para código de estúdio visual para enviar e receber mensagens entre o seu dispositivo e o IoT Hub

![Diagrama de ponta a ponta](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[As Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) são uma extensão útil do Código do Estúdio Visual que facilita a gestão do IoT Hub e o desenvolvimento de aplicações IoT. Este artigo centra-se em como usar ferramentas Azure IoT para código de estúdio visual para enviar e receber mensagens entre o seu dispositivo e o seu hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>O que vai aprender

Aprende a usar ferramentas Azure IoT para o Código do Estúdio Visual para monitorizar mensagens de dispositivo-nuvem e enviar mensagens cloud-to-device. As mensagens dispositivo-a-nuvem podem ser dados de sensores que o seu dispositivo recolhe e depois envia para o seu hub IoT. As mensagens cloud-to-device podem ser comandos que o seu hub IoT envia para o seu dispositivo para piscar um LED que está ligado ao seu dispositivo.

## <a name="what-you-will-do"></a>O que vai fazer

* Utilize ferramentas Azure IoT para o Código do Estúdio Visual para monitorizar as mensagens dispositivo-cloud.

* Utilize ferramentas Azure IoT para o Código do Estúdio Visual para enviar mensagens cloud-to-device.

## <a name="what-you-need"></a>Do que precisa

* Uma subscrição ativa do Azure.

* Um hub Azure IoT sob a sua assinatura.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Ferramentas Azure IoT para código VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou cópia e cola este URL numa janela do navegador:`vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

1. Na visão do **Explorer** do Código VS, expanda a secção de **dispositivos Hub Azure IoT** no canto inferior esquerdo.

2. Clique em **Selecionar Hub IoT** no menu de contexto.

3. Um pop-up vai aparecer no canto inferior direito para deixá-lo assinar no Azure pela primeira vez.

4. Depois de iniciar sessão, será mostrada a sua lista de subscrição Azure e, em seguida, selecione A Subscrição Azure e o IoT Hub.

5. A lista de dispositivos será mostrada no separador **Dispositivos Hub Azure IoT** em poucos segundos.

   > [!Note]
   > Também pode concluir a configuração ao escolher **Definir Cadeia de Ligação do Hub IoT**. Introduza a cadeia de ligação política **iothubowner** para o hub IoT a que o seu dispositivo IoT se conecta na janela pop-up.

## <a name="monitor-device-to-cloud-messages"></a>Monitorizar mensagens dispositivo-nuvem

Para monitorizar as mensagens enviadas do seu dispositivo para o seu hub IoT, siga estes passos:

1. Clique no seu dispositivo e selecione **Iniciar monitorização do ponto final do evento incorporado**.

2. As mensagens monitorizadas serão mostradas na vista **OUTPUT** > **Azure IoT Hub.**

3. Para parar a monitorização, clique à direita na vista **OUTPUT** e selecione **Stop Monitoring Built-in Event Endpoint**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu hub IoT para o seu dispositivo, siga estes passos:

1. Clique no seu dispositivo e selecione **Enviar mensagem C2D para o dispositivo**.

2. Introduza a mensagem na caixa de entrada.

3. Os resultados serão mostrados na vista **OUTPUT** > **Azure IoT Hub.**

## <a name="next-steps"></a>Passos seguintes

Aprendeu a monitorizar mensagens dispositivo-nuvem e a enviar mensagens cloud-to-device entre o seu dispositivo IoT e o Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]