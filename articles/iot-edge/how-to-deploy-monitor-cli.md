---
title: Criar implementações automáticas a partir da linha de comando - Azure IoT Edge | Documentos da Microsoft
description: Utilizar a extensão de IoT para a CLI do Azure para criar implementações automáticas para grupos do IoT Edge de dispositivos
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 39edbc77215d3a4f6477beae3be9d7d47cbba4f0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540923"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Implementar e monitorizar os módulos do IoT Edge em escala com a CLI do Azure

Criar uma **implementação automática do IoT Edge** usando a interface de linha de comandos do Azure para gerir implementações em curso para o número de dispositivos ao mesmo tempo. As implementações automáticas para o IoT Edge são parte do [gestão de dispositivos automática](/azure/iot-hub/iot-hub-automatic-device-management) recurso do IoT Hub. As implementações são processos dinâmicos que permitem-lhe implementar vários módulos para vários dispositivos, controlar o estado de funcionamento dos módulos e fazer alterações quando for necessário. 

Para obter mais informações, consulte [implementações automáticas de compreender o IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

Neste artigo, iremos configurar a CLI do Azure e a extensão de IoT. Em seguida, irá aprender a implementar módulos para um conjunto de dispositivos do IoT Edge e monitorizar o progresso usando os comandos da CLI disponíveis.

## <a name="cli-prerequisites"></a>Pré-requisitos CLI

* Uma [IoT hub](../iot-hub/iot-hub-create-using-cli.md) na sua subscrição do Azure. 
* [Dispositivos IoT Edge](how-to-register-device-cli.md) com o runtime do IoT Edge instalado.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az –-version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack. 
* O [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações, consulte [Saiba como implementar módulos e estabelecer as rotas no IoT Edge](module-composition.md).

Para implementar módulos com a CLI do Azure, guarde o manifesto de implantação localmente como um arquivo. txt. Utilize o caminho do ficheiro na próxima seção quando executar o comando para aplicar a configuração para o seu dispositivo. 

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
}
```

## <a name="identify-devices-using-tags"></a>Identificar os dispositivos utilizando etiquetas

Antes de poder criar uma implementação, terá de ser capazes de especificar quais os dispositivos que quer afetar. O Azure IoT Edge identifica dispositivos que utilizam **etiquetas** no dispositivo duplo. Cada dispositivo pode ter várias etiquetas que definem de qualquer forma que faz sentido para a sua solução. Por exemplo, se gerencia um campus de edifícios inteligentes, poderá adicionar as seguintes etiquetas a um dispositivo:

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

Utilize o [criar a implementação do az iot edge](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) comando para criar uma implementação:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

A implementação criar comando utiliza os seguintes parâmetros: 

* **– id de implementação** -o nome da implementação que será criada no IoT hub. Dê um nome exclusivo que é até 128 minúsculas de sua implementação. Evite espaços e os seguintes carateres inválidos: `& ^ [ ] { } \ | " < > /`.
* **-nome do hub** -nome do hub IoT em que a implementação será criada. O hub tem de ser na subscrição atual. Alterar a sua subscrição atual com o `az account set -s [subscription name]` comando.
* **– conteúdo** -Filepath para a implementação de manifesto JSON. 
* **– etiquetas** -adicionar etiquetas para ajudar a monitorizar as suas implementações. As etiquetas são pares de valor que descrevem a implementação, nome de. Etiquetas de levar a formatação do JSON para os nomes e valores. Por exemplo, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **– condição de destino** -introduza uma condição de destino para determinar quais os dispositivos que serão visados para esta implementação. A condição baseia-se nas etiquetas de twin do dispositivo ou o dispositivo duplo propriedades comunicadas e deve corresponder ao formato de expressão. Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **– prioridade** -um número inteiro positivo. No caso de duas ou mais implementações destinam-se no mesmo dispositivo, será aplicada a implementação com o maior valor numérico da prioridade.

## <a name="monitor-a-deployment"></a>Monitorizar uma implementação

Utilize o [show de implementação do az iot edge](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) comando para apresentar os detalhes de uma implementação única:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

A implementação Mostrar comando utiliza os seguintes parâmetros:
* **– id de implementação** -o nome da implementação que existe no IoT hub.
* **-nome do hub** -nome do hub IoT na qual existe a implementação. O hub tem de ser na subscrição atual. Mude para a subscrição pretendida com o comando `az account set -s [subscription name]`

Inspecione a implementação na janela de comando. O **métricas** listas de propriedades de uma contagem para cada métrica que é avaliada por cada hub:

* **targetedCount** -uma métrica de sistema que especifica o número de dispositivos duplos no IoT Hub que correspondem à condição de destino.
* **appliedCount** -uma métrica do sistema Especifica o número de dispositivos que tenha tido a conteúdo de implementação aplicada a seus duplos de módulo do IoT Hub.
* **reportedSuccessfulCount** -uma métrica de dispositivo que especifica o número de dispositivos do IoT Edge no sucesso do tempo de execução de cliente do IoT Edge de relatório de implantação.
* **reportedFailedCount** -uma métrica de dispositivo que especifica o número de dispositivos do IoT Edge no relatório de falha de tempo de execução do cliente do IoT Edge de implantação.

Pode mostrar uma lista de identificações de dispositivo ou objetos para cada uma das métricas de utilizando o [az iot edge implementação show-métrica](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) comando:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

O comando de implementação de métrica show precisa dos seguintes parâmetros: 
* **– id de implementação** -o nome da implementação que existe no IoT hub.
* **– id de métrica** – o nome da métrica para o qual pretende ver a lista de dispositivos IDs, por exemplo `reportedFailedCount`
* **-nome do hub** -nome do hub IoT na qual existe a implementação. O hub tem de ser na subscrição atual. Mude para a subscrição pretendida com o comando `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Modificar uma implementação

Quando modifica uma implementação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se atualizar a condição de destino, ocorrem as seguintes atualizações:

* Se um dispositivo não cumpre a condição de destino antigo, mas atenda à nova condição de destino e esta implementação é a prioridade mais alta para esse dispositivo, em seguida, esta implementação é aplicada ao dispositivo. 
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino, desinstala esta implementação e sobre a implementação de prioridade mais alta seguinte. 
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino e não cumpre a condição de destino de todas as implementações, em seguida, nenhuma alteração ocorre no dispositivo. O dispositivo continua a ser executada os respectivos módulos atuais no respetivo estado atual, mas não é gerido como parte desta implementação mais. Assim que ele atenda à condição de destino de qualquer outra implementação, desinstala esta implementação e demora no novo. 

Utilize o [atualização da implementação az iot edge](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) comando para atualizar uma implementação:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

O comando de atualização de implementação precisa dos seguintes parâmetros:
* **– id de implementação** -o nome da implementação que existe no IoT hub.
* **-nome do hub** -nome do hub IoT na qual existe a implementação. O hub tem de ser na subscrição atual. Mude para a subscrição pretendida com o comando `az account set -s [subscription name]`
* **-definir** -atualizar uma propriedade na implementação. Pode atualizar as seguintes propriedades:
  * targetCondition - por exemplo `targetCondition=tags.location.state='Oregon'`
  * etiquetas 
  * prioridade


## <a name="delete-a-deployment"></a>Eliminar uma implementação

Quando elimina uma implementação, demorar todos os dispositivos na sua implementação de prioridade mais alta seguinte. Se os dispositivos não cumprem a condição de destino de qualquer outra implementação, os módulos não são removidos quando a implementação é eliminada. 

Utilize o [eliminar implementação az iot edge](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) comando para eliminar uma implementação:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

O comando de eliminação da implementação precisa dos seguintes parâmetros: 
* **– id de implementação** -o nome da implementação que existe no IoT hub.
* **-nome do hub** -nome do hub IoT na qual existe a implementação. O hub tem de ser na subscrição atual. Mude para a subscrição pretendida com o comando `az account set -s [subscription name]`

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [implementar módulos para dispositivos do IoT Edge](module-deployment-monitoring.md).
