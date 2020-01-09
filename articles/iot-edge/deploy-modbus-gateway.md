---
title: Traduzir protocolos modbus com gateways - Azure IoT Edge | Documentos da Microsoft
description: Permitir que os dispositivos que utilizam o Modbus TCP para comunicar com o Azure IoT Hub mediante a criação de um dispositivo de gateway do IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 8a9f0008f1a1ea1a57f3c0e7e17b8cf3ae5e959c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434546"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Ligar dispositivos Modbus TCP através de um gateway de dispositivo do IoT Edge

Se desejar conectar dispositivos IoT que usam protocolos TCP ou RTU Modbus a um hub IoT do Azure, você poderá usar um dispositivo IoT Edge como um gateway. O dispositivo de gateway lê os dados dos seus dispositivos Modbus e comunica-os para a cloud com um protocolo suportado.

![Dispositivos Modbus se conectam ao Hub IoT por meio do gateway de IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Este artigo mostra como criar a sua própria imagem de contentor para um módulo do Modbus (também pode utilizar um exemplo pré-criado) e, em seguida, implementá-la no dispositivo IoT Edge que vai funcionar como o gateway.

Este artigo pressupõe que está a utilizar o protocolo Modbus TCP. Para obter mais informações sobre como configurar o módulo para dar suporte ao Modbus RTU, consulte o projeto de [módulo Azure IOT Edge Modbus](https://github.com/Azure/iot-edge-modbus) no github.

## <a name="prerequisites"></a>Pré-requisitos
* Um dispositivo Azure IoT Edge. Para obter instruções sobre como configurar um, consulte [implantar Azure IOT Edge no Windows](quickstart.md) ou [Linux](quickstart-linux.md).
* A cadeia de ligação da chave primária do dispositivo IoT Edge.
* Um dispositivo Modbus físico ou simulado que suporte o Modbus TCP. Você precisará saber seu endereço IPv4.

## <a name="prepare-a-modbus-container"></a>Preparar um contentor do Modbus

Se quiser testar a funcionalidade do gateway do Modbus, a Microsoft disponibiliza-lhe um módulo de exemplo. Você pode acessar o módulo no Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)ou com o URI da imagem, `mcr.microsoft.com/azureiotedge/modbus:1.0`.

Se você quiser criar seu próprio módulo e personalizá-lo para o seu ambiente, há um projeto de [módulo Azure IOT Edge Modbus](https://github.com/Azure/iot-edge-modbus) de código aberto no github. Siga as orientações desse projeto para criar a sua própria imagem de contentor. Para criar uma imagem de contêiner, consulte [desenvolver C# módulos no Visual Studio](how-to-visual-studio-develop-csharp-module.md) ou [desenvolver módulos no Visual Studio Code](how-to-vs-code-develop-module.md). Esses artigos fornecem instruções sobre como criar novos módulos e publicar imagens de contêiner em um registro.

## <a name="try-the-solution"></a>Experimente a solução

Esta seção percorre a implantação do módulo Modbus de exemplo da Microsoft em seu dispositivo IoT Edge.

1. No [portal do Azure](https://portal.azure.com/), aceda ao seu hub IoT.

2. Aceda a **IoT Edge** e clique no seu dispositivo IoT Edge.

3. Selecione **Definir módulos**.

4. Na seção **módulos IOT Edge** , adicione o módulo Modbus:

   1. Clique na lista suspensa **Adicionar** e selecione **módulo Marketplace**.
   2. Procure `Modbus` e selecione o **módulo TCP Modbus** pela Microsoft.
   3. O módulo é configurado automaticamente para o Hub IoT e aparece na lista de módulos IoT Edge. As rotas também são configuradas automaticamente. Selecione **Rever + criar**.
   4. Examine o manifesto de implantação e selecione **criar**.

5. Selecione o módulo MODBUS, `ModbusTCPModule`, na lista e selecione a guia **configurações de configuração do módulo** . O JSON necessário para as propriedades desejadas do módulo para cima é populado automaticamente.

6. Procure a propriedade **SlaveConnection** no JSON e defina seu valor como o endereço IPv4 do seu dispositivo Modbus.

7. Selecione **Atualizar**.

8. Selecione **revisão + criar**, examine a implantação e, em seguida, selecione **criar**.

9. Regresse à página de detalhes do dispositivo e selecione **Atualizar**. Você deve ver o novo módulo `ModbusTCPModule` em execução junto com o IoT Edge Runtime.

## <a name="view-data"></a>Ver dados

Exiba os dados provenientes do módulo Modbus:

```cmd/sh
iotedge logs modbus
```

Você também pode exibir a telemetria que o dispositivo está enviando usando a [extensão do kit de ferramentas do Hub IOT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente, a extensão do kit de ferramentas do Azure IOT).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como IoT Edge dispositivos podem atuar como gateways, consulte [criar um dispositivo de IOT Edge que atue como um gateway transparente](./how-to-create-transparent-gateway.md).
* Para obter mais informações sobre como IoT Edge módulos funcionam, consulte [entender módulos Azure IOT Edge](iot-edge-modules.md).
