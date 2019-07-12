---
title: Implementar módulos da linha de comando - Azure IoT Edge | Documentos da Microsoft
description: Utilize a extensão de IoT para a CLI do Azure para implementar módulos para um dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: edcde083934bb454f62ce3dd55c026138ad8fcca
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797683"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Implementar módulos do Azure IoT Edge com a CLI do Azure

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os.

[CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma código-fonte aberto para várias plataforma ferramenta da linha de comandos para gerir recursos do Azure, como o IoT Edge. Permite-lhe gerir recursos, instâncias de serviço aprovisionamento de dispositivos e hubs ligados a caixa do IoT Hub do Azure. A nova extensão de IoT otimiza a CLI do Azure com funcionalidades como a gestão de dispositivos e a capacidade total do IoT Edge.

Este artigo mostra como criar um manifesto de implantação de JSON, em seguida, utilizar esse ficheiro para emitir a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação direcionada para vários dispositivos com base nas respetivas etiquetas partilhadas, consulte [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma [IoT hub](../iot-hub/iot-hub-create-using-cli.md) na sua subscrição do Azure.
* Uma [dispositivo IoT Edge](how-to-register-device-cli.md) com o runtime do IoT Edge instalado.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* O [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como o trabalho de manifestos de implantação e como criá-los, consulte [compreender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

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

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Implementar módulos no seu dispositivo ao aplicar o manifesto de implantação que configurou com as informações de módulo.

Altere os diretórios para a pasta onde o manifesto de implantação é guardado. Se tiver utilizado um dos modelos de código de VS IoT Edge, utilize o `deployment.json` de ficheiros a **config** pasta do seu diretório de solução e não o `deployment.template.json` ficheiro.

Utilize o seguinte comando para aplicar a configuração para um dispositivo IoT Edge:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

O parâmetro de id de dispositivo diferencia maiúsculas de minúsculas. Os pontos de parâmetro de conteúdo para a implementação do manifestam do ficheiro que guardou.

   ![saída de conjunto de módulos do AZ iot edge](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Após a implantação de módulos para o seu dispositivo, pode ver todos eles com o seguinte comando:

Veja os módulos no seu dispositivo do IoT Edge:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

O parâmetro de id de dispositivo diferencia maiúsculas de minúsculas.

   ![saída da lista de identidade do módulo AZ iot hub](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba como [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md)
