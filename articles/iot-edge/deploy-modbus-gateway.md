---
title: Traduzir protocolos modbus com gateways - Azure IoT Edge | Documentos da Microsoft
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
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Ligar dispositivos Modbus TCP através de um gateway de dispositivo do IoT Edge

Se quiser ligar dispositivos IoT que utilizam os protocolos Modbus TCP ou RTU a um hub IoT do Azure, utilize um dispositivo IoT Edge como gateway. O dispositivo de gateway lê os dados dos seus dispositivos Modbus e comunica-os para a cloud com um protocolo suportado.

![Dispositivos Modbus se conectam ao Hub IoT por meio do gateway de IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Este artigo mostra como criar a sua própria imagem de contentor para um módulo do Modbus (também pode utilizar um exemplo pré-criado) e, em seguida, implementá-la no dispositivo IoT Edge que vai funcionar como o gateway.

Este artigo pressupõe que está a utilizar o protocolo Modbus TCP. Para obter mais informações sobre como configurar o módulo para dar suporte ao Modbus RTU, consulte o projeto de [módulo Azure IOT Edge Modbus](https://github.com/Azure/iot-edge-modbus) no github.

## <a name="prerequisites"></a>Pré-requisitos
* Um dispositivo Azure IoT Edge. Para obter instruções sobre como configurar um, consulte [implantar Azure IOT Edge no Windows](quickstart.md) ou [Linux](quickstart-linux.md).
* A cadeia de ligação da chave primária do dispositivo IoT Edge.
* Um dispositivo Modbus físico ou simulado que suporte o Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Preparar um contentor do Modbus

Se quiser testar a funcionalidade do gateway do Modbus, a Microsoft disponibiliza-lhe um módulo de exemplo. Você pode acessar o módulo no Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)ou com o URI da imagem, **MCR.Microsoft.com/azureiotedge/Modbus:1.0**.

Se você quiser criar seu próprio módulo e personalizá-lo para o seu ambiente, há um projeto de [módulo Azure IOT Edge Modbus](https://github.com/Azure/iot-edge-modbus) de código aberto no github. Siga as orientações desse projeto para criar a sua própria imagem de contentor. Para criar uma imagem de contêiner, consulte [desenvolver C# módulos no Visual Studio](how-to-visual-studio-develop-csharp-module.md) ou [desenvolver módulos no Visual Studio Code](how-to-vs-code-develop-module.md). Esses artigos fornecem instruções sobre como criar novos módulos e publicar imagens de contêiner em um registro.

## <a name="try-the-solution"></a>Experimente a solução

Esta seção percorre a implantação do módulo Modbus de exemplo da Microsoft em seu dispositivo IoT Edge.

1. No [portal do Azure](https://portal.azure.com/), aceda ao seu hub IoT.

2. Vá para **IOT Edge** e clique em seu dispositivo de IOT Edge.

3. Selecione **Definir módulos**.

4. Adicione o módulo do Modbus:

   1. Clique em **Adicionar** e selecione **IOT Edge módulo**.

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

7. No passo **Especificar Rotas**, copie o JSON seguinte para a caixa de texto. Esta rota envia todas as mensagens recolhidas pelo módulo Modbus para o IoT Hub. Nessa rota, **modbusOutput** é o ponto de extremidade que o módulo Modbus usa para dados de saída e **$upstream** é um destino especial que informa IOT Edge Hub para enviar mensagens ao Hub IOT.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Selecione **Seguinte**.

9. No passo **Rever Implementação**, selecione **Submeter**.

10. Regresse à página de detalhes do dispositivo e selecione **Atualizar**. Você deve ver o novo módulo **Modbus** em execução junto com o IOT Edge Runtime.

## <a name="view-data"></a>Ver dados
Ver os dados que vêm do módulo modbus:
```cmd/sh
iotedge logs modbus
```

Você também pode exibir a telemetria que o dispositivo está enviando usando a [extensão do kit de ferramentas do Hub IOT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente, a extensão do kit de ferramentas do Azure IOT).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como IoT Edge dispositivos podem atuar como gateways, consulte [criar um dispositivo de IOT Edge que atue como um gateway transparente](./how-to-create-transparent-gateway.md).
- Para obter mais informações sobre como IoT Edge módulos funcionam, consulte [entender módulos Azure IOT Edge](iot-edge-modules.md).
