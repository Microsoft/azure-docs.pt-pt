---
title: Crie um Hub Azure IoT utilizando ferramentas Azure IoT para código VS [ Código VS] Microsoft Docs
description: Aprenda a usar as ferramentas Azure IoT para visual studio code para criar um hub Azure IoT em um grupo de recursos.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912252"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Crie um hub IoT usando as ferramentas Azure IoT para código de estúdio visual

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra-lhe como usar as [Ferramentas Azure IoT para o Código](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) do Estúdio Visual para criar um hub Azure IoT. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para completar este artigo, precisa do seguinte:

- Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- [Código de estúdio visual](https://code.visualstudio.com/)

- [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Código de Estúdio Visual.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

1. No Visual Studio Code, abra a vista **Explorer.**

2. Na parte inferior do Explorer, expanda a secção de **dispositivos Hub Azure IoT.** 

   ![Expandir dispositivos hub Azure IoT](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Clique no **...** no cabeçalho da secção **dispositivos do Hub Azure IoT.** Se não vires a elipse, paira sobre o cabeceamento. 

4. Escolha **Criar hub IoT**.

5. Um pop-up vai aparecer no canto inferior direito para deixá-lo assinar no Azure pela primeira vez.

6. Selecione subscrição Azure. 

7. Selecione grupo de recursos.

8. Selecione a localização.

9. Selecione o nível de preços.

10. Introduza um nome globalmente único para o seu Hub IoT.

11. Espere alguns minutos até que o Hub IoT seja criado.

## <a name="next-steps"></a>Passos seguintes

Agora implementou um hub IoT usando as ferramentas Azure IoT para código de estúdio visual. Para explorar mais, confira os seguintes artigos:

* [Utilize as ferramentas Azure IoT para o Código do Estúdio Visual para enviar e receber mensagens entre o seu dispositivo e um Hub IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Utilize as ferramentas Azure IoT para código de estúdio visual para a gestão de dispositivos Azure IoT Hub](iot-hub-device-management-iot-toolkit.md)

* [Consulte o Hub Azure IoT para a página wiki do Código VS](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
