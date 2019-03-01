---
title: Compreender duplos de dispositivo do IoT Hub do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – utilizar dispositivos duplos para sincronizar dados de configuração e o estado entre o IoT Hub e os seus dispositivos
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 4cbb8e389f403aeb149998acc21956ebce40be78
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011505"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Compreender e utilizar dispositivos duplos no IoT Hub

*Dispositivos duplos* são documentos JSON que armazenam informações de estado do dispositivo incluindo metadados, configurações e condições. O IoT Hub do Azure mantém um dispositivo duplo para cada dispositivo que ligar ao IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do dispositivo duplo: *etiquetas*, *pretendido* e *propriedades comunicadas*.
* As operações que aplicações de dispositivos e back-ends podem executar em dispositivos duplos.

Utilize dispositivos duplos para:

* Store metadados específicos do dispositivo na cloud. Por exemplo, a localização de implementação de uma máquina de venda automática.

* Comunicar informações de estado atuais, como recursos disponíveis e condições da sua aplicação de dispositivo. Por exemplo, um dispositivo está ligado ao seu IoT hub via rede móvel ou Wi-Fi.

* Sincronize o estado dos fluxos de trabalho de longa duração entre a aplicação de dispositivo e aplicação de back-end. Por exemplo, quando uma solução final Especifica a nova versão de firmware para instalar e a aplicação de dispositivo comunica os diversos estágios do processo de atualização.

* Consulte os metadados do dispositivo, a configuração ou o estado.

Consulte a [documentação de orientação do dispositivo-para-cloud comunicação](iot-hub-devguide-d2c-guidance.md) para obter orientações sobre como utilizar propriedades comunicadas, mensagens do dispositivo para a cloud ou carregamento de ficheiros.

Consulte a [documentação de orientação de comunicação de Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md) para obter orientações sobre como utilizar as propriedades pretendidas, métodos diretos ou mensagens na cloud para o dispositivo.

## <a name="device-twins"></a>Dispositivos duplos

Dispositivos duplos armazenam informações relacionadas com o dispositivo que:

* As extremidades de dispositivo e o back podem utilizar para sincronizar as condições de dispositivo e a configuração.

* A solução de back-end pode utilizar para consulta e o destino de execução longa operações.

O ciclo de vida de um dispositivo duplo está ligado a correspondente [identidade de dispositivo](iot-hub-devguide-identity-registry.md). Dispositivos duplos são implicitamente criados e eliminados quando uma identidade de dispositivo é criada ou eliminada no IoT Hub.

Um dispositivo duplo é um documento JSON que inclui:

* **Tags**. Uma secção do documento JSON que o back-end de solução pode ler e escrever. As etiquetas não estão visíveis para aplicações de dispositivos.

* **Propriedades pretendidas**. Utilizados em conjunto com as propriedades comunicadas para sincronizar a configuração do dispositivo ou condições. O back-end de solução pode definir as propriedades pretendidas e, a aplicação de dispositivo pode lê-los. A aplicação de dispositivo também pode receber notificações de alterações nas propriedades pretendidas.

* **Propriedades comunicadas**. Utilizados em conjunto com as propriedades pretendidas para sincronizar a configuração do dispositivo ou condições. A aplicação de dispositivo, pode definir as propriedades comunicadas e o back-end de solução pode ler e consultá-los.

* **Propriedades de identidade de dispositivo**. A raiz do documento JSON twin do dispositivo contém as propriedades só de leitura desde a identidade de dispositivo correspondente armazenada no [registo de identidade](iot-hub-devguide-identity-registry.md).

![Captura de ecrã das propriedades de twin do dispositivo](./media/iot-hub-devguide-device-twins/twin.png)

O exemplo seguinte mostra um documento JSON do dispositivo duplo:

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

No objeto raiz é o dispositivo, propriedades de identidade e objetos de contentor para `tags` e ambos `reported` e `desired` propriedades. O `properties` contentor contém alguns elementos de só de leitura (`$metadata`, `$etag`, e `$version`) descrito o [metadados do dispositivo duplo](iot-hub-devguide-device-twins.md#device-twin-metadata) e [a simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) seções.

### <a name="reported-property-example"></a>Exemplo de propriedades comunicadas

No exemplo anterior, o dispositivo duplo contém um `batteryLevel` propriedade que é comunicada pela aplicação de dispositivo. Esta propriedade torna possível consultar e operar em dispositivos com base no último nível da bateria comunicadas. Outros exemplos incluem as opções de conectividade ou funcionalidades de dispositivos de geração de relatórios de aplicação de dispositivo.

> [!NOTE]
> Propriedades comunicadas simplificam cenários em que a solução de back-end está interessada no último valor conhecido de uma propriedade. Uso [mensagens dispositivo-para-cloud](iot-hub-devguide-messages-d2c.md) se a solução de back-end tem de processar a telemetria do dispositivo na forma de sequências de eventos de timestamped, como a série de tempo.

### <a name="desired-property-example"></a>Exemplo de propriedade pretendida

No exemplo anterior, o `telemetryConfig` desejado do dispositivo duplo e propriedades comunicadas são utilizadas pela solução de back-end e a aplicação de dispositivo para sincronizar a configuração de telemetria para este dispositivo. Por exemplo:

1. A solução de back-end define a propriedade pretendida com o valor de configuração pretendida. Esta é a parte do documento com o conjunto de propriedade pretendida:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. A aplicação de dispositivo é notificada da alteração imediatamente se ligado, ou o restabelecimento de ligação primeiro. A aplicação de dispositivo, em seguida, reporta a configuração atualizada (ou uma condição de erro usando o `status` propriedade). Esta é a parte das propriedades comunicadas:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. A solução de back-end pode controlar os resultados da operação de configuração em vários dispositivos por [consultar](iot-hub-devguide-query-language.md) dispositivos duplos.

> [!NOTE]
> Os trechos de código anteriores são exemplos, otimizados para facilitar a leitura, de uma forma de codificar uma configuração de dispositivo e o respetivo estado. IoT Hub não impõem um esquema específico para o dispositivo duplo desejado e propriedades nos dispositivos duplos comunicadas.
> 

Pode utilizar o gémeos para sincronizar as operações de longa execução, tais como atualizações de firmware. Para obter mais informações sobre como utilizar propriedades para sincronizar e controlar uma operação de longa execução em vários dispositivos, consulte [utilização pretendida propriedades para configurar dispositivos](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Operações de back-end

O back-end de solução funciona no dispositivo duplo usando as seguintes operações atômicas, expostas através de HTTPS:

* **Obter o dispositivo duplo por ID de**. Esta operação devolve o documento de twin do dispositivo, incluindo etiquetas e propriedades do sistema pretendido e informado.

* **Atualizar o dispositivo duplo parcialmente**. Esta operação permite que a solução de back-end parcialmente atualizar as etiquetas ou as propriedades pretendidas num dispositivo duplo. A atualização parcial é expresso como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades definidas para `null` são removidos. O exemplo seguinte cria uma nova propriedade pretendida com o valor `{"newProperty": "newValue"}`, substitui o valor `existingProperty` com `"otherNewValue"`e remove `otherOldProperty`. Não existem outras alterações são feitas para as propriedades pretendidas existentes ou etiquetas:

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

* **Receber notificações de duplo**. Esta operação permite que a solução de back-end ser notificado quando o duplo é modificado. Para fazer isso, sua solução de IoT precisa de criar uma rota e definir a origem de dados igual ao *twinChangeEvents*. Por predefinição, nenhuma dessas rotas existirem previamente, para que não existem notificações de duplo são enviadas. Se a taxa de alteração é demasiado elevada, ou por outros motivos, como falhas internos, o IoT Hub pode enviar apenas uma notificação que contém todas as alterações. Portanto, se seu aplicativo precisar fiável de auditoria e registo de todos os Estados intermediários, deve usar mensagens dispositivo-para-cloud. A mensagem de notificação de duplo inclui propriedades e o corpo.

   - Propriedades

   | Name | Value |
   | --- | --- |
   $content-type | application/json |
   $iothub-enqueuedtime |  Tempo quando a notificação foi enviada |
   $iothub-message-source | twinChangeEvents |
   $content-encoding | utf-8 |
   deviceId | ID do dispositivo |
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

Suportam a todas as operações anteriores [a simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e requerem a **ServiceConnect** permissão, conforme definido na [controlar o acesso ao IoT Hub](iot-hub-devguide-security.md).

Além destas operações, o back-end de solução pode:

* Consultar os dispositivos duplos com o tipo de SQL [linguagem de consulta do IoT Hub](iot-hub-devguide-query-language.md).

* Efetuar operações em grandes conjuntos de dispositivos duplos com [tarefas](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operações de dispositivo

A aplicação de dispositivo funciona no dispositivo duplo usando as seguintes operações atômicas:

* **Obter o dispositivo duplo**. Esta operação devolve o documento de twin de dispositivos (incluindo etiquetas e propriedades do sistema pretendido e comunicadas) para o dispositivo atualmente ligado.

* **Parcialmente atualizar propriedades comunicadas**. Esta operação permite que a atualização parcial das propriedades comunicadas do dispositivo atualmente conectado. Esta operação utiliza o mesmo formato de atualização JSON que a solução de cópia de utilizações de final de uma atualização parcial de propriedades pretendidas.

* **Observe as propriedades pretendidas**. O dispositivo atualmente conectado pode optar por ser notificado das atualizações para as propriedades pretendidas quando eles ocorrem. O dispositivo recebe o mesmo formulário de atualização (substituição total ou parcial), executado pela solução de back-end.

Todas as operações anteriores exigirem o **DeviceConnect** permissão, conforme definido na [controlar o acesso ao IoT Hub](iot-hub-devguide-security.md).

O [do Azure IoT device SDKs](iot-hub-devguide-sdks.md) facilitam a utilizar operações anteriores de vários idiomas e plataformas. Para obter mais informações sobre os detalhes dos primitivos de IoT Hub para a sincronização de propriedades pretendidas, consulte [fluxo de restabelecimento de ligação do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Formato de etiquetas e propriedades

As etiquetas, as propriedades pretendidas e propriedades comunicadas são objetos JSON, com as seguintes restrições:

* Todas as chaves em objetos JSON são maiúsculas e minúsculas 64 bytes cadeias de caracteres UNICODE UTF-8. Permitido carateres excluir carateres de controlo do UNICODE (segmentos C0 e C1), e `.`, `$`e SP.

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

## <a name="device-twin-size"></a>Tamanho do dispositivo duplo

IoT Hub impõe um limite de tamanho de 8KB em cada um dos respetivos valores totais de `tags`, `properties/desired`, e `properties/reported`, excluindo os elementos somente leitura.

O tamanho é computado com contagem de todos os carateres, excluindo os caracteres de controle UNICODE (segmentos C0 e C1) e espaços que estejam fora constantes de cadeia de caracteres.

IoT Hub com um erro rejeita todas as operações que aumentará o tamanho de um desses documentos acima do limite.

## <a name="device-twin-metadata"></a>Metadados do dispositivo duplo

O IoT Hub mantém o carimbo de hora da última atualização para cada objeto JSON no dispositivo duplo desejado e propriedades comunicadas. Os carimbos de hora são em formato UTC e codificado no [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) formato `YYYY-MM-DDTHH:MM:SS.mmmZ`.

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

Dispositivo duplo desejado e propriedades comunicadas não têm de ETags, mas tem um `$version` valor que é garantido que estará incremental. Da mesma forma para uma ETag, pode ser utilizada a versão pela atualização parte para impor a consistência das atualizações. Por exemplo, uma aplicação de dispositivo para uma propriedade comunicada ou a solução de back-end para uma propriedade pretendida.

Versões também são úteis quando um agente observing (por exemplo, a aplicação de dispositivo observar as propriedades pretendidas) tem de reconciliar corridas entre o resultado de uma operação de obtenção e uma notificação de atualização. O [secção de fluxo de restabelecimento de ligação do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) fornece mais informações.

## <a name="device-reconnection-flow"></a>Fluxo de restabelecimento de ligação do dispositivo

IoT Hub não preserva a notificações de atualização de propriedades pretendidas para os dispositivos desligados. Ela segue que um dispositivo que está a ligar tem de obter o documento completo de propriedades pretendidas, além de subscrever notificações de atualização de. Dada a possibilidade de corridas entre notificações de atualização e de recuperação completa, deve ser garantido o fluxo seguinte:

1. Aplicação de dispositivo liga-se para um hub IoT.
2. Aplicação de dispositivo se inscreve para as propriedades pretendidas notificações de atualização.
3. Aplicação de dispositivo obtém o documento completo para as propriedades pretendidas.

A aplicação de dispositivo, pode ignorar todas as notificações com `$version` inferior ou igual à versão do documento obtido completo. Essa abordagem é possível porque o IoT Hub garante que sempre incrementar versões.

> [!NOTE]
> Essa lógica já está implementada no [do Azure IoT device SDKs](iot-hub-devguide-sdks.md). Esta descrição é útil apenas se a aplicação de dispositivo não é possível utilizar qualquer um dos SDKs de dispositivo do IoT do Azure e tem a programar diretamente a interface MQTT.
> 

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador do IoT Hub incluem:

* O [pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) artigo descreve os vários pontos de extremidade que cada hub IoT expõe para operações de tempo de execução e gestão.

* O [quotas e limitação](iot-hub-devguide-quotas-throttling.md) artigo descreve as quotas que se aplicam ao serviço IoT Hub e o comportamento da limitação esperar ao utilizar o serviço.

* O [SDKs de dispositivo e de serviços do Azure IoT](iot-hub-devguide-sdks.md) artigo lista o idioma de vários SDKs, pode utilizar ao desenvolver aplicações de dispositivos e de serviços que interagem com o IoT Hub.

* O [linguagem de consulta do IoT Hub para dispositivos duplos, tarefas e encaminhamento de mensagens](iot-hub-devguide-query-language.md) artigo descreve a linguagem de consulta do IoT Hub pode utilizar para recuperar informações a partir do IoT Hub sobre os seus dispositivos duplos e trabalhos.

* O [suporte para MQTT do IoT Hub](iot-hub-mqtt-support.md) artigo fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu sobre dispositivos duplos, poderá estar interessado nos seguintes tópicos de guia de programadores de IoT Hub:

* [Compreender e utilizar duplos de módulo no IoT Hub](iot-hub-devguide-module-twins.md)
* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md)
* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do IoT Hub:

* [Como utilizar o dispositivo duplo](iot-hub-node-node-twin-getstarted.md)
* [Como utilizar propriedades dos dispositivos duplos](tutorial-device-twins.md)
* [Gestão de dispositivos com ferramentas de IoT do Azure para VS Code](iot-hub-device-management-iot-toolkit.md)
