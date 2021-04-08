---
title: Consulta sobre o encaminhamento de mensagens Azure IoT Hub | Microsoft Docs
description: Conheça a linguagem de encaminhamento de mensagens IoT Hub que pode usar para aplicar consultas ricas em mensagens para receber os dados que lhe interessam.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 83c290adea02915db1dc52bd359b4d3165611522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92547712"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Sintaxe de consulta do encaminhamento de mensagens do Hub IoT

O encaminhamento de mensagens permite que os utilizadores encaminhem diferentes tipos de dados, nomeadamente, mensagens de telemetria do dispositivo, eventos de ciclo de vida do dispositivo e eventos de alteração de dois dispositivos para vários pontos finais. Também pode aplicar consultas ricas a estes dados antes de os encaminhar para receber os dados que lhe interessam. Este artigo descreve a linguagem de encaminhamento de mensagens IoT Hub e fornece alguns padrões de consulta comuns.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O encaminhamento de mensagens permite-lhe consultar as propriedades da mensagem e o corpo da mensagem, bem como as etiquetas gémeas do dispositivo e as propriedades gémeas do dispositivo. Se o corpo da mensagem não for JSON, o encaminhamento de mensagens ainda pode encaminhar a mensagem, mas as consultas não podem ser aplicadas ao corpo da mensagem.  As consultas são descritas como expressões booleanas onde um boolean verdadeiro faz a consulta ter sucesso que encaminha todos os dados de entrada, e Boolean falso falha a consulta e nenhum dado é encaminhado. Se a expressão avaliar a nulidade ou indefinida, é tratada como falsa e um erro será gerado nas rotas IoT Hub registos de [registos](monitor-iot-hub-reference.md#routes) em caso de falha. A sintaxe de consulta deve estar correta para que a rota seja guardada e avaliada.  

## <a name="message-routing-query-based-on-message-properties"></a>Consulta de encaminhamento de mensagens com base em propriedades de mensagens 

O IoT Hub define um [formato comum](iot-hub-devguide-messages-construct.md) para todas as mensagens dispositivo-a-nuvem para interoperabilidade através de protocolos. A mensagem IoT Hub pressupõe a seguinte representação JSON da mensagem. As propriedades do sistema são adicionadas para todos os utilizadores e identificam o conteúdo da mensagem. Os utilizadores podem adicionar seletivamente propriedades de aplicação à mensagem. Recomendamos que o uso de nomes de propriedade únicos, uma vez que as mensagens IoT Hub para nuvem não são sensíveis a casos. Por exemplo, se tiver várias propriedades com o mesmo nome, o IoT Hub enviará apenas uma das propriedades.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Propriedades do sistema

As propriedades do sistema ajudam a identificar o conteúdo e a origem das mensagens. 

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| conteúdoType | string | O utilizador especifica o tipo de conteúdo da mensagem. Para permitir a consulta no corpo da mensagem, este valor deve ser definido aplicação/JSON. |
| contentEncoding | string | O utilizador especifica o tipo de codificação da mensagem. Os valores permitidos são UTF-8, UTF-16, UTF-32 se o conteúdoType estiver definido para aplicação/JSON. |
| iothub-connection-device-id | string | Este valor é definido pelo IoT Hub e identifica o ID do dispositivo. Para consultar, use `$connectionDeviceId` . |
| iothub-enqueuedtime | string | Este valor é definido pelo IoT Hub e representa o tempo real de mensagem na UTC. Para consultar, use `enqueuedTime` . |
| dt-dataschema | string |  Este valor é definido pelo hub IoT em mensagens dispositivo-a-nuvem. Contém o iD do modelo do dispositivo na ligação do dispositivo. Para consultar, use `$dt-dataschema` . |
| dt-sujeito | string | O nome do componente que está a enviar as mensagens dispositivo-a-nuvem. Para consultar, use `$dt-subject` . |

Como descrito nas [Mensagens IoT Hub,](iot-hub-devguide-messages-construct.md)existem propriedades adicionais do sistema numa mensagem. Além das propriedades acima na tabela anterior, pode ainda consultar **a ligaçãoDeviceId,** **connectionModuleId**.

### <a name="application-properties"></a>Propriedades da aplicação

As propriedades da aplicação são cadeias definidas pelo utilizador que podem ser adicionadas à mensagem. Estes campos são opcionais.  

### <a name="query-expressions"></a>Expressões de consulta

Uma consulta sobre as propriedades do sistema de mensagens precisa de ser pré-fixado com o `$` símbolo. As consultas sobre as propriedades da aplicação são acedidas com o seu nome e não devem ser pré-fixados com o `$` símbolo. Se um nome de propriedade de aplicação começar com `$` , então IoT Hub irá procurá-lo nas propriedades do sistema, e não é encontrado, então irá olhar nas propriedades da aplicação. Por exemplo: 

Para consultar o conteúdo da propriedade do sistemaEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Para consultar o processamento de propriedade de aplicaçãoAta:

```sql
processingPath = 'hot'
```

Para combinar estas consultas, pode utilizar expressões e funções booleanas:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Uma lista completa dos operadores e funções apoiados é mostrada na Expressão e nas [condições](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Consulta de encaminhamento de mensagens com base no corpo da mensagem

Para permitir a consulta no corpo da mensagem, a mensagem deve estar codificada em UTF-8, UTF-16 ou UTF-32. O `contentType` deve ser definido para e para uma das `application/JSON` `contentEncoding` codificações UF suportadas na propriedade do sistema. Se estas propriedades não forem especificadas, o IoT Hub não avaliará a expressão de consulta no corpo da mensagem. 

O exemplo a seguir mostra como criar uma mensagem com um corpo JSON devidamente formado e codificado: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> Isto mostra como lidar com a codificação do corpo em javascript. Se quiser ver uma amostra em C#, descarregue as [amostras Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Desaperte o ficheiro master.zip. O ficheiro *Dedóvico* de .cs de Conceção do Estúdio Visual mostra como codificar e enviar mensagens para um Hub IoT. Esta é a mesma amostra utilizada para testar o encaminhamento de mensagens, como explicado no [tutorial de encaminhamento de mensagens](tutorial-routing.md). Na parte inferior do Programa.cs, também tem um método para ler num dos ficheiros codificados, descodificá-lo e escrevê-lo de volta como ASCII para que possa lê-lo. 


### <a name="query-expressions"></a>Expressões de consulta

Uma consulta no corpo da mensagem precisa de ser pré-fixado com o `$body` . Pode utilizar uma referência corporal, referência de matriz corporal ou múltiplas referências corporais na expressão de consulta. A sua expressão de consulta também pode combinar uma referência corporal com propriedades do sistema de mensagens e referência de propriedades de aplicação de mensagens. Por exemplo, todas são expressões de consulta válidas: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Consulta de encaminhamento de mensagens com base no twin do dispositivo 

O encaminhamento de mensagens permite-lhe consultar as etiquetas e propriedades [do Device Twin,](iot-hub-devguide-device-twins.md) que são objetos JSON. A consulta no módulo twin também é suportada. Uma amostra de etiquetas e propriedades do Dispositivo Twin é mostrada abaixo.

```JSON
{
    "tags": { 
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

### <a name="query-expressions"></a>Expressões de consulta

Uma consulta sobre a mensagem gémea precisa de ser pré-fixado com o `$twin` . A sua expressão de consulta também pode combinar uma referência de etiqueta dupla ou propriedade com uma referência corporal, propriedades do sistema de mensagens e referência de propriedades de aplicação de mensagens. Recomendamos a utilização de nomes únicos em tags e propriedades, uma vez que a consulta não é sensível a casos. Isto aplica-se tanto aos gémeos do dispositivo como aos gémeos módulos. Também se abstenha de `twin` `$twin` usar, `body` `$body` ou, como nomes de propriedade. Por exemplo, todas são expressões de consulta válidas: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

A consulta de encaminhamento no corpo ou no dispositivo twin com um período na carga útil ou no nome da propriedade não é suportada.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md).
* Experimente o [tutorial de encaminhamento de mensagens](tutorial-routing.md).
