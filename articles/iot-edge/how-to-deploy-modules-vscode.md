---
title: Implementar módulos do Visual Studio Code - Azure IoT Edge | Documentos da Microsoft
description: Utilize o Visual Studio Code para implementar módulos para um dispositivo IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 91a074cf98291b105864a69730314efff3482254
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126414"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Implementar módulos do Azure IoT Edge do Visual Studio Code

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os. 

Este artigo mostra como criar um manifesto de implantação de JSON, em seguida, utilizar esse ficheiro para emitir a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação direcionada para vários dispositivos com base nas respetivas etiquetas partilhadas, consulte [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma [IoT hub](../iot-hub/iot-hub-create-through-portal.md) na sua subscrição do Azure. 
* Uma [dispositivo IoT Edge](how-to-register-device-portal.md) com o runtime do IoT Edge instalado. 
* [Visual Studio Code](https://code.visualstudio.com/).
* [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como o trabalho de manifestos de implantação e como criá-los, consulte [compreender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

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
             "tempSensor": {
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
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```
   
## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu hub IoT

Pode utilizar as extensões de IoT do Azure para Visual Studio Code para realizar operações com o seu hub IoT. Para essas operações trabalhar, terá de iniciar sessão na sua conta do Azure e selecione o hub de IoT que está a trabalhar.

1. No Visual Studio Code, abra a **Explorer** vista.

2. Na parte inferior do Explorer, expanda o **dispositivos do Azure IoT Hub** secção. 

   ![Expanda a secção de dispositivos de Hub IoT do Azure](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Clique no **...**  no **dispositivos do Azure IoT Hub** cabeçalho de secção. Se não vir o botão de reticências, Paire o rato sobre o cabeçalho. 

4. Escolher **selecione o IoT Hub**.

5. Se não tiver sessão iniciada sua conta do Azure, siga as instruções para fazer isso. 

6. Selecione a sua subscrição do Azure. 

7. Selecione o seu hub IoT. 


## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Implementar módulos no seu dispositivo ao aplicar o manifesto de implantação que configurou com as informações de módulo. 

1. Na vista de Explorador do Visual Studio Code, expanda o **dispositivos do Azure IoT Hub** secção. 

1. Clique com o botão direito no dispositivo IoT Edge que pretende configurar com o manifesto de implantação.

    > [!TIP]
    > Para confirmar que o dispositivo que escolheu é um dispositivo IoT Edge, selecione-a para expandir a lista de módulos e certifique-se a presença de **$edgeHub** e **$edgeAgent**. Todos os dispositivos do IoT Edge incluem esses dois módulos.

1. Selecione **criar a implementação de único dispositivo**.

4. Navegue para o manifesto JSON ficheiro de implementação que pretende utilizar e clique em **selecionar o manifesto de implantação do Edge**. 

   ![Selecione o manifesto de implantação do Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


Os resultados da implementação são impressos no resultado do VS Code. Implementações bem-sucedidas são aplicadas em poucos minutos, se o dispositivo de destino está em execução e ligadas à internet. 

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Após a implantação de módulos para o seu dispositivo, pode ver todos eles do **dispositivos do Azure IoT Hub** secção. Selecione a seta junto ao seu dispositivo IoT Edge para expandi-la. São apresentados todos os módulos em execução. 

Se tiver implementado recentemente novos módulos num dispositivo, Paire o rato sobre o **dispositivos do Azure IoT Hub** cabeçalho de secção e selecione o ícone de atualização para atualizar a vista. 

Faça duplo clique o nome de um módulo para ver e editar o módulo duplo. 

## <a name="next-steps"></a>Passos Seguintes

Saiba como [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md)