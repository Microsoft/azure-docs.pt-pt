---
title: Declarar módulos e rotas com manifestos de implantação - Azure IoT Edge | Documentos da Microsoft
description: Saiba como um manifesto de implantação declara que módulos para implementar, como implementá-los e como criar rotas de mensagens entre eles.
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 03/28/2019
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f4a562cab445398986c1b8f379f6cb90ca843342
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363211"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Saiba como implementar módulos e estabelecer as rotas no IoT Edge

Cada dispositivo IoT Edge é executado, pelo menos, dois módulos: $edgeAgent e $edgeHub, que faz parte do tempo de execução do IoT Edge. Além disso, qualquer dispositivo IoT Edge pode executar vários módulos para executar qualquer número de processos. Implementar todos esses módulos para um dispositivo ao mesmo tempo, pelo IoT Edge fornece uma maneira de declarar os módulos para instalar e como configurá-las a trabalhar em conjunto. 

O *manifesto de implantação* é um documento JSON que descreve:

* O **agente do IoT Edge** duplo do módulo, que inclui a imagem de contentor para cada módulo, as credenciais para registos de contentores privado de acesso e instruções sobre como cada módulo deve ser criado e gerido.
* O **hub do IoT Edge** duplo do módulo, que inclui a forma como o fluxo de mensagens entre módulos e, eventualmente, para o IoT Hub.
* Opcionalmente, as propriedades pretendidas do qualquer duplos de módulo adicionais.

Todos os dispositivos do IoT Edge tem de ser configurados com um manifesto de implantação. Os relatórios de um tempo de execução do IoT Edge instalado recentemente um código de erro até que configurado com um manifesto válido. 

Os tutoriais do Azure IoT Edge, vai criar um manifesto de implantação através de um assistente no portal do Azure IoT Edge. Também pode aplicar um manifesto de implantação por meio de programação através do REST ou o SDK do serviço Hub IoT. Para obter mais informações, consulte [implementações do IoT Edge compreender](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Criar um manifesto de implantação

Num alto nível, um manifesto de implantação é uma lista de duplos de módulo que estão configurados com as respetivas propriedades pretendidas. Um manifesto de implantação informa um dispositivo IoT Edge (ou um grupo de dispositivos), quais os módulos para instalar e como configurá-las. Manifestos de implantação incluem a *propriedades pretendidas* para cada módulo duplo. Dispositivos IoT Edge relatar o *propriedades comunicadas* para cada módulo. 

Dois módulos são necessárias em cada manifesto de implantação: `$edgeAgent`, e `$edgeHub`. Estes módulos fazem parte do tempo de execução do IoT Edge que gere o dispositivo do IoT Edge e os módulos em execução no mesmo. Para obter mais informações sobre esses módulos, consulte [compreender o tempo de execução do IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Além dos dois módulos de tempo de execução, pode adicionar até 20 módulos suas próprias para serem executadas num dispositivo IoT Edge. 

Um manifesto de implantação que contém apenas o runtime do IoT Edge (edgeAgent e edgeHub) é válido.

Manifestos de implantação siga esta estrutura:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Configurar módulos

Defina como o runtime do IoT Edge instala os módulos na sua implementação. O agente do IoT Edge é o componente de tempo de execução que gere a instalação, atualizações e estado de criação de relatórios para um dispositivo IoT Edge. Por conseguinte, o módulo duplo de $edgeAgent requer a configuração e informações de gestão para todos os módulos. Estas informações incluem os parâmetros de configuração para o próprio agente de IoT Edge. 

Para obter uma lista completa de propriedades que pode ou tem de ser incluída, consulte [propriedades do agente do IoT Edge e hub do IoT Edge](module-edgeagent-edgehub.md).

As propriedades de $edgeAgent siga esta estrutura:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Declarar as rotas

O hub IoT Edge gerencia a comunicação entre módulos, o IoT Hub e quaisquer dispositivos de folha. Por conseguinte, o módulo duplo de $edgeHub contém uma chamada de propriedade pretendida *rotas* que declara a forma como as mensagens são passadas dentro de uma implantação. Pode ter várias rotas dentro da mesma implementação.

As rotas são declaradas na **$edgeHub** pretendido propriedades com a seguinte sintaxe:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Cada rota tem uma origem e sink, mas a condição é uma parte opcional que pode utilizar para filtrar as mensagens. 


### <a name="source"></a>Origem

A origem Especifica de onde vêm as mensagens. IoT Edge podem encaminhar as mensagens de dispositivos de folha ou módulos.

A propriedade de origem pode ser qualquer um dos seguintes valores:

| Origem | Descrição |
| ------ | ----------- |
| `/*` | Todas as mensagens do dispositivo para a cloud ou duplo notificações de alteração de qualquer dispositivo de módulo ou folha |
| `/twinChangeNotifications` | Qualquer alteração de duplos (propriedades comunicadas) proveniente de qualquer dispositivo de módulo ou folha |
| `/messages/*` | Qualquer mensagem de dispositivo-para-cloud enviada por um dispositivo de módulo ou folha por meio de alguns ou nenhum resultado |
| `/messages/modules/*` | Qualquer mensagem de dispositivo-para-cloud enviada por um módulo por meio de alguns ou nenhum resultado |
| `/messages/modules/<moduleId>/*` | Qualquer mensagem de dispositivo-para-cloud enviada por um módulo específico por meio de alguns ou nenhum resultado |
| `/messages/modules/<moduleId>/outputs/*` | Qualquer mensagem de dispositivo-para-cloud enviada por um módulo específico por meio de algumas saídas |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualquer mensagem de dispositivo-para-cloud enviada por um módulo específico por meio de uma saída específico |

### <a name="condition"></a>Condição
A condição é opcional numa declaração de rota. Se quiser passar todas as mensagens de coletor para a origem, basta omitir a **onde** cláusula inteiramente. Ou pode utilizar o [linguagem de consulta do IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar determinadas mensagens ou tipos de mensagem que satisfaçam a condição. As rotas do IoT Edge não oferecem suporte a mensagens de filtragem com base em etiquetas de duplo ou propriedades. 

As mensagens que passam entre módulos no IoT Edge são formatadas as mesmas que as mensagens que passam entre os dispositivos e IoT Hub do Azure. Todas as mensagens são formatadas como JSON e ter **systemProperties**, **appProperties**, e **corpo** parâmetros. 

Pode criar consultas em torno de qualquer um dos três parâmetros com a seguinte sintaxe: 

* Propriedades do sistema: `$<propertyName>` ou `{$<propertyName>}`
* Propriedades da aplicação: `<propertyName>`
* Propriedades do corpo: `$body.<propertyName>` 

Para obter exemplos sobre como criar consultas para as propriedades da mensagem, consulte [rotas de expressões de consulta de mensagem de dispositivo-para-cloud](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Um exemplo que é específico do IoT Edge é quando se deseja para filtrar as mensagens que chegaram a um dispositivo de gateway, a partir de um dispositivo de folha. As mensagens provenientes de módulos incluem uma propriedade de sistema chamada **connectionModuleId**. Então, se pretender encaminhar mensagens a partir de dispositivos de folha diretamente para o IoT Hub, utilize a rota seguinte para excluir as mensagens do módulo:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink
O sink define onde as mensagens são enviadas. Apenas módulos e o IoT Hub podem receber mensagens. Não não possível encaminhar mensagens para outros dispositivos. Não há nenhuma opção de carateres universais na propriedade sink. 

A propriedade de coletor pode ser qualquer um dos seguintes valores:

| Sink | Descrição |
| ---- | ----------- |
| `$upstream` | Enviar a mensagem para o IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Enviar a mensagem para uma introdução específica de um módulo específico |

IoT Edge fornece garantias pelo-menos-uma vez. O hub IoT Edge armazena mensagens localmente no caso de uma rota não é possível entregar a mensagem para o sink. Por exemplo, se o hub IoT Edge não é possível ligar ao IoT Hub ou o módulo de destino não está ligado.

Hub do IoT Edge armazena as mensagens até à hora especificada no `storeAndForwardConfiguration.timeToLiveSecs` propriedade o [propriedades pretendidas do hub do IoT Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definir ou atualizar as propriedades pretendidas 

O manifesto de implantação Especifica as propriedades pretendidas para cada módulo implementado no dispositivo IoT Edge. As propriedades pretendidas no manifesto de implantação substituem quaisquer propriedades pretendidas atualmente no duplo do módulo.

Se não especificar as propriedades de pretendidas do duplo do módulo no manifesto de implantação, o IoT Hub não irá modificar o duplo do módulo de qualquer forma. Em vez disso, pode definir as propriedades pretendidas por meio de programação.

Os mesmos mecanismos que permitem modificar dispositivos duplos são utilizados para modificar duplos de módulo. Para obter mais informações, consulte a [Guia do programador do módulo duplo](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Exemplo de manifesto de implantação

O exemplo seguinte mostra o que pareçam um documento de manifesto de implantação válido.

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
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
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
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
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
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma lista completa das propriedades que podem ou têm de ser incluídos na $edgeAgent e $edgeHub, consulte [propriedades do agente do IoT Edge e hub do IoT Edge](module-edgeagent-edgehub.md).

* Agora que sabe como os módulos do IoT Edge são usados, [compreender os requisitos e as ferramentas para desenvolver módulos do IoT Edge](module-development.md).

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-routing-query-syntax.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
