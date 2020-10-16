---
title: Utilize ferramentas Azure IoT para VSCode para gerente de mensagens IT Hub
description: Aprenda a usar ferramentas Azure IoT para o Código do Estúdio Visual monitorizar o dispositivo para monitorizar mensagens na nuvem e enviar mensagens de nuvem para o dispositivo no Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: b9d8f0c2a573ba1749b414a389cd7181ddd5f95a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321688"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Utilize ferramentas IoT Azure para o Código do Estúdio Visual para enviar e receber mensagens entre o seu dispositivo e o IoT Hub

![Diagrama de ponta a ponta](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) é uma extensão útil do Código do Estúdio Visual que facilita a gestão do IoT Hub e o desenvolvimento de aplicações IoT. Este artigo centra-se em como usar ferramentas Azure IoT para o Código do Estúdio Visual para enviar e receber mensagens entre o seu dispositivo e o seu hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>O que vai aprender

Aprende a usar ferramentas Azure IoT para o Código do Estúdio Visual monitorizar mensagens de dispositivo para nuvem e enviar mensagens cloud-to-device. As mensagens dispositivo-nuvem podem ser dados de sensores que o seu dispositivo recolhe e depois enviam para o seu hub IoT. As mensagens nuvem-dispositivo podem ser comandos que o seu hub IoT envia para o seu dispositivo para piscar um LED que está ligado ao seu dispositivo.

## <a name="what-you-will-do"></a>O que vai fazer

* Utilize ferramentas Azure IoT para código de estúdio visual para monitorizar mensagens dispositivo-a-nuvem.

* Utilize ferramentas IoT Azure para o Código do Estúdio Visual para enviar mensagens nuvem-dispositivo.

## <a name="what-you-need"></a>O que precisa

* Uma subscrição ativa do Azure.

* Um hub Azure IoT sob a sua assinatura.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Ferramentas Azure IoT para código VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou copiar e colar este URL numa janela do navegador: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

1. Na visão do **Explorer** do Código VS, expanda a secção **de Dispositivos hub Azure IoT** no canto inferior esquerdo.

2. Clique **em Select IoT Hub** no menu de contexto.

3. Um pop-up aparecerá no canto inferior direito para que se inscreva no Azure pela primeira vez.

4. Depois de iniciar sposição, a sua lista de subscrições Azure será apresentada e, em seguida, selecione Azure Subscription e IoT Hub.

5. A lista de dispositivos será mostrada no separador **Azure IoT Hub Devices** em poucos segundos.

   > [!Note]
   > Também pode concluir a configuração ao escolher **Definir Cadeia de Ligação do Hub IoT**. Introduza a cadeia de ligação de **iothubowner** para o hub IoT a que o seu dispositivo IoT se conecta na janela pop-up.

## <a name="monitor-device-to-cloud-messages"></a>Monitorizar mensagens dispositivo-nuvem

Para monitorizar as mensagens enviadas do seu dispositivo para o seu hub IoT, siga estes passos:

1. Clique com o botão direito no seu dispositivo e selecione **Iniciar a monitorização do ponto final do evento incorporado**.

2. As mensagens monitorizadas serão mostradas na vista **OUTPUT**  >  **Azure IoT Hub.**

3. Para parar a monitorização, clique com o botão direito na vista **OUTPUT** e selecione **Stop Monitoring Built-in Event Endpoint**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu hub IoT para o seu dispositivo, siga estes passos:

1. Clique com o botão direito no seu dispositivo e selecione **Enviar mensagem C2D para dispositivo**.

2. Introduza a mensagem na caixa de entrada.

3. Os resultados serão mostrados na vista **OUTPUT**  >  **Azure IoT Hub.**

## <a name="next-steps"></a>Passos seguintes

Aprendeu a monitorizar mensagens de dispositivo para nuvem e a enviar mensagens nuvem-a-dispositivo entre o seu dispositivo IoT e o Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]