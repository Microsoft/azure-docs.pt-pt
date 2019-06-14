---
title: Consulta no roteamento de mensagens do IoT Hub do Azure | Documentos da Microsoft
description: Guia do programador - sintaxe de consulta para o encaminhamento de mensagens no IoT Hub do Azure.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 94d3599fe919cf648be7115be68002d2aa458ee3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400648"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Sintaxe de consulta de encaminhamento de mensagens de IoT Hub

Roteamento de mensagens permite que os utilizadores encaminhar os diferentes tipos de dados ou seja, mensagens de telemetria do dispositivo, eventos de ciclo de vida do dispositivo e dispositivo duplo eventos de alteração para vários pontos de extremidade. Também pode aplicar consultas a estes dados antes de encaminhamento para receber os dados que é importante para si. Este artigo descreve a linguagem de consulta de encaminhamento de mensagens do IoT Hub e fornece alguns padrões comuns de consulta.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Roteamento de mensagens permite-lhe consultar sobre as propriedades da mensagem e corpo da mensagem, bem como as etiquetas do dispositivo duplo e propriedades dos dispositivos duplos. Se o corpo da mensagem não é um JSON, o encaminhamento de mensagens pode rotear a mensagem, mas não podem ser aplicadas a consultas para o corpo da mensagem.  Consultas são descritas como expressões booleanas onde uma verdadeira booleana torna a consulta concluída com êxito que encaminha todos os dados de entrada e false booleano falha a consulta e dados não são encaminhados. Se a expressão for avaliada como nula ou indefinida, ela é tratada como false e será gerado um erro nos registos de diagnóstico em caso de falha. A sintaxe de consulta tem de ser correta para a rota sejam salvas e avaliada.  

## <a name="message-routing-query-based-on-message-properties"></a>Consulta de encaminhamento de mensagens com base nas propriedades da mensagem 

O IoT Hub define um [formato comum](iot-hub-devguide-messages-construct.md) para todo dispositivo-para-cloud messaging para interoperabilidade entre protocolos. Mensagem do IoT Hub pressupõe a seguinte representação JSON da mensagem. Propriedades do sistema são adicionadas para todos os utilizadores e identificar o conteúdo da mensagem. Os usuários podem seletivamente adicionar propriedades da aplicação para a mensagem. Recomendamos que utilize nomes de propriedade única, como mensagens de dispositivo-para-cloud do IoT Hub não diferencia maiúsculas de minúsculas. Por exemplo, se tiver várias propriedades com o mesmo nome, o IoT Hub irá enviar apenas uma das propriedades.  

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

Propriedades do sistema ajudam a identificar conteúdo e a origem das mensagens. 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| contentType | string | O utilizador Especifica o tipo de conteúdo da mensagem. Para permitir que a consulta no corpo da mensagem, este valor deve ser definido application/JSON. |
| contentEncoding | string | O utilizador Especifica o tipo de codificação da mensagem. Valores permitidos são UTF-8, UTF-16, UTF-32 se o contentType é definido como application/JSON. |
| iothub-connection-device-id | string | Este valor é definido pelo IoT Hub e identifica o ID do dispositivo. Para consultar, utilize `$connectionDeviceId`. |
| iothub-enqueuedtime | string | Este valor é definido pelo IoT Hub e representa o tempo em questão de enfileiramento de mensagens em UTC. Para consultar, utilize `enqueuedTime`. |

Conforme descrito no [mensagens do Hub IoT](iot-hub-devguide-messages-construct.md), existem propriedades do sistema adicionais numa mensagem. Para além **contentType**, **contentEncoding**, e **enqueuedTime**, o **connectionDeviceId** e  **connectionModuleId** também podem ser consultados.

### <a name="application-properties"></a>Propriedades da aplicação

Propriedades da aplicação são definidas pelo utilizador as cadeias de caracteres que podem ser adicionadas à mensagem. Estes campos são opcionais.  

### <a name="query-expressions"></a>Expressões de consulta

Uma consulta nas propriedades do sistema de mensagem precisa de ter o prefixo a `$` símbolo. Consultas nas propriedades da aplicação são acedidas com os respetivos nomes e não deve ter o prefixo com o `$`símbolo. Se um nome de propriedade da aplicação começa com `$`, em seguida, o IoT Hub será procurá-lo nas propriedades do sistema e não for encontrado, em seguida, irá procurar nas propriedades da aplicação. Por exemplo: 

Para consultar no contentEncoding de propriedade de sistema 

```sql
$contentEncoding = 'UTF-8'
```

Para consultar no processingPath de propriedade do aplicativo:

```sql
processingPath = 'hot'
```

Para combinar estas consultas, pode utilizar funções e expressões booleanas:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Uma lista completa dos operadores de suporte e as funções são listadas no [expressão e condições](iot-hub-devguide-query-language.md#expressions-and-conditions)

## <a name="message-routing-query-based-on-message-body"></a>Consulta de encaminhamento de mensagens com base no corpo da mensagem 

Para ativar a consultar no corpo da mensagem, a mensagem deve ser num JSON codificado em UTF-8, UTF-16 ou UTF-32. O `contentType` deve ser definida como `application/JSON` e `contentEncoding` para um das codificações UTF suportadas na propriedade de sistema. Se estas propriedades não forem especificadas, o IoT Hub não avaliará a expressão de consulta no corpo da mensagem. 

O exemplo seguinte mostra como criar uma mensagem com um corpo JSON corretamente formado e codificado: 

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

Uma consulta no corpo da mensagem tem de ter o prefixo a `$body`. Pode utilizar uma referência de corpo, referência da matriz de corpo ou várias referências de corpo da expressão de consulta. A expressão de consulta também pode combinar uma referência de corpo com as propriedades do sistema de mensagem e referência de propriedades de aplicativo de mensagens. Por exemplo, seguem-se todas as expressões de consulta válida: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Consulta de encaminhamento de mensagens com base no dispositivo duplo 

Roteamento de mensagens permite-lhe consultar no [dispositivo duplo](iot-hub-devguide-device-twins.md) etiquetas e propriedades, que são objetos JSON. Tenha em atenção que a consulta num módulo duplo não é suportado. Um exemplo de dispositivo duplo etiquetas e propriedades é mostrado abaixo.

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

Uma consulta no corpo da mensagem tem de ter o prefixo a `$twin`. A expressão de consulta também pode combinar uma referência de etiqueta ou propriedade duplo com uma referência de corpo, propriedades do sistema de mensagem e referência de propriedades de aplicativo de mensagens. Recomendamos que utilize nomes exclusivos em etiquetas e propriedades, como a consulta não diferencia maiúsculas de minúsculas. Também evite o uso `twin`, `$twin`, `body`, ou `$body`, como nomes de propriedade. Por exemplo, seguem-se todas as expressões de consulta válida: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [roteamento de mensagens](iot-hub-devguide-messages-d2c.md).
* Experimente o [tutorial de encaminhamento de mensagens](tutorial-routing.md).