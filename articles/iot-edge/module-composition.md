---
title: Implementar módulos & rotas com manifestos de implantação - Azure IoT Edge
description: Saiba como um manifesto de implantação declara quais os módulos a implementar, como implantá-los e como criar rotas de mensagens entre eles.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2e3f1891a786751365a0bea58097e03bd41f85bb
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489928"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Saiba como implementar módulos e estabelecer rotas no IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Cada dispositivo IoT Edge executa pelo menos dois módulos: $edgeAgent e $edgeHub, que fazem parte do tempo de funcionaamento do IoT Edge. O dispositivo IoT Edge pode executar vários módulos adicionais para qualquer número de processos. Utilize um manifesto de implantação para dizer ao seu dispositivo quais os módulos a instalar e como configurá-los para trabalharem em conjunto.

O *manifesto de implantação* é um documento JSON que descreve:

* O módulo de **agente IoT Edge** twin, que inclui três componentes:
  * A imagem do recipiente para cada módulo que funciona no dispositivo.
  * As credenciais para aceder a registos privados de contentores que contêm imagens de módulos.
  * Instruções para como cada módulo deve ser criado e gerido.
* O módulo **hub IoT Edge** twin, que inclui como as mensagens fluem entre módulos e eventualmente para ioT Hub.
* As propriedades desejadas de quaisquer gémeos módulos adicionais (opcional).

Todos os dispositivos IoT Edge devem ser configurados com um manifesto de implantação. Um tempo de execução IoT Edge recentemente instalado relata um código de erro até configurar com um manifesto válido.

Nos tutoriais Azure IoT Edge, constrói-se um manifesto de implantação através de um assistente no portal Azure IoT Edge. Também pode aplicar um manifesto de implantação programáticamente utilizando REST ou o IoT Hub Service SDK. Para obter mais informações, consulte [as implementações do IoT Edge.](module-deployment-monitoring.md)

## <a name="create-a-deployment-manifest"></a>Criar um manifesto de implementação

A um nível elevado, um manifesto de implantação é uma lista de gémeos módulos que são configurados com as suas propriedades desejadas. Um manifesto de implantação diz a um dispositivo IoT Edge (ou a um grupo de dispositivos) quais os módulos para instalar e como os configurar. Os manifestos de implantação incluem as *propriedades desejadas* para cada módulo gémeo. Os dispositivos IoT Edge reportam as *propriedades reportadas* para cada módulo.

São necessários dois módulos em todos os manifestos de implantação: `$edgeAgent` e `$edgeHub` . Estes módulos fazem parte do tempo de funcionamento do IoT Edge que gere o dispositivo IoT Edge e os módulos que funcionam nele. Para obter mais informações sobre estes módulos, consulte [o tempo de execução IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Além dos dois módulos de tempo de execução, pode adicionar até 50 módulos próprios para executar num dispositivo IoT Edge.

Um manifesto de implantação que contenha apenas o tempo de execução IoT Edge (edgeAgent e edgeHub) é válido.

Os manifestos de implantação seguem esta estrutura:

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
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
    }
  }
}
```

## <a name="configure-modules"></a>Módulos de configuração

Defina como o tempo de funcionamento IoT Edge instala os módulos na sua implantação. O agente IoT Edge é o componente de tempo de execução que gere a instalação, atualizações e relatórios de estado para um dispositivo IoT Edge. Portanto, o módulo $edgeAgent twin contém a informação de configuração e gestão para todos os módulos. Esta informação inclui os parâmetros de configuração do próprio agente IoT Edge.

As propriedades $edgeAgent seguem esta estrutura:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
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
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

O esquema de esquema do agente IoT Edge versão 1.1 foi lançado juntamente com a versão 1.0.10 do IoT Edge e permite a ordem de arranque do módulo. A versão 1.1 do Schema é recomendada para qualquer implementação IoT Edge que executa a versão 1.0.10 ou posterior.

### <a name="module-configuration-and-management"></a>Configuração e gestão de módulos

A lista de propriedades desejada pelo agente IoT Edge é onde se define quais os módulos que são implantados num dispositivo IoT Edge e como devem ser configurados e geridos.

Para obter uma lista completa das propriedades desejadas que podem ou devem ser incluídas, consulte [as propriedades do agente IoT Edge e do hub IoT Edge](module-edgeagent-edgehub.md).

Por exemplo:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Cada módulo tem uma propriedade **de definições** que contém a **imagem** do módulo, um endereço para a imagem do recipiente num registo de contentores, e quaisquer **criações Opções** para configurar a imagem no arranque. Para obter mais informações, consulte [Como configurar o recipiente para criar opções para módulos IoT Edge](how-to-use-create-options.md).

O módulo edgeHub e os módulos personalizados também têm três propriedades que dizem ao agente IoT Edge como geri-los:

* **Estado**: Se o módulo deve estar em funcionamento ou parado quando foi implantado pela primeira vez. Obrigatório.
* **ReiniciarPolío**: Quando e se o agente IoT Edge deve reiniciar o módulo se parar. Obrigatório.
* **StartupOrder**: *Introduzido na versão 1.0.10 do IoT Edge.* Que encomenda o agente IoT Edge deve iniciar os módulos quando foi implantado pela primeira vez. A ordem é declarada com números inteiros, onde um módulo dado um valor de arranque de 0 é iniciado primeiro e depois números mais altos seguem. O módulo edgeAgent não tem um valor de arranque porque começa sempre primeiro. Opcional.

  O agente IoT Edge inicia os módulos por ordem do valor de arranque, mas não espera que cada módulo termine antes de ir para o próximo.

  A ordem de arranque é útil se alguns módulos dependerem de outros. Por exemplo, pode querer que o módulo EdgeHub comece primeiro para que esteja pronto para encaminhar mensagens quando os outros módulos começarem. Ou pode querer iniciar um módulo de armazenamento antes dos módulos que lhe enviam dados. No entanto, deve sempre conceber os seus módulos para lidar com falhas de outros módulos. É a natureza dos contentores que podem parar e reiniciar a qualquer momento, e em qualquer número de vezes.

## <a name="declare-routes"></a>Declarar rotas

O hub IoT Edge gere a comunicação entre módulos, IoT Hub e quaisquer dispositivos de folha. Portanto, o módulo $edgeHub twin contém uma propriedade desejada chamada *rotas* que declara como as mensagens são passadas dentro de uma implantação. Pode ter várias rotas dentro da mesma implantação.

As rotas são declaradas no **$edgeHub** propriedades desejadas com a seguinte sintaxe:

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

A versão 1.1 do hub IoT Edge foi lançada juntamente com a versão 1.0.10 do IoT Edge e permite a priorização da rota e o tempo de vida. A versão 1.1 do Schema é recomendada para qualquer implementação IoT Edge que executa a versão 1.0.10 ou posterior.

Todas as rotas precisam de uma *fonte* de onde vêm as mensagens e de um *lavatório* para onde vão as mensagens. A *condição* é uma peça opcional que pode usar para filtrar mensagens.

Pode atribuir *prioridade* às rotas que pretende certificar-se de que processam as suas mensagens primeiro. Esta funcionalidade é útil em cenários em que a ligação a montante é fraca ou limitada e você tem dados críticos que devem ser priorizados sobre mensagens de telemetria padrão.

### <a name="source"></a>Origem

A fonte especifica de onde vêm as mensagens. O IoT Edge pode encaminhar mensagens de módulos ou dispositivos de folha.

Utilizando os SDKs IoT, os módulos podem declarar filas de saída específicas para as suas mensagens utilizando a classe MóduloClient. As filas de saída não são necessárias, mas são úteis para gerir várias rotas. Os dispositivos leaf podem utilizar a classe DeviceClient dos SDKs IoT para enviar mensagens para dispositivos de gateway IoT Edge da mesma forma que enviariam mensagens para o IoT Hub. Para obter mais informações, consulte [Compreender e utilizar SDKs Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).

A propriedade de origem pode ser qualquer um dos seguintes valores:

| Fonte | Descrição |
| ------ | ----------- |
| `/*` | Todas as mensagens dispositivo-a-nuvem ou notificações de alteração dupla de qualquer módulo ou dispositivo de folha |
| `/twinChangeNotifications` | Qualquer alteração gémea (propriedades reportadas) proveniente de qualquer módulo ou dispositivo de folha |
| `/messages/*` | Qualquer mensagem dispositivo-nuvem enviada por um módulo através de alguma ou nenhuma saída, ou por um dispositivo de folha |
| `/messages/modules/*` | Qualquer mensagem dispositivo-nuvem enviada por um módulo através de alguma ou nenhuma saída |
| `/messages/modules/<moduleId>/*` | Qualquer mensagem dispositivo-nuvem enviada por um módulo específico através de alguma ou nenhuma saída |
| `/messages/modules/<moduleId>/outputs/*` | Qualquer mensagem dispositivo-nuvem enviada por um módulo específico através de alguma saída |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualquer mensagem dispositivo-nuvem enviada por um módulo específico através de uma saída específica |

### <a name="condition"></a>Condição

A condição é opcional numa declaração de rota. Se quiser passar todas as mensagens da fonte para a pia, deixe de fora a cláusula **WHERE** inteiramente. Ou pode usar o [idioma de consulta IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar determinadas mensagens ou tipos de mensagens que satisfaçam a condição. As rotas IoT Edge não suportam mensagens de filtragem baseadas em etiquetas ou propriedades duplas.

As mensagens que passam entre módulos no IoT Edge são formatadas da mesma forma que as mensagens que passam entre os seus dispositivos e o Azure IoT Hub. Todas as mensagens são formatadas como JSON e têm **sistemasProperties,** **appProperties** e parâmetros **corporais.**

Pode criar consultas em torno de qualquer um dos três parâmetros com a seguinte sintaxe:

* Propriedades do sistema: `$<propertyName>` ou `{$<propertyName>}`
* Propriedades da aplicação: `<propertyName>`
* Propriedades do corpo: `$body.<propertyName>`

Por exemplo, sobre como criar consultas para propriedades de mensagens, consulte [as expressões de perguntas de perguntas de mensagens dispositivo-a-nuvem](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Um exemplo específico do IoT Edge é quando se pretende filtrar mensagens que chegaram a um dispositivo de gateway a partir de um dispositivo de folha. As mensagens provenientes de módulos incluem uma propriedade do sistema chamada **conexãoModuleId**. Por isso, se pretender encaminhar mensagens de dispositivos de folha diretamente para o IoT Hub, utilize a seguinte rota para excluir mensagens de módulos:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink

A pia define para onde as mensagens são enviadas. Apenas os módulos e o IoT Hub podem receber mensagens. As mensagens não podem ser encaminhadas para outros dispositivos. Não há opções wildcard na propriedade da pia.

A propriedade da pia pode ser qualquer um dos seguintes valores:

| Sink | Descrição |
| ---- | ----------- |
| `$upstream` | Envie a mensagem para o IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Envie a mensagem para uma entrada específica de um módulo específico |

O IoT Edge oferece pelo menos uma vez garantias. O hub IoT Edge armazena mensagens localmente no caso de uma rota não conseguir entregar a mensagem à pia. Por exemplo, se o hub IoT Edge não conseguir ligar-se ao IoT Hub, ou se o módulo-alvo não estiver ligado.

O hub IoT Edge armazena as mensagens até ao momento especificado na `storeAndForwardConfiguration.timeToLiveSecs` propriedade do [hub IoT Edge pretendido.](module-edgeagent-edgehub.md)

### <a name="priority-and-time-to-live"></a>Prioridade e tempo a viver

As rotas podem ser declaradas apenas com uma corda que define a rota, ou como um objeto que toma uma corda de rota, um número inteiro prioritário, e um número inteiro de tempo para viver.

Opção 1:

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

Opção 2, introduzida na versão 1.0.10 do IoT Edge com o esquema do hub IoT Edge versão 1.1:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

Os valores **prioritários** podem ser 0-9, inclusive, onde 0 é a maior prioridade. As mensagens são em fila com base nos seus pontos finais. Todas as mensagens prioritárias 0 que visam um ponto final específico serão processadas antes de serem processadas quaisquer mensagens prioritárias 1 que visem o mesmo ponto final e, ao fundo da linha. Se várias rotas para o mesmo ponto final tiverem a mesma prioridade, as suas mensagens serão processadas numa base de primeiro a chegar primeiro a ser servida. Se não for especificada qualquer prioridade, a rota é atribuída à menor prioridade.

A propriedade **timeToLiveSecs** herda o seu valor a partir da loja do hub IoT **EdgeAndForwardConfiguration,** a menos que explicitamente definido. O valor pode ser qualquer inteiro positivo.

Para obter informações detalhadas sobre como as filas prioritárias são geridas, consulte a página de referência para [a prioridade da Rota e tempo de vida.](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md)

## <a name="define-or-update-desired-properties"></a>Definir ou atualizar as propriedades desejadas

O manifesto de implantação especifica as propriedades desejadas para cada módulo implantado no dispositivo IoT Edge. Propriedades desejadas no manifesto de implantação substituem quaisquer propriedades desejadas atualmente no módulo twin.

Se não especificar as propriedades desejadas de um módulo twin no manifesto de implantação, o IoT Hub não modificará de forma alguma o módulo gémeo. Em vez disso, pode definir as propriedades desejadas programáticamente.

Os mesmos mecanismos que lhe permitem modificar os gémeos do dispositivo são usados para modificar gémeos módulos. Para mais informações, consulte o [guia de desenvolvimento twin do módulo.](../iot-hub/iot-hub-devguide-module-twins.md)

## <a name="deployment-manifest-example"></a>Exemplo manifesto de implantação

O exemplo a seguir mostra como pode ser um documento manifesto de implantação válido.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
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
            "startupOrder": 2,
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
            "startupOrder": 1,
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
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista completa de propriedades que podem ou devem ser incluídas em $edgeAgent e $edgeHub, consulte [propriedades do agente IoT Edge e do hub IoT Edge](module-edgeagent-edgehub.md).

* Agora que sabe como os módulos IoT Edge são usados, [compreenda os requisitos e ferramentas para desenvolver módulos IoT Edge](module-development.md).
