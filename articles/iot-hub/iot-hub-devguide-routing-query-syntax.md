---
title: Consulta no encaminhamento de mensagem do Hub Azure IoT Microsoft Docs
description: Saiba mais sobre a linguagem de consulta de envio de mensagens IoT Hub que pode usar para aplicar consultas ricas em mensagens para receber os dados que lhe interessam.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: b76ef431e4c0ad63929378c1f48c6ab06776cb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271112"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Sintaxe de consulta do encaminhamento de mensagens do Hub IoT

O encaminhamento de mensagens permite que os utilizadores direcionem diferentes tipos de dados, nomeadamente, mensagens de telemetria do dispositivo, eventos de ciclo de vida do dispositivo e eventos de mudança de dispositivo sintetizam para vários pontos finais. Também pode aplicar consultas ricas a estes dados antes de encaminhar para receber os dados que lhe interessam. Este artigo descreve a linguagem de encaminhamento de mensagens IoT Hub, e fornece alguns padrões comuns de consulta.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O encaminhamento de mensagens permite-lhe consultar as propriedades da mensagem e do corpo da mensagem, bem como as duas etiquetas do dispositivo e as propriedades gémeas do dispositivo. Se o corpo da mensagem não for JSON, o encaminhamento de mensagens ainda pode direcionar a mensagem, mas as consultas não podem ser aplicadas ao corpo da mensagem.  As consultas são descritas como expressões booleanas onde uma verdade booleana faz com que a consulta tenha sucesso, que encaminha todos os dados que chegam, e Boolean falsa falha a consulta e nenhum dado é encaminhado. Se a expressão avaliar a nula ou indefinida, é tratada como falsa e um erro será gerado em registos de diagnóstico em caso de falha. A sintaxe de consulta deve estar correta para que a rota seja guardada e avaliada.  

## <a name="message-routing-query-based-on-message-properties"></a>Consulta de encaminhamento de mensagens com base nas propriedades da mensagem 

O IoT Hub define um [formato comum](iot-hub-devguide-messages-construct.md) para todas as mensagens dispositivo-nuvem para interoperabilidade através de protocolos. A mensagem IoT Hub assume a seguinte representação da JSON da mensagem. As propriedades do sistema são adicionadas para todos os utilizadores e identificam o conteúdo da mensagem. Os utilizadores podem adicionar seletivamente propriedades da aplicação à mensagem. Recomendamos que o uso de nomes de propriedade únicos, uma vez que as mensagens ioT Hub-to-cloud não são sensíveis a casos. Por exemplo, se tiver várias propriedades com o mesmo nome, o IoT Hub só enviará uma das propriedades.  

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

As propriedades do sistema ajudam a identificar conteúdos e fonte seleções das mensagens. 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| conteúdoType | string | O utilizador especifica o tipo de conteúdo da mensagem. Para permitir a consulta no corpo da mensagem, este valor deve ser definido aplicação/JSON. |
| contentEncoding | string | O utilizador especifica o tipo de codificação da mensagem. Os valores permitidos são UTF-8, UTF-16, UTF-32 se o conteúdoType for definido para aplicação/JSON. |
| iothub-ligação-dispositivo-id | string | Este valor é definido pelo IoT Hub e identifica a identificação do dispositivo. Para consultar, `$connectionDeviceId`use. |
| iothub-enqueuedtime | string | Este valor é definido pelo IoT Hub e representa o tempo real de enquecância da mensagem na UTC. Para consultar, `enqueuedTime`use. |
| iothub-interface-nome | string | Este valor é definido pelo utilizador e representa o nome da interface dupla digital que implementa a mensagem de telemetria. Para consultar, `$interfaceName`use. Esta funcionalidade está disponível como parte da [pré-visualização pública IoT Plug e Play](../iot-pnp/overview-iot-plug-and-play.md). |

Como descrito nas [Mensagens IoT Hub,](iot-hub-devguide-messages-construct.md)existem propriedades adicionais do sistema numa mensagem. Além do **conteúdoType**, **contentEncoding,** e **enqueuedTime,** a **ligaçãoDeviceId** e **ligaçãoModuleId** também podem ser consultadas.

### <a name="application-properties"></a>Propriedades da aplicação

As propriedades da aplicação são cordas definidas pelo utilizador que podem ser adicionadas à mensagem. Estes campos são opcionais.  

### <a name="query-expressions"></a>Expressões de consulta

Uma consulta sobre as propriedades do sistema `$` de mensagens precisa de ser pré-fixada com o símbolo. As consultas nas propriedades da aplicação são acedidas `$`com o seu nome e não devem ser pré-fixadas com o símbolo. Se um nome de `$`propriedade de aplicação começar com , então o IoT Hub irá procurá-lo nas propriedades do sistema, e não for encontrado, então ele irá olhar nas propriedades da aplicação. Por exemplo: 

Para consultar o conteúdo da propriedade do sistemaEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Para consultar o processamento de propriedade de aplicaçãoPath:

```sql
processingPath = 'hot'
```

Para combinar estas consultas, pode utilizar expressões e funções booleanas:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Uma lista completa de operadores e funções suportadas é mostrada em [Expressão e condições](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Consulta de encaminhamento de mensagens baseada no corpo da mensagem

Para permitir a consulta no corpo da mensagem, a mensagem deve estar num JSON codificado em UTF-8, UTF-16 ou UTF-32. O `contentType` deve ser `application/JSON` `contentEncoding` definido para e para uma das codificações uTF suportadas na propriedade do sistema. Se estas propriedades não forem especificadas, o IoT Hub não avaliará a expressão de consulta no corpo da mensagem. 

O exemplo que se segue mostra como criar uma mensagem com um corpo JSON devidamente formado e codificado: 

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
> Isto mostra como lidar com a codificação do corpo em javascript. Se quiser ver uma amostra em C#, descarregue as [amostras Azure IoT C# .](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Desaperte o ficheiro master.zip. A solução Visual Studio *Simulado Program.cs*ficheiro do Estúdio mostra como codificar e enviar mensagens para um Hub IoT. Esta é a mesma amostra utilizada para testar o encaminhamento da mensagem, como explicado no tutorial de [Encaminhamento de Mensagens](tutorial-routing.md). Na parte inferior da Program.cs, também tem um método para ler num dos ficheiros codificados, descodificá-lo e escrevê-lo novamente como ASCII para que possa lê-lo. 


### <a name="query-expressions"></a>Expressões de consulta

Uma consulta no corpo da mensagem precisa `$body`de ser pré-fixada com o . Pode utilizar uma referência corporal, referência de matriz corporal ou múltiplas referências corporais na expressão de consulta. A sua expressão de consulta também pode combinar uma referência corporal com propriedades do sistema de mensagens, e referência de propriedades de aplicação de mensagens. Por exemplo, todos os seguintes são expressões de consulta válidas: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Consulta de encaminhamento de mensagens baseada em dispositivo gémeo 

O encaminhamento de mensagens permite-lhe consultar as etiquetas e propriedades [do Dispositivo Twin,](iot-hub-devguide-device-twins.md) que são objetos JSON. A consulta no módulo twin também é suportada. Uma amostra de etiquetas e propriedades do Dispositivo Twin é mostrada abaixo.

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

Uma consulta no gémeo da mensagem precisa `$twin`de ser pré-fixada com o . A sua expressão de consulta também pode combinar uma etiqueta dupla ou referência de propriedade com uma referência corporal, propriedades do sistema de mensagens e referência de propriedades de aplicação de mensagens. Recomendamos a utilização de nomes únicos em etiquetas e propriedades, uma vez que a consulta não é sensível a casos. Isto aplica-se tanto aos gémeos dispositivos como aos gémeos módulos. Também se `twin`abstenha de usar, `$twin` `body`ou `$body`, como nomes de propriedade. Por exemplo, todos os seguintes são expressões de consulta válidas: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Não é suportada a consulta de encaminhamento no corpo ou no dispositivo twin com um período de carga útil ou nome de propriedade.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o encaminhamento de mensagens.](iot-hub-devguide-messages-d2c.md)
* Experimente o tutorial de [encaminhamento de mensagens.](tutorial-routing.md)
