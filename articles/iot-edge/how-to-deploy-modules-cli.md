---
title: Implementar módulos da linha de comando - Azure IoT Edge | Documentos da Microsoft
description: Utilize a extensão de IoT para a CLI do Azure para implementar módulos para um dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 72535b69c81aee880eb16bf5d10e11dedb36f3a7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457472"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Implementar módulos do Azure IoT Edge com a CLI do Azure

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os.

[CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure, como IOT Edge. Permite-lhe gerir recursos, instâncias de serviço aprovisionamento de dispositivos e hubs ligados a caixa do IoT Hub do Azure. A nova extensão de IoT otimiza a CLI do Azure com funcionalidades como a gestão de dispositivos e a capacidade total do IoT Edge.

Este artigo mostra como criar um manifesto de implantação de JSON, em seguida, utilizar esse ficheiro para emitir a implementação para um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação que se destina a vários dispositivos com base em suas marcas compartilhadas, consulte [implantar e monitorar módulos de IOT Edge em escala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IOT](../iot-hub/iot-hub-create-using-cli.md) em sua assinatura do Azure.
* Um [dispositivo IOT Edge](how-to-register-device.md#register-with-the-azure-cli) com o IOT Edge Runtime instalado.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [entender como IOT Edge módulos podem ser usados, configurados e reutilizados](module-composition.md).

Para implementar módulos com a CLI do Azure, guarde o manifesto de implantação localmente como um ficheiro. JSON. Irá utilizar o caminho do ficheiro na próxima seção ao executar o comando para aplicar a configuração para o seu dispositivo.

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

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Implementar módulos no seu dispositivo ao aplicar o manifesto de implantação que configurou com as informações de módulo.

Altere os diretórios para a pasta onde o manifesto de implantação é guardado. Se você usou um dos modelos de IoT Edge VS Code, use o arquivo `deployment.json` na pasta **config** do diretório da solução e não no arquivo `deployment.template.json`.

Utilize o seguinte comando para aplicar a configuração para um dispositivo IoT Edge:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

O parâmetro de ID do dispositivo diferencia maiúsculas de minúsculas. Os pontos de parâmetro de conteúdo para a implementação do manifestam do ficheiro que guardou.

   ![saída de conjunto de módulos do AZ iot edge](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Após a implantação de módulos para o seu dispositivo, pode ver todos eles com o seguinte comando:

Veja os módulos no seu dispositivo do IoT Edge:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

O parâmetro de ID do dispositivo diferencia maiúsculas de minúsculas.

   ![saída da lista de identidade do módulo AZ iot hub](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar e monitorar módulos IOT Edge em escala](how-to-deploy-monitor.md)
