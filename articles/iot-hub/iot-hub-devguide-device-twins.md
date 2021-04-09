---
title: Compreenda os gémeos do dispositivo Azure IoT Hub | Microsoft Docs
description: Guia do desenvolvedor - use gémeos do dispositivo para sincronizar dados de estado e de configuração entre o IoT Hub e os seus dispositivos
author: nehsin
manager: philmea
ms.author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: fff4b9157c30203f47c65a74b211e3dbf6426d92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093034"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Compreender e usar gémeos dispositivo no IoT Hub

*Os gémeos do dispositivo* são documentos JSON que armazenam informações do estado do dispositivo, incluindo metadados, configurações e condições. O Hub IoT do Azure mantém um dispositivo duplo para cada dispositivo que ligar ao serviço. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do dispositivo twin: *tags,* propriedades *desejadas* e *reportadas*.
* As operações que as aplicações do dispositivo e as extremidades traseiras podem ser operações em gémeos do dispositivo.

Utilize gémeos do dispositivo para:

* Guarde metadados específicos do dispositivo na nuvem. Por exemplo, a localização de uma máquina de venda automática.

* Informe as informações atuais do estado, tais como capacidades e condições disponíveis a partir da aplicação do seu dispositivo. Por exemplo, um dispositivo está ligado ao seu hub IoT sobre celular ou Wi-Fi.

* Sincronizar o estado dos fluxos de trabalho de longa duração entre a aplicação do dispositivo e a aplicação back-end. Por exemplo, quando a solução traseira especifica a nova versão do firmware para instalar, e a aplicação do dispositivo reporta as várias fases do processo de atualização.

* Consultar os metadados, configuração ou estado do seu dispositivo.

Consulte a [orientação de comunicação dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) para obter orientações sobre a utilização de propriedades reportadas, mensagens de dispositivo para nuvem ou upload de ficheiros.

Consulte as [orientações de comunicação cloud-to-device](iot-hub-devguide-c2d-guidance.md) para obter orientações sobre a utilização de propriedades desejadas, métodos diretos ou mensagens nuvem-dispositivo.

Para saber como os gémeos do dispositivo se relacionam com o modelo do dispositivo utilizado por um dispositivo Azure IoT Plug and Play, consulte [o Understand IoT Plug e play digital twins](../iot-pnp/concepts-digital-twin.md).

## <a name="device-twins"></a>Gémeos do dispositivo

Os gémeos do dispositivo armazenam informações relacionadas com o dispositivo que:

* As extremidades traseiras e dispositivos podem ser utilizadas para sincronizar as condições e configurações do dispositivo.

* A solução back end pode ser utilizada para consultar e direcionar as operações de longo prazo.

O ciclo de vida de um gémeo dispositivo está ligado à identidade do [dispositivo](iot-hub-devguide-identity-registry.md)correspondente . Os gémeos do dispositivo são implicitamente criados e eliminados quando uma identidade do dispositivo é criada ou eliminada no IoT Hub.

Um dispositivo gémeo é um documento JSON que inclui:

* **Etiquetas.** Uma secção do documento JSON que a solução de trás pode ler e escrever para. As etiquetas não são visíveis para aplicações de dispositivos.

* **Propriedades desejadas**. Utilizado juntamente com propriedades reportadas para sincronizar a configuração ou condições do dispositivo. A solução traseira pode definir as propriedades desejadas, e a aplicação do dispositivo pode lê-las. A aplicação do dispositivo também pode receber notificações de alterações nas propriedades desejadas.

* **Propriedades reportadas**. Utilizado juntamente com as propriedades desejadas para sincronizar a configuração ou condições do dispositivo. A aplicação do dispositivo pode definir propriedades reportadas, e a solução traseira pode lê-las e questioná-las.

* **Propriedades de identidade do dispositivo**. A raiz do documento JSON duplo do dispositivo contém as propriedades apenas de leitura a partir da identidade do dispositivo correspondente armazenada no [registo de identidade](iot-hub-devguide-identity-registry.md). Propriedades `connectionStateUpdatedTime` e `generationId` não serão incluídas.

![Screenshot das propriedades gémeas do dispositivo](./media/iot-hub-devguide-device-twins/twin.png)

O exemplo a seguir mostra um documento JSON gémeo do dispositivo:

```json
{
    "deviceId": "devA",
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

No objeto raiz encontram-se as propriedades de identidade do dispositivo e objetos de contentor `tags` para ambos `reported` e `desired` propriedades. O `properties` recipiente contém alguns elementos apenas de leitura `$metadata` (, e ) `$etag` `$version` descritos nos [metadados duplos](iot-hub-devguide-device-twins.md#device-twin-metadata) do dispositivo e secções [de conuscção otimistas.](iot-hub-devguide-device-twins.md#optimistic-concurrency)

### <a name="reported-property-example"></a>Exemplo de propriedade reportada

No exemplo anterior, o twin do dispositivo contém uma `batteryLevel` propriedade que é reportada pela aplicação do dispositivo. Esta propriedade permite consultar e operar em dispositivos com base no último nível de bateria relatado. Outros exemplos incluem as capacidades do dispositivo de reporte de aplicações ou opções de conectividade.

> [!NOTE]
> As propriedades reportadas simplificam cenários onde a solução back end está interessada no último valor conhecido de um imóvel. Utilize [mensagens dispositivo-a-nuvem](iot-hub-devguide-messages-d2c.md) se a solução traseira precisar de processar a telemetria do dispositivo sob a forma de sequências de eventos com prazos, como séries de tempo.

### <a name="desired-property-example"></a>Exemplo de propriedade desejada

No exemplo anterior, as `telemetryConfig` propriedades duplas desejadas e reportadas são utilizadas pela solução traseira e pela aplicação do dispositivo para sincronizar a configuração de telemetria deste dispositivo. Por exemplo:

1. A solução traseira define a propriedade desejada com o valor de configuração pretendido. Aqui está a parte do documento com o conjunto de propriedade pretendido:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. A aplicação do dispositivo é notificada da alteração imediatamente se estiver ligada, ou na primeira reconectação. A aplicação do dispositivo relata então a configuração atualizada (ou uma condição de erro utilizando a `status` propriedade). Aqui está a parte das propriedades relatadas:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. A solução traseira pode acompanhar os resultados da operação de configuração em muitos dispositivos [consultando](iot-hub-devguide-query-language.md) gémeos do dispositivo.

> [!NOTE]
> Os excertos anteriores são exemplos, otimizados para a legibilidade, de uma forma de codificar uma configuração do dispositivo e o seu estado. O IoT Hub não impõe um esquema específico para o dispositivo que o twin desejado e relatado nas gémeas do dispositivo.
> 

Pode utilizar gémeos para sincronizar operações de longa duração, como atualizações de firmware. Para obter mais informações sobre como utilizar propriedades para sincronizar e rastrear uma longa operação em execução através dos dispositivos, consulte [utilizar as propriedades desejadas para configurar dispositivos](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Operações de back-end

A extremidade traseira da solução funciona no dispositivo twin utilizando as seguintes operações atómicas, expostas através de HTTPS:

* **Recuperar o dispositivo gémeo por ID**. Esta operação devolve o documento duplo do dispositivo, incluindo etiquetas e propriedades do sistema desejadas e reportadas.

* **Atualizar parcialmente o dispositivo gémeo**. Esta operação permite que a extremidade traseira da solução atualize parcialmente as etiquetas ou as propriedades desejadas num dispositivo gémeo. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades definidas para `null` serem removidas. O exemplo a seguir cria uma nova propriedade desejada com `{"newProperty": "newValue"}` valor, substitui o valor existente `existingProperty` de, `"otherNewValue"` e remove `otherOldProperty` . Não são feitas outras alterações às propriedades ou etiquetas existentes:

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

* **Receba notificações duplas.** Esta operação permite que a extremidade traseira da solução seja notificada quando o gémeo é modificado. Para tal, a sua solução IoT necessita de criar uma rota e de definir a Fonte de Dados igual à *twinChangeEvents*. Por padrão, não existem tais rotas pré-existentes, pelo que não são enviadas notificações duplas. Se a taxa de variação for demasiado elevada, ou por outras razões, como falhas internas, o IoT Hub poderá enviar apenas uma notificação que contenha todas as alterações. Portanto, se a sua aplicação necessitar de auditoria fiável e registo de todos os estados intermédios, deve utilizar mensagens dispositivo-a-nuvem. A mensagem de notificação dupla inclui propriedades e corpo.

  - Propriedades

    | Name | Valor |
    | --- | --- |
    $content tipo | application/json |
    $iothub-enquestime |  Hora em que a notificação foi enviada |
    $iothub-mensagem-fonte | twinChangeEvents |
    $content codificação | utf-8 |
    deviceId | ID do dispositivo |
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

Todas as operações anteriores suportam [a concordância otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e requerem a permissão **ServiceConnect,** tal como definida no [acesso ao Control ao IoT Hub](iot-hub-devguide-security.md).

Além destas operações, a solução traseira pode:

* Consultar os gémeos do dispositivo utilizando a linguagem de [consulta IoT Hub](iot-hub-devguide-query-language.md)semelhante ao SQL .

* Executar operações em grandes conjuntos de gémeos dispositivos utilizando [trabalhos](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operações de dispositivos

A aplicação do dispositivo funciona no dispositivo twin utilizando as seguintes operações atómicas:

* **Recuperar o dispositivo gémeo**. Esta operação devolve o documento duplo do dispositivo (incluindo as propriedades desejadas e reportadas do sistema) para o dispositivo atualmente ligado. (As etiquetas não são visíveis para aplicações de dispositivos.)

* **Atualizar parcialmente as propriedades reportadas**. Esta operação permite a atualização parcial das propriedades reportadas do dispositivo atualmente ligado. Esta operação utiliza o mesmo formato de atualização JSON que a solução back end utiliza para uma atualização parcial das propriedades desejadas.

* **Observe as propriedades desejadas.** O dispositivo atualmente ligado pode optar por ser notificado das atualizações às propriedades desejadas quando elas ocorrem. O dispositivo recebe a mesma forma de atualização (substituição parcial ou completa) executada pela extremidade traseira da solução.

Todas as operações anteriores requerem a permissão **DeviceConnect,** tal como definida no [Control Access to IoT Hub](iot-hub-devguide-security.md).

Os [SDKs de dispositivo Azure IoT](iot-hub-devguide-sdks.md) facilitam a utilização das operações anteriores de muitas línguas e plataformas. Para obter mais informações sobre os detalhes dos primitivos IoT Hub para a sincronização de propriedades desejadas, consulte [o fluxo de reconexão do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow).

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

## <a name="device-twin-size"></a>Tamanho duplo do dispositivo

O IoT Hub impõe um limite de tamanho de 8 KB no valor de , e um limite de tamanho de `tags` 32 KB cada um no valor de `properties/desired` e `properties/reported` . Estes totais são exclusivos de elementos só de leitura como `$etag` `$version` , e `$metadata/$lastUpdated` .

O tamanho gémeo é calculado da seguinte forma:

* Para cada imóvel no documento JSON, o IoT Hub calcula cumulativamente e adiciona o comprimento da chave e valor da propriedade.

* As chaves de propriedade são consideradas cordas codificadas pela UTF8.

* Os valores simples de propriedade são considerados como cordas codificadas utf8, valores numéricos (8 Bytes) ou valores booleanos (4 Bytes).

* O tamanho das cordas codificadas utf8 é calculado contando todos os caracteres, excluindo caracteres de controlo UNICODE (segmentos C0 e C1).

* Os valores de propriedade complexos (objetos aninhados) são calculados com base no tamanho agregado das chaves de propriedade e valores de propriedade que contêm.

O IoT Hub rejeita com um erro todas as operações que aumentariam o tamanho do `tags` `properties/desired` , ou `properties/reported` documentos acima do limite.

## <a name="device-twin-metadata"></a>Metadados duplos do dispositivo

O IoT Hub mantém o tempotando da última atualização para cada objeto JSON em propriedades duplas e reportadas do dispositivo. Os cartões de tempo estão na UTC e codificados no formato [ISO8601.](https://en.wikipedia.org/wiki/ISO_8601) `YYYY-MM-DDTHH:MM:SS.mmmZ`

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
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
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

As propriedades duplas do dispositivo não têm ETags, mas têm um `$version` valor que é garantido ser incremental. Da mesma forma que um ETag, a versão pode ser usada pela parte de atualização para impor a consistência das atualizações. Por exemplo, uma aplicação de dispositivo para uma propriedade reportada ou a solução back end para uma propriedade desejada.

As versões também são úteis quando um agente de observação (como a aplicação do dispositivo que observa as propriedades desejadas) deve conciliar as corridas entre o resultado de uma operação de recuperação e uma notificação de atualização. A [secção de fluxo de reconexão do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) fornece mais informações.

## <a name="device-reconnection-flow"></a>Fluxo de reconexão do dispositivo

O IoT Hub não preserva as notificações de atualização de propriedades desejadas para dispositivos desligados. Daí resulta que um dispositivo que está a ligar deve recuperar todo o documento de propriedades pretendido, além de subscrever notificações de atualização. Dada a possibilidade de corridas entre notificações de atualização e recuperação total, deve ser assegurado o seguinte fluxo:

1. A aplicação do dispositivo liga-se a um hub IoT.
2. A aplicação do dispositivo subscreve as notificações de atualização de propriedades desejadas.
3. A aplicação do dispositivo recupera todo o documento para as propriedades desejadas.

A aplicação do dispositivo pode ignorar todas as notificações com `$version` menos ou igual do que a versão do documento completo recuperado. Esta abordagem é possível porque o IoT Hub garante que as versões incrementam sempre.

> [!NOTE]
> Esta lógica já está implementada nos [SDKs do dispositivo Azure IoT](iot-hub-devguide-sdks.md). Esta descrição só é útil se a aplicação do dispositivo não puder utilizar nenhum dos SDKs do dispositivo Azure IoT e deve programar diretamente a interface MQTT.
> 

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* O artigo [de pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descreve os vários pontos finais que cada hub IoT expõe para operações de tempo de execução e gestão.

* O artigo [de Throttling e quotas](iot-hub-devguide-quotas-throttling.md) descreve as quotas que se aplicam ao serviço IoT Hub e o comportamento de estrangulamento que se espera quando utiliza o serviço.

* O [artigo da Azure IoT e do serviço SDKs](iot-hub-devguide-sdks.md) lista os vários SDKs de língua que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* A [linguagem de consulta IoT Hub para gémeos de dispositivos, empregos e artigo de encaminhamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta IoT Hub que pode usar para obter informações do IoT Hub sobre os gémeos e empregos do seu dispositivo.

* O artigo [de suporte do IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre os gémeos do dispositivo, pode estar interessado nos seguintes tópicos de guia de desenvolvimento do IoT Hub:

* [Compreender e usar gémeos módulos no IoT Hub](iot-hub-devguide-module-twins.md)
* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md)
* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do IoT Hub:

* [Como usar o dispositivo twin](iot-hub-node-node-twin-getstarted.md)
* [Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)
* [Gestão de dispositivos com as Ferramentas do Azure IoT para VS Code](iot-hub-device-management-iot-toolkit.md)
