---
title: Implementar módulos do Código do Estúdio Visual - Azure IoT Edge
description: Utilize o Código de Estúdio Visual com as Ferramentas IoT Azure para empurrar um módulo IoT Edge do seu Hub IoT para o seu dispositivo IoT Edge, conforme configurado por um manifesto de implantação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b4840b36d5dadc14bbd664b844e3bcce6f6fec4d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201680"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Implementar módulos Azure IoT Edge a partir do Código do Estúdio Visual

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Uma vez criados módulos IoT Edge com a sua lógica de negócio, pretende implantá-los nos seus dispositivos para operar na borda. Se tiver vários módulos que trabalham em conjunto para recolher e processar dados, pode implantá-los todos de uma vez e declarar as regras de encaminhamento que os ligam.

Este artigo mostra como criar um manifesto de implementação JSON e, em seguida, usar esse ficheiro para empurrar a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação que direcione vários dispositivos com base nas suas tags partilhadas, consulte [os módulos Deploy IoT Edge em escala utilizando o Código do Estúdio Visual](how-to-deploy-vscode-at-scale.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua assinatura Azure.
* Um dispositivo IoT Edge

  Se não tiver um dispositivo IoT Edge configurado, pode criar um numa máquina virtual Azure. Siga os passos num dos artigos de arranque rápido para [criar um dispositivo Linux virtual](quickstart-linux.md) ou criar um dispositivo Virtual [Windows](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Código de Estúdio Visual.

## <a name="configure-a-deployment-manifest"></a>Configure um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implementar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte como os [módulos IoT Edge podem ser usados, configurados e reutilizados.](module-composition.md)

Para implementar módulos utilizando o Código do Estúdio Visual, guarde o manifesto de implantação localmente como um . Arquivo JSON. Utilizará o caminho do ficheiro na secção seguinte quando executar o comando para aplicar a configuração no seu dispositivo.

Aqui está um manifesto básico de implantação com um módulo como exemplo:

>[!NOTE]
>Este manifesto de implantação da amostra utiliza a versão 1.1 do esquema para o agente e o hub IoT Edge. A versão 1.1 do Schema foi lançada juntamente com a versão 1.0.10 do IoT Edge e permite funcionalidades como a ordem de arranque do módulo e a priorização da rota.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.1",
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
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
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
           "schemaVersion": "1.1",
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

Pode utilizar as extensões Azure IoT para o Código do Estúdio Visual para realizar operações com o seu hub IoT. Para que estas operações funcionem, tem de iniciar sedência na sua conta Azure e selecionar o hub IoT em que está a trabalhar.

1. No Código do Estúdio Visual, abra a vista **explorer.**

1. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub.**

   ![Expandir a secção Azure IoT Hub](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Clique no **...** no cabeçalho da secção **Azure IoT Hub.** Se não vir a elipse, paire sobre o cabeçalho.

1. Escolha **Select IoT Hub**.

1. Se não estiver inscrito na sua conta Azure, siga as instruções para o fazer.

1. Selecione a sua subscrição do Azure.

1. Selecione o seu hub IoT.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Implementa módulos no seu dispositivo aplicando o manifesto de implantação que configura com a informação do módulo.

1. Na vista do explorador do Código do Estúdio Visual, expanda a secção **Azure IoT Hub** e, em seguida, expanda o nó **dispositivos.**

1. Clique com o botão direito no dispositivo IoT Edge que pretende configurar com o manifesto de implantação.

    > [!TIP]
    > Para confirmar que o dispositivo que escolheu é um dispositivo IoT Edge, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Cada dispositivo IoT Edge inclui estes dois módulos.

1. Selecione **Criar Implementação para um único dispositivo**.

1. Navegue para o ficheiro JSON manifesto de implementação que pretende utilizar e clique em **Select Edge Deployment Manifest**.

   ![Selecione Manifesto de implantação de borda](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Os resultados da sua implementação estão impressos na saída do Código VS. As implementações bem sucedidas são aplicadas dentro de poucos minutos se o dispositivo alvo estiver em funcionamento e ligado à internet.

## <a name="view-modules-on-your-device"></a>Ver módulos no seu dispositivo

Depois de ter implantado módulos no seu dispositivo, pode vê-los todos na secção **Azure IoT Hub.** Selecione a seta ao lado do seu dispositivo IoT Edge para expandi-la. Todos os módulos atualmente em funcionamento são apresentados.

Se recentemente implementou novos módulos para um dispositivo, paire sobre o cabeçalho da secção **Azure IoT Hub Devices** e selecione o ícone de atualização para atualizar a vista.

Clique com o botão direito no nome de um módulo para visualizar e editar o módulo twin.

## <a name="next-steps"></a>Passos seguintes

Saiba como [implementar e monitorizar os módulos IoT Edge em escala utilizando o Código do Estúdio Visual](how-to-deploy-at-scale.md)
