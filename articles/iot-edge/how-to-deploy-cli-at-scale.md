---
title: Implementar módulos à escala utilizando Azure CLI - Azure IoT Edge
description: Utilize a extensão IoT para o Azure CLI para criar implementações automáticas para grupos de dispositivos IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 4ecb1c3dc0e72523b19e3183e17306774b3ce164
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370381"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Implementar e monitorizar os módulos IoT Edge em escala utilizando o CLI Azure

Crie uma **implementação automática IoT Edge** utilizando a interface da linha de comando Azure para gerir as implementações em curso para muitos dispositivos ao mesmo tempo. As implementações automáticas para ioT Edge fazem parte da funcionalidade automática de [gestão](../iot-hub/iot-hub-automatic-device-management.md) do dispositivo do IoT Hub. As implementações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o estado e a saúde dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [as configurações automáticas Understand IoT Edge para dispositivos individuais ou à escala.](module-deployment-monitoring.md)

Neste artigo, você configura Azure CLI e a extensão IoT. Em seguida, aprende a implantar módulos num conjunto de dispositivos IoT Edge e monitorizar o progresso utilizando os comandos CLI disponíveis.

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure.
* Um ou mais dispositivos IoT Edge.

  Se não tiver um dispositivo IoT Edge configurado, pode criar um numa máquina virtual Azure. Siga os passos num dos artigos de arranque rápido para [criar um dispositivo Linux virtual](quickstart-linux.md) ou criar um dispositivo Virtual [Windows](quickstart.md).

* [Azure CLI](/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configure um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implementar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. Para obter mais informações, consulte [Saiba como implementar módulos e estabelecer rotas no IoT Edge.](module-composition.md)

Para implementar módulos utilizando o Azure CLI, guarde o manifesto de implantação localmente como um ficheiro .txt. Utilize o caminho do ficheiro na secção seguinte quando executar o comando para aplicar a configuração no seu dispositivo.

Aqui está um manifesto básico de implantação com um módulo como exemplo:

>[!NOTE]
>Este manifesto de implantação da amostra utiliza a versão 1.1 do esquema para o agente e o hub IoT Edge. A versão 1.1 do Schema foi lançada juntamente com a versão 1.0.10 do IoT Edge e permite funcionalidades como a ordem de arranque do módulo e a priorização da rota.

```json
{
  "content": {
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
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

As implementações em camadas são um tipo de implementação automática que pode ser empilhada em cima umas das outras. Para obter mais informações sobre as implementações em camadas, consulte [as implementações automáticas Understand IoT Edge para dispositivos individuais ou à escala](module-deployment-monitoring.md).

As implementações em camadas podem ser criadas e geridas com o CLI Azure como qualquer implementação automática, com apenas algumas diferenças. Uma vez criada uma implementação em camadas, o mesmo Azure CLI trabalha para implementações em camadas da mesma forma que qualquer implementação. Para criar uma implantação em camadas, adicione a `--layered` bandeira ao comando criar.

A segunda diferença está na construção do manifesto de implantação. Embora a implementação automática padrão contenha os módulos de tempo de funcionamento do sistema, para além de quaisquer módulos do utilizador, as implementações em camadas só podem conter módulos do utilizador. Em vez disso, as implementações em camadas precisam de uma implementação automática padrão também num dispositivo, para fornecer os componentes necessários de cada dispositivo IoT Edge, como os módulos de tempo de execução do sistema.

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

O exemplo anterior mostrou uma implementação em camadas que define o `properties.desired` módulo. Se esta implementação em camadas visasse um dispositivo onde o mesmo módulo já estava aplicado, substituiria quaisquer propriedades desejadas existentes. Para atualizar, em vez de substituir as propriedades desejadas, pode definir uma nova subsecção. Por exemplo:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Para obter mais informações sobre a configuração de gémeos módulos em implementações em camadas, consulte [a implementação em camadas](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando tags

Antes de poder criar uma implementação, tem de ser capaz de especificar quais os dispositivos que pretende afetar. A Azure IoT Edge identifica dispositivos que usam **etiquetas** no dispositivo twin. Cada dispositivo pode ter várias tags que define de qualquer forma que faça sentido para a sua solução. Por exemplo, se gerir um campus de edifícios inteligentes, pode adicionar as seguintes etiquetas a um dispositivo:

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

Para obter mais informações sobre os gémeos e tags do dispositivo, consulte [Compreender e utilizar gémeos do dispositivo no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Create a deployment (Criar uma implementação)

Implementa módulos nos seus dispositivos-alvo criando uma implementação que consiste no manifesto de implantação, bem como outros parâmetros.

Utilize o comando de implementação de [borda az iot](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-create) para criar uma implementação:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Use o mesmo comando com a `--layered` bandeira para criar uma implantação em camadas.

O comando de criação de implantação requer os seguintes parâmetros:

* **--camadas** - Uma bandeira opcional para identificar a implantação como uma implantação em camadas.
* **--id de implantação** - O nome da implantação que será criada no hub IoT. Dê à sua implantação um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /` . Parâmetro necessário.
* **--conteúdo** - Filepath para o manifesto de implantação JSON. Parâmetro necessário.
* **--hub-name** - Nome do hub IoT no qual a implantação será criada. O centro deve estar na subscrição atual. Altere a sua subscrição atual com o `az account set -s [subscription name]` comando.
* **--rótulos** - Adicione etiquetas para ajudar a rastrear as suas implementações. As etiquetas são Nome, pares de valor que descrevem a sua implantação. As etiquetas levam a formatação JSON para os nomes e valores. Por exemplo, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **...-condição-alvo** - Introduza uma condição de destino para determinar quais os dispositivos que serão alvo com esta implementação. A condição baseia-se em etiquetas gémeas do dispositivo ou no dispositivo que as propriedades reportadas por gémeos e devem corresponder ao formato de expressão. Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--prioridade** - Um número inteiro positivo. No caso de duas ou mais implementações serem direcionadas para o mesmo dispositivo, aplicar-se-á a implantação com o valor numérico mais elevado de Prioridade.
* **--métricas** - Criar métricas que consultam as propriedades reportadas edgeHub para rastrear o estado de uma implantação. As métricas tomam a entrada JSON ou um filepath. Por exemplo, `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Para monitorizar uma implementação utilizando o Azure CLI, consulte [as implementações do IoT Edge do monitor](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando modificar uma implementação, as alterações replicam-se imediatamente em todos os dispositivos direcionados.

Se atualizar a condição do alvo, ocorrem as seguintes atualizações:

* Se um dispositivo não satisfaça a antiga condição alvo, mas cumpre a nova condição alvo e esta implementação é a maior prioridade para esse dispositivo, então esta implementação é aplicada ao dispositivo.
* Se um dispositivo atualmente em execução esta implementação deixar de cumprir a condição alvo, desinstala esta implementação e assume a próxima maior prioridade de implantação.
* Se um dispositivo atualmente em execução esta implementação deixar de cumprir a condição alvo e não cumprir a condição alvo de quaisquer outras implementações, então não ocorre qualquer alteração no dispositivo. O dispositivo continua a executar os seus módulos atuais no seu estado atual, mas já não é gerido como parte desta implementação. Uma vez que satisfaça a condição alvo de qualquer outra implementação, desinstala esta implementação e assume a nova.

Não é possível atualizar o conteúdo de uma implementação, que inclui os módulos e rotas definidos no manifesto de implantação. Se pretender atualizar o conteúdo de uma implementação, fá-lo criando uma nova implementação que vise os mesmos dispositivos com uma maior prioridade. Pode modificar certas propriedades de um módulo existente, incluindo a condição alvo, etiquetas, métricas e prioridade.

Utilize o comando [de atualização de implementação de arestas az iot](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-update) para atualizar uma implementação:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

O comando de atualização de implementação requer os seguintes parâmetros:

* **...-id-implantação -** O nome da implantação que existe no hub IoT.
* **--hub-name** - Nome do hub IoT em que a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]`
* **--set** - Atualizar uma propriedade na implementação. Pode atualizar as seguintes propriedades:
  * targetCondição - por exemplo `targetCondition=tags.location.state='Oregon'`
  * rótulos
  * prioridade
* **--adicionar** - Adicione uma nova propriedade à implantação, incluindo condições-alvo ou etiquetas.
* **--remover** - Remover uma propriedade existente, incluindo condições-alvo ou etiquetas.

## <a name="delete-a-deployment"></a>Eliminar uma implantação

Quando elimina uma implantação, qualquer dispositivo assume a sua próxima implantação de maior prioridade. Se os seus dispositivos não cumprirem a condição alvo de qualquer outra implantação, os módulos não são removidos quando a implementação é eliminada.

Utilize o comando [de exclusão de borda az iot](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-delete) para eliminar uma implantação:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

O comando de eliminação de implantação requer os seguintes parâmetros:

* **...-id-implantação -** O nome da implantação que existe no hub IoT.
* **--hub-name** - Nome do hub IoT em que a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]`

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implementação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).