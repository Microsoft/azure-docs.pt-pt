---
title: Implementar módulos e rotas com manifestos de implantação - Borda Azure IoT
description: Saiba como um manifesto de implantação declara que módulos para implementar, como implementá-los e como criar rotas de mensagens entre eles.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8eb24fe878638853cd8519c08045552a91f0c190
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379407"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Saiba como implementar módulos e estabelecer as rotas no IoT Edge

Cada dispositivo IoT Edge é executado, pelo menos, dois módulos: $edgeAgent e $edgeHub, que faz parte do tempo de execução do IoT Edge. O dispositivo IoT Edge pode executar vários módulos adicionais para qualquer número de processos. Utilize um manifesto de implantação para dizer ao seu dispositivo quais os módulos a instalar e como configurá-los para trabalhar em conjunto.

O *manifesto de implantação* é um documento da JSON que descreve:

* O módulo de módulo **do agente IoT Edge** twin, que inclui três componentes:
  * A imagem do recipiente para cada módulo que funciona no dispositivo.
  * As credenciais de acesso a registos de contentores privados que contenham imagens de módulos.
  * Instruções para a forma como cada módulo deve ser criado e gerido.
* O módulo hub **IoT Edge** twin, que inclui como as mensagens fluem entre módulos e eventualmente para o Hub IoT.
* As propriedades desejadas de quaisquer gémeos de módulo adicional (opcional).

Todos os dispositivos do IoT Edge tem de ser configurados com um manifesto de implantação. Os relatórios de um tempo de execução do IoT Edge instalado recentemente um código de erro até que configurado com um manifesto válido.

Os tutoriais do Azure IoT Edge, vai criar um manifesto de implantação através de um assistente no portal do Azure IoT Edge. Também pode aplicar um manifesto de implantação por meio de programação através do REST ou o SDK do serviço Hub IoT. Para mais informações, consulte [as implementações de Understand IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Criar um manifesto de implantação

Num alto nível, um manifesto de implantação é uma lista de duplos de módulo que estão configurados com as respetivas propriedades pretendidas. Um manifesto de implantação informa um dispositivo IoT Edge (ou um grupo de dispositivos), quais os módulos para instalar e como configurá-las. Os manifestos de implantação incluem as *propriedades desejadas* para cada módulo gémeo. Os dispositivos IoT Edge relatam as *propriedades reportadas* para cada módulo.

São necessários dois módulos em todos os manifestos de implantação: `$edgeAgent`e `$edgeHub`. Estes módulos fazem parte do tempo de execução do IoT Edge que gere o dispositivo do IoT Edge e os módulos em execução no mesmo. Para obter mais informações sobre estes módulos, consulte Compreender o tempo de [funcionao do IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

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

Defina como o runtime do IoT Edge instala os módulos na sua implementação. O agente do IoT Edge é o componente de tempo de execução que gere a instalação, atualizações e estado de criação de relatórios para um dispositivo IoT Edge. Portanto, o $edgeAgent módulo twin contém a configuração e informação de gestão para todos os módulos. Esta informação inclui os parâmetros de configuração para o próprio agente IoT Edge.

Para obter uma lista completa de propriedades que podem ou devem ser incluídas, consulte [Propriedades do agente IoT Edge e hub IoT Edge](module-edgeagent-edgehub.md).

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

O hub IoT Edge gerencia a comunicação entre módulos, o IoT Hub e quaisquer dispositivos de folha. Portanto, o $edgeHub módulo twin contém uma propriedade desejada chamada *rotas* que declara como as mensagens são passadas dentro de uma implementação. Pode ter várias rotas dentro da mesma implementação.

As rotas são declaradas no **$edgeHub** propriedades desejadas com a seguinte sintaxe:

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

A origem Especifica de onde vêm as mensagens. O IoT Edge pode direcionar mensagens de módulos ou dispositivos de folhas.

Utilizando os SDKs IoT, os módulos podem declarar filas de saída específicas para as suas mensagens utilizando a classe ModuleClient. As filas de saída não são necessárias, mas são úteis para gerir várias rotas. Os dispositivos Leaf podem utilizar a classe DeviceClient dos SDKs IoT para enviar mensagens para dispositivos gateway IoT Edge da mesma forma que enviariam mensagens para o IoT Hub. Para mais informações, consulte [Compreender e utilizar Os DSKs do Hub Azure IoT](../iot-hub/iot-hub-devguide-sdks.md).

A propriedade de origem pode ser qualquer um dos seguintes valores:

| Origem | Descrição |
| ------ | ----------- |
| `/*` | Todas as mensagens do dispositivo para a cloud ou duplo notificações de alteração de qualquer dispositivo de módulo ou folha |
| `/twinChangeNotifications` | Qualquer alteração de duplos (propriedades comunicadas) proveniente de qualquer dispositivo de módulo ou folha |
| `/messages/*` | Qualquer mensagem dispositivo-nuvem enviada por um módulo através de alguma ou nenhuma saída, ou por um dispositivo de folha |
| `/messages/modules/*` | Qualquer mensagem de dispositivo-para-cloud enviada por um módulo por meio de alguns ou nenhum resultado |
| `/messages/modules/<moduleId>/*` | Qualquer mensagem de dispositivo-para-cloud enviada por um módulo específico por meio de alguns ou nenhum resultado |
| `/messages/modules/<moduleId>/outputs/*` | Qualquer mensagem de dispositivo-para-cloud enviada por um módulo específico por meio de algumas saídas |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualquer mensagem de dispositivo-para-cloud enviada por um módulo específico por meio de uma saída específico |

### <a name="condition"></a>condição

A condição é opcional numa declaração de rota. Se quiser passar todas as mensagens da fonte para a pia, deixe de fora a cláusula **WHERE** inteiramente. Ou pode utilizar a linguagem de [consulta ioT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar para determinadas mensagens ou tipos de mensagens que satisfaçam a condição. As rotas do IoT Edge não oferecem suporte a mensagens de filtragem com base em etiquetas de duplo ou propriedades.

As mensagens que passam entre módulos no IoT Edge são formatadas as mesmas que as mensagens que passam entre os dispositivos e IoT Hub do Azure. Todas as mensagens são formatadas como JSON e têm **sistemaProperties,** **appProperties**e parâmetros **corporais.**

Pode criar consultas em torno de qualquer um dos três parâmetros com a seguinte sintaxe:

* Propriedades do sistema: `$<propertyName>` ou `{$<propertyName>}`
* Propriedades de aplicação: `<propertyName>`
* Propriedades do corpo: `$body.<propertyName>`

Por exemplo, sobre como criar consultas para propriedades de mensagens, consulte as [rotas de chamada de mensagens Dispositivo-a-nuvem expressões](../iot-hub/iot-hub-devguide-routing-query-syntax.md)de consulta .

Um exemplo que é específico do IoT Edge é quando se deseja para filtrar as mensagens que chegaram a um dispositivo de gateway, a partir de um dispositivo de folha. As mensagens que vêm de módulos incluem uma propriedade do sistema chamada **connectionModuleId**. Então, se pretender encaminhar mensagens a partir de dispositivos de folha diretamente para o IoT Hub, utilize a rota seguinte para excluir as mensagens do módulo:

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

IoT Edge fornece garantias pelo-menos-uma vez. O hub IoT Edge armazena mensagens localmente no caso de uma rota não poder entregar a mensagem à pia. Por exemplo, se o hub IoT Edge não conseguir ligar-se ao IoT Hub, ou se o módulo alvo não estiver ligado.

O hub IoT Edge armazena as mensagens até ao tempo especificada na propriedade `storeAndForwardConfiguration.timeToLiveSecs` do [hub IoT Edge desejadas propriedades.](module-edgeagent-edgehub.md)

## <a name="define-or-update-desired-properties"></a>Definir ou atualizar as propriedades pretendidas

O manifesto de implantação Especifica as propriedades pretendidas para cada módulo implementado no dispositivo IoT Edge. As propriedades pretendidas no manifesto de implantação substituem quaisquer propriedades pretendidas atualmente no duplo do módulo.

Se não especificar as propriedades de pretendidas do duplo do módulo no manifesto de implantação, o IoT Hub não irá modificar o duplo do módulo de qualquer forma. Em vez disso, pode definir as propriedades pretendidas por meio de programação.

Os mesmos mecanismos que permitem modificar dispositivos duplos são utilizados para modificar duplos de módulo. Para mais informações, consulte o guia de [desenvolvimento twin do módulo](../iot-hub/iot-hub-devguide-module-twins.md).

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
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
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

* Para obter uma lista completa de propriedades que podem ou devem ser incluídas em $edgeAgent e $edgeHub, consulte [Propriedades do agente IoT Edge e do hub IoT Edge](module-edgeagent-edgehub.md).

* Agora que sabe como são utilizados módulos IoT Edge, [compreenda os requisitos e ferramentas para desenvolver módulos IoT Edge](module-development.md).
