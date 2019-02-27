---
title: Traduzir protocolos modbus com gateways - Azure IoT Edge | Documentos da Microsoft
description: Permitir que os dispositivos que utilizam o Modbus TCP para comunicar com o Azure IoT Hub mediante a criação de um dispositivo de gateway do IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1c9855f982b888e8e1d68bfe5233983db8c826ad
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873768"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Ligar dispositivos Modbus TCP através de um gateway de dispositivo do IoT Edge

Se quiser ligar dispositivos IoT que utilizam os protocolos Modbus TCP ou RTU a um hub IoT do Azure, utilize um dispositivo IoT Edge como gateway. O dispositivo de gateway lê os dados dos seus dispositivos Modbus e comunica-os para a cloud com um protocolo suportado.

![Dispositivos Modbus ligarem ao IoT Hub através do gateway do IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Este artigo mostra como criar a sua própria imagem de contentor para um módulo do Modbus (também pode utilizar um exemplo pré-criado) e, em seguida, implementá-la no dispositivo IoT Edge que vai funcionar como o gateway.

Este artigo pressupõe que está a utilizar o protocolo Modbus TCP. Para obter mais informações sobre como configurar o módulo para suportar o Modbus RTU, veja a [módulo do Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) projeto no GitHub.

## <a name="prerequisites"></a>Pré-requisitos
* Um dispositivo Azure IoT Edge. Para obter instruções sobre como configurar um, consulte [implementar o Azure IoT Edge no Windows](quickstart.md) ou [Linux](quickstart-linux.md).
* A cadeia de ligação da chave primária do dispositivo IoT Edge.
* Um dispositivo Modbus físico ou simulado que suporte o Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Preparar um contentor do Modbus

Se quiser testar a funcionalidade do gateway do Modbus, a Microsoft disponibiliza-lhe um módulo de exemplo. Pode acessar o módulo do Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), ou com o URI, da imagem **mcr.microsoft.com/azureiotedge/modbus:1.0**.

Se quiser criar seu próprio módulo e personalizá-lo para o seu ambiente, existe uma código-fonte aberto [módulo do Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) projeto no GitHub. Siga as orientações desse projeto para criar a sua própria imagem de contentor. Se criar sua própria imagem de contentor, consulte [desenvolver C# módulos no Visual Studio](how-to-visual-studio-develop-csharp-module.md) ou [desenvolver módulos no Visual Studio Code](how-to-vs-code-develop-module.md). Esses artigos fornecem instruções sobre como criar novos módulos e publicar imagens de contentor para um registo.

## <a name="try-the-solution"></a>Experimente a solução

Esta secção guia-o através da implementação do módulo do Modbus de exemplo da Microsoft para o seu dispositivo IoT Edge.

1. No [portal do Azure](https://portal.azure.com/), aceda ao seu hub IoT.

2. Aceda a **IoT Edge** e clique no seu dispositivo IoT Edge.

3. Selecione **Definir módulos**.

4. Adicione o módulo do Modbus:

   1. Clique em **Add** e selecione **módulo do IoT Edge**.

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
                  "StartAddress":"400001",
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

7. No passo **Especificar Rotas**, copie o JSON seguinte para a caixa de texto. Esta rota envia todas as mensagens recolhidas pelo módulo Modbus para o IoT Hub. Nesta rota, ' modbusOutput ' é o ponto final que o módulo Modbus utiliza para dados de saída e "upstream" é um destino especial que diz ao hub IoT Edge para enviar mensagens para o IoT Hub.
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Selecione **Seguinte**.

9. No passo **Rever Implementação**, selecione **Submeter**.

10. Regresse à página de detalhes do dispositivo e selecione **Atualizar**. Deverá ver a nova **modbus** módulo em execução, juntamente com o runtime do IoT Edge.

## <a name="view-data"></a>Ver dados
Ver os dados que vêm do módulo modbus:
```cmd/sh
iotedge logs modbus
```

Também pode ver a telemetria que o dispositivo está a enviar ao utilizar o [extensão de Kit de ferramentas do Azure IoT Hub para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente conhecido como extensão do Kit de ferramentas do Azure IoT).

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como os dispositivos do IoT Edge podem atuar como gateways, veja [criar um dispositivo IoT Edge que atue como gateway transparente](./how-to-create-transparent-gateway.md).
- Para obter mais informações sobre como funcionam os módulos do IoT Edge, veja [módulos de compreender o Azure IoT Edge](iot-edge-modules.md).
