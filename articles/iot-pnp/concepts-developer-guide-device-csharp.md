---
title: Guia de desenvolvimento de dispositivos (C#) - IoT Plug and Play Microsoft Docs
description: Descrição de IoT Plug e Play para desenvolvedores de dispositivos C#
author: rido-min
ms.author: rmpablos
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 22ffbed56b15b55b28bf150b90e489be9e4cfeaf
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090425"
---
# <a name="iot-plug-and-play-device-developer-guide-c"></a>Guia de desenvolvimento de dispositivos IoT Plug and Play (C#)

[!INCLUDE [iot-pnp-device-devguide-selector.md](../../includes/iot-pnp-device-devguide-selector.md)]

[!INCLUDE [iot-pnp-device-devguide-intro.md](../../includes/iot-pnp-device-devguide-intro.md)]

## <a name="model-id-announcement"></a>Anúncio de ID modelo

Para anunciar o ID do modelo, o dispositivo deve incluí-lo nas informações de ligação:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

A nova `ClientOptions` sobrecarga está disponível em todos os `DeviceClient` métodos utilizados para inicializar uma ligação.

> [!TIP]
> Para módulos e IoT Edge, utilize `ModuleClient` no lugar de `DeviceClient` .

## <a name="dps-payload"></a>Payload do DPS

Os dispositivos que utilizam o [Serviço de Provisionamento de Dispositivos (DPS)](../iot-dps/about-iot-dps.md) podem incluir o `modelId` a ser utilizado durante o processo de provisionamento utilizando a seguinte carga útil JSON.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementar telemetria, propriedades e comandos

Conforme descrito nos [componentes Do Understand nos modelos IoT Plug e Play,](concepts-components.md)os fabricantes de dispositivos devem decidir se pretendem utilizar componentes para descrever os seus dispositivos. Ao utilizar componentes, os dispositivos devem seguir as regras descritas nesta secção.

### <a name="telemetry"></a>Telemetria

Um componente predefinido não requer nenhuma propriedade especial.

Ao utilizar componentes aninhados, os dispositivos devem definir uma propriedade de mensagem com o nome do componente:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Propriedades apenas de leitura

Reportar uma propriedade a partir do componente padrão não requer qualquer construção especial:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo twin é atualizado com a próxima propriedade reportada:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Ao utilizar componentes aninhados, as propriedades devem ser criadas dentro do nome do componente:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo twin é atualizado com a próxima propriedade reportada:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Propriedades writable

Estas propriedades podem ser definidas pelo dispositivo ou atualizadas pela solução. Se a solução atualizar um imóvel, o cliente recebe uma notificação como uma chamada no `DeviceClient` ou `ModuleClient` . Para seguir as convenções IoT Plug and Play, o dispositivo deve informar o serviço de que a propriedade foi recebida com sucesso.

#### <a name="report-a-writable-property"></a>Reportar uma propriedade writable

Quando um dispositivo reporta uma propriedade por si só, deve incluir os `ack` valores definidos nas convenções.

Para reportar uma propriedade por defeito do componente padrão:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo twin é atualizado com a próxima propriedade reportada:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Para reportar uma propriedade writable de um componente aninhado, o gémeo deve incluir um marcador:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo twin é atualizado com a próxima propriedade reportada:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Subscreva as atualizações de propriedade desejadas

Os serviços podem atualizar as propriedades desejadas que desencadeiam uma notificação nos dispositivos conectados. Esta notificação inclui as propriedades desejadas atualizadas, incluindo o número de versão que identifica a atualização. Os dispositivos devem responder com a mesma `ack` mensagem que as propriedades reportadas.

Um componente predefinido vê a propriedade única e cria o reportado `ack` com a versão recebida:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

O dispositivo twin mostra a propriedade nas secções desejadas e reportadas:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Um componente aninhado recebe as propriedades desejadas embrulhadas com o nome do componente, e deve reportar de volta a `ack` propriedade relatada:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

O dispositivo gémeo para um componente aninhado mostra as secções desejadas e reportadas da seguinte forma:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Comandos

Um componente predefinido recebe o nome de comando tal como foi invocado pelo serviço.

Um componente aninhado recebe o nome de comando pré-fixado com o nome do componente e o `*` separador.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Cargas de pedido e resposta

Os comandos utilizam tipos para definir as suas cargas de pedido e resposta. Um dispositivo deve deserizar o parâmetro de entrada de entrada e serializar a resposta. O exemplo a seguir mostra como implementar um comando com tipos complexos definidos nas cargas:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Os seguintes fragmentos de código mostram como um dispositivo implementa esta definição de comando, incluindo os tipos utilizados para permitir a serialização e a deserialização:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> Os nomes de pedido e resposta não estão presentes nas cargas serializadas transmitidas pelo fio.

[!INCLUDE [iot-pnp-device-devguide-summary.md](../../includes/iot-pnp-device-devguide-summary.md)]
