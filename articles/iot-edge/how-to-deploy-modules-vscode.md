---
title: Implantar módulos de Visual Studio Code Azure IoT Edge | Microsoft Docs
description: Usar Visual Studio Code para implantar módulos em um dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c3d721427075736138ba73fda51a4fd515125f1c
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964864"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Implantar módulos de Azure IoT Edge de Visual Studio Code

Depois de criar IoT Edge módulos com sua lógica de negócios, você deseja implantá-los em seus dispositivos para operar na borda. Se você tiver vários módulos que trabalham juntos para coletar e processar dados, poderá implantá-los todos de uma vez e declarar as regras de roteamento que os conectam.

Este artigo mostra como criar um manifesto de implantação JSON e, em seguida, usar esse arquivo para enviar por push a implantação para um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação que se destina a vários dispositivos com base em suas marcas compartilhadas, consulte [implantar e monitorar módulos de IOT Edge em escala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) em sua assinatura do Azure.
* Um [dispositivo IOT Edge](how-to-register-device.md#register-with-visual-studio-code) com o IOT Edge Runtime instalado.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos serão implantados, como os dados fluem entre os módulos e as propriedades desejadas do módulo gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [entender como IOT Edge módulos podem ser usados, configurados e reutilizados](module-composition.md).

Para implantar módulos usando Visual Studio Code, salve o manifesto de implantação localmente como um. Arquivo JSON. Você usará o caminho do arquivo na próxima seção ao executar o comando para aplicar a configuração ao seu dispositivo.

Aqui está um manifesto de implantação básica com um módulo como exemplo:

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

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar seu hub IoT

Você pode usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, você precisa entrar em sua conta do Azure e selecionar o Hub IoT no qual você está trabalhando.

1. No Visual Studio Code, abra a exibição do **Explorer** .

1. Na parte inferior do Explorer, expanda a seção **dispositivos do Hub IOT do Azure** .

   ![Seção expandir dispositivos do Hub IoT do Azure](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Clique em **...** no cabeçalho da seção **dispositivos do Hub IOT do Azure** . Se você não vir as reticências, passe o mouse sobre o cabeçalho.

1. Escolha **selecionar Hub IOT**.

1. Se você não estiver conectado à sua conta do Azure, siga os prompts para fazer isso.

1. Selecione a sua subscrição do Azure.

1. Selecione o Hub IoT.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Você implanta módulos em seu dispositivo aplicando o manifesto de implantação que você configurou com as informações do módulo.

1. Na exibição do Visual Studio Code Explorer, expanda a seção **dispositivos do Hub IOT do Azure** .

1. Clique com o botão direito do mouse no dispositivo IoT Edge que você deseja configurar com o manifesto de implantação.

    > [!TIP]
    > Para confirmar que o dispositivo escolhido é um IoT Edge dispositivo, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Cada dispositivo de IoT Edge inclui esses dois módulos.

1. Selecione **criar implantação para um único dispositivo**.

1. Navegue até o arquivo JSON do manifesto de implantação que você deseja usar e clique em **selecionar o manifesto de implantação do Edge**.

   ![Selecionar o manifesto de implantação do Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Os resultados de sua implantação são impressos na saída VS Code. Implantações bem-sucedidas serão aplicadas em alguns minutos se o dispositivo de destino estiver em execução e conectado à Internet.

## <a name="view-modules-on-your-device"></a>Exibir módulos em seu dispositivo

Depois de implantar módulos no seu dispositivo, você pode exibir todos eles na seção dispositivos do **Hub IOT do Azure** . Selecione a seta ao lado do dispositivo IoT Edge para expandi-lo. Todos os módulos em execução no momento são exibidos.

Se você implantou recentemente novos módulos em um dispositivo, passe o mouse sobre o cabeçalho da seção **dispositivos do Hub IOT do Azure** e selecione o ícone de atualização para atualizar a exibição.

Clique com o botão direito do mouse no nome de um módulo para exibir e editar o módulo.

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar e monitorar módulos IOT Edge em escala](how-to-deploy-monitor.md)
