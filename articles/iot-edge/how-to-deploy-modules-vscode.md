---
title: Implementar módulos a partir do Código do Estúdio Visual - Azure IoT Edge
description: Utilize o Código do Estúdio Visual com as Ferramentas Azure IoT para empurrar um módulo IoT Edge do seu Hub IoT para o seu dispositivo IoT Edge, tal como configurado por um manifesto de implementação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209210"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Implementar módulos do Azure IoT Edge do Visual Studio Code

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os.

Este artigo mostra como criar um manifesto de implantação de JSON, em seguida, utilizar esse ficheiro para emitir a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação que visa vários dispositivos com base nas suas tags partilhadas, consulte os [módulos Deploy IoT Edge em escala utilizando](how-to-deploy-monitor-vscode.md)o Código do Estúdio Visual .

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua assinatura Azure.
* Um [dispositivo IoT Edge](how-to-register-device.md#register-with-visual-studio-code) com o tempo de execução do IoT Edge instalado.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Código de Estúdio Visual.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entenda como os módulos IoT Edge podem ser usados, configurados e reutilizados](module-composition.md).

Para implementar módulos com o Visual Studio Code, guarde o manifesto de implantação localmente como um. Ficheiro JSON. Irá utilizar o caminho do ficheiro na próxima seção ao executar o comando para aplicar a configuração para o seu dispositivo.

Aqui está um manifesto de implantação básico com um módulo como exemplo:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu hub IoT

Pode utilizar as extensões de IoT do Azure para Visual Studio Code para realizar operações com o seu hub IoT. Para essas operações trabalhar, terá de iniciar sessão na sua conta do Azure e selecione o hub de IoT que está a trabalhar.

1. No Visual Studio Code, abra a vista **Explorer.**

1. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub.**

   ![Expandir a secção Hub Azure IoT](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Clique no **...** no cabeçalho da secção **Azure IoT Hub.** Se não vir o botão de reticências, Paire o rato sobre o cabeçalho.

1. Escolha **Selecione Selecione IoT Hub**.

1. Se não tiver sessão iniciada sua conta do Azure, siga as instruções para fazer isso.

1. Selecione a sua subscrição do Azure.

1. Selecione o seu hub IoT.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Implementar módulos no seu dispositivo ao aplicar o manifesto de implantação que configurou com as informações de módulo.

1. Na vista do explorador do Código do Estúdio Visual, expanda a secção **Azure IoT Hub** e, em seguida, expanda o nó de **Dispositivos.**

1. Clique à direita no dispositivo IoT Edge que pretende configurar com o manifesto de implementação.

    > [!TIP]
    > Para confirmar que o dispositivo que escolheu é um dispositivo IoT Edge, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Todos os dispositivos IoT Edge incluem estes dois módulos.

1. Selecione **Criar a implementação para um dispositivo único**.

1. Navegue para o ficheiro JSON manifesto de implementação que pretende utilizar e clique em **Select Edge Deployment Manifest**.

   ![Selecione o manifesto de implantação do Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Os resultados da implementação são impressos no resultado do VS Code. Implementações bem-sucedidas são aplicadas em poucos minutos, se o dispositivo de destino está em execução e ligadas à internet.

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Depois de ter implantado módulos no seu dispositivo, pode vê-los todos na secção **Hub Azure IoT.** Selecione a seta junto ao seu dispositivo IoT Edge para expandi-la. São apresentados todos os módulos em execução.

Se recentemente implementou novos módulos para um dispositivo, paire sobre o cabeçalho da secção **dispositivos Hub Azure IoT** e selecione o ícone de atualização para atualizar a vista.

Faça duplo clique o nome de um módulo para ver e editar o módulo duplo.

## <a name="next-steps"></a>Passos seguintes

Saiba como [implementar e monitorizar módulos IoT Edge em escala usando o Código de Estúdio Visual](how-to-deploy-monitor.md)
