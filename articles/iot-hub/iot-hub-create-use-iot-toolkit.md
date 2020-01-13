---
title: Criar um hub IoT do Azure usando as ferramentas de IoT do Azure para VS Code | Microsoft Docs
description: Saiba como usar as ferramentas de IoT do Azure para Visual Studio Code para criar um hub IoT do Azure em um grupo de recursos.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912252"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Criar um hub IoT usando as ferramentas de IoT do Azure para Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra como usar as ferramentas do [Azure IOT para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para criar um hub IOT do Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este artigo, você precisará do seguinte:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

1. No Visual Studio Code, abra a **Explorer** vista.

2. Na parte inferior do Explorer, expanda o **dispositivos do Azure IoT Hub** secção. 

   ![Expandir dispositivos do Hub IoT do Azure](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Clique no **...**  no **dispositivos do Azure IoT Hub** cabeçalho de secção. Se não vir o botão de reticências, Paire o rato sobre o cabeçalho. 

4. Escolha **criar Hub IOT**.

5. Um pop-up será exibido no canto inferior direito para permitir que você entre no Azure pela primeira vez.

6. Selecione assinatura do Azure. 

7. Selecione Grupo de recursos.

8. Selecione o local.

9. Selecione tipo de preço.

10. Insira um nome globalmente exclusivo para o Hub IoT.

11. Aguarde alguns minutos até que o Hub IoT seja criado.

## <a name="next-steps"></a>Passos seguintes

Agora você implantou um hub IoT usando as ferramentas de IoT do Azure para Visual Studio Code. Para explorar ainda mais, confira os seguintes artigos:

* [Use as ferramentas de IOT do Azure para Visual Studio Code para enviar e receber mensagens entre o dispositivo e um hub IOT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Usar as ferramentas do Azure IoT para Visual Studio Code para o gerenciamento de dispositivos do Hub IoT do Azure](iot-hub-device-management-iot-toolkit.md)

* [Consulte a página wiki do Hub IOT do Azure para vs Code](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
