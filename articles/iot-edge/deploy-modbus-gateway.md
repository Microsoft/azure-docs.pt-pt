---
title: Translate modbus protocols with gateways - Azure IoT Edge | Microsoft Docs
description: Permitir que os dispositivos que utilizam o Modbus TCP para comunicar com o Azure IoT Hub mediante a criação de um dispositivo de gateway do IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457209"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connect Modbus TCP devices through an IoT Edge device gateway

Se quiser ligar dispositivos IoT que utilizam os protocolos Modbus TCP ou RTU a um hub IoT do Azure, utilize um dispositivo IoT Edge como gateway. O dispositivo de gateway lê os dados dos seus dispositivos Modbus e comunica-os para a cloud com um protocolo suportado.

![Modbus devices connect to IoT Hub through IoT Edge gateway](./media/deploy-modbus-gateway/diagram.png)

Este artigo mostra como criar a sua própria imagem de contentor para um módulo do Modbus (também pode utilizar um exemplo pré-criado) e, em seguida, implementá-la no dispositivo IoT Edge que vai funcionar como o gateway.

Este artigo pressupõe que está a utilizar o protocolo Modbus TCP. For more information about how to configure the module to support Modbus RTU, see the [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) project on GitHub.

## <a name="prerequisites"></a>Pré-requisitos
* Um dispositivo Azure IoT Edge. For a walkthrough on how to set up one, see [Deploy Azure IoT Edge on Windows](quickstart.md) or [Linux](quickstart-linux.md).
* A cadeia de ligação da chave primária do dispositivo IoT Edge.
* Um dispositivo Modbus físico ou simulado que suporte o Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Preparar um contentor do Modbus

Se quiser testar a funcionalidade do gateway do Modbus, a Microsoft disponibiliza-lhe um módulo de exemplo. You can access the module from the Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), or with the image URI, **mcr.microsoft.com/azureiotedge/modbus:1.0**.

If you want to create your own module and customize it for your environment, there is an open-source [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) project on GitHub. Siga as orientações desse projeto para criar a sua própria imagem de contentor. To create a container image, refer to [Develop C# modules in Visual Studio](how-to-visual-studio-develop-csharp-module.md) or [Develop modules in Visual Studio Code](how-to-vs-code-develop-module.md). Those articles provide instructions on creating new modules and publishing container images to a registry.

## <a name="try-the-solution"></a>Try the solution

This section walks through deploying Microsoft's sample Modbus module to your IoT Edge device.

1. No [portal do Azure](https://portal.azure.com/), aceda ao seu hub IoT.

2. Go to **IoT Edge** and click on your IoT Edge device.

3. Selecione **Definir módulos**.

4. Adicione o módulo do Modbus:

   1. Click **Add** and select **IoT Edge module**.

   2. No campo **Nome**, introduza "modbus".

   3. No campo **Imagem**, introduza o URI da imagem do contentor de exemplo: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Selecione a caixa **Ativar** para atualizar as propriedades pretendidas do duplo do módulo.

   5. Copie o JSON seguinte para a caixa de texto. Altere o valor de **SlaveConnection** para o endereço IPv4 do seu dispositivo Modbus.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"40001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Selecione **Guardar**.

5. Novamente no passo **Adicionar módulos**, selecione **Seguinte**.

7. No passo **Especificar Rotas**, copie o JSON seguinte para a caixa de texto. Esta rota envia todas as mensagens recolhidas pelo módulo Modbus para o IoT Hub. In this route, **modbusOutput** is the endpoint that Modbus module uses to output data and **$upstream** is a special destination that tells IoT Edge hub to send messages to IoT Hub.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Selecione **Seguinte**.

9. No passo **Rever Implementação**, selecione **Submeter**.

10. Regresse à página de detalhes do dispositivo e selecione **Atualizar**. You should see the new **modbus** module running along with the IoT Edge runtime.

## <a name="view-data"></a>Ver dados
Ver os dados que vêm do módulo modbus:
```cmd/sh
iotedge logs modbus
```

You can also view the telemetry the device is sending by using the [Azure IoT Hub Toolkit extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (formerly Azure IoT Toolkit extension).

## <a name="next-steps"></a>Passos seguintes

- To learn more about how IoT Edge devices can act as gateways, see [Create an IoT Edge device that acts as a transparent gateway](./how-to-create-transparent-gateway.md).
- For more information about how IoT Edge modules work, see [Understand Azure IoT Edge modules](iot-edge-modules.md).
