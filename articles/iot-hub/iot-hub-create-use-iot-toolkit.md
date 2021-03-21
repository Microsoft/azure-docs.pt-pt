---
title: Crie um Hub Azure IoT utilizando ferramentas Azure IoT para código VS | Microsoft Docs
description: Aprenda a usar as ferramentas Azure IoT para o Código do Estúdio Visual para criar um hub Azure IoT num grupo de recursos.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75912252"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Criar um hub IoT utilizando as ferramentas Azure IoT para código de estúdio visual

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra-lhe como usar as [Ferramentas Azure IoT para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para criar um hub Azure IoT. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para completar este artigo, precisa do seguinte:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Código de Estúdio Visual.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

1. No Código do Estúdio Visual, abra a vista **explorer.**

2. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub Devices.** 

   ![Expandir dispositivos hub Azure IoT](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Clique no **cabeçalho ...** na secção **Azure IoT Hub Devices.** Se não vir a elipse, paire sobre o cabeçalho. 

4. Escolha **Criar Hub IoT**.

5. Um pop-up aparecerá no canto inferior direito para que se inscreva no Azure pela primeira vez.

6. Selecione a subscrição Azure. 

7. Selecione grupo de recursos.

8. Selecione a localização.

9. Selecione o nível de preços.

10. Insira um nome globalmente único para o seu IoT Hub.

11. Aguarde alguns minutos até que o Hub IoT seja criado.

## <a name="next-steps"></a>Passos seguintes

Agora implementou um hub IoT usando as Ferramentas Azure IoT para Código de Estúdio Visual. Para explorar mais, confira os seguintes artigos:

* [Utilize as Ferramentas Azure IoT para o Código do Estúdio Visual para enviar e receber mensagens entre o seu dispositivo e um Hub IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Utilize as ferramentas Azure IoT para código de estúdio visual para gestão de dispositivos Azure IoT Hub](iot-hub-device-management-iot-toolkit.md)

* [Consulte o Hub Azure IoT para a página wiki do Código VS](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
