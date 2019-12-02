---
title: Implantar o módulo & rotas com manifestos de implantação-Azure IoT Edge
description: Saiba como um manifesto de implantação declara quais módulos implantar, como implantá-los e como criar rotas de mensagens entre eles.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 31a83d3edb1bc297fc53b089384ab940482e5b28
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665836"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Saiba como implantar módulos e estabelecer rotas no IoT Edge

Cada dispositivo de IoT Edge executa pelo menos dois módulos: $edgeAgent e $edgeHub, que fazem parte do tempo de execução de IoT Edge. IoT Edge dispositivo pode executar vários módulos adicionais para qualquer número de processos. Use um manifesto de implantação para informar ao dispositivo quais módulos instalar e como configurá-los para que funcionem juntos. 

O *manifesto de implantação* é um documento JSON que descreve:

* O módulo do **agente de IOT Edge** e o, que inclui três componentes. 
  * A imagem de contêiner para cada módulo que é executado no dispositivo.
  * As credenciais para acessar registros de contêiner privado que contêm imagens de módulo.
  * Instruções sobre como cada módulo deve ser criado e gerenciado.
* O módulo de **Hub de IOT Edge** "e", que inclui como as mensagens fluem entre os módulos e, eventualmente, para o Hub IOT.
* Opcionalmente, as propriedades desejadas de qualquer gêmeos de módulo adicional.

Todos os dispositivos IoT Edge devem ser configurados com um manifesto de implantação. Um IoT Edge Runtime recentemente instalado relata um código de erro até que seja configurado com um manifesto válido. 

Nos tutoriais de Azure IoT Edge, você cria um manifesto de implantação através de um assistente no portal de Azure IoT Edge. Você também pode aplicar um manifesto de implantação programaticamente usando o REST ou o SDK do serviço de Hub IoT. Para obter mais informações, consulte [entender IOT Edge implantações](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Criar um manifesto de implantação

Em um alto nível, um manifesto de implantação é uma lista de gêmeos de módulo configurados com suas propriedades desejadas. Um manifesto de implantação informa a um dispositivo IoT Edge (ou um grupo de dispositivos) quais módulos instalar e como configurá-los. Os manifestos de implantação incluem as *propriedades desejadas* para cada módulo. IoT Edge dispositivos reportam as *Propriedades relatadas* para cada módulo. 

Dois módulos são necessários em todos os manifestos de implantação: `$edgeAgent`e `$edgeHub`. Esses módulos fazem parte do tempo de execução de IoT Edge que gerencia o dispositivo IoT Edge e os módulos em execução nele. Para obter mais informações sobre esses módulos, consulte [entender o tempo de execução de IOT Edge e sua arquitetura](iot-edge-runtime.md).

Além dos dois módulos de tempo de execução, você pode adicionar até 20 módulos próprios para serem executados em um dispositivo IoT Edge. 

Um manifesto de implantação que contém apenas o tempo de execução de IoT Edge (edgeAgent e edgeHub) é válido.

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

## <a name="configure-modules"></a>Configurar módulos

Defina como o tempo de execução do IoT Edge instalará os módulos em sua implantação. O agente de IoT Edge é o componente de tempo de execução que gerencia a instalação, as atualizações e o relatório de status de um dispositivo IoT Edge. Portanto, o módulo de $edgeAgent "e" é necessário para a configuração e as informações de gerenciamento de todos os módulos. Essas informações incluem os parâmetros de configuração para o próprio agente de IoT Edge. 

Para obter uma lista completa das propriedades que podem ou devem ser incluídas, consulte [Propriedades do agente de IOT Edge e do hub de IOT Edge](module-edgeagent-edgehub.md).

As propriedades de $edgeAgent seguem esta estrutura:

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

O Hub de IoT Edge gerencia a comunicação entre módulos, o Hub IoT e qualquer dispositivo folha. Portanto, o módulo de $edgeHub "folha" contém uma propriedade desejada chamada *rotas* que declara como as mensagens são passadas dentro de uma implantação. Você pode ter várias rotas na mesma implantação.

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

Cada rota precisa de uma fonte e de um coletor, mas a condição é uma parte opcional que você pode usar para filtrar mensagens. 


### <a name="source"></a>Origem

A origem especifica de onde vêm as mensagens. IoT Edge pode rotear mensagens de módulos ou dispositivos folha. 

Usando os SDKs de IoT, os módulos podem declarar filas de saída específicas para suas mensagens usando a classe ModuleClient. As filas de saída não são necessárias, mas são úteis para gerenciar várias rotas. Os dispositivos de folha podem usar a classe DeviceClient dos SDKs de IoT para enviar mensagens para dispositivos IoT Edge gateway da mesma forma que eles enviam mensagens ao Hub IoT. Para obter mais informações, consulte [entender e usar SDKs do Hub IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md).

A propriedade Source pode ser qualquer um dos seguintes valores:

| Origem | Descrição |
| ------ | ----------- |
| `/*` | Todas as mensagens do dispositivo para a nuvem ou as notificações de alteração de troca de qualquer módulo ou dispositivo de folha |
| `/twinChangeNotifications` | Qualquer alteração de folha (Propriedades relatadas) proveniente de qualquer módulo ou dispositivo de folha |
| `/messages/*` | Qualquer mensagem do dispositivo para a nuvem enviada por um módulo por meio de algumas ou nenhuma saída, ou por um dispositivo folha |
| `/messages/modules/*` | Qualquer mensagem do dispositivo para a nuvem enviada por um módulo por meio de algumas ou nenhuma saída |
| `/messages/modules/<moduleId>/*` | Qualquer mensagem do dispositivo para a nuvem enviada por um módulo específico por meio de algumas ou nenhuma saída |
| `/messages/modules/<moduleId>/outputs/*` | Qualquer mensagem do dispositivo para a nuvem enviada por um módulo específico por meio de alguma saída |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualquer mensagem do dispositivo para a nuvem enviada por um módulo específico por meio de uma saída específica |

### <a name="condition"></a>Condição
A condição é opcional em uma declaração de rota. Se você quiser passar todas as mensagens da origem para o coletor, simplesmente deixe a cláusula **Where** inteiramente. Ou você pode usar a [linguagem de consulta do Hub IOT](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar determinadas mensagens ou tipos de mensagem que atendam à condição. IoT Edge rotas não dão suporte à filtragem de mensagens com base em marcas ou propriedades de entrelaçamento. 

As mensagens que passam entre os módulos no IoT Edge são formatadas da mesma forma que as mensagens que passam entre os dispositivos e o Hub IoT do Azure. Todas as mensagens são formatadas como JSON e têm os parâmetros **SystemProperties**, **appProperties**e **Body** . 

Você pode criar consultas em qualquer um dos três parâmetros com a seguinte sintaxe: 

* Propriedades do sistema: `$<propertyName>` ou `{$<propertyName>}`
* Propriedades do aplicativo: `<propertyName>`
* Propriedades do corpo: `$body.<propertyName>` 

Para obter exemplos de como criar consultas para propriedades de mensagens, consulte [expressões de consulta de rotas de mensagens do dispositivo para a nuvem](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Um exemplo específico para IoT Edge é quando você deseja filtrar as mensagens que chegaram a um dispositivo de gateway de um dispositivo de folha. As mensagens provenientes de módulos incluem uma propriedade do sistema chamada **connectionModuleId**. Portanto, se você quiser rotear mensagens de dispositivos folha diretamente para o Hub IoT, use a rota a seguir para excluir mensagens do módulo:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink
O coletor define onde as mensagens são enviadas. Somente os módulos e o Hub IoT podem receber mensagens. As mensagens não podem ser roteadas para outros dispositivos. Não há opções de curinga na propriedade Sink. 

A propriedade Sink pode ser qualquer um dos seguintes valores:

| Sink | Descrição |
| ---- | ----------- |
| `$upstream` | Enviar a mensagem para o Hub IoT |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Enviar a mensagem para uma entrada específica de um módulo específico |

IoT Edge fornece garantias pelo menos uma vez. O Hub de IoT Edge armazena mensagens localmente no caso de uma rota não conseguir entregar a mensagem ao coletor. Por exemplo, se o Hub de IoT Edge não puder se conectar ao Hub IoT ou se o módulo de destino não estiver conectado.

IoT Edge Hub armazena as mensagens até o tempo especificado na propriedade `storeAndForwardConfiguration.timeToLiveSecs` das [propriedades desejadas do hub IOT Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definir ou atualizar as propriedades desejadas 

O manifesto de implantação especifica as propriedades desejadas para cada módulo implantado no dispositivo IoT Edge. As propriedades desejadas no manifesto de implantação substituem todas as propriedades desejadas atualmente no módulo "d".

Se você não especificar as propriedades desejadas de um módulo d no manifesto de implantação, o Hub IoT não modificará o módulo de r. Em vez disso, você pode definir as propriedades desejadas programaticamente.

Os mesmos mecanismos que permitem modificar dispositivos gêmeos são usados para modificar o módulo gêmeos. Para obter mais informações, consulte o [Guia do desenvolvedor do módulo](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Exemplo de manifesto de implantação

O exemplo a seguir mostra como pode ser um documento de manifesto de implantação válido.

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

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista completa das propriedades que podem ou devem ser incluídas em $edgeAgent e $edgeHub, consulte [Propriedades do agente de IOT Edge e hub IOT Edge](module-edgeagent-edgehub.md).

* Agora que você sabe como os módulos IoT Edge são usados, [entenda os requisitos e as ferramentas para o desenvolvimento de módulos IOT Edge](module-development.md).
