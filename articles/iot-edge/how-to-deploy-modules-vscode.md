---
title: Implementar módulos do Visual Studio Code - Azure IoT Edge | Documentos da Microsoft
description: Utilize o Visual Studio Code para implementar módulos para um dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 415494fed0870a855a20314816bcebbe22680a98
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457409"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Implementar módulos do Azure IoT Edge do Visual Studio Code

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os.

Este artigo mostra como criar um manifesto de implantação de JSON, em seguida, utilizar esse ficheiro para emitir a implementação para um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação que se destina a vários dispositivos com base em suas marcas compartilhadas, consulte [implantar e monitorar módulos de IOT Edge em escala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) em sua assinatura do Azure.
* Um [dispositivo IOT Edge](how-to-register-device.md#register-with-visual-studio-code) com o IOT Edge Runtime instalado.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [entender como IOT Edge módulos podem ser usados, configurados e reutilizados](module-composition.md).

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
                 "createOptions": "{}"
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
               "route": "FROM /* INTO $upstream"
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

1. No Visual Studio Code, abra a exibição do **Explorer** .

1. Na parte inferior do Explorer, expanda a seção **dispositivos do Hub IOT do Azure** .

   ![Expanda a secção de dispositivos de Hub IoT do Azure](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Clique em **...** no cabeçalho da seção **dispositivos do Hub IOT do Azure** . Se não vir o botão de reticências, Paire o rato sobre o cabeçalho.

1. Escolha **selecionar Hub IOT**.

1. Se não tiver sessão iniciada sua conta do Azure, siga as instruções para fazer isso.

1. Selecione a sua subscrição do Azure.

1. Selecione o seu hub IoT.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Implementar módulos no seu dispositivo ao aplicar o manifesto de implantação que configurou com as informações de módulo.

1. Na exibição do Visual Studio Code Explorer, expanda a seção **dispositivos do Hub IOT do Azure** .

1. Clique com o botão direito do mouse no dispositivo IoT Edge que você deseja configurar com o manifesto de implantação.

    > [!TIP]
    > Para confirmar que o dispositivo escolhido é um IoT Edge dispositivo, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Cada dispositivo de IoT Edge inclui esses dois módulos.

1. Selecione **criar implantação para um único dispositivo**.

1. Navegue até o arquivo JSON do manifesto de implantação que você deseja usar e clique em **selecionar o manifesto de implantação do Edge**.

   ![Selecione o manifesto de implantação do Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Os resultados da implementação são impressos no resultado do VS Code. Implementações bem-sucedidas são aplicadas em poucos minutos, se o dispositivo de destino está em execução e ligadas à internet.

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Depois de implantar módulos no seu dispositivo, você pode exibir todos eles na seção dispositivos do **Hub IOT do Azure** . Selecione a seta junto ao seu dispositivo IoT Edge para expandi-la. São apresentados todos os módulos em execução.

Se você implantou recentemente novos módulos em um dispositivo, passe o mouse sobre o cabeçalho da seção **dispositivos do Hub IOT do Azure** e selecione o ícone de atualização para atualizar a exibição.

Faça duplo clique o nome de um módulo para ver e editar o módulo duplo.

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar e monitorar módulos IOT Edge em escala](how-to-deploy-monitor.md)
