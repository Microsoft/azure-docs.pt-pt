---
title: Implementar rotas de módulos & com manifestos de implantação - Borda Azure IoT
description: Saiba como um manifesto de implantação declara quais os módulos a implantar, como implantá-los e como criar rotas de mensagens entre eles.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a4b90d8b6fe67de26c8e652e0dc5b62cc27023f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545636"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Saiba como implementar módulos e estabelecer rotas no IoT Edge

Cada dispositivo IoT Edge executa pelo menos dois módulos: $edgeAgent e $edgeHub, que fazem parte do tempo de funcionação do IoT Edge. O dispositivo IoT Edge pode executar vários módulos adicionais para qualquer número de processos. Utilize um manifesto de implantação para dizer ao seu dispositivo quais os módulos a instalar e como configurá-los para trabalhar em conjunto.

O *manifesto de implantação* é um documento da JSON que descreve:

* O módulo de módulo **do agente IoT Edge** twin, que inclui três componentes:
  * A imagem do recipiente para cada módulo que funciona no dispositivo.
  * As credenciais de acesso a registos de contentores privados que contenham imagens de módulos.
  * Instruções para a forma como cada módulo deve ser criado e gerido.
* O módulo hub **IoT Edge** twin, que inclui como as mensagens fluem entre módulos e eventualmente para o Hub IoT.
* As propriedades desejadas de quaisquer gémeos de módulo adicional (opcional).

Todos os dispositivos IoT Edge devem ser configurados com um manifesto de implantação. Um prazo de execução ioT edge recentemente instalado reporta um código de erro até configurar com um manifesto válido.

Nos tutoriais Azure IoT Edge, você constrói um manifesto de implantação através de um assistente no portal Azure IoT Edge. Também pode aplicar um manifesto de implantação programática utilizando o REST ou o IoT Hub Service SDK. Para mais informações, consulte [as implementações de Understand IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Criar um manifesto de implantação

A um nível elevado, um manifesto de implantação é uma lista de gémeos módulos que são configurados com as suas propriedades desejadas. Um manifesto de implantação diz a um dispositivo IoT Edge (ou um grupo de dispositivos) quais os módulos para instalar e como configurá-los. Os manifestos de implantação incluem as *propriedades desejadas* para cada módulo gémeo. Os dispositivos IoT Edge relatam as *propriedades reportadas* para cada módulo.

São necessários dois módulos em `$edgeAgent`cada `$edgeHub`manifesto de implantação: e . Estes módulos fazem parte do tempo de funcionamento do IoT Edge que gere o dispositivo IoT Edge e os módulos que funcionam nele. Para obter mais informações sobre estes módulos, consulte Compreender o tempo de [funcionao do IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Além dos dois módulos de tempo de execução, pode adicionar até 20 módulos próprios para funcionar num dispositivo IoT Edge.

Um manifesto de implantação que contém apenas o tempo de execução do IoT Edge (EdgeAgent e edgeHub) é válido.

Os manifestos de implantação seguem esta estrutura:

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

## <a name="configure-modules"></a>Configure módulos

Defina como o tempo de funcionamento do IoT Edge instala os módulos na sua implementação. O agente IoT Edge é o componente de tempo de execução que gere a instalação, atualizações e relatórios de estado para um dispositivo IoT Edge. Portanto, o $edgeAgent módulo twin contém a configuração e informação de gestão para todos os módulos. Esta informação inclui os parâmetros de configuração para o próprio agente IoT Edge.

Para obter uma lista completa de propriedades que podem ou devem ser incluídas, consulte [Propriedades do agente IoT Edge e hub IoT Edge](module-edgeagent-edgehub.md).

As propriedades $edgeAgent seguem esta estrutura:

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

## <a name="declare-routes"></a>Declarar rotas

O hub IoT Edge gere a comunicação entre módulos, IoT Hub e quaisquer dispositivos de folhas. Portanto, o $edgeHub módulo twin contém uma propriedade desejada chamada *rotas* que declara como as mensagens são passadas dentro de uma implementação. Pode ter várias rotas dentro da mesma implantação.

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

Cada rota precisa de uma fonte e de um lavatório, mas a condição é uma peça opcional que pode usar para filtrar mensagens.

### <a name="source"></a>Origem

A fonte especifica de onde vêm as mensagens. O IoT Edge pode direcionar mensagens de módulos ou dispositivos de folhas.

Utilizando os SDKs IoT, os módulos podem declarar filas de saída específicas para as suas mensagens utilizando a classe ModuleClient. As filas de saída não são necessárias, mas são úteis para gerir várias rotas. Os dispositivos Leaf podem utilizar a classe DeviceClient dos SDKs IoT para enviar mensagens para dispositivos gateway IoT Edge da mesma forma que enviariam mensagens para o IoT Hub. Para mais informações, consulte [Compreender e utilizar Os DSKs do Hub Azure IoT](../iot-hub/iot-hub-devguide-sdks.md).

A propriedade de origem pode ser qualquer um dos seguintes valores:

| Origem | Descrição |
| ------ | ----------- |
| `/*` | Todas as mensagens dispositivo-to-cloud ou notificações de alteração dupla de qualquer módulo ou dispositivo de folha |
| `/twinChangeNotifications` | Qualquer alteração dupla (propriedades reportadas) proveniente de qualquer módulo ou dispositivo de folha |
| `/messages/*` | Qualquer mensagem dispositivo-nuvem enviada por um módulo através de alguma ou nenhuma saída, ou por um dispositivo de folha |
| `/messages/modules/*` | Qualquer mensagem dispositivo-nuvem enviada por um módulo através de alguma ou nenhuma saída |
| `/messages/modules/<moduleId>/*` | Qualquer mensagem dispositivo-nuvem enviada por um módulo específico através de alguma ou nenhuma saída |
| `/messages/modules/<moduleId>/outputs/*` | Qualquer mensagem dispositivo-nuvem enviada por um módulo específico através de alguma saída |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualquer mensagem dispositivo-nuvem enviada por um módulo específico através de uma saída específica |

### <a name="condition"></a>Condição

A condição é opcional numa declaração de rota. Se quiser passar todas as mensagens da fonte para a pia, deixe de fora a cláusula **WHERE** inteiramente. Ou pode utilizar a linguagem de [consulta ioT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar para determinadas mensagens ou tipos de mensagens que satisfaçam a condição. As rotas IoT Edge não suportam mensagens de filtragem com base em tags ou propriedades gémeas.

As mensagens que passam entre módulos em IoT Edge são formatadas da mesma forma que as mensagens que passam entre os seus dispositivos e o Hub Azure IoT. Todas as mensagens são formatadas como JSON e têm **sistemaProperties,** **appProperties**e parâmetros **corporais.**

Pode construir consultas em torno de qualquer um dos três parâmetros com a seguinte sintaxe:

* Propriedades do `$<propertyName>` sistema: ou`{$<propertyName>}`
* Propriedades de aplicação:`<propertyName>`
* Propriedades do corpo:`$body.<propertyName>`

Por exemplo, sobre como criar consultas para propriedades de mensagens, consulte as [rotas de chamada de mensagens Dispositivo-a-nuvem expressões](../iot-hub/iot-hub-devguide-routing-query-syntax.md)de consulta .

Um exemplo específico do IoT Edge é quando se pretende filtrar mensagens que chegaram a um dispositivo de gateway a partir de um dispositivo de folha. As mensagens que vêm de módulos incluem uma propriedade do sistema chamada **connectionModuleId**. Por isso, se pretender encaminhar mensagens diretamente de dispositivos de folhas para o IoT Hub, utilize a seguinte rota para excluir mensagens de módulos:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink

A pia define para onde as mensagens são enviadas. Apenas os módulos e o IoT Hub podem receber mensagens. As mensagens não podem ser encaminhadas para outros dispositivos. Não há opções wildcard na propriedade da pia.

A propriedade do lavatório pode ser qualquer um dos seguintes valores:

| Sink | Descrição |
| ---- | ----------- |
| `$upstream` | Envie a mensagem para IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Envie a mensagem para uma entrada específica de um módulo específico |

IoT Edge fornece pelo menos uma garantia. O hub IoT Edge armazena mensagens localmente no caso de uma rota não poder entregar a mensagem à pia. Por exemplo, se o hub IoT Edge não conseguir ligar-se ao IoT Hub, ou se o módulo alvo não estiver ligado.

O hub IoT Edge armazena as `storeAndForwardConfiguration.timeToLiveSecs` mensagens até ao tempo especificada na propriedade das [propriedades desejadas pelo hub IoT Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definir ou atualizar propriedades desejadas

O manifesto de implantação especifica as propriedades desejadas para cada módulo implantado no dispositivo IoT Edge. As propriedades desejadas no manifesto de implantação sobreporem quaisquer propriedades desejadas atualmente no módulo twin.

Se não especificar as propriedades desejadas de um módulo twin no manifesto de implantação, o IoT Hub não modifica o módulo gémeo de forma alguma. Em vez disso, pode definir as propriedades desejadas programáticamente.

Os mesmos mecanismos que permitem modificar os gémeos do dispositivo são usados para modificar os gémeos módulos. Para mais informações, consulte o guia de [desenvolvimento twin do módulo](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Exemplo manifesto de implantação

O exemplo que se segue mostra como pode parecer um documento manifesto de implantação válido.

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
            "env": {
              "tempLimit": {"value": "100"}
            },
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

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista completa de propriedades que podem ou devem ser incluídas em $edgeAgent e $edgeHub, consulte [Propriedades do agente IoT Edge e do hub IoT Edge](module-edgeagent-edgehub.md).

* Agora que sabe como são utilizados módulos IoT Edge, [compreenda os requisitos e ferramentas para desenvolver módulos IoT Edge](module-development.md).
