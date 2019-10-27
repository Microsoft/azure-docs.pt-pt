---
title: Criar implantações automáticas por meio de linha de comando-Azure IoT Edge | Microsoft Docs
description: Use a extensão de IoT para CLI do Azure para criar implantações automáticas para grupos de dispositivos IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7ba38e1aa7196263b0ac64a6c92984cc3e7416a6
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964801"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Implantar e monitorar módulos IoT Edge em escala usando o CLI do Azure

Crie uma **IOT Edge implantação automática** usando a interface de linha de comando do Azure para gerenciar implantações em andamento para vários dispositivos de uma vez. As implantações automáticas para IoT Edge fazem parte do recurso de [gerenciamento automático de dispositivo](/azure/iot-hub/iot-hub-automatic-device-management) do Hub IOT. As implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o status e a integridade dos módulos e fazer alterações quando necessário. 

Para obter mais informações, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

Neste artigo, você configura CLI do Azure e a extensão de IoT. Em seguida, você aprende como implantar módulos em um conjunto de dispositivos IoT Edge e monitorar o progresso usando os comandos da CLI disponíveis.

## <a name="cli-prerequisites"></a>Pré-requisitos da CLI

* Um [Hub IOT](../iot-hub/iot-hub-create-using-cli.md) em sua assinatura do Azure. 
* [IOT Edge dispositivos](how-to-register-device.md#prerequisites-for-the-azure-cli) com o IOT Edge Runtime instalado.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, sua versão de CLI do Azure deve ser 2.0.24 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack. 
* A [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos serão implantados, como os dados fluem entre os módulos e as propriedades desejadas do módulo gêmeos. Para obter mais informações, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).

Para implantar módulos usando CLI do Azure, salve o manifesto de implantação localmente como um arquivo. txt. Você usará o caminho do arquivo na próxima seção ao executar o comando para aplicar a configuração ao seu dispositivo. 

Aqui está um manifesto de implantação básica com um módulo como exemplo:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
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
}
```

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcas

Antes de criar uma implantação, você precisa ser capaz de especificar quais dispositivos deseja afetar. Azure IoT Edge identifica os dispositivos usando **marcas** no dispositivo. Cada dispositivo pode ter várias marcas que você define de qualquer forma que faça sentido para sua solução. Por exemplo, se você gerenciar um campus de prédios inteligentes, poderá adicionar as seguintes marcas a um dispositivo:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Para obter mais informações sobre dispositivos gêmeos e marcas, consulte [entender e usar o dispositivo gêmeos no Hub IOT](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implantação

Você implanta módulos nos dispositivos de destino criando uma implantação que consiste no manifesto de implantação, bem como em outros parâmetros. 

Use o comando [AZ IOT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) para criar uma implantação:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

O comando de criação de implantação usa os seguintes parâmetros: 

* **--Deployment-ID** -o nome da implantação que será criada no Hub IOT. Dê à sua implantação um nome exclusivo que tenha até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
* **--Hub-nome** -nome do Hub IOT no qual a implantação será criada. O hub deve estar na assinatura atual. Altere sua assinatura atual com o comando `az account set -s [subscription name]`.
* **--Content** -filePath para o JSON do manifesto de implantação. 
* **--Rótulos** -adicione rótulos para ajudar a acompanhar suas implantações. Os rótulos são pares nome, valor que descrevem sua implantação. Os rótulos usam a formatação JSON para os nomes e valores. Por exemplo, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-Condition** -Insira uma condição de destino para determinar quais dispositivos serão direcionados a essa implantação. A condição é baseada nas marcas de dispositivo ou nas propriedades relatadas do dispositivo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--Priority** -um inteiro positivo. Caso duas ou mais implantações sejam destinadas ao mesmo dispositivo, a implantação com o maior valor numérico para prioridade será aplicada.

## <a name="monitor-a-deployment"></a>Monitorar uma implantação

Use o comando [AZ IOT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) para exibir os detalhes de uma única implantação:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

O comando Deployment show usa os seguintes parâmetros:
* **--Deployment-ID** -o nome da implantação que existe no Hub IOT.
* **--Hub-nome** -nome do Hub IOT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

Inspecione a implantação na janela de comando. A propriedade de **métricas** lista uma contagem de cada métrica que é avaliada por cada Hub:

* **targetedCount** -uma métrica do sistema que especifica o número de dispositivos gêmeos no Hub IOT que correspondem à condição de destino.
* **appliedCount** -uma métrica do sistema especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado ao seu módulo gêmeos no Hub IOT.
* **reportedSuccessfulCount** -uma métrica de dispositivo que especifica o número de dispositivos IOT Edge na implantação que relataram o sucesso do tempo de execução do cliente IOT Edge.
* **reportedFailedCount** -uma métrica de dispositivo que especifica o número de dispositivos IOT Edge na implantação que relatam a falha do tempo de execução do cliente IOT Edge.

Você pode mostrar uma lista de IDs de dispositivo ou objetos para cada uma das métricas usando o comando [AZ IOT Edge Deployment show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

O comando Deployment show-Metric usa os seguintes parâmetros: 
* **--Deployment-ID** -o nome da implantação que existe no Hub IOT.
* **--Metric-ID** -o nome da métrica para a qual você deseja ver a lista de IDs de dispositivo, por exemplo `reportedFailedCount`
* **--Hub-nome** -nome do Hub IOT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos de destino. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Se um dispositivo não atender à condição de destino antiga, mas atender à nova condição de destino e essa implantação for a prioridade mais alta para esse dispositivo, essa implantação será aplicada ao dispositivo. 
* Se um dispositivo atualmente executando essa implantação não atender mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação de prioridade mais alta. 
* Se um dispositivo atualmente executando essa implantação não atender mais à condição de destino e não atender à condição de destino de nenhuma outra implantação, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Quando ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e assume a nova. 

Use o comando [AZ IOT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) para atualizar uma implantação:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

O comando de atualização de implantação usa os seguintes parâmetros:
* **--Deployment-ID** -o nome da implantação que existe no Hub IOT.
* **--Hub-nome** -nome do Hub IOT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`
* **--set** -atualizar uma propriedade na implantação. Você pode atualizar as seguintes propriedades:
  * targetCondition-por exemplo `targetCondition=tags.location.state='Oregon'`
  * las 
  * prioridade


## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos assumem sua próxima implantação de prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída. 

Use o comando de [exclusão de implantação AZ IOT Edge](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) para excluir uma implantação:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

O comando de exclusão de implantação usa os seguintes parâmetros: 
* **--Deployment-ID** -o nome da implantação que existe no Hub IOT.
* **--Hub-nome** -nome do Hub IOT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implantação de módulos em dispositivos IOT Edge](module-deployment-monitoring.md).
