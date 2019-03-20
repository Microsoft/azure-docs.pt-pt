---
title: Compreender duplos de módulo do IoT Hub do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – uso aos duplos de módulo para sincronizar dados de configuração e o estado entre o IoT Hub e os seus dispositivos
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 789657e53f8575b4e001fd3ec2629aaefe1a2d8b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082012"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Compreender e utilizar duplos de módulo no IoT Hub

Este artigo pressupõe que já leu [compreender e utilizar dispositivos duplos no IoT Hub](iot-hub-devguide-device-twins.md) primeiro. No IoT Hub, em cada identidade de dispositivo, pode criar até 20 identidades de módulo. Cada identidade de módulo gera implicitamente um duplo do módulo. Assim como os dispositivos duplos, duplos de módulo são documentos JSON que armazenam informações de estado do módulo incluindo metadados, configurações e condições. O IoT Hub do Azure mantém um módulo duplo para cada módulo que se liga ao IoT Hub. 

No lado do dispositivo, os SDKs de dispositivo do IoT Hub permitem-lhe criar módulos onde cada um deles é aberta uma ligação independente para o IoT Hub. Esta funcionalidade permite-lhe utilizar espaços de nomes separados para diferentes componentes no seu dispositivo. Por exemplo, tem uma máquina de venda que tem três sensores diferentes. Cada sensor é controlada por departamentos diferentes da sua empresa. Pode criar um módulo para cada sensor. Dessa forma, cada departamento só é capaz de enviar tarefas ou métodos diretos para o sensor que elas controlam, evitando conflitos e erros de utilizador.

 Identidade do módulo e módulo duplo fornecem as mesmas capacidades como a identidade de dispositivo e dispositivo duplo, mas a uma granularidade mais fina. Este granularidade mais fina permite compatível com dispositivos, como dispositivos baseados no sistema operativo ou de dispositivos com firmware gerir vários componentes, para isolar a configuração e condições para cada um desses componentes. Identidade do módulo e duplos de módulo fornecem uma separação da gestão de problemas ao trabalhar com dispositivos de IoT que tenham componentes de modular software. Visamos em dar suporte a todas as funcionalidades de gémeos de dispositivo no nível de duplo do módulo de disponibilidade geral do módulo duplo. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do módulo duplo: *etiquetas*, *pretendido* e *propriedades comunicadas*.
* As operações que os módulos e o back-ends podem realizar no duplos de módulo.

Consulte a [documentação de orientação do dispositivo-para-cloud comunicação](iot-hub-devguide-d2c-guidance.md) para obter orientações sobre como utilizar propriedades comunicadas, mensagens do dispositivo para a cloud ou carregamento de ficheiros.

Consulte a [documentação de orientação de comunicação de Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md) para obter orientações sobre como utilizar as propriedades pretendidas, métodos diretos ou mensagens na cloud para o dispositivo.

## <a name="module-twins"></a>Duplos de módulo

Duplos de módulo armazenam informações relacionadas com o módulo que:

* Módulos no dispositivo e o IoT Hub podem utilizar para sincronizar as condições de módulo e a configuração.

* A solução de back-end pode utilizar para consulta e o destino de execução longa operações.

O ciclo de vida de um módulo duplo está ligado a correspondente [identidade do módulo](iot-hub-devguide-identity-registry.md). Os gémeos de módulos implicitamente são criados e eliminados quando uma identidade de módulo é criada ou eliminada no IoT Hub.

Um módulo duplo é um documento JSON que inclui:

* **Tags**. Uma secção do documento JSON que o back-end de solução pode ler e escrever. As etiquetas não estão visíveis para os módulos no dispositivo. As etiquetas são definidas para a finalidade de consulta.

* **Propriedades pretendidas**. Utilizados em conjunto com as propriedades comunicadas para sincronizar a configuração do módulo ou condições. O back-end de solução pode definir as propriedades pretendidas e, a aplicação de módulo pode lê-los. A aplicação de módulo também pode receber notificações de alterações nas propriedades pretendidas.

* **Propriedades comunicadas**. Utilizados em conjunto com as propriedades pretendidas para sincronizar a configuração do módulo ou condições. A aplicação de módulo pode definir as propriedades comunicadas e o back-end de solução pode ler e consultá-los.

* **Propriedades de identidade do módulo**. A raiz do documento JSON duplo do módulo contém as propriedades só de leitura desde a identidade de módulo correspondente armazenada no [registo de identidade](iot-hub-devguide-identity-registry.md).

![Representação de arquitetura de dispositivo duplo](./media/iot-hub-devguide-device-twins/module-twin.jpg)

O exemplo seguinte mostra um documento JSON de duplo do módulo:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

No objeto raiz é o módulo de propriedades de identidade e objetos de contentor para `tags` e ambos `reported` e `desired` propriedades. O `properties` contentor contém alguns elementos de só de leitura (`$metadata`, `$etag`, e `$version`) descrito o [metadados do módulo duplo](iot-hub-devguide-module-twins.md#module-twin-metadata) e [a simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) seções.

### <a name="reported-property-example"></a>Exemplo de propriedades comunicadas

No exemplo anterior, o duplo do módulo contém um `batteryLevel` propriedade que é comunicada pela aplicação do módulo. Esta propriedade torna possível consultar e operam em módulos com base no último nível da bateria comunicadas. Outros exemplos incluem as opções de conectividade ou capacidades de módulo de relatórios de aplicação do módulo.

> [!NOTE]
> Propriedades comunicadas simplificam cenários em que a solução de back-end está interessada no último valor conhecido de uma propriedade. Uso [mensagens dispositivo-para-cloud](iot-hub-devguide-messages-d2c.md) se a solução de back-end tem de processar a telemetria de módulo na forma de sequências de eventos de timestamped, como a série de tempo.

### <a name="desired-property-example"></a>Exemplo de propriedade pretendida

No exemplo anterior, o `telemetryConfig` pretendidas do duplo do módulo e as propriedades comunicadas são utilizadas pela solução de back-end e a aplicação de módulo para sincronizar a configuração de telemetria para este módulo. Por exemplo:

1. A solução de back-end define a propriedade pretendida com o valor de configuração pretendida. Esta é a parte do documento com o conjunto de propriedade pretendida:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. A aplicação de módulo é notificada da alteração imediatamente se ligado, ou o restabelecimento de ligação primeiro. A aplicação de módulo, em seguida, reporta a configuração atualizada (ou uma condição de erro usando o `status` propriedade). Esta é a parte das propriedades comunicadas:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. A solução de back-end pode controlar os resultados da operação de configuração nos vários módulos, por [consultar](iot-hub-devguide-query-language.md) duplos de módulo.

> [!NOTE]
> Os trechos de código anteriores são exemplos, otimizados para facilitar a leitura, de uma forma de codificar uma configuração de módulo e o respetivo estado. IoT Hub não impõem um esquema específico para o módulo duplo desejado e propriedades no duplos de módulo comunicadas.
> 
> 

## <a name="back-end-operations"></a>Operações de back-end
O back-end de solução funciona com o módulo duplo usando as seguintes operações atômicas, expostas através de HTTPS:

* **Obter módulo duplo por ID de**. Esta operação devolve o documento de duplo do módulo, incluindo etiquetas e propriedades do sistema pretendido e informado.

* **Atualizar parcialmente módulo duplo**. Esta operação permite que a solução de back-end parcialmente atualizar as etiquetas ou as propriedades pretendidas num duplo do módulo. A atualização parcial é expresso como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades definidas para `null` são removidos. O exemplo seguinte cria uma nova propriedade pretendida com o valor `{"newProperty": "newValue"}`, substitui o valor `existingProperty` com `"otherNewValue"`e remove `otherOldProperty`. Não existem outras alterações são feitas para as propriedades pretendidas existentes ou etiquetas:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Substituir as propriedades pretendidas**. Esta operação permite que a solução de back-end completamente substituir as propriedades pretendidas de contas existentes e substituir um novo documento JSON para `properties/desired`.

* **Substituir marcas**. Esta operação permite que a solução de back-end completamente substituir todas as etiquetas e substituir um novo documento JSON para `tags`.

* **Receber notificações de duplo**. Esta operação permite que a solução de back-end ser notificado quando o duplo é modificado. Para fazer isso, sua solução de IoT precisa de criar uma rota e definir a origem de dados igual ao *twinChangeEvents*. Por predefinição, não existem notificações de duplo são enviadas, ou seja, nenhuma dessas rotas existem previamente. Se a taxa de alteração é demasiado elevada, ou por outros motivos, como falhas internos, o IoT Hub pode enviar apenas uma notificação que contém todas as alterações. Portanto, se seu aplicativo precisar fiável de auditoria e registo de todos os Estados intermediários, deve usar mensagens dispositivo-para-cloud. A mensagem de notificação de duplo inclui propriedades e o corpo.

  - Propriedades

    | Name | Value |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Tempo quando a notificação foi enviada |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID do dispositivo |
    moduleId | ID do módulo |
    hubName | Nome do IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) timestamp da operação |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" ou "updateTwin" |

    Propriedades do sistema de mensagens têm o prefixo a `$` símbolo.

  - Corpo
        
    Esta secção inclui todas as alterações de duplo em formato JSON. Utiliza o mesmo formato como um patch, com a diferença que ele pode conter todas as seções de duplo: etiquetas, properties.reported, properties.desired e que contém os elementos de "$metadata". Por exemplo,

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Suportam a todas as operações anteriores [a simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e requerem a **ServiceConnect** permissão, conforme definido no [controlar o acesso ao IoT Hub](iot-hub-devguide-security.md) artigo.

Além destas operações, a solução de back-end pode consultar os duplos de módulo com o tipo SQL [linguagem de consulta do IoT Hub](iot-hub-devguide-query-language.md).

## <a name="module-operations"></a>Operações de módulos

A aplicação de módulo funciona com o módulo duplo usando as seguintes operações atômicas:

* **Obter módulo duplo**. Esta operação devolve o documento de duplo do módulo (incluindo etiquetas e propriedades do sistema pretendido e comunicadas) para o módulo atualmente ligado.

* **Parcialmente atualizar propriedades comunicadas**. Esta operação permite que a atualização parcial das propriedades comunicadas do módulo atualmente ligada. Esta operação utiliza o mesmo formato de atualização JSON que a solução de cópia de utilizações de final de uma atualização parcial de propriedades pretendidas.

* **Observe as propriedades pretendidas**. O módulo atualmente ligado pode optar por ser notificado das atualizações para as propriedades pretendidas quando eles ocorrem. O módulo recebe o mesmo formulário de atualização (substituição total ou parcial), executado pela solução de back-end.

Todas as operações anteriores exigirem o **ModuleConnect** permissão, conforme definido na [controlar o acesso ao IoT Hub](iot-hub-devguide-security.md) artigo.

O [do Azure IoT device SDKs](iot-hub-devguide-sdks.md) facilitam a utilizar operações anteriores de vários idiomas e plataformas.

## <a name="tags-and-properties-format"></a>Formato de etiquetas e propriedades

As etiquetas, as propriedades pretendidas e propriedades comunicadas são objetos JSON, com as seguintes restrições:

* Todas as chaves em objetos JSON são maiúsculas e minúsculas 64 bytes cadeias de caracteres UNICODE UTF-8. Permitido carateres excluir carateres de controlo do UNICODE (segmentos C0 e C1), e `.`, SP, e `$`.

* Todos os valores em objetos JSON podem ser dos seguintes tipos de JSON: booleano, número, de cadeia de caracteres, de objeto. As matrizes não são permitidas. O valor máximo de números inteiros é 4503599627370495 e o valor mínimo para números inteiros é-4503599627370496.

* Todos os objetos JSON etiquetas, propriedades pretendidas e informadas podem ter uma profundidade máxima de 5. Por exemplo, o seguinte objeto é válido:

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* Todos os valores de cadeia de caracteres podem ter um máximo de 512 bytes de comprimento.

## <a name="module-twin-size"></a>Tamanho do módulo duplo

IoT Hub impõe um limite de tamanho de 8KB em cada um dos respetivos valores totais de `tags`, `properties/desired`, e `properties/reported`, excluindo os elementos somente leitura.

O tamanho é computado com contagem de todos os carateres, excluindo os caracteres de controle UNICODE (segmentos C0 e C1) e espaços que estejam fora constantes de cadeia de caracteres.

IoT Hub com um erro rejeita todas as operações que aumentará o tamanho de um desses documentos acima do limite.

## <a name="module-twin-metadata"></a>Metadados do módulo duplo

O IoT Hub mantém o carimbo de hora da última atualização para cada objeto JSON no módulo duplo desejado e propriedades comunicadas. Os carimbos de hora são em formato UTC e codificado no [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) formato `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por exemplo:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                }
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Estas informações encontram-se a todos os níveis (não apenas os folhas da estrutura JSON) para preservar as atualizações que remover chaves do objeto.

## <a name="optimistic-concurrency"></a>Simultaneidade otimista

As etiquetas, assim o desejar e a simultaneidade otimista suporte todas as propriedades comunicadas.
Etiquetas tem uma ETag, de acordo [RFC7232](https://tools.ietf.org/html/rfc7232), que representa a representação do JSON da marca. Pode utilizar ETags em operações de atualização condicional da solução de back-end para garantir a consistência.

Módulo duplo desejado e propriedades comunicadas não têm de ETags, mas tem um `$version` valor que é garantido que estará incremental. Da mesma forma para uma ETag, pode ser utilizada a versão pela atualização parte para impor a consistência das atualizações. Por exemplo, uma aplicação de módulo para uma propriedade comunicada ou a solução de back-end para uma propriedade pretendida.

Versões também são úteis quando um agente observing (por exemplo, a aplicação de módulo observar as propriedades pretendidas) tem de reconciliar corridas entre o resultado de uma operação de obtenção e uma notificação de atualização. A secção [fluxo de restabelecimento de ligação do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) fornece mais informações. 

## <a name="next-steps"></a>Passos Seguintes

Para experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do IoT Hub:

* [Introdução ao IoT Hub módulo identidade e o módulo duplo com back-end de .NET e o dispositivo de .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)
