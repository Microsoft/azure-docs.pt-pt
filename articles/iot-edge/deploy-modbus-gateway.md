---
title: Traduzir protocolos de modbus com gateways - Azure IoT Edge | Microsoft Docs
description: Permitir que os dispositivos que utilizam o Modbus TCP para comunicar com o Azure IoT Hub mediante a criação de um dispositivo de gateway do IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 0388520903e208b3225375d5cee81e8321740a1b
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487739"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Ligue os dispositivos Modbus TCP através de um gateway de dispositivo IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Se pretender ligar dispositivos IoT que utilizem protocolos Modbus TCP ou RTU a um hub Azure IoT, pode utilizar um dispositivo IoT Edge como porta de entrada. O dispositivo de gateway lê os dados dos seus dispositivos Modbus e comunica-os para a cloud com um protocolo suportado.

![Os dispositivos Modbus ligam-se ao IoT Hub através do gateway IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Este artigo mostra como criar a sua própria imagem de contentor para um módulo do Modbus (também pode utilizar um exemplo pré-criado) e, em seguida, implementá-la no dispositivo IoT Edge que vai funcionar como o gateway.

Este artigo pressupõe que está a utilizar o protocolo Modbus TCP. Para obter mais informações sobre como configurar o módulo de suporte ao Modbus RTU, consulte o projeto do [módulo Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo Azure IoT Edge. Para uma passagem sobre como configurar um, consulte [Implementar Azure IoT Edge no Windows](quickstart.md) ou [Linux](quickstart-linux.md).
* A cadeia de ligação da chave primária do dispositivo IoT Edge.
* Um dispositivo Modbus físico ou simulado que suporte o Modbus TCP. Terá de saber o endereço IPv4.

## <a name="prepare-a-modbus-container"></a>Preparar um contentor do Modbus

Se quiser testar a funcionalidade do gateway do Modbus, a Microsoft disponibiliza-lhe um módulo de exemplo. Pode aceder ao módulo a partir do Azure Marketplace, [Modbus,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)ou com a imagem URI, `mcr.microsoft.com/azureiotedge/modbus:1.0` .

Se quiser criar o seu próprio módulo e personalizá-lo para o seu ambiente, existe um projeto de [módulo Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) de código aberto no GitHub. Siga as orientações desse projeto para criar a sua própria imagem de contentor. Para criar uma imagem de recipiente, consulte [os módulos Develop C# em Visual Studio](./how-to-visual-studio-develop-module.md) ou desenvolver [módulos no Código do Estúdio Visual](how-to-vs-code-develop-module.md). Esses artigos fornecem instruções sobre a criação de novos módulos e a publicação de imagens de contentores num registo.

## <a name="try-the-solution"></a>Experimente a solução

Esta secção percorre a implementação do módulo Modbus de amostra da Microsoft para o seu dispositivo IoT Edge.

1. No [portal do Azure](https://portal.azure.com/), aceda ao seu hub IoT.

2. Vá ao **IoT Edge** e clique no seu dispositivo IoT Edge.

3. Selecione **Definir módulos**.

4. Na secção **módulos IoT Edge,** adicione o módulo Modbus:

   1. Clique no **Add** dropdown e selecione **Marketplace Module**.
   2. Procure `Modbus` e selecione o **Módulo TCP Modbus** pela Microsoft.
   3. O módulo é configurado automaticamente para o seu Hub IoT e aparece na lista de Módulos IoT Edge. As Rotas também são configuradas automaticamente. Selecione **Rever + criar**.
   4. Reveja o manifesto de implementação e **selecione Criar**.

5. Selecione o módulo Modbus, `ModbusTCPModule` na lista e selecione o **separador Definições Gémeas do Módulo.** O JSON necessário para as propriedades duplas do módulo é auto-povoado.

6. Procure a propriedade **SlaveConnection** no JSON e desaponte o seu valor para o endereço IPv4 do seu dispositivo Modbus.

7. Selecione **Atualizar**.

8. Selecione **Rever + criar,** rever a implementação e, em seguida, selecione **Criar**.

9. Regresse à página de detalhes do dispositivo e selecione **Atualizar**. Deve ver o novo `ModbusTCPModule` módulo a funcionar juntamente com o tempo de execução IoT Edge.

## <a name="view-data"></a>Ver dados

Veja os dados que chegam através do módulo Modbus:

```cmd/sh
iotedge logs modbus
```

Também pode ver a telemetria que o dispositivo está a enviar utilizando a [extensão Azure IoT Hub para o Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente extensão Azure IoT Toolkit).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como os dispositivos IoT Edge podem funcionar como portais, consulte [Criar um dispositivo IoT Edge que funciona como um gateway transparente](./how-to-create-transparent-gateway.md).
* Para obter mais informações sobre como os módulos IoT Edge funcionam, consulte [os módulos Understand Azure IoT Edge](iot-edge-modules.md).