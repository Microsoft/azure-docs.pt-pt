---
title: Compreenda os gémeos do módulo Azure IoT Hub | Microsoft Docs
description: Developer guide - use gémeos módulos para sincronizar dados de estado e de configuração entre o IoT Hub e os seus dispositivos
author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: nehsin
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6e728eaf8335a102e38a3b4b07ab5e504d452294
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91996467"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Compreender e usar gémeos módulos no IoT Hub

Este artigo pressupõe que leu [Compreender e usar gémeos de dispositivo no IoT Hub](iot-hub-devguide-device-twins.md) primeiro. No IoT Hub, sob cada identidade do dispositivo, pode criar até 50 identidades de módulos. Cada identidade do módulo gera implicitamente um módulo gémeo. Semelhantes aos gémeos do dispositivo, os gémeos módulos são documentos JSON que armazenam informações do estado do módulo, incluindo metadados, configurações e condições. O Azure IoT Hub mantém um módulo gémeo para cada módulo que se conecta ao IoT Hub. 

Do lado do dispositivo, os SDKs do dispositivo IoT Hub permitem criar módulos onde cada um abre uma ligação independente ao IoT Hub. Esta funcionalidade permite-lhe utilizar espaços de nome separados para diferentes componentes do seu dispositivo. Por exemplo, tem uma máquina de venda automática que tem três sensores diferentes. Cada sensor é controlado por diferentes departamentos da sua empresa. Pode criar um módulo para cada sensor. Desta forma, cada departamento só é capaz de enviar empregos ou métodos diretos para o sensor que controlam, evitando conflitos e erros do utilizador.

 A identidade do módulo e o módulo gémeo fornecem as mesmas capacidades que a identidade do dispositivo e o dispositivo gémeo, mas com uma granularidade mais fina. Esta granularidade mais fina permite que dispositivos capazes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware que gerem vários componentes, isolem a configuração e as condições para cada um desses componentes. A identidade do módulo e os gémeos módulos proporcionam uma separação de gestão de preocupações ao trabalhar com dispositivos IoT que têm componentes modulares de software. Pretendemos suportar toda a funcionalidade twin do dispositivo a nível twin do módulo por módulo twin disponibilidade geral. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do módulo twin: *tags,* propriedades *desejadas* e *reportadas.*
* As operações que os módulos e as extremidades traseiras podem realizar em gémeos módulos.

Consulte a [orientação de comunicação dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) para obter orientações sobre a utilização de propriedades reportadas, mensagens de dispositivo para nuvem ou upload de ficheiros.

Consulte as [orientações de comunicação cloud-to-device](iot-hub-devguide-c2d-guidance.md) para obter orientações sobre a utilização de propriedades desejadas, métodos diretos ou mensagens nuvem-dispositivo.

## <a name="module-twins"></a>Gémeos módulos

As gémeas módulos armazenam informações relacionadas com módulos que:

* Os módulos no dispositivo e no IoT Hub podem ser utilizados para sincronizar as condições e configurações do módulo.

* A solução back end pode ser utilizada para consultar e direcionar as operações de longo prazo.

O ciclo de vida de um módulo gémeo está ligado à identidade do [módulo](iot-hub-devguide-identity-registry.md)correspondente. Os gémeos módulos são implicitamente criados e eliminados quando uma identidade do módulo é criada ou eliminada no IoT Hub.

Um módulo gémeo é um documento JSON que inclui:

* **Etiquetas.** Uma secção do documento JSON que a solução de trás pode ler e escrever para. As etiquetas não são visíveis para os módulos do dispositivo. As etiquetas estão definidas para o propósito de consulta.

* **Propriedades desejadas**. Utilizado juntamente com propriedades reportadas para sincronizar a configuração ou condições do módulo. A solução traseira pode definir as propriedades desejadas, e a aplicação do módulo pode lê-las. A aplicação do módulo também pode receber notificações de alterações nas propriedades desejadas.

* **Propriedades reportadas**. Utilizado juntamente com as propriedades desejadas para sincronizar a configuração ou condições do módulo. A aplicação do módulo pode definir propriedades reportadas, e a solução back end pode lê-las e questioná-las.

* **Propriedades de identidade do módulo.** A raiz do documento JSON gémeo do módulo contém as propriedades apenas de leitura a partir da identidade do módulo correspondente armazenada no [registo de identidade.](iot-hub-devguide-identity-registry.md)

![Representação arquitetônica do dispositivo twin](./media/iot-hub-devguide-device-twins/module-twin.jpg)

O exemplo a seguir mostra um documento JSON gémeo módulo:

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
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

No objeto raiz estão as propriedades de identidade do módulo, e objetos de recipiente para `tags` e ambos `reported` e `desired` propriedades. O `properties` recipiente contém alguns elementos apenas de leitura `$metadata` (, e ) `$etag` `$version` descritos nos [metadados gémeos](iot-hub-devguide-module-twins.md#module-twin-metadata) módulos e secções [de conuscção otimistas.](iot-hub-devguide-device-twins.md#optimistic-concurrency)

### <a name="reported-property-example"></a>Exemplo de propriedade reportada

No exemplo anterior, o módulo twin contém uma `batteryLevel` propriedade que é reportada pela app do módulo. Esta propriedade permite consultar e operar em módulos com base no último nível de bateria relatado. Outros exemplos incluem as capacidades do módulo de relatório de aplicativos ou opções de conectividade.

> [!NOTE]
> As propriedades reportadas simplificam cenários onde a solução back end está interessada no último valor conhecido de um imóvel. Utilize [mensagens dispositivo-a-nuvem](iot-hub-devguide-messages-d2c.md) se a solução traseira precisar de processar a telemetria do módulo sob a forma de sequências de eventos com tempotado, como séries de tempo.

### <a name="desired-property-example"></a>Exemplo de propriedade desejada

No exemplo anterior, as `telemetryConfig` propriedades duplas do módulo desejadas e reportadas são utilizadas pela solução traseira e pela aplicação do módulo para sincronizar a configuração da telemetria para este módulo. Por exemplo:

1. A solução traseira define a propriedade desejada com o valor de configuração pretendido. Aqui está a parte do documento com o conjunto de propriedade pretendido:

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

2. A aplicação do módulo é notificada da alteração imediatamente se estiver ligada, ou na primeira reconexão. A aplicação do módulo reporta então a configuração atualizada (ou uma condição de erro utilizando a `status` propriedade). Aqui está a parte das propriedades relatadas:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. A solução traseira pode acompanhar os resultados da operação de configuração em muitos módulos, [consultando](iot-hub-devguide-query-language.md) gémeos módulos.

> [!NOTE]
> Os excertos anteriores são exemplos, otimizados para a legibilidade, de uma forma de codificar uma configuração do módulo e o seu estado. O IoT Hub não impõe um esquema específico para o módulo gémeo desejado e relatado propriedades nos gémeos módulos.
> 
> 

## <a name="back-end-operations"></a>Operações de back-end
A extremidade traseira da solução funciona no módulo twin utilizando as seguintes operações atómicas, expostas através de HTTPS:

* **Recuperar módulo gémeo por ID**. Esta operação devolve o documento gémeo do módulo, incluindo etiquetas e propriedades do sistema desejadas e reportadas.

* **Atualizar parcialmente o módulo gémeo**. Esta operação permite que a extremidade traseira da solução atualize parcialmente as etiquetas ou as propriedades desejadas num módulo twin. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades definidas para `null` serem removidas. O exemplo a seguir cria uma nova propriedade desejada com `{"newProperty": "newValue"}` valor, substitui o valor existente `existingProperty` de, `"otherNewValue"` e remove `otherOldProperty` . Não são feitas outras alterações às propriedades ou etiquetas existentes:

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

* **Substitua as propriedades desejadas.** Esta operação permite que a solução volte a substituir completamente todas as propriedades existentes e substitua um novo documento JSON para `properties/desired` .

* **Substitua as etiquetas**. Esta operação permite que a solução substitua completamente todas as etiquetas existentes e substitua um novo documento JSON para `tags` .

* **Receba notificações duplas.** Esta operação permite que a extremidade traseira da solução seja notificada quando o gémeo é modificado. Para tal, a sua solução IoT necessita de criar uma rota e de definir a Fonte de Dados igual à *twinChangeEvents*. Por padrão, não são enviadas notificações gémeas, ou seja, não existem tais rotas pré-existentes. Se a taxa de variação for demasiado elevada, ou por outras razões, como falhas internas, o IoT Hub poderá enviar apenas uma notificação que contenha todas as alterações. Portanto, se a sua aplicação necessitar de auditoria fiável e registo de todos os estados intermédios, deve utilizar mensagens dispositivo-a-nuvem. A mensagem de notificação dupla inclui propriedades e corpo.

  - Propriedades

    | Name | Valor |
    | --- | --- |
    $content tipo | application/json |
    $iothub-enquestime |  Hora em que a notificação foi enviada |
    $iothub-mensagem-fonte | twinChangeEvents |
    $content codificação | utf-8 |
    deviceId | ID do dispositivo |
    moduleId | ID do módulo |
    nome hub | Nome do Hub IoT |
    operaçãoTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) timetamp de funcionamento |
    iothub-mensagem-schema | twinChangeNotification |
    opType | "substituirTwin" ou "updateTwin" |

    As propriedades do sistema de mensagem estão pré-fixas com o `$` símbolo.

  - Corpo
        
    Esta secção inclui todas as alterações gémeas num formato JSON. Utiliza o mesmo formato que um patch, com a diferença de que pode conter todas as secções gémeas: tags, propriedades.relatados, propriedades.desejadas, e que contém os elementos "$metadata". Por exemplo,

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

Todas as operações anteriores suportam [a concordância otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e requerem a permissão **ServiceConnect,** tal como definida no artigo [Access to IoT Hub.](iot-hub-devguide-security.md)

Além destas operações, a solução traseira pode consultar os gémeos módulos utilizando a linguagem de [consulta IoT Hub](iot-hub-devguide-query-language.md)semelhante ao SQL.

## <a name="module-operations"></a>Operações de módulos

A aplicação do módulo funciona no módulo twin utilizando as seguintes operações atómicas:

* **Recuperar módulo gémeo**. Esta operação devolve o documento gémeo do módulo (incluindo etiquetas e propriedades do sistema desejadas e reportadas) para o módulo atualmente ligado.

* **Atualizar parcialmente as propriedades reportadas**. Esta operação permite a atualização parcial das propriedades reportadas do módulo atualmente ligado. Esta operação utiliza o mesmo formato de atualização JSON que a solução back end utiliza para uma atualização parcial das propriedades desejadas.

* **Observe as propriedades desejadas.** O módulo atualmente conectado pode optar por ser notificado das atualizações às propriedades desejadas quando elas acontecem. O módulo recebe a mesma forma de atualização (substituição parcial ou completa) executada pela solução traseira.

Todas as operações anteriores requerem a permissão **ModuleConnect,** tal como definida no artigo [Access to IoT Hub.](iot-hub-devguide-security.md)

Os [SDKs de dispositivo Azure IoT](iot-hub-devguide-sdks.md) facilitam a utilização das operações anteriores de muitas línguas e plataformas.

## <a name="tags-and-properties-format"></a>Formato de tags e propriedades

Etiquetas, propriedades desejadas e propriedades reportadas são objetos JSON com as seguintes restrições:

* **Teclas**: Todas as teclas em objetos JSON são UTF-8 codificadas, sensíveis a maiísso e até 1 KB de comprimento. Os caracteres permitidos excluem caracteres de controlo UNICODE (segmentos C0 e C1) `.` `$` e, e SP.

* **Valores**: Todos os valores em objetos JSON podem ser dos seguintes tipos JSON: boolean, número, corda, objeto. As matrizes também são suportadas.

    * Os números inteiros podem ter um valor mínimo de -4503599627370496 e um valor máximo de 4503599627370495.

    * Os valores das cordas são UTF-8 codificados e podem ter um comprimento máximo de 4 KB.

* **Profundidade**: A profundidade máxima dos objetos JSON em etiquetas, propriedades desejadas e propriedades reportadas é de 10. Por exemplo, o seguinte objeto é válido:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="module-twin-size"></a>Tamanho gémeo do módulo

O IoT Hub impõe um limite de tamanho de 8 KB no valor de , e um limite de tamanho de `tags` 32 KB cada um no valor de `properties/desired` e `properties/reported` . Estes totais são exclusivos de elementos só de leitura como `$etag` `$version` , e `$metadata/$lastUpdated` .

O tamanho gémeo é calculado da seguinte forma:

* Para cada imóvel no documento JSON, o IoT Hub calcula cumulativamente e adiciona o comprimento da chave e valor da propriedade.

* As chaves de propriedade são consideradas cordas codificadas pela UTF8.

* Os valores simples de propriedade são considerados como cordas codificadas utf8, valores numéricos (8 Bytes) ou valores booleanos (4 Bytes).

* O tamanho das cordas codificadas utf8 é calculado contando todos os caracteres, excluindo caracteres de controlo UNICODE (segmentos C0 e C1).

* Os valores de propriedade complexos (objetos aninhados) são calculados com base no tamanho agregado das chaves de propriedade e valores de propriedade que contêm.

O IoT Hub rejeita com um erro todas as operações que aumentariam o tamanho desses documentos acima do limite.

## <a name="module-twin-metadata"></a>Metadados duplos do módulo

O IoT Hub mantém o tempotando da última atualização para cada objeto JSON em propriedades duplas de módulos desejadas e reportadas. Os cartões de tempo estão na UTC e codificados no formato [ISO8601.](https://en.wikipedia.org/wiki/ISO_8601) `YYYY-MM-DDTHH:MM:SS.mmmZ`
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
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
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

Esta informação é mantida em todos os níveis (e não apenas nas folhas da estrutura JSON) para preservar atualizações que removem as teclas do objeto.

## <a name="optimistic-concurrency"></a>Concordância otimista

Etiquetas, propriedades desejadas e reportadas todos suportam concuência otimista.
As tags têm um ETag, de acordo com [o RFC7232,](https://tools.ietf.org/html/rfc7232)que representa a representação JSON da tag. Pode utilizar ETags em operações de atualização condicional a partir da parte traseira da solução para garantir a consistência.

As propriedades do módulo twin desejadas e reportadas não têm ETags, mas têm um `$version` valor que é garantido ser incremental. Da mesma forma que um ETag, a versão pode ser usada pela parte de atualização para impor a consistência das atualizações. Por exemplo, uma aplicação de módulo para uma propriedade reportada ou a solução back end para uma propriedade desejada.

As versões também são úteis quando um agente de observação (como a app do módulo que observa as propriedades desejadas) deve conciliar as corridas entre o resultado de uma operação de recuperação e uma notificação de atualização. A secção [Fluxo de reconexão do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) fornece mais informações. 

## <a name="next-steps"></a>Passos seguintes

Para experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do IoT Hub:

* [Começa com a identidade do módulo IoT Hub e o módulo twin usando .NET back end e .NET device](iot-hub-csharp-csharp-module-twin-getstarted.md)
