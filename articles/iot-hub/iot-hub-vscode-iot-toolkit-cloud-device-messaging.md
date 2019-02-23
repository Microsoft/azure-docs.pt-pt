---
title: Gerir o dispositivo do IoT Hub do Azure na cloud messaging com ferramentas de IoT do Azure para Visual Studio Code | Documentos da Microsoft
description: Saiba como utilizar ferramentas de IoT do Azure para Visual Studio Code para monitorizar o dispositivo para mensagens na cloud e enviar na cloud para mensagens de dispositivos no IoT Hub do Azure.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 0455dafaa39f9f6bc19399fab31663d6b2e06e95
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726351"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Utilizar ferramentas de IoT do Azure para Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o IoT Hub

![Diagrama de ponto a ponto](media/iot-hub-get-started-e2e-diagram/2.png)

[Ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) é uma extensão do Visual Studio Code útil que facilita o IoT Hub gestão e desenvolvimento de aplicações de IoT. Este artigo se concentra em como usar ferramentas de IoT do Azure para Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o seu hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-will-learn"></a>O que aprenderá

Saiba como utilizar ferramentas de IoT do Azure para Visual Studio Code para monitorizar mensagens do dispositivo para a cloud e para enviar mensagens da cloud para o dispositivo. Mensagens do dispositivo para a cloud podem ser dados de sensor que o dispositivo recolhe e, em seguida, envia ao seu hub IoT. Mensagens cloud para o dispositivo podem ser comandos que o seu hub IoT envia para o seu dispositivo para blink um LED que está ligada ao seu dispositivo.

## <a name="what-you-will-do"></a>O que fará

- Utilize ferramentas de IoT do Azure para Visual Studio Code para monitorizar mensagens do dispositivo para a cloud.
- Utilize ferramentas de IoT do Azure para Visual Studio Code para enviar mensagens de cloud-para-dispositivo.

## <a name="what-you-need"></a>Do que precisa

- Uma subscrição ativa do Azure.
- Um hub IoT do Azure com a sua subscrição.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Ferramentas de IoT do Azure para o VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu hub IoT

1. Na **Explorer** ver do VS Code, expanda **dispositivos do Azure IoT Hub** secção no canto inferior esquerdo.
1. Clique em **selecione o IoT Hub** no menu de contexto.
1. Mostra um pop-up no canto inferior direito para permitem-lhe iniciar sessão no Azure pela primeira vez.
1. Depois de iniciar sessão, sua lista de subscrições do Azure serão apresentados, em seguida, selecione a subscrição do Azure e o IoT Hub.
1. A lista de dispositivos será mostrada na **dispositivos do Azure IoT Hub** guia em poucos segundos.

   > [!Note]
   > Também pode concluir a configuração ao escolher **Definir Cadeia de Ligação do Hub IoT**. Introduza a cadeia de ligação do hub IoT que se liga o dispositivo de IoT na janela de pop-up.
   
## <a name="monitor-device-to-cloud-messages"></a>Monitorizar mensagens do dispositivo para a cloud

Para monitorizar mensagens que são enviadas a partir do seu dispositivo ao seu hub IoT, siga estes passos:

1. O dispositivo com o botão direito e selecione **iniciar mensagens D2C monitorização**.
1. As mensagens monitorizadas serão mostradas na **saída** > **o Kit de ferramentas do Azure IoT Hub** vista.
1. Para parar a monitorização, clique com botão direito a **saída** ver e selecionar **parar mensagens D2C monitorização**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu hub IoT para o seu dispositivo, siga estes passos:

1. O dispositivo com o botão direito e selecione **enviar mensagem de C2D para dispositivos**. 
1. Introduza a mensagem na caixa de entrada.
1. Os resultados serão mostrados na **saída** > **o Kit de ferramentas do Azure IoT Hub** vista.

## <a name="next-steps"></a>Passos Seguintes

Já aprendeu a monitorizar mensagens do dispositivo para a cloud e enviar mensagens de cloud-para-dispositivo entre o seu dispositivo IoT e IoT Hub do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
