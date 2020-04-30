---
title: Compreenda os gémeos do dispositivo Azure IoT Hub [ Microsoft Docs
description: Guia de desenvolvedores - use gémeos dispositivos para sincronizar dados de estado e configuração entre o IoT Hub e os seus dispositivos
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.custom: mqtt
ms.openlocfilehash: 3bec3d19ed68b7eb8bb50baa8f6c11135ef778cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731471"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Compreender e usar gémeos dispositivos em IoT Hub

*Os gémeos* do dispositivo são documentos JSON que armazenam informações estatais do dispositivo, incluindo metadados, configurações e condições. O Hub IoT do Azure mantém um dispositivo duplo para cada dispositivo que ligar ao serviço. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do dispositivo twin: *tags,* propriedades *desejadas* e *comunicadas*.
* As operações que as aplicações do dispositivo e as extremidades traseiras podem ser operadas em gémeos dispositivos.

Utilize gémeos do dispositivo para:

* Guarde metadados específicos do dispositivo na nuvem. Por exemplo, a localização de implantação de uma máquina de venda automática.

* Informe as informações atuais do Estado, tais como capacidades e condições disponíveis da aplicação do seu dispositivo. Por exemplo, um dispositivo está ligado ao seu hub IoT sobre celular ou WiFi.

* Sincronizar o estado dos fluxos de trabalho de longo prazo entre a aplicação do dispositivo e a aplicação back-end. Por exemplo, quando a solução traseira especifica a nova versão do firmware para instalar, e a aplicação do dispositivo reporta as várias fases do processo de atualização.

* Consulta dos metadados, configuração ou estado do seu dispositivo.

Consulte [a orientação de comunicação Dispositivo-nuvem](iot-hub-devguide-d2c-guidance.md) para obter orientações sobre a utilização de propriedades reportadas, mensagens dispositivo-a-nuvem ou upload de ficheiros.

Consulte [a orientação de comunicação Cloud-to-device](iot-hub-devguide-c2d-guidance.md) para obter orientações sobre a utilização de propriedades desejadas, métodos diretos ou mensagens cloud-to-device.

## <a name="device-twins"></a>Gémeos dispositivo

Os gémeos do dispositivo armazenam informações relacionadas com dispositivos que:

* As extremidades do dispositivo e das costas podem ser utilizadas para sincronizar as condições e configurações do dispositivo.

* A extremidade traseira da solução pode usar para consultar e visar operações de longo prazo.

O ciclo de vida de um dispositivo twin está ligado à identidade do [dispositivo](iot-hub-devguide-identity-registry.md)correspondente . Os gémeos do dispositivo são implicitamente criados e eliminados quando uma identidade do dispositivo é criada ou eliminada no IoT Hub.

Um twin de dispositivo é um documento JSON que inclui:

* **Etiquetas.** Uma secção do documento JSON que a solução traseira pode ler e escrever. As etiquetas não são visíveis para aplicações de dispositivos.

* **Propriedades desejadas.** Usado juntamente com propriedades reportadas para sincronizar a configuração ou as condições do dispositivo. A solução traseira pode definir as propriedades desejadas, e a aplicação do dispositivo pode lê-las. A aplicação do dispositivo também pode receber notificações de alterações nas propriedades desejadas.

* **Propriedades reportadas.** Usado juntamente com as propriedades desejadas para sincronizar a configuração ou as condições do dispositivo. A aplicação do dispositivo pode definir propriedades reportadas, e a solução traseira pode lê-las e questioná-las.

* **Propriedades de identidade do dispositivo.** A raiz do documento JSON gémeo do dispositivo contém as propriedades apenas de leitura a partir da identidade do dispositivo correspondente armazenada no [registo de identidade](iot-hub-devguide-identity-registry.md). Propriedades `connectionStateUpdatedTime` `generationId` e não serão incluídas.

![Screenshot das propriedades gémeas do dispositivo](./media/iot-hub-devguide-device-twins/twin.png)

O exemplo seguinte mostra um documento JSON gémeo do dispositivo:

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

No objeto raiz encontram-se as propriedades `tags` de `reported` identidade `desired` do dispositivo, e objetos de contentores para e ambas e propriedades. O `properties` recipiente contém alguns elementos apenas de leitura (`$metadata`, `$etag` `$version`e ) descritos nos [metadados gémeos do Dispositivo](iot-hub-devguide-device-twins.md#device-twin-metadata) e secções de [conmoeda otimista.](iot-hub-devguide-device-twins.md#optimistic-concurrency)

### <a name="reported-property-example"></a>Exemplo de propriedade reportada

No exemplo anterior, o dispositivo `batteryLevel` twin contém uma propriedade que é reportada pela aplicação do dispositivo. Esta propriedade permite consultar e operar em dispositivos com base no último nível de bateria reportado. Outros exemplos incluem as capacidades do dispositivo de reporte de dispositivos ou opções de conectividade.

> [!NOTE]
> As propriedades reportadas simplificam cenários onde a solução final está interessada no último valor conhecido de um imóvel. Utilize [mensagens dispositivo-cloud](iot-hub-devguide-messages-d2c.md) se a extremidade traseira da solução precisar de processar a telemetria do dispositivo sob a forma de sequências de eventos timestamped, como séries de tempo.

### <a name="desired-property-example"></a>Exemplo de propriedade desejada

No exemplo anterior, `telemetryConfig` as propriedades desejadas e reportadas pelo dispositivo são utilizadas pela extremidade traseira da solução e pela aplicação do dispositivo para sincronizar a configuração da telemetria para este dispositivo. Por exemplo:

1. A extremidade traseira da solução define a propriedade desejada com o valor de configuração desejado. Aqui está a parte do documento com o conjunto de propriedades pretendido:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. A aplicação do dispositivo é notificada da alteração imediatamente se estiver ligada ou no primeiro reconectar-se. A aplicação do dispositivo reporta então `status` a configuração atualizada (ou uma condição de erro utilizando a propriedade). Aqui está a parte das propriedades reportadas:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. A extremidade traseira da solução pode acompanhar os resultados da operação de configuração em muitos dispositivos [consultando](iot-hub-devguide-query-language.md) gémeos dispositivos.

> [!NOTE]
> Os excertos anteriores são exemplos, otimizados para a legibilidade, de uma forma de codificar a configuração de um dispositivo e o seu estado. O IoT Hub não impõe um esquema específico para o dispositivo que twin desejado e reportado propriedades no dispositivo gémeos.
> 

Pode utilizar gémeos para sincronizar operações de longo prazo, como atualizações de firmware. Para obter mais informações sobre como utilizar propriedades para sincronizar e rastrear uma operação de longo curso através dos dispositivos, consulte [utilize propriedades desejadas para configurar dispositivos](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Operações de back-end

A extremidade traseira da solução funciona no dispositivo twin utilizando as seguintes operações atómicas, expostas através de HTTPS:

* **Recuperar dispositivo gémeo por ID**. Esta operação devolve o documento twin do dispositivo, incluindo etiquetas e propriedades desistema desejadas e reportadas.

* **Atualizar parcialmente o dispositivo twin**. Esta operação permite que a solução volte a atualizar parcialmente as etiquetas ou propriedades desejadas num dispositivo twin. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades `null` definidas para serem removidas. O exemplo seguinte cria um novo `{"newProperty": "newValue"}`imóvel desejado com valor, `"otherNewValue"`substitui o `otherOldProperty`valor existente de `existingProperty` , e remove . Não são feitas outras alterações às propriedades ou etiquetas existentes:

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

* **Receba notificações duplas**. Esta operação permite que a solução de volta seja notificada quando o gémeo é modificado. Para tal, a sua solução IoT precisa de criar uma rota e de definir a Fonte de Dados igual a *twinChangeEvents*. Por predefinição, não existem tais rotas antes, pelo que não são enviadas notificações gémeas. Se a taxa de alteração for demasiado elevada, ou por outras razões, como falhas internas, o IoT Hub poderá enviar apenas uma notificação que contenha todas as alterações. Portanto, se a sua aplicação necessitar de auditoria e registo fiável de todos os estados intermédios, deve utilizar mensagens dispositivo-cloud. A mensagem de notificação gémea inclui propriedades e corpo.

  - Propriedades

    | Nome | Valor |
    | --- | --- |
    $content | application/json |
    $iothub enqueuedtime |  Hora da notificação ser enviada |
    $iothub-fonte de mensagem | twinChangeEvents |
    $content-codificação | utf-8 |
    deviceId | ID do dispositivo |
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

Todas as operações anteriores suportam [a conmoeda otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e requerem a permissão **ServiceConnect,** tal como definida no [acesso ao IoT Hub](iot-hub-devguide-security.md).

Além destas operações, a solução de fundo pode:

* Consultar os gémeos do dispositivo usando a linguagem de [consulta IoT Hub](iot-hub-devguide-query-language.md)semelhante a SQL .

* Efetuar operações em grandes conjuntos de gémeos dispositivos utilizando [trabalhos](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operações de dispositivos

A aplicação do dispositivo funciona no dispositivo twin utilizando as seguintes operações atómicas:

* **Recuperar o dispositivo twin**. Esta operação devolve o documento twin do dispositivo (incluindo as propriedades do sistema desejados e reportados) para o dispositivo atualmente ligado. (As etiquetas não são visíveis para aplicações de dispositivos.)

* **Atualizar parcialmente as propriedades reportadas.** Esta operação permite a atualização parcial das propriedades reportadas do dispositivo atualmente ligado. Esta operação utiliza o mesmo formato de atualização JSON que a solução traseira utiliza para uma atualização parcial das propriedades desejadas.

* **Observe as propriedades desejadas.** O dispositivo atualmente ligado pode optar por ser notificado das atualizações para as propriedades desejadas quando elas acontecerem. O dispositivo recebe a mesma forma de atualização (substituição parcial ou completa) executada pela extremidade traseira da solução.

Todas as operações anteriores requerem a permissão **DeviceConnect,** tal como definida no [Control Access to IoT Hub](iot-hub-devguide-security.md).

Os [SDKs do dispositivo Azure IoT](iot-hub-devguide-sdks.md) facilitam a utilização das operações anteriores de muitos idiomas e plataformas. Para obter mais informações sobre os detalhes dos primitivos do IoT Hub para sincronização de propriedades desejadas, consulte o fluxo de [reconexão do Dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Formato de tags e propriedades

Tags, propriedades desejadas e propriedades reportadas são objetos JSON com as seguintes restrições:

* **Teclas**: Todas as teclas em objetos JSON são codificadas, sensíveis a casos e até 1 KB de comprimento. Os caracteres permitidos excluem caracteres de controlo UNICODE `$`(segmentos C0 e C1), e, `.`e SP.

* **Valores**: Todos os valores em objetos JSON podem ser dos seguintes tipos JSON: booleano, número, corda, objeto. Não são permitidas matrizes.

    * Os inteiros podem ter um valor mínimo de -4503599627370496 e um valor máximo de 4503599627370495.

    * Os valores das cordas são uTF-8 codificados e podem ter um comprimento máximo de 4 KB.

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

## <a name="device-twin-size"></a>Tamanho gémeo do dispositivo

O IoT Hub impõe um limite de `tags`tamanho de 8 KB no valor de `properties/desired` `properties/reported`, e um limite de tamanho de 32 KB cada um no valor de e . Estes totais são exclusivos de elementos `$version`só `$metadata/$lastUpdated`de leitura como `$etag`, e .

O tamanho gémeo é calculado da seguinte forma:

* Para cada imóvel no documento JSON, o IoT Hub calcula cumulativamente e adiciona o comprimento da chave e valor da propriedade.

* As chaves de propriedade são consideradas como cordas codificadas pelo UTF8.

* Os valores de propriedade simples são considerados como cordas codificadas por UTF8, valores numéricos (8 Bytes) ou valores booleanos (4 Bytes).

* O tamanho das cordas codificadas uTF8 é calculado contando todos os caracteres, excluindo caracteres de controlo UNICODE (segmentos C0 e C1).

* Os valores de propriedade complexos (objetos aninhados) são calculados com base no tamanho agregado das chaves de propriedade e valores de propriedade que contêm.

O IoT Hub rejeita com um erro todas `tags` `properties/desired`as `properties/reported` operações que aumentariam a dimensão do , ou documentos acima do limite.

## <a name="device-twin-metadata"></a>Metadados gémeos do dispositivo

O IoT Hub mantém o carimbo de tempo da última atualização para cada objeto JSON em propriedades desejadas e reportadas pelo dispositivo. Os carimbos temporais estão em UTC e codificados no formato `YYYY-MM-DDTHH:MM:SS.mmmZ` [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) .

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

As propriedades desejadas e reportadas não têm `$version` ETags, mas têm um valor que é garantido ser incremental. Da mesma forma que um ETag, a versão pode ser usada pela parte de atualização para impor a consistência das atualizações. Por exemplo, um aplicativo de dispositivo para uma propriedade reportada ou a solução traseira para uma propriedade desejada.

As versões também são úteis quando um agente de observação (como a aplicação do dispositivo que observa as propriedades desejadas) deve conciliar as corridas entre o resultado de uma operação de recuperação e uma notificação de atualização. A secção de fluxo de [reconexão](iot-hub-devguide-device-twins.md#device-reconnection-flow) do Dispositivo fornece mais informações.

## <a name="device-reconnection-flow"></a>Fluxo de reconexão do dispositivo

O IoT Hub não preserva as notificações de atualização de propriedades desejadas para dispositivos desligados. Daí resulta que um dispositivo que está em ligação deve recuperar o documento de propriedades desejadas, além de subscrever notificações de atualização. Dada a possibilidade de corridas entre notificações atualizadas e recuperação total, deve ser assegurado o seguinte fluxo:

1. A aplicação do dispositivo liga-se a um hub IoT.
2. A aplicação do dispositivo subscreve as notificações de atualização de propriedades desejadas.
3. A aplicação do dispositivo recupera o documento completo para as propriedades desejadas.

A aplicação do dispositivo `$version` pode ignorar todas as notificações com menos ou igual do que a versão do documento recuperado completo. Esta abordagem é possível porque o IoT Hub garante que as versões sempre incrementam.

> [!NOTE]
> Esta lógica já está implementada nos [SDKs do dispositivo Azure IoT.](iot-hub-devguide-sdks.md) Esta descrição só é útil se a aplicação do dispositivo não puder utilizar nenhum dos SDKs do dispositivo Azure IoT e deve programar a interface MQTT diretamente.
> 

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* O artigo de [pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descreve os vários pontos finais que cada hub IoT expõe para operações de execução e gestão.

* O artigo [de Estrangulamento e quotas](iot-hub-devguide-quotas-throttling.md) descreve as quotas aplicáveis ao serviço IoT Hub e o comportamento de estrangulamento a esperar quando utilizar o serviço.

* O [artigo do Dispositivo E Serviço Azure IoT](iot-hub-devguide-sdks.md) lista os vários SDKs linguísticos que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* A linguagem de [consulta IoT Hub para gémeos de dispositivos, empregos e](iot-hub-devguide-query-language.md) artigo de encaminhamento de mensagens descreve a linguagem de consulta IoT Hub que você pode usar para obter informações do IoT Hub sobre os seus gémeos e empregos do seu dispositivo.

* O artigo de [suporte IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que já aprendeu sobre gémeos dispositivos, pode estar interessado nos seguintes tópicos de guia de desenvolvimento do IoT Hub:

* [Compreender e usar gémeos módulos no IoT Hub](iot-hub-devguide-module-twins.md)
* [Invoque um método direto num dispositivo](iot-hub-devguide-direct-methods.md)
* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais IoT Hub:

* [Como usar o dispositivo twin](iot-hub-node-node-twin-getstarted.md)
* [Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)
* [Gestão de dispositivos com as Ferramentas do Azure IoT para VS Code](iot-hub-device-management-iot-toolkit.md)
