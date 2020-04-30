---
title: Implementar módulos em escala utilizando o Azure CLI - Azure IoT Edge
description: Utilize a extensão IoT para o Azure CLI para criar implementações automáticas para grupos de dispositivos IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ff6bb9e4d4e40c02b52f35bd56bf065a8804a43a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82134380"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Implementar e monitorizar módulos IoT Edge em escala utilizando o AZURE CLI

Crie uma **implementação automática IoT Edge** utilizando a interface de linha de comando Azure para gerir as implementações em curso para muitos dispositivos ao mesmo tempo. As implementações automáticas para IoT Edge fazem parte da funcionalidade [de gestão automática](/azure/iot-hub/iot-hub-automatic-device-management) do dispositivo do IoT Hub. As implementações são processos dinâmicos que permitem implementar vários módulos em vários dispositivos, rastrear o estado e a saúde dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [as implementações automáticas De IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

Neste artigo, você configura o Azure CLI e a extensão IoT. Em seguida, aprende-se a implantar módulos num conjunto de dispositivos IoT Edge e monitoriza o progresso utilizando os comandos CLI disponíveis.

## <a name="cli-prerequisites"></a>Pré-requisitos cli

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure.
* [Dispositivos IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) com o tempo de execução do IoT Edge instalado.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implantar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. Para mais informações, consulte [Saiba como implementar módulos e estabelecer rotas em IoT Edge](module-composition.md).

Para utilizar módulos utilizando o Azure CLI, guarde o manifesto de implantação localmente como um ficheiro .txt. Utiliza o caminho do ficheiro na secção seguinte quando executar o comando para aplicar a configuração no seu dispositivo.

Aqui está um manifesto básico de implantação com um módulo como exemplo:

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

As implantações em camadas são um tipo de implantação automática que pode ser empilhada em cima umas das outras. Para obter mais informações sobre implementações em camadas, consulte [as implementações automáticas De IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

As implementações em camadas podem ser criadas e geridas com o AZURE CLI como qualquer implantação automática, com apenas algumas diferenças. Uma vez criada uma implantação em camadas, o mesmo Azure CLI trabalha para implementações em camadas como qualquer implantação. Para criar uma implantação `--layered` em camadas, adicione a bandeira ao comando de criação.

A segunda diferença está na construção do manifesto de implantação. Embora a implementação automática padrão deva conter os módulos de tempo de funcionamento do sistema para além de quaisquer módulos de utilizador, as implementações em camadas só podem conter módulos de utilizador. Em vez disso, as implementações em camadas precisam de uma implementação automática padrão também num dispositivo, para fornecer os componentes necessários de cada dispositivo IoT Edge, como os módulos de tempo de funcionamento do sistema.

Aqui está um manifesto básico de implantação em camadas com um módulo como exemplo:

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

O exemplo anterior mostrou uma `properties.desired` implementação em camadas que define o para um módulo. Se esta implementação em camadas tivesse como alvo um dispositivo onde o mesmo módulo já estava aplicado, substituiria quaisquer propriedades existentes. Para atualizar, em vez de sobrepor, propriedades desejadas, pode definir uma nova subsecção. Por exemplo:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Para obter mais informações sobre a configuração de gémeos módulos em implementações em camadas, consulte [a implantação em camadas](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando tags

Antes de poder criar uma implementação, tem de ser capaz de especificar quais os dispositivos que pretende afetar. O Azure IoT Edge identifica dispositivos que utilizam **etiquetas** no dispositivo twin. Cada dispositivo pode ter várias tags que define de qualquer forma que faça sentido para a sua solução. Por exemplo, se gerir um campus de edifícios inteligentes, poderá adicionar as seguintes etiquetas a um dispositivo:

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

Para obter mais informações sobre gémeos e tags de dispositivos, consulte [Compreender e utilizar gémeos dispositivos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implantação

Implementa módulos para os seus dispositivos-alvo criando uma implementação que consiste no manifesto de implantação, bem como em outros parâmetros.

Utilize a implantação de [borda az iot criar](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) comando para criar uma implantação:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Use o mesmo `--layered` comando com a bandeira para criar uma implantação em camadas.

A implementação cria o comando tem os seguintes parâmetros:

* **-em camadas** - Uma bandeira opcional para identificar a implantação como uma implantação em camadas.
* **--implantação-id** - O nome da implantação que será criado no centro ioT. Dê à sua implantação um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`. Parâmetro necessário.
* **--conteúdo** - Arquivo para o manifesto de implantação JSON. Parâmetro necessário.
* **--hub-name** - Nome do centro IoT no qual a implantação será criada. O centro deve estar na subscrição atual. Altere a sua `az account set -s [subscription name]` subscrição atual com o comando.
* **--labels** - Adicione etiquetas para ajudar a rastrear as suas implementações. As etiquetas são Nome, pares de valor que descrevem a sua implantação. As etiquetas tomam formatação JSON para os nomes e valores. Por exemplo, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--condição do alvo** - Introduza uma condição-alvo para determinar quais os dispositivos que serão alvo com esta implementação.A condição baseia-se em etiquetas duplas do dispositivo ou propriedades reportadas de dispositivos twin e deve corresponder ao formato de expressão.Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--prioridade** - Um inteiro positivo. No caso de duas ou mais implementações serem direcionadas para o mesmo dispositivo, aplicar-se-á a implantação com o maior valor numérico para a Priority.
* **--métricas** - Crie métricas que consultam as propriedades do EdgeHub para rastrear o estado de uma implementação. As métricas tomam a entrada da JSON ou um ficheiro. Por exemplo, `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Para monitorizar uma implantação utilizando o Azure CLI, consulte [as implementações do Monitor IoT Edge](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando modifica uma implementação, as alterações replicam-se imediatamente em todos os dispositivos visados.

Se atualizar a condição-alvo, ocorrem as seguintes atualizações:

* Se um dispositivo não cumprir a condição de destino antiga, mas cumprir a nova condição-alvo e esta implementação for a maior prioridade para esse dispositivo, então esta implementação é aplicada ao dispositivo.
* Se um dispositivo atualmente em execução desta implementação já não cumprir a condição-alvo, desinstala esta implementação e assume a próxima maior implementação prioritária.
* Se um dispositivo que está atualmente a executar esta implementação já não cumprir a condição-alvo e não cumprir o estado-alvo de quaisquer outras implementações, então não ocorre qualquer alteração no dispositivo. O dispositivo continua a executar os seus módulos atuais no seu estado atual, mas já não é gerido como parte desta implementação. Uma vez que cumpre a condição-alvo de qualquer outra implementação, desinstala esta implementação e assume a nova.

Não é possível atualizar o conteúdo de uma implementação, que inclui os módulos e rotas definidos no manifesto de implantação. Se pretender atualizar o conteúdo de uma implementação, fá-lo criando uma nova implementação que visa os mesmos dispositivos com uma prioridade maior. Pode modificar certas propriedades de um módulo existente, incluindo a condição alvo, etiquetas, métricas e prioridade.

Utilize o comando de atualização de atualização de atualização de [borda az iot](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) para atualizar uma implementação:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

O comando de atualização de implantação tem os seguintes parâmetros:

* **--implantação-id** - O nome da implantação que existe no centro ioT.
* **--hub-name** - Nome do centro IoT em que a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando`az account set -s [subscription name]`
* **-set** - Atualizar uma propriedade na implementação. Pode atualizar as seguintes propriedades:
  * targetCondition - por exemplo`targetCondition=tags.location.state='Oregon'`
  * rótulos
  * prioridade
* **--adicionar** - Adicione um novo imóvel à implementação, incluindo condições de destino ou etiquetas.
* **-remover** - Remova uma propriedade existente, incluindo condições de destino ou etiquetas.

## <a name="delete-a-deployment"></a>Eliminar uma implantação

Ao eliminar uma implementação, quaisquer dispositivos assumem a sua próxima implementação de prioridade máxima. Se os seus dispositivos não cumprirem o estado-alvo de qualquer outra implementação, os módulos não são removidos quando a implementação for eliminada.

Utilize o comando de eliminação de [arestas az iot](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) para eliminar uma implantação:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

O comando de eliminação de implantação tem os seguintes parâmetros:

* **--implantação-id** - O nome da implantação que existe no centro ioT.
* **--hub-name** - Nome do centro IoT em que a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando`az account set -s [subscription name]`

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implementação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).
