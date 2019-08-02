---
title: Entender o dispositivo gêmeos do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – usar dispositivos gêmeos para sincronizar dados de estado e de configuração entre o Hub IoT e seus dispositivos
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: f4db353e3c2f625478df6a547d1b67c5d074d18a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640627"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Entender e usar dispositivos gêmeos no Hub IoT

*Dispositivos gêmeos* são documentos JSON que armazenam informações de estado do dispositivo, incluindo metadados, configurações e condições. O Hub IoT do Azure mantém um dispositivo "r" para cada dispositivo que você conecta ao Hub IoT. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do dispositivo: *marcas*, *Propriedades*desejadas e relatadas.
* As operações que os aplicativos de dispositivo e back-ends podem executar no dispositivo gêmeos.

Use dispositivos gêmeos para:

* Armazene metadados específicos do dispositivo na nuvem. Por exemplo, o local de implantação de uma máquina de venda.

* Relatar informações de estado atual, como recursos disponíveis e condições do seu aplicativo de dispositivo. Por exemplo, um dispositivo está conectado ao seu hub IoT por celular ou Wi-Fi.

* Sincronize o estado dos fluxos de trabalho de longa execução entre o aplicativo de dispositivo e o aplicativo de back-end. Por exemplo, quando o back-end da solução especifica a nova versão do firmware a ser instalada, e o aplicativo do dispositivo relata os vários estágios do processo de atualização.

* Consulte os metadados, a configuração ou o estado do dispositivo.

Consulte as [diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) para obter orientação sobre como usar Propriedades relatadas, mensagens do dispositivo para a nuvem ou carregamento de arquivo.

Consulte as [diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md) para obter orientação sobre como usar as propriedades desejadas, métodos diretos ou mensagens da nuvem para o dispositivo.

## <a name="device-twins"></a>Dispositivo gêmeos

Dispositivo gêmeos armazene informações relacionadas ao dispositivo que:

* O dispositivo e back-ends podem usar para sincronizar as condições do dispositivo e a configuração.

* O back-end da solução pode usar para consultar e direcionar operações de longa execução.

O ciclo de vida de um dispositivo é vinculado à [identidade do dispositivo](iot-hub-devguide-identity-registry.md)correspondente. Dispositivos gêmeos são criados e excluídos implicitamente quando uma identidade de dispositivo é criada ou excluída no Hub IoT.

Um dispositivo "r" é um documento JSON que inclui:

* **Marcações**. Uma seção do documento JSON que o back-end da solução pode ler e gravar. As marcas não são visíveis para os aplicativos do dispositivo.

* **Propriedades**desejadas. Usado junto com as propriedades relatadas para sincronizar a configuração ou as condições do dispositivo. O back-end da solução pode definir as propriedades desejadas e o aplicativo do dispositivo pode lê-las. O aplicativo do dispositivo também pode receber notificações de alterações nas propriedades desejadas.

* **Propriedades**relatadas. Usado junto com as propriedades desejadas para sincronizar a configuração ou as condições do dispositivo. O aplicativo do dispositivo pode definir propriedades relatadas e o back-end da solução pode lê-las e consultá-las.

* **Propriedades de identidade do dispositivo**. A raiz do documento JSON de dispositivo de alta disponibilidade contém as propriedades somente leitura da identidade do dispositivo correspondente armazenada no [registro de identidade](iot-hub-devguide-identity-registry.md).

![Captura de tela das propriedades de dispositivo.](./media/iot-hub-devguide-device-twins/twin.png)

O exemplo a seguir mostra um documento JSON de dispositivo de entrelaçamento:

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

No objeto raiz estão as propriedades de identidade do dispositivo e os objetos de `tags` contêiner para `reported` e `desired` as propriedades e. O `properties` contêiner contém alguns elementos somente leitura (`$metadata`, `$etag`e `$version`) descritos nos [metadados do dispositivo](iot-hub-devguide-device-twins.md#device-twin-metadata) e nas seções de [simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) .

### <a name="reported-property-example"></a>Exemplo da propriedade relatada

No exemplo anterior, o dispositivo "r" contém `batteryLevel` uma propriedade que é relatada pelo aplicativo do dispositivo. Essa propriedade possibilita consultar e operar em dispositivos com base no último nível de bateria relatado. Outros exemplos incluem os recursos de dispositivo de relatório de aplicativo do dispositivo ou as opções de conectividade.

> [!NOTE]
> As propriedades relatadas simplificam os cenários em que o back-end da solução está interessado no último valor conhecido de uma propriedade. Use [mensagens do dispositivo para a nuvem](iot-hub-devguide-messages-d2c.md) se o back-end da solução precisar processar a telemetria do dispositivo na forma de sequências de eventos com carimbo de data/hora, como uma série temporal.

### <a name="desired-property-example"></a>Exemplo da propriedade desejada

No exemplo anterior, as propriedades `telemetryConfig` relatadas e reportadas do dispositivo é usada pelo back-end da solução e o aplicativo do dispositivo para sincronizar a configuração de telemetria para este dispositivo. Por exemplo:

1. O back-end da solução define a propriedade desejada com o valor de configuração desejado. Aqui está a parte do documento com o conjunto de propriedades desejado:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. O aplicativo do dispositivo é notificado sobre a alteração imediatamente se conectado ou na primeira reconexão. Em seguida, o aplicativo de dispositivo relata a configuração atualizada (ou uma condição `status` de erro usando a propriedade). Aqui está a parte das propriedades relatadas:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. O back-end da solução pode acompanhar os resultados da operação de configuração em vários [](iot-hub-devguide-query-language.md) dispositivos consultando dispositivos gêmeos.

> [!NOTE]
> Os trechos de código anteriores são exemplos, otimizados para facilitar a leitura, de uma maneira de codificar uma configuração de dispositivo e seu status. O Hub IoT não impõe um esquema específico para as propriedades desejadas do dispositivo e relatadas no dispositivo gêmeos.
> 

Você pode usar o gêmeos para sincronizar operações de longa execução, como atualizações de firmware. Para obter mais informações sobre como usar propriedades para sincronizar e acompanhar uma operação de execução longa em dispositivos, consulte [usar as propriedades desejadas para configurar dispositivos](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Operações de back-end

O back-end da solução opera no dispositivo usando as seguintes operações atômicas, expostas por meio de HTTPS:

* **Recuperar o dispositivo de entrelaçamento por ID**. Esta operação retorna o documento de dispositivo de entrelaçamento, incluindo marcas e propriedades do sistema desejadas e reportadas.

* **Atualização parcial do dispositivo**. Essa operação permite que o back-end da solução atualize parcialmente as marcas ou as propriedades desejadas em um dispositivo. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades definidas `null` como são removidas. O exemplo a seguir cria uma nova propriedade desejada com `{"newProperty": "newValue"}`valor, substitui o valor existente de `existingProperty` por `"otherNewValue"`e remove `otherOldProperty`. Nenhuma outra alteração é feita nas propriedades ou marcas desejadas existentes:

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

* **Substituir as propriedades**desejadas. Essa operação permite que o back-end da solução substitua completamente todas as propriedades desejadas existentes e substitua `properties/desired`um novo documento JSON por.

* **Substituir marcas**. Essa operação permite que o back-end da solução substitua completamente todas as marcas existentes e substitua um novo `tags`documento JSON por.

* **Receber notificações**de entrelaçamento. Esta operação permite que o back-end da solução seja notificado quando a cópia de cópia for modificada. Para fazer isso, sua solução de IoT precisa criar uma rota e definir a fonte de dados igual a *twinChangeEvents*. Por padrão, essas rotas não existem previamente, portanto, nenhuma notificação de entrelaçamento é enviada. Se a taxa de alteração for muito alta, ou por outros motivos, como falhas internas, o Hub IoT poderá enviar apenas uma notificação que contenha todas as alterações. Portanto, se seu aplicativo precisar de auditoria e log confiáveis de todos os Estados intermediários, você deverá usar mensagens do dispositivo para a nuvem. A mensagem de notificação de entrelaçamento inclui propriedades e corpo.

  - properties

    | Nome | Value |
    | --- | --- |
    tipo de $content | application/json |
    $iothub-enqueuedtime |  Hora em que a notificação foi enviada |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID do dispositivo |
    hubName | Nome do Hub IoT |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) carimbo de data/hora da operação |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" ou "updateTwin" |

    As propriedades do sistema de mensagens são prefixadas com o `$` símbolo.

  - Body
        
    Esta seção inclui todas as alterações de entrelaçamento em um formato JSON. Ele usa o mesmo formato que um patch, com a diferença de que ele pode conter todas as seções de myup: Tags, Properties. reported, Properties. Desired e que ele contém os elementos "$metadata". Por exemplo,

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

Todas as operações anteriores dão suporte à [simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e exigem a permissão de perconnect, conforme definido em [controlar o acesso ao Hub IOT](iot-hub-devguide-security.md).

Além dessas operações, o back-end da solução pode:

* Consulte o dispositivo gêmeos usando a [linguagem de consulta do Hub IOT](iot-hub-devguide-query-language.md)semelhante ao SQL.

* Executar operações em grandes conjuntos de dispositivos gêmeos usando [trabalhos](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operações do dispositivo

O aplicativo do dispositivo opera no dispositivo usando as seguintes operações atômicas:

* **Recuperar o dispositivo**. Esta operação retorna o documento de dispositivo de documentos (incluindo as propriedades do sistema desejadas e reportadas) para o dispositivo conectado no momento. (As marcas não são visíveis para os aplicativos do dispositivo.)

* **Atualizar parcialmente as propriedades**relatadas. Essa operação habilita a atualização parcial das propriedades relatadas do dispositivo conectado no momento. Esta operação usa o mesmo formato de atualização JSON usado pelo back-end da solução para uma atualização parcial das propriedades desejadas.

* **Observe as propriedades**desejadas. O dispositivo conectado no momento pode optar por ser notificado das atualizações para as propriedades desejadas quando elas ocorrerem. O dispositivo recebe a mesma forma de atualização (substituição parcial ou completa) executada pelo back-end da solução.

Todas as operações anteriores exigem a permissão **DeviceConnect** , conforme definido em [controlar o acesso ao Hub IOT](iot-hub-devguide-security.md).

Os [SDKs do dispositivo IOT do Azure](iot-hub-devguide-sdks.md) facilitam o uso das operações anteriores de várias linguagens e plataformas. Para obter mais informações sobre os detalhes dos primitivos do Hub IoT para sincronização de propriedades desejadas, consulte fluxo de reconexão do [dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Formato de marcas e propriedades

Marcas, propriedades desejadas e propriedades relatadas são objetos JSON com as seguintes restrições:

* Todas as chaves em objetos JSON diferenciam maiúsculas de minúsculas 64 bytes de cadeias de caracteres UNICODE UTF-8. Os caracteres permitidos excluem caracteres de controle Unicode (segmentos C0 e C1 `.`) `$`, e, e SP.

* Todos os valores em objetos JSON podem ser dos seguintes tipos JSON: booliano, número, Cadeia de caracteres, objeto. Não são permitidas matrizes. O valor máximo de inteiros é 4503599627370495 e o valor mínimo para inteiros é-4503599627370496.

* Todos os objetos JSON em marcas, propriedades desejadas e relatadas podem ter uma profundidade máxima de 5. Por exemplo, o seguinte objeto é válido:

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

* Todos os valores de cadeia de caracteres podem ter no máximo 512 bytes de comprimento.

## <a name="device-twin-size"></a>Tamanho do dispositivo

O Hub IOT impõe uma limitação de tamanho de 8 KB em cada um dos respectivos valores `tags`totais de `properties/reported`, `properties/desired`e, excluindo elementos somente leitura.

O tamanho é calculado pela contagem de todos os caracteres, excluindo caracteres de controle UNICODE (segmentos C0 e C1) e espaços que estão fora das constantes de cadeia de caracteres.

O Hub IoT rejeita com um erro todas as operações que aumentariam o tamanho desses documentos acima do limite.

## <a name="device-twin-metadata"></a>Metadados de entrelaçamento do dispositivo

O Hub IoT mantém o carimbo de data/hora da última atualização para cada objeto JSON nas propriedades relatadas e desejadas do dispositivo. Os carimbos de data/hora estão em UTC e [](https://en.wikipedia.org/wiki/ISO_8601) são codificados no formato `YYYY-MM-DDTHH:MM:SS.mmmZ`ISO8601.

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

Essas informações são mantidas em todos os níveis (não apenas nas folhas da estrutura JSON) para preservar as atualizações que removem as chaves de objeto.

## <a name="optimistic-concurrency"></a>Simultaneidade otimista

As propriedades de marcas, desejadas e relatadas oferecem suporte à simultaneidade otimista.
As marcas têm uma ETag, de acordo com a [RFC7232](https://tools.ietf.org/html/rfc7232), que representa a representação JSON da marca. Você pode usar ETags em operações de atualização condicionais do back-end da solução para garantir a consistência.

As propriedades desejadas e relatadas do dispositivo ' r ' não `$version` têm ETags, mas têm um valor que é garantido para ser incremental. Da mesma forma que uma ETag, a versão pode ser usada pela parte de atualização para impor a consistência das atualizações. Por exemplo, um aplicativo de dispositivo para uma propriedade relatada ou o back-end da solução para uma propriedade desejada.

As versões também são úteis quando um agente de observação (como o aplicativo de dispositivo que observa as propriedades desejadas) deve reconciliar corridas entre o resultado de uma operação de recuperação e uma notificação de atualização. A [seção fluxo](iot-hub-devguide-device-twins.md#device-reconnection-flow) de reconexão do dispositivo fornece mais informações.

## <a name="device-reconnection-flow"></a>Fluxo de reconexão do dispositivo

O Hub IoT não preserva as propriedades desejadas notificações de atualização para dispositivos desconectados. Ele segue que um dispositivo que está se conectando deve recuperar o documento de propriedades desejadas completo, além de assinar notificações de atualização. Devido à possibilidade de corridas entre as notificações de atualização e a recuperação completa, o seguinte fluxo deve ser garantido:

1. O aplicativo do dispositivo se conecta a um hub IoT.
2. O aplicativo de dispositivo assina as notificações de atualização das propriedades desejadas.
3. O aplicativo do dispositivo recupera o documento completo para as propriedades desejadas.

O aplicativo do dispositivo pode ignorar todas as `$version` notificações com menor ou igual à versão do documento recuperado completo. Essa abordagem é possível porque o Hub IoT garante que as versões sempre incrementam.

> [!NOTE]
> Essa lógica já está implementada nos [SDKs do dispositivo IOT do Azure](iot-hub-devguide-sdks.md). Essa descrição será útil somente se o aplicativo do dispositivo não puder usar nenhum dos SDKs do dispositivo IoT do Azure e precisar programar a interface MQTT diretamente.
> 

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia do desenvolvedor do Hub IoT incluem:

* O artigo [pontos de extremidade do Hub IOT](iot-hub-devguide-endpoints.md) descreve os vários pontos de extremidade que cada Hub IOT expõe para operações de tempo de execução e de gerenciamento.

* O artigo [limitação e cotas](iot-hub-devguide-quotas-throttling.md) descreve as cotas que se aplicam ao serviço Hub IOT e o comportamento de limitação esperado ao usar o serviço.

* O artigo [SDKs do dispositivo e do serviço do Azure IOT](iot-hub-devguide-sdks.md) lista os diversos SDKs de linguagem que você pode usar ao desenvolver aplicativos de dispositivo e de serviço que interagem com o Hub IOT.

* O artigo [linguagem de consulta do Hub IOT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta do Hub IOT que você pode usar para recuperar informações do Hub IOT sobre seu dispositivo gêmeos e trabalhos.

* O artigo de [suporte do MQTT Hub IOT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do Hub IOT para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre dispositivos gêmeos, talvez esteja interessado nos seguintes tópicos do guia do desenvolvedor do Hub IoT:

* [Entender e usar o gêmeos do módulo no Hub IoT](iot-hub-devguide-module-twins.md)
* [Invocar um método direto em um dispositivo](iot-hub-devguide-direct-methods.md)
* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do Hub IoT:

* [Como usar o dispositivo.](iot-hub-node-node-twin-getstarted.md)
* [Como usar as propriedades de dispositivo.](tutorial-device-twins.md)
* [Gerenciamento de dispositivos com as ferramentas de IoT do Azure para VS Code](iot-hub-device-management-iot-toolkit.md)
