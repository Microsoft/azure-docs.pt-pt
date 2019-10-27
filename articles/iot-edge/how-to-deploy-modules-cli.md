---
title: Implantar módulos da linha de comando-Azure IoT Edge | Microsoft Docs
description: Usar a extensão de IoT para CLI do Azure implantar módulos em um dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8cd6e2d13e252f9a7560b55eca58341e791db5a
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964927"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Implantar módulos de Azure IoT Edge com CLI do Azure

Depois de criar IoT Edge módulos com sua lógica de negócios, você deseja implantá-los em seus dispositivos para operar na borda. Se você tiver vários módulos que trabalham juntos para coletar e processar dados, poderá implantá-los todos de uma vez e declarar as regras de roteamento que os conectam.

[CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure, como IOT Edge. Ele permite que você gerencie recursos do Hub IoT do Azure, instâncias do serviço de provisionamento de dispositivos e hubs vinculados prontos para uso. A nova extensão de IoT enriquece CLI do Azure com recursos como o gerenciamento de dispositivos e o recurso de IoT Edge completo.

Este artigo mostra como criar um manifesto de implantação JSON e, em seguida, usar esse arquivo para enviar por push a implantação para um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação que se destina a vários dispositivos com base em suas marcas compartilhadas, consulte [implantar e monitorar módulos de IOT Edge em escala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IOT](../iot-hub/iot-hub-create-using-cli.md) em sua assinatura do Azure.
* Um [dispositivo IOT Edge](how-to-register-device.md#register-with-the-azure-cli) com o IOT Edge Runtime instalado.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, sua versão de CLI do Azure deve ser 2.0.24 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos serão implantados, como os dados fluem entre os módulos e as propriedades desejadas do módulo gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [entender como IOT Edge módulos podem ser usados, configurados e reutilizados](module-composition.md).

Para implantar módulos usando o CLI do Azure, salve o manifesto de implantação localmente como um arquivo. JSON. Você usará o caminho do arquivo na próxima seção ao executar o comando para aplicar a configuração ao seu dispositivo.

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

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Você implanta módulos em seu dispositivo aplicando o manifesto de implantação que você configurou com as informações do módulo.

Altere os diretórios para a pasta em que o manifesto de implantação foi salvo. Se você usou um dos modelos de IoT Edge VS Code, use o arquivo `deployment.json` na pasta **config** do diretório da solução e não no arquivo `deployment.template.json`.

Use o seguinte comando para aplicar a configuração a um dispositivo IoT Edge:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

O parâmetro de ID do dispositivo diferencia maiúsculas de minúsculas. O parâmetro de conteúdo aponta para o arquivo de manifesto de implantação que você salvou.

   ![saída de módulos AZ IOT Edge Set-modules](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Exibir módulos em seu dispositivo

Depois de implantar módulos no seu dispositivo, você pode exibir todos eles com o seguinte comando:

Veja os módulos no seu dispositivo do IoT Edge:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

O parâmetro de ID do dispositivo diferencia maiúsculas de minúsculas.

   ![módulo AZ IOT Hub-saída da lista de identidades](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar e monitorar módulos IOT Edge em escala](how-to-deploy-monitor.md)
