---
title: Implantar módulos em escala usando o CLI do Azure-Azure IoT Edge
description: Utilizar a extensão de IoT para a CLI do Azure para criar implementações automáticas para grupos do IoT Edge de dispositivos
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29aab4437b7d77b9a00b5745d68dcb5c44a4efe6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434211"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Implementar e monitorizar os módulos do IoT Edge em escala com a CLI do Azure

Crie uma **IOT Edge implantação automática** usando a interface de linha de comando do Azure para gerenciar implantações em andamento para vários dispositivos de uma vez. As implantações automáticas para IoT Edge fazem parte do recurso de [gerenciamento automático de dispositivo](/azure/iot-hub/iot-hub-automatic-device-management) do Hub IOT. As implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o status e a integridade dos módulos e fazer alterações quando necessário. 

Para obter mais informações, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

Neste artigo, iremos configurar a CLI do Azure e a extensão de IoT. Em seguida, você aprende como implantar módulos em um conjunto de dispositivos IoT Edge e monitorar o progresso usando os comandos da CLI disponíveis.

## <a name="cli-prerequisites"></a>Pré-requisitos CLI

* Uma [IoT hub](../iot-hub/iot-hub-create-using-cli.md) na sua subscrição do Azure. 
* [Dispositivos IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) com o runtime do IoT Edge instalado.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack. 
* O [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).

Para implementar módulos com a CLI do Azure, guarde o manifesto de implantação localmente como um arquivo. txt. Você usará o caminho do arquivo na próxima seção ao executar o comando para aplicar a configuração ao seu dispositivo. 

Aqui está um manifesto de implantação básico com um módulo como exemplo:

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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="layered-deployment"></a>Implantação em camadas

Implantações em camadas são um tipo de implantação automática que pode ser empilhada umas sobre as outras. Para obter mais informações sobre implantações em camadas, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md). 

Implantações em camadas podem ser criadas e gerenciadas com o CLI do Azure como qualquer implantação automática, com apenas algumas diferenças. Depois que uma implantação em camadas é criada, o mesmo CLI do Azure funciona para implantações em camadas da mesma forma que qualquer implantação. Para criar uma implantação em camadas, adicione o sinalizador `--layered` ao comando criar. 

A segunda diferença está na construção do manifesto de implantação. Embora a implantação automática padrão deva conter os módulos de tempo de execução do sistema além de quaisquer módulos de usuário, as implantações em camadas só podem conter módulos de usuário. Em vez disso, as implantações em camadas precisam de uma implantação automática padrão também em um dispositivo, para fornecer os componentes necessários de cada dispositivo IoT Edge, como os módulos de tempo de execução do sistema. 

Aqui está um manifesto de implantação em camadas básico com um módulo como um exemplo: 

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

O exemplo anterior mostrou uma configuração de implantação em camadas `properties.desired` para um módulo. Se essa implantação em camadas tiver como destino um dispositivo em que o mesmo módulo já foi aplicado, ele substituirá as propriedades desejadas existentes. Para atualizar, em vez de substituir as propriedades desejadas, você pode definir uma nova subseção. Por exemplo: 

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Para obter mais informações sobre como configurar o módulo gêmeos em implantações em camadas, consulte [implantação em camadas](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identificar os dispositivos utilizando etiquetas

Antes de poder criar uma implementação, terá de ser capazes de especificar quais os dispositivos que quer afetar. O Azure IoT Edge identifica dispositivos que utilizam **etiquetas** no dispositivo duplo. Cada dispositivo pode ter várias marcas que você define de qualquer forma que faça sentido para sua solução. Por exemplo, se gerencia um campus de edifícios inteligentes, poderá adicionar as seguintes etiquetas a um dispositivo:

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

Para obter mais informações sobre dispositivos duplos e etiquetas, consulte [compreender e utilizar dispositivos duplos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implementação

Implementar módulos nos seus dispositivos de destino através da criação de uma implementação que consiste o manifesto de implantação, bem como outros parâmetros. 

Use o comando [AZ IOT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) para criar uma implantação:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Use o mesmo comando com o sinalizador `--layered` para criar um deploymet em camadas.

O comando de criação de implantação usa os seguintes parâmetros: 

* **--em camadas** – um sinalizador opcional para identificar a implantação como uma implantação em camadas.
* **– id de implementação** -o nome da implementação que será criada no IoT hub. Dê um nome exclusivo que é até 128 minúsculas de sua implementação. Evite espaços e os seguintes carateres inválidos: `& ^ [ ] { } \ | " < > /`. Parâmetro necessário. 
* **– conteúdo** -Filepath para a implementação de manifesto JSON. Parâmetro necessário. 
* **-nome do hub** -nome do hub IoT em que a implementação será criada. O hub tem de ser na subscrição atual. Altere sua assinatura atual com o comando `az account set -s [subscription name]`.
* **– etiquetas** -adicionar etiquetas para ajudar a monitorizar as suas implementações. As etiquetas são pares de valor que descrevem a implementação, nome de. Os rótulos usam a formatação JSON para os nomes e valores. Por exemplo, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **– condição de destino** -introduza uma condição de destino para determinar quais os dispositivos que serão visados para esta implementação. A condição é baseada nas marcas de dispositivo ou nas propriedades relatadas do dispositivo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **– prioridade** -um número inteiro positivo. No caso de duas ou mais implementações destinam-se no mesmo dispositivo, será aplicada a implementação com o maior valor numérico da prioridade.
* **--métricas** – crie métricas que consultam as propriedades relatadas edgeHub para acompanhar o status de uma implantação. As métricas usam a entrada JSON ou um FilePath. Por exemplo, `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`. 

## <a name="monitor-a-deployment"></a>Monitorizar uma implementação

Use o comando [AZ IOT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) para exibir os detalhes de uma única implantação:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

O comando Deployment show usa os seguintes parâmetros:
* **– id de implementação** -o nome da implementação que existe no IoT hub. Parâmetro necessário. 
* **-nome do hub** -nome do hub IoT na qual existe a implementação. O hub tem de ser na subscrição atual. Mude para a subscrição pretendida com o comando `az account set -s [subscription name]`

Inspecione a implementação na janela de comando. A propriedade de **métricas** lista uma contagem de cada métrica que é avaliada por cada Hub:

* **targetedCount** -uma métrica de sistema que especifica o número de dispositivos duplos no IoT Hub que correspondem à condição de destino.
* **appliedCount** -uma métrica do sistema Especifica o número de dispositivos que tenha tido a conteúdo de implementação aplicada a seus duplos de módulo do IoT Hub.
* **reportedSuccessfulCount** -uma métrica de dispositivo que especifica o número de dispositivos IOT Edge na implantação que relataram o sucesso do tempo de execução do cliente IOT Edge.
* **reportedFailedCount** -uma métrica de dispositivo que especifica o número de dispositivos IOT Edge na implantação que relatam a falha do tempo de execução do cliente IOT Edge.

Você pode mostrar uma lista de IDs de dispositivo ou objetos para cada uma das métricas usando o comando [AZ IOT Edge Deployment show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

O comando Deployment show-Metric usa os seguintes parâmetros: 
* **– id de implementação** -o nome da implementação que existe no IoT hub.
* **--Metric-ID** -o nome da métrica para a qual você deseja ver a lista de IDs de dispositivo, por exemplo `reportedFailedCount`.
* **-nome do hub** -nome do hub IoT na qual existe a implementação. O hub tem de ser na subscrição atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Modificar uma implementação

Quando modifica uma implementação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se atualizar a condição de destino, ocorrem as seguintes atualizações:

* Se um dispositivo não cumpre a condição de destino antigo, mas atenda à nova condição de destino e esta implementação é a prioridade mais alta para esse dispositivo, em seguida, esta implementação é aplicada ao dispositivo. 
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino, desinstala esta implementação e sobre a implementação de prioridade mais alta seguinte. 
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino e não cumpre a condição de destino de todas as implementações, em seguida, nenhuma alteração ocorre no dispositivo. O dispositivo continua a ser executada os respectivos módulos atuais no respetivo estado atual, mas não é gerido como parte desta implementação mais. Assim que ele atenda à condição de destino de qualquer outra implementação, desinstala esta implementação e demora no novo. 

Você não pode atualizar o conteúdo de uma implantação, que inclui os módulos e rotas definidos no manifesto de implantação. Se você quiser atualizar o conteúdo de uma implantação, faça isso criando uma nova implantação que se destina aos mesmos dispositivos com uma prioridade mais alta. Você pode modificar determinadas propriedades de um módulo existente, incluindo a condição de destino, os rótulos, as métricas e a prioridade. 

Use o comando [AZ IOT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) para atualizar uma implantação:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

O comando de atualização de implantação usa os seguintes parâmetros:
* **– id de implementação** -o nome da implementação que existe no IoT hub.
* **-nome do hub** -nome do hub IoT na qual existe a implementação. O hub tem de ser na subscrição atual. Mude para a subscrição pretendida com o comando `az account set -s [subscription name]`
* **-definir** -atualizar uma propriedade na implementação. Pode atualizar as seguintes propriedades:
  * targetCondition - por exemplo `targetCondition=tags.location.state='Oregon'`
  * etiquetas 
  * prioridade
* **--Add** -adiciona uma nova propriedade à implantação, incluindo condições ou rótulos de destino. 
* **--Remove** -remove uma propriedade existente, incluindo condições ou rótulos de destino. 


## <a name="delete-a-deployment"></a>Eliminar uma implementação

Quando elimina uma implementação, demorar todos os dispositivos na sua implementação de prioridade mais alta seguinte. Se os dispositivos não cumprem a condição de destino de qualquer outra implementação, os módulos não são removidos quando a implementação é eliminada. 

Use o comando de [exclusão de implantação AZ IOT Edge](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) para excluir uma implantação:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

O comando de exclusão de implantação usa os seguintes parâmetros: 
* **– id de implementação** -o nome da implementação que existe no IoT hub.
* **-nome do hub** -nome do hub IoT na qual existe a implementação. O hub tem de ser na subscrição atual. Mude para a subscrição pretendida com o comando `az account set -s [subscription name]`

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implantação de módulos em dispositivos IOT Edge](module-deployment-monitoring.md).
