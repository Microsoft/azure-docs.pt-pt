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
ms.openlocfilehash: e4ac1a6e56cdbf47fd174d5244fc6ab51c63fb07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133888"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Implementar módulos Azure IoT Edge do Visual Studio Code

Assim que criar módulos IoT Edge com a sua lógica de negócio, pretende implantá-los nos seus dispositivos para operar na borda. Se tiver vários módulos que trabalham em conjunto para recolher e processar dados, pode implantá-los todos de uma vez e declarar as regras de encaminhamento que os ligam.

Este artigo mostra como criar um manifesto de implantação JSON, em seguida, use esse ficheiro para empurrar a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação que visa vários dispositivos com base nas suas tags partilhadas, consulte os [módulos Deploy IoT Edge em escala utilizando](how-to-deploy-vscode-at-scale.md)o Código do Estúdio Visual .

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua assinatura Azure.
* Um [dispositivo IoT Edge](how-to-register-device.md#register-with-visual-studio-code) com o tempo de execução do IoT Edge instalado.
* [Código de estúdio visual.](https://code.visualstudio.com/)
* [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Código de Estúdio Visual.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implantar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entenda como os módulos IoT Edge podem ser usados, configurados e reutilizados](module-composition.md).

Para implementar módulos utilizando o Código do Estúdio Visual, guarde o manifesto de implantação localmente como a . Ficheiro JSON. Utilizará o caminho do ficheiro na secção seguinte quando executar o comando para aplicar a configuração no seu dispositivo.

Aqui está um manifesto básico de implantação com um módulo como exemplo:

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

## <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

Pode utilizar as extensões Azure IoT para O Código do Estúdio Visual para realizar operações com o seu hub IoT. Para que estas operações funcionem, tem de iniciar sessão na sua conta Azure e selecionar o hub IoT em que está a trabalhar.

1. No Visual Studio Code, abra a vista **Explorer.**

1. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub.**

   ![Expandir a secção Hub Azure IoT](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Clique no **...** no cabeçalho da secção **Azure IoT Hub.** Se não vires a elipse, paira sobre o cabeceamento.

1. Escolha **Selecione Selecione IoT Hub**.

1. Se não estiver inscrito na sua conta Azure, siga as instruções para o fazer.

1. Selecione a sua subscrição do Azure.

1. Selecione o seu hub ioT.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Implementa módulos no seu dispositivo aplicando o manifesto de implementação que configuracom a informação do módulo.

1. Na vista do explorador do Código do Estúdio Visual, expanda a secção **Azure IoT Hub** e, em seguida, expanda o nó de **Dispositivos.**

1. Clique à direita no dispositivo IoT Edge que pretende configurar com o manifesto de implementação.

    > [!TIP]
    > Para confirmar que o dispositivo que escolheu é um dispositivo IoT Edge, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Todos os dispositivos IoT Edge incluem estes dois módulos.

1. Selecione **Criar a implementação para um dispositivo único**.

1. Navegue para o ficheiro JSON manifesto de implementação que pretende utilizar e clique em **Select Edge Deployment Manifest**.

   ![Selecione manifesto de implantação de bordas](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Os resultados da sua implementação estão impressos na saída do Código VS. As implementações bem sucedidas são aplicadas dentro de alguns minutos se o dispositivo alvo estiver em execução e ligado à internet.

## <a name="view-modules-on-your-device"></a>Ver módulos no seu dispositivo

Depois de ter implantado módulos no seu dispositivo, pode vê-los todos na secção **Hub Azure IoT.** Selecione a seta ao lado do seu dispositivo IoT Edge para expandi-la. Todos os módulos em execução atualmente são apresentados.

Se recentemente implementou novos módulos para um dispositivo, paire sobre o cabeçalho da secção **dispositivos Hub Azure IoT** e selecione o ícone de atualização para atualizar a vista.

Clique no nome de um módulo para visualizar e editar o módulo twin.

## <a name="next-steps"></a>Passos seguintes

Saiba como [implementar e monitorizar módulos IoT Edge em escala usando o Código de Estúdio Visual](how-to-deploy-at-scale.md)
