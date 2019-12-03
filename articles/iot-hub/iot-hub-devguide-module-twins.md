---
title: Entender o módulo gêmeos do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor-Use o módulo gêmeos para sincronizar dados de estado e configuração entre o Hub IoT e seus dispositivos
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: b6ab1e3e01f66e071e3d16b196b3ecdcd30c2620
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701801"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Entender e usar o gêmeos do módulo no Hub IoT

Este artigo pressupõe que você tenha lido [entender e usar dispositivos gêmeos no Hub IOT](iot-hub-devguide-device-twins.md) primeiro. No Hub IoT, em cada identidade do dispositivo, você pode criar até 20 identidades de módulo. Cada identidade de módulo gera implicitamente um módulo. Semelhante ao dispositivo gêmeos, o módulo gêmeos são documentos JSON que armazenam informações de estado do módulo, incluindo metadados, configurações e condições. O Hub IoT do Azure mantém um módulo "r" para cada módulo que você conecta ao Hub IoT. 

No lado do dispositivo, os SDKs do dispositivo do Hub IoT permitem criar módulos em que cada um abre uma conexão independente com o Hub IoT. Essa funcionalidade permite que você use namespaces separados para diferentes componentes em seu dispositivo. Por exemplo, você tem uma máquina de venda que tem três sensores diferentes. Cada sensor é controlado por diferentes departamentos em sua empresa. Você pode criar um módulo para cada sensor. Dessa forma, cada departamento só é capaz de enviar trabalhos ou métodos diretos para o sensor que eles controlam, evitando conflitos e erros do usuário.

 A identidade do módulo e o módulo "r" fornecem os mesmos recursos que o dispositivo e a identidade do dispositivo, mas com uma granularidade mais fina. Essa granularidade mais fina permite que dispositivos compatíveis, como dispositivos baseados no sistema operacional ou dispositivos de firmware, gerenciem vários componentes, Isolem a configuração e as condições de cada um desses componentes. A identidade do módulo e o módulo gêmeos fornecem uma separação de gerenciamento de preocupações ao trabalhar com dispositivos IoT que têm componentes de software modulares. Nosso objetivo é dar suporte a todas as funcionalidades de dispositivos de dispositivo no módulo/nível/disponibilidade geral do módulo. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do módulo.: *marcas*, propriedades *desejadas* e *relatadas*.
* As operações que os módulos e back-ends podem executar no módulo gêmeos.

Consulte as [diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) para obter orientação sobre como usar Propriedades relatadas, mensagens do dispositivo para a nuvem ou carregamento de arquivo.

Consulte as [diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md) para obter orientação sobre como usar as propriedades desejadas, métodos diretos ou mensagens da nuvem para o dispositivo.

## <a name="module-twins"></a>Gêmeos do módulo

Módulo gêmeos armazene informações relacionadas ao módulo que:

* Os módulos no dispositivo e no Hub IoT podem usar o para sincronizar as condições do módulo e a configuração.

* O back-end da solução pode usar para consultar e direcionar operações de longa execução.

O ciclo de vida de um módulo "r" está vinculado à [identidade do módulo](iot-hub-devguide-identity-registry.md)correspondente. Os módulos gêmeos são criados e excluídos implicitamente quando uma identidade de módulo é criada ou excluída no Hub IoT.

Um módulo "r" é um documento JSON que inclui:

* **Marcações**. Uma seção do documento JSON que o back-end da solução pode ler e gravar. As marcas não são visíveis para os módulos no dispositivo. As marcas são definidas para fins de consulta.

* **Propriedades desejadas**. Usado junto com as propriedades relatadas para sincronizar a configuração ou as condições do módulo. O back-end da solução pode definir as propriedades desejadas e o aplicativo de módulo pode lê-las. O aplicativo de módulo também pode receber notificações de alterações nas propriedades desejadas.

* **Propriedades relatadas**. Usado junto com as propriedades desejadas para sincronizar a configuração ou as condições do módulo. O aplicativo de módulo pode definir propriedades relatadas e o back-end da solução pode lê-las e consultá-las.

* **Propriedades de identidade do módulo**. A raiz do documento JSON de módulo de entrelaçamento contém as propriedades somente leitura da identidade do módulo correspondente armazenada no [registro de identidade](iot-hub-devguide-identity-registry.md).

![Representação de arquitetura do dispositivo de entrelaçamento](./media/iot-hub-devguide-device-twins/module-twin.jpg)

O exemplo a seguir mostra um documento JSON de módulo "r":

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

No objeto raiz estão as propriedades de identidade do módulo e os objetos de contêiner para `tags` e as propriedades `reported` e `desired`. O contêiner de `properties` contém alguns elementos somente leitura (`$metadata`, `$etag`e `$version`) descritos nas seções metadados e de [simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) do [módulo](iot-hub-devguide-module-twins.md#module-twin-metadata) .

### <a name="reported-property-example"></a>Exemplo da propriedade relatada

No exemplo anterior, o módulo r contém uma propriedade `batteryLevel` que é relatada pelo aplicativo de módulo. Essa propriedade possibilita consultar e operar em módulos com base no último nível de bateria relatado. Outros exemplos incluem os recursos do módulo de relatório do aplicativo de módulo ou as opções de conectividade.

> [!NOTE]
> As propriedades relatadas simplificam os cenários em que o back-end da solução está interessado no último valor conhecido de uma propriedade. Use [mensagens do dispositivo para a nuvem](iot-hub-devguide-messages-d2c.md) se o back-end da solução precisar processar a telemetria do módulo na forma de sequências de eventos com carimbo de data/hora, como uma série temporal.

### <a name="desired-property-example"></a>Exemplo da propriedade desejada

No exemplo anterior, as propriedades desejadas e relatadas do módulo de `telemetryConfig` são usadas pelo back-end da solução e o aplicativo de módulo para sincronizar a configuração de telemetria para este módulo. Por exemplo:

1. O back-end da solução define a propriedade desejada com o valor de configuração desejado. Aqui está a parte do documento com o conjunto de propriedades desejado:

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

2. O aplicativo de módulo é notificado sobre a alteração imediatamente se conectado ou na primeira reconexão. Em seguida, o aplicativo de módulo relata a configuração atualizada (ou uma condição de erro usando a propriedade `status`). Aqui está a parte das propriedades relatadas:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. O back-end da solução pode acompanhar os resultados da operação de configuração em vários módulos, [consultando](iot-hub-devguide-query-language.md) o módulo gêmeos.

> [!NOTE]
> Os trechos de código anteriores são exemplos, otimizados para facilitar a leitura, de uma maneira de codificar uma configuração de módulo e seu status. O Hub IoT não impõe um esquema específico para o módulo... as propriedades desejadas e relatadas no módulo gêmeos.
> 
> 

## <a name="back-end-operations"></a>Operações de back-end
O back-end da solução opera no módulo "cópia" usando as seguintes operações atômicas, expostas por meio de HTTPS:

* Recupere o módulo "My" **por ID**. Esta operação retorna o documento de entrelaçamento do módulo, incluindo marcas e propriedades do sistema desejadas e reportadas.

* **Atualização parcial do módulo**. Essa operação permite que o back-end da solução atualize parcialmente as marcas ou as propriedades desejadas em um módulo de cópia. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades definidas como `null` são removidas. O exemplo a seguir cria uma nova propriedade desejada com o valor `{"newProperty": "newValue"}`, substitui o valor existente de `existingProperty` por `"otherNewValue"`e remove `otherOldProperty`. Nenhuma outra alteração é feita nas propriedades ou marcas desejadas existentes:

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

* **Substituir as propriedades desejadas**. Essa operação permite que o back-end da solução substitua completamente todas as propriedades desejadas existentes e substitua um novo documento JSON por `properties/desired`.

* **Substituir marcas**. Essa operação permite que o back-end da solução substitua completamente todas as marcas existentes e substitua um novo documento JSON por `tags`.

* **Receber notificações de entrelaçamento**. Esta operação permite que o back-end da solução seja notificado quando a cópia de cópia for modificada. Para fazer isso, sua solução de IoT precisa criar uma rota e definir a fonte de dados igual a *twinChangeEvents*. Por padrão, nenhuma notificação de entrelaçamento é enviada, ou seja, essas rotas não existem previamente. Se a taxa de alteração for muito alta, ou por outros motivos, como falhas internas, o Hub IoT poderá enviar apenas uma notificação que contenha todas as alterações. Portanto, se seu aplicativo precisar de auditoria e log confiáveis de todos os Estados intermediários, você deverá usar mensagens do dispositivo para a nuvem. A mensagem de notificação de entrelaçamento inclui propriedades e corpo.

  - Propriedades

    | Nome | Valor |
    | --- | --- |
    tipo de $content | application/json |
    $iothub-enqueuedtime |  Hora em que a notificação foi enviada |
    fonte de $iothub-mensagem | twinChangeEvents |
    codificação de $content | UTF-8 |
    deviceId | ID do dispositivo |
    moduleId | ID do módulo |
    hubName | Nome do Hub IoT |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) carimbo de data/hora da operação |
    iothub-mensagem-esquema | deviceLifecycleNotification |
    opType | "replaceTwin" ou "updateTwin" |

    As propriedades do sistema de mensagens são prefixadas com o símbolo de `$`.

  - Corpo
        
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

Todas as operações anteriores dão suporte à [simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e exigem a permissão de **perconnect** , conforme definido no artigo [controlar o acesso ao Hub IOT](iot-hub-devguide-security.md) .

Além dessas operações, o back-end da solução pode consultar o módulo gêmeos usando a [linguagem de consulta do Hub IOT](iot-hub-devguide-query-language.md)do tipo SQL.

## <a name="module-operations"></a>Operações de módulo

O aplicativo de módulo opera no módulo "/" usando as seguintes operações atômicas:

* **Recupere o módulo**. Esta operação retorna o documento do módulo "/" (incluindo marcas e propriedades de sistema desejadas e reportadas) para o módulo conectado no momento.

* **Atualizar parcialmente as propriedades relatadas**. Essa operação habilita a atualização parcial das propriedades relatadas do módulo atualmente conectado. Esta operação usa o mesmo formato de atualização JSON usado pelo back-end da solução para uma atualização parcial das propriedades desejadas.

* **Observe as propriedades desejadas**. O módulo conectado no momento pode optar por ser notificado sobre atualizações para as propriedades desejadas quando elas acontecem. O módulo recebe a mesma forma de atualização (substituição parcial ou completa) executada pelo back-end da solução.

Todas as operações anteriores exigem a permissão **ModuleConnect** , conforme definido no artigo [controlar o acesso ao Hub IOT](iot-hub-devguide-security.md) .

Os [SDKs do dispositivo IOT do Azure](iot-hub-devguide-sdks.md) facilitam o uso das operações anteriores de várias linguagens e plataformas.

## <a name="tags-and-properties-format"></a>Formato de marcas e propriedades

Marcas, propriedades desejadas e propriedades relatadas são objetos JSON com as seguintes restrições:

* Todas as chaves em objetos JSON diferenciam maiúsculas de minúsculas 64 bytes de cadeias de caracteres UNICODE UTF-8. Os caracteres permitidos excluem caracteres de controle UNICODE (segmentos C0 e C1) e `.`, SP e `$`.

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

## <a name="module-twin-size"></a>Tamanho do módulo/do

O Hub IoT impõe um limite de tamanho de 8 KB no valor de `tags`e um limite de tamanho de 32 KB, cada um com o valor de `properties/desired` e `properties/reported`. Esses totais são exclusivos de elementos somente leitura.

O tamanho é calculado pela contagem de todos os caracteres, excluindo caracteres de controle UNICODE (segmentos C0 e C1) e espaços que estão fora das constantes de cadeia de caracteres.

O Hub IoT rejeita com um erro todas as operações que aumentariam o tamanho desses documentos acima do limite.

## <a name="module-twin-metadata"></a>Metadados de módulo de entrelaçamento

O Hub IoT mantém o carimbo de data/hora da última atualização para cada objeto JSON no módulo e as propriedades relatadas desejadas. Os carimbos de data/hora estão em UTC e são codificados no formato [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

As propriedades desejadas e relatadas do módulo "r" não têm ETags, mas têm um valor `$version` que é garantido como incremental. Da mesma forma que uma ETag, a versão pode ser usada pela parte de atualização para impor a consistência das atualizações. Por exemplo, um aplicativo de módulo para uma propriedade relatada ou o back-end da solução para uma propriedade desejada.

As versões também são úteis quando um agente de observação (como o aplicativo de módulo que observa as propriedades desejadas) deve reconciliar corridas entre o resultado de uma operação de recuperação e uma notificação de atualização. A seção [fluxo de reconexão do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) fornece mais informações. 

## <a name="next-steps"></a>Passos seguintes

Para experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do Hub IoT:

* [Introdução à identidade do módulo do Hub IoT e ao módulo "cópia" usando o back-end do .NET e o dispositivo .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)
