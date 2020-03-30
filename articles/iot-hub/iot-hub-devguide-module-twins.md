---
title: Compreenda os gémeos do módulo Azure IoT Hub [ Microsoft Docs
description: Guia de desenvolvedores - use gémeos módulos para sincronizar dados de estado e configuração entre o IoT Hub e os seus dispositivos
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: menchi
ms.openlocfilehash: 5ef6c4de288a764abbe434c5d84fc99e154f7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303601"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Compreender e usar gémeos módulos no IoT Hub

Este artigo assume que leu ["Compreender" e usar gémeos dispositivos no IoT Hub](iot-hub-devguide-device-twins.md) primeiro. No IoT Hub, sob cada identidade de dispositivo, pode criar até 20 identidades de módulos. Cada identidade de módulo gera implicitamente um módulo twin. Semelhantes aos gémeos do dispositivo, os gémeos módulos são documentos JSON que armazenam informações estatais do módulo, incluindo metadados, configurações e condições. O Azure IoT Hub mantém um módulo twin para cada módulo que liga ao IoT Hub. 

Do lado do dispositivo, os SDKs do dispositivo IoT Hub permitem criar módulos onde cada um abre uma ligação independente ao IoT Hub. Esta funcionalidade permite-lhe utilizar espaços de nome separados para diferentes componentes no seu dispositivo. Por exemplo, tem uma máquina de venda automática que tem três sensores diferentes. Cada sensor é controlado por diferentes departamentos da sua empresa. Pode criar um módulo para cada sensor. Desta forma, cada departamento só é capaz de enviar empregos ou métodos diretos para o sensor que controla, evitando conflitos e erros de utilizador.

 A identidade do módulo e o módulo twin fornecem as mesmas capacidades que a identidade do dispositivo e o dispositivo twin, mas com uma granularidade mais fina. Esta granularidade mais fina permite que dispositivos capazes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware que gerem vários componentes, isole a configuração e as condições para cada um desses componentes. A identidade do módulo e os gémeos módulos proporcionam uma separação de preocupações de gestão ao trabalhar com dispositivos IoT que têm componentes modulares de software. Pretendemos suportar toda a funcionalidade gémea do dispositivo ao nível twin do módulo por módulo twin disponibilidade geral. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do módulo twin: *tags,* propriedades *desejadas* e *comunicadas.*
* As operações que os módulos e as extremidades traseiras podem realizar em gémeos módulos.

Consulte [a orientação de comunicação Dispositivo-nuvem](iot-hub-devguide-d2c-guidance.md) para obter orientações sobre a utilização de propriedades reportadas, mensagens dispositivo-a-nuvem ou upload de ficheiros.

Consulte [a orientação de comunicação Cloud-to-device](iot-hub-devguide-c2d-guidance.md) para obter orientações sobre a utilização de propriedades desejadas, métodos diretos ou mensagens cloud-to-device.

## <a name="module-twins"></a>Gémeos módulo

Os gémeos módulos armazenam informações relacionadas com módulos que:

* Os módulos no dispositivo e no IoT Hub podem ser utilizados para sincronizar as condições e configurações do módulo.

* A extremidade traseira da solução pode usar para consultar e visar operações de longo prazo.

O ciclo de vida de um módulo twin está ligado à identidade do [módulo](iot-hub-devguide-identity-registry.md)correspondente. Os gémeos módulos são implicitamente criados e eliminados quando uma identidade de módulo é criada ou eliminada no IoT Hub.

Um módulo twin é um documento JSON que inclui:

* **Etiquetas.** Uma secção do documento JSON que a solução traseira pode ler e escrever. As etiquetas não são visíveis para os módulos do dispositivo. As etiquetas estão definidas para fins de consulta.

* **Propriedades desejadas.** Usado juntamente com propriedades reportadas para sincronizar a configuração ou condições do módulo. A solução traseira pode definir as propriedades desejadas, e a aplicação do módulo pode lê-las. A aplicação do módulo também pode receber notificações de alterações nas propriedades desejadas.

* **Propriedades reportadas.** Usado juntamente com as propriedades desejadas para sincronizar a configuração ou as condições do módulo. A aplicação do módulo pode definir propriedades reportadas, e a solução traseira pode lê-las e questioná-las.

* **Propriedades de identidade do módulo.** A raiz do documento JSON twin do módulo contém as propriedades apenas de leitura a partir da identidade do módulo correspondente armazenada no [registo de identidade](iot-hub-devguide-identity-registry.md).

![Representação arquitetónica do dispositivo gémeo](./media/iot-hub-devguide-device-twins/module-twin.jpg)

O exemplo seguinte mostra um documento JSON twin módulo:

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

No objeto raiz encontram-se as propriedades `tags` de `reported` identidade `desired` do módulo, e objetos de contentores para e ambas e propriedades. O `properties` recipiente contém alguns elementos apenas de leitura (`$metadata`, `$etag` `$version`e ) descritos no Módulo de [metadados duplos](iot-hub-devguide-module-twins.md#module-twin-metadata) e secções de [conmoeda otimista.](iot-hub-devguide-device-twins.md#optimistic-concurrency)

### <a name="reported-property-example"></a>Exemplo de propriedade reportada

No exemplo anterior, o módulo `batteryLevel` twin contém uma propriedade que é reportada pela aplicação do módulo. Esta propriedade permite consultar e operar em módulos com base no último nível de bateria reportado. Outros exemplos incluem as capacidades do módulo de relatório de aplicativos do módulo de módulo ou opções de conectividade.

> [!NOTE]
> As propriedades reportadas simplificam cenários onde a solução final está interessada no último valor conhecido de um imóvel. Utilize [mensagens dispositivo-cloud](iot-hub-devguide-messages-d2c.md) se a extremidade traseira da solução precisar de processar a telemetria do módulo sob a forma de sequências de eventos timestamped, como séries de tempo.

### <a name="desired-property-example"></a>Exemplo de propriedade desejada

No exemplo anterior, `telemetryConfig` as propriedades desejadas e reportadas pelo módulo são utilizadas pela solução traseira e pela aplicação do módulo para sincronizar a configuração da telemetria para este módulo. Por exemplo:

1. A extremidade traseira da solução define a propriedade desejada com o valor de configuração desejado. Aqui está a parte do documento com o conjunto de propriedades pretendido:

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

2. A aplicação do módulo é notificada da alteração imediatamente se estiver ligada ou na primeira religação. A aplicação do módulo reporta então `status` a configuração atualizada (ou uma condição de erro utilizando a propriedade). Aqui está a parte das propriedades reportadas:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. A extremidade traseira da solução pode acompanhar os resultados da operação de configuração em muitos módulos, [consultando](iot-hub-devguide-query-language.md) os gémeos módulos.

> [!NOTE]
> Os snippets anteriores são exemplos, otimizados para a legibilidade, de uma forma de codificar uma configuração de módulo e o seu estado. O IoT Hub não impõe um esquema específico para o módulo twin desejado e reportado propriedades no módulo gémeos.
> 
> 

## <a name="back-end-operations"></a>Operações de back-end
A extremidade traseira da solução funciona no módulo twin utilizando as seguintes operações atómicas, expostas através de HTTPS:

* **Recuperar o módulo twin por ID**. Esta operação devolve o documento twin do módulo, incluindo etiquetas e propriedades desistema desejadas e reportadas.

* **Atualizar parcialmente o módulo twin**. Esta operação permite que a solução volte a atualizar parcialmente as etiquetas ou propriedades desejadas num módulo twin. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades `null` definidas para serem removidas. O exemplo seguinte cria um novo `{"newProperty": "newValue"}`imóvel desejado com valor, `"otherNewValue"`substitui o `otherOldProperty`valor existente de `existingProperty` , e remove . Não são feitas outras alterações às propriedades ou etiquetas existentes:

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

* **Substitua as propriedades desejadas**. Esta operação permite que a solução volte a substituir completamente todas as propriedades `properties/desired`existentes e substituir um novo documento JSON por .

* **Substitua as etiquetas**. Esta operação permite que a solução volte a substituir todas as etiquetas `tags`existentes e substituir um novo documento JSON por .

* **Receba notificações duplas**. Esta operação permite que a solução de volta seja notificada quando o gémeo é modificado. Para tal, a sua solução IoT precisa de criar uma rota e de definir a Fonte de Dados igual a *twinChangeEvents*. Por padrão, não são enviadas notificações gémeas, ou seja, não existem tais rotas antes. Se a taxa de alteração for demasiado elevada, ou por outras razões, como falhas internas, o IoT Hub poderá enviar apenas uma notificação que contenha todas as alterações. Portanto, se a sua aplicação necessitar de auditoria e registo fiável de todos os estados intermédios, deve utilizar mensagens dispositivo-cloud. A mensagem de notificação gémea inclui propriedades e corpo.

  - Propriedades

    | Nome | Valor |
    | --- | --- |
    $content | application/json |
    $iothub enqueuedtime |  Hora da notificação ser enviada |
    $iothub-fonte de mensagem | twinChangeEvents |
    $content-codificação | utf-8 |
    deviceId | ID do dispositivo |
    móduloId | ID do módulo |
    hubName | Nome do Hub IoT |
    operaçãoTimestamp | Carimbo de tempo [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) de operação |
    iothub-mensagem-schema | twinChangeNotification |
    opType | "substituir Twin" ou "updateTwin" |

    As propriedades do sistema `$` de mensagens são pré-fixadas com o símbolo.

  - Corpo
        
    Esta secção inclui todas as alterações gémeas num formato JSON. Usa o mesmo formato que um patch, com a diferença de que pode conter todas as secções gémeas: tags, properties.reported, properties.desireed, e que contém os elementos "$metadata". Por exemplo,

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

Todas as operações anteriores suportam [a conmoeda otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e requerem a permissão **ServiceConnect,** tal como definida no artigo [controle access to IoT Hub.](iot-hub-devguide-security.md)

Além destas operações, a solução na parte de trás pode consultar os gémeos do módulo usando a linguagem de [consulta IoT Hub](iot-hub-devguide-query-language.md)semelhante a SQL .

## <a name="module-operations"></a>Operações de módulos

A aplicação do módulo funciona no módulo twin utilizando as seguintes operações atómicas:

* **Recuperar o módulo twin**. Esta operação devolve o documento twin do módulo (incluindo etiquetas e propriedades desistema desejadas e reportadas) para o módulo atualmente ligado.

* **Atualizar parcialmente as propriedades reportadas.** Esta operação permite a atualização parcial das propriedades reportadas do módulo atualmente ligado. Esta operação utiliza o mesmo formato de atualização JSON que a solução traseira utiliza para uma atualização parcial das propriedades desejadas.

* **Observe as propriedades desejadas.** O módulo atualmente ligado pode optar por ser notificado das atualizações para as propriedades desejadas quando elas acontecem. O módulo recebe a mesma forma de atualização (substituição parcial ou completa) executada pela extremidade traseira da solução.

Todas as operações anteriores requerem a permissão **ModuleConnect,** tal como definida no artigo [De controlo de acesso ao IoT Hub.](iot-hub-devguide-security.md)

Os [SDKs do dispositivo Azure IoT](iot-hub-devguide-sdks.md) facilitam a utilização das operações anteriores de muitos idiomas e plataformas.

## <a name="tags-and-properties-format"></a>Formato de tags e propriedades

Tags, propriedades desejadas e propriedades reportadas são objetos JSON com as seguintes restrições:

* **Teclas**: Todas as teclas em objetos JSON são sensíveis a 64 bytes UTF-8 bytes UniCODE. Os caracteres permitidos excluem caracteres de controlo UNICODE (segmentos C0 e C1), e `.`, SP, e `$`.

* **Valores**: Todos os valores em objetos JSON podem ser dos seguintes tipos JSON: booleano, número, corda, objeto. Não são permitidas matrizes.

    * Os inteiros podem ter um valor mínimo de -4503599627370496 e um valor máximo de 4503599627370495.

    * Os valores das cordas são uTF-8 codificados e podem ter um comprimento máximo de 512 bytes.

* **Profundidade**: Todos os objetos JSON em etiquetas, propriedades desejadas e reportadas podem ter uma profundidade máxima de 5. Por exemplo, o seguinte objeto é válido:

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

## <a name="module-twin-size"></a>Módulo tamanho gémeo

O IoT Hub impõe um limite de `tags`tamanho de 8 KB no valor de `properties/desired` `properties/reported`, e um limite de tamanho de 32 KB cada um no valor de e . Estes totais são exclusivos de elementos `$version`só `$metadata/$lastUpdated`de leitura como `$etag`, e .

O tamanho gémeo é calculado da seguinte forma:

* Para cada imóvel no documento JSON, o IoT Hub calcula cumulativamente e adiciona o comprimento da chave e valor da propriedade.

* As chaves de propriedade são consideradas como cordas codificadas pelo UTF8.

* Os valores de propriedade simples são considerados como cordas codificadas por UTF8, valores numéricos (8 Bytes) ou valores booleanos (4 Bytes).

* O tamanho das cordas codificadas uTF8 é calculado contando todos os caracteres, excluindo caracteres de controlo UNICODE (segmentos C0 e C1).

* Os valores de propriedade complexos (objetos aninhados) são calculados com base no tamanho agregado das chaves de propriedade e valores de propriedade que contêm.

O IoT Hub rejeita com um erro todas as operações que aumentariam a dimensão desses documentos acima do limite.

## <a name="module-twin-metadata"></a>Módulo de metadados gémeos

O IoT Hub mantém o carimbo de tempo da última atualização para cada objeto JSON em propriedades desejadas e reportadas pelo módulo Twin. Os carimbos temporais estão em UTC e codificados no formato `YYYY-MM-DDTHH:MM:SS.mmmZ` [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) .
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

## <a name="optimistic-concurrency"></a>Conmoeda otimista

Tags, desejados e propriedades relatadas todos suportam conmoeda otimista.
As etiquetas têm um ETag, de acordo com [o RFC7232,](https://tools.ietf.org/html/rfc7232)que representa a representação json da etiqueta. Pode utilizar ETags em operações de atualização condicional a partir da extremidade traseira da solução para garantir a consistência.

As propriedades desimediadas e reportadas não `$version` têm ETags, mas têm um valor que é garantido ser incremental. Da mesma forma que um ETag, a versão pode ser usada pela parte de atualização para impor a consistência das atualizações. Por exemplo, uma aplicação de módulos para uma propriedade reportada ou a solução traseira para uma propriedade desejada.

As versões também são úteis quando um agente de observação (como a aplicação de módulos que observa as propriedades desejadas) deve conciliar as raças entre o resultado de uma operação de recuperação e uma notificação de atualização. O fluxo de [reconexão](iot-hub-devguide-device-twins.md#device-reconnection-flow) do dispositivo de secção fornece mais informações. 

## <a name="next-steps"></a>Passos seguintes

Para experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais IoT Hub:

* [Inicie com identidade do módulo IoT Hub e módulo twin usando .NET back back back e .NET dispositivo](iot-hub-csharp-csharp-module-twin-getstarted.md)
