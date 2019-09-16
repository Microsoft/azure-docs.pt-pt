---
title: Consulta no roteamento de mensagens do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – sintaxe de consulta para roteamento de mensagens no Hub IoT do Azure.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 7f6439d79e5d46621b92b1c24ba5caf87889f443
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877063"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Sintaxe de consulta de roteamento de mensagens do Hub IoT

O roteamento de mensagens permite aos usuários rotear diferentes tipos de dados, ou seja, mensagens de telemetria de dispositivo, eventos de ciclo de vida do dispositivo e eventos de alteração de dispositivos Você também pode aplicar consultas avançadas a esses dados antes de roteá-los para receber os dados que são importantes para você. Este artigo descreve a linguagem de consulta de roteamento de mensagens do Hub IoT e fornece alguns padrões de consulta comuns.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O roteamento de mensagens permite consultar as propriedades da mensagem e o corpo da mensagem, bem como as marcas de dispositivo e as propriedades do dispositivo. Se o corpo da mensagem não for JSON, o roteamento de mensagens ainda poderá rotear a mensagem, mas as consultas não poderão ser aplicadas ao corpo da mensagem.  As consultas são descritas como expressões booleanas em que um booliano true torna a consulta bem-sucedida, que roteia todos os dados de entrada, e booliano false falha na consulta e nenhum dado é roteado. Se a expressão for avaliada como nula ou indefinida, ela será tratada como false e um erro será gerado nos logs de diagnóstico em caso de falha. A sintaxe da consulta deve estar correta para que a rota seja salva e avaliada.  

## <a name="message-routing-query-based-on-message-properties"></a>Consulta de roteamento de mensagens com base nas propriedades da mensagem 

O Hub IoT define um [formato comum](iot-hub-devguide-messages-construct.md) para todas as mensagens do dispositivo para a nuvem para interoperabilidade entre protocolos. A mensagem do Hub IoT pressupõe a seguinte representação JSON da mensagem. As propriedades do sistema são adicionadas a todos os usuários e identificam o conteúdo da mensagem. Os usuários podem adicionar propriedades de aplicativo seletivamente à mensagem. É recomendável usar nomes de propriedade exclusivos, pois as mensagens do dispositivo para a nuvem do Hub IoT não diferenciam maiúsculas de minúsculas. Por exemplo, se você tiver várias propriedades com o mesmo nome, o Hub IoT enviará apenas uma das propriedades.  

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

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| contentType | string | O usuário especifica o tipo de conteúdo da mensagem. Para permitir a consulta no corpo da mensagem, esse valor deve ser definido Application/JSON. |
| contentEncoding | string | O usuário especifica o tipo de codificação da mensagem. Os valores permitidos são UTF-8, UTF-16, UTF-32 se o contentType estiver definido como Application/JSON. |
| iothub-connection-device-id | string | Esse valor é definido pelo Hub IoT e identifica a ID do dispositivo. Para consultar, use `$connectionDeviceId`. |
| iothub-enqueuedtime | string | Esse valor é definido pelo Hub IoT e representa a hora real de enfileirar a mensagem em UTC. Para consultar, use `enqueuedTime`. |
| iothub-interface-nome | string | Esse valor é definido pelo usuário e representa o nome da interface de entrelaçamento digital que implementa a mensagem de telemetria. Para consultar, use `$interfaceName`. Esse recurso está disponível como parte da [Visualização pública do IoT plug and Play](../iot-pnp/overview-iot-plug-and-play.md). |

Conforme descrito nas [mensagens do Hub IOT](iot-hub-devguide-messages-construct.md), há propriedades adicionais do sistema em uma mensagem. Além de **ContentType**, **contentEncoding**e **enqueuedTime**, o **connectionDeviceId** e o **connectionModuleId** também podem ser consultados.

### <a name="application-properties"></a>Propriedades da aplicação

As propriedades do aplicativo são cadeias de caracteres definidas pelo usuário que podem ser adicionadas à mensagem. Esses campos são opcionais.  

### <a name="query-expressions"></a>Expressões de consulta

Uma consulta nas propriedades do sistema de mensagens precisa ser prefixada `$` com o símbolo. As consultas nas propriedades do aplicativo são acessadas com seu nome e não devem ser `$`prefixadas com o símbolo. Se um nome de propriedade de aplicativo `$`começar com, o Hub IOT irá procurá-lo nas propriedades do sistema e não será encontrado e, em seguida, ele examinará as propriedades do aplicativo. Por exemplo: 

Para consultar a propriedade do sistema contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Para consultar a propriedade de aplicativo processingPath:

```sql
processingPath = 'hot'
```

Para combinar essas consultas, você pode usar as funções e expressões boolianas:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Uma lista completa de operadores e funções com suporte é mostrada em [expressão e condições](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Consulta de roteamento de mensagens com base no corpo da mensagem 

Para habilitar a consulta no corpo da mensagem, a mensagem deve estar em um JSON codificado em UTF-8, UTF-16 ou UTF-32. O `contentType` deve ser definido como `application/JSON` e `contentEncoding` como uma das codificações UTF com suporte na Propriedade do sistema. Se essas propriedades não forem especificadas, o Hub IoT não avaliará a expressão de consulta no corpo da mensagem. 

O exemplo a seguir mostra como criar uma mensagem com um corpo JSON corretamente formado e codificado: 

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

### <a name="query-expressions"></a>Expressões de consulta

Uma consulta no corpo da mensagem precisa ser prefixada com `$body`o. Você pode usar uma referência de corpo, referência de matriz de corpo ou várias referências de corpo na expressão de consulta. A expressão de consulta também pode combinar uma referência de corpo com propriedades do sistema de mensagens e referência de propriedades do aplicativo de mensagens. Por exemplo, as seguintes são todas as expressões de consulta válidas: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Consulta de roteamento de mensagens com base no dispositivo. 

O roteamento de mensagens permite que você consulte as marcas e propriedades do [dispositivo](iot-hub-devguide-device-twins.md) e que são objetos JSON. Não há suporte para a consulta no módulo e. Um exemplo de marcas e propriedades de dispositivos de dispositivo é mostrado abaixo.

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

Uma consulta no corpo da mensagem precisa ser prefixada com `$twin`o. A expressão de consulta também pode combinar uma marca de texto ou referência de propriedade com uma referência de corpo, propriedades do sistema de mensagens e referência de propriedades do aplicativo de mensagens. É recomendável usar nomes exclusivos em marcas e propriedades, pois a consulta não diferencia maiúsculas de minúsculas. Além disso, evite `twin`usar `$twin` `body`,, ou `$body`, como nomes de propriedade. Por exemplo, as seguintes são todas as expressões de consulta válidas: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Roteamento de mensagens](iot-hub-devguide-messages-d2c.md).
* Experimente o [tutorial de roteamento de mensagens](tutorial-routing.md).
