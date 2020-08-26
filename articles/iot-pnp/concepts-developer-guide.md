---
title: Guia de desenvolvimento - IoT Plug e Play Preview / Microsoft Docs
description: Descrição de IoT Plug e Play para desenvolvedores
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9e6d13fedbfa495448164c1354868e12992dd71c
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856026"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>Guia de desenvolvimento ioT plug e play preview

O IoT Plug and Play Preview permite-lhe construir dispositivos inteligentes que anunciam as suas capacidades para aplicações Azure IoT. Os dispositivos IoT Plug e Play não necessitam de configuração manual quando um cliente os liga a aplicações IoT Plug e Play.

Este guia descreve os passos básicos necessários para criar um dispositivo que segue as [convenções IoT Plug and Play](concepts-convention.md), e as APIs rest disponíveis que pode utilizar para interagir com o dispositivo.

Para construir um dispositivo IoT Plug and Play, siga os passos de teses:

1. Certifique-se de que o seu dispositivo está a utilizar o protocolo MQTT ou MQTT sobre WebSockets para ligar ao Azure IoT Hub.
1. Crie um modelo [digital de linguagem de definição de gémeos (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) para descrever o seu dispositivo. Para saber mais, consulte [os componentes nos modelos IoT Plug and Play](concepts-components.md).
1. Atualize o seu dispositivo para anunciar a `model-id` ligação do dispositivo como parte integrante.
1. Implementar telemetria, propriedades e comandos usando as [convenções IoT Plug and Play](concepts-convention.md)

Assim que a implementação do dispositivo estiver pronta, utilize o [explorador Azure IoT](howto-use-iot-explorer.md) para validar que o dispositivo segue as convenções IoT Plug and Play.

> [!Tip]
> Todos os fragmentos de código neste artigo usam C#, mas os conceitos são aplicáveis a qualquer um dos SDKs disponíveis para C, Python, Node e Java.

## <a name="model-id-announcement"></a>Anúncio de ID modelo

Para anunciar o ID do modelo, o dispositivo deve incluí-lo nas informações de ligação:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

A nova `ClientOptions` sobrecarga está disponível em todos os `DeviceClient` métodos utilizados para inicializar uma ligação.

O anúncio do ID do modelo foi adicionado às próximas versões dos SDKs

|SDK|Versão|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Nó|1.17.0|
|Python|2.1.4|

## <a name="dps-payload"></a>Carga útil do DPS

Os dispositivos que utilizam o [Serviço de Provisionamento de Dispositivos (DPS)](/iot-dps/) podem incluir o `modelId` a ser utilizado durante o processo de provisionamento utilizando a seguinte carga útil JSON.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementar telemetria, propriedades e comandos

Conforme descrito nos [componentes Do Understand nos modelos IoT Plug e Play,](concepts-components.md)os fabricantes de dispositivos devem decidir se pretendem utilizar componentes para descrever os seus dispositivos. Ao utilizar componentes, os dispositivos devem seguir as regras descritas nesta secção.

### <a name="telemetry"></a>Telemetria

Modelos sem componentes não requerem qualquer propriedade especial.

Ao utilizar componentes, os dispositivos devem definir uma propriedade de mensagem com o nome do componente:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Propriedades apenas de leitura

Os modelos sem componentes não requerem qualquer construção especial:

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

Ao utilizar componentes, as propriedades devem ser criadas dentro do nome do componente:

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

Estas propriedades podem ser definidas pelo dispositivo ou atualizadas pela solução. Se a solução atualizar um imóvel, o cliente recebe uma notificação como uma chamada de retorno no `DeviceClient` . Para seguir as convenções IoT Plug and Play, o dispositivo deve informar o serviço de que a propriedade foi recebida com sucesso.

#### <a name="report-a-writable-property"></a>Reportar uma propriedade writable

Quando um dispositivo reporta uma propriedade por si só, deve incluir os `ack` valores definidos nas convenções.

Para reportar uma propriedade writable sem componentes:

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

Para reportar uma propriedade writable de um componente, o gémeo deve incluir um marcador:

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

Os modelos sem componentes vêem a propriedade única e criam o reportado `ack` com a versão recebida:

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

Os modelos com componentes recebem as propriedades desejadas embrulhadas com o nome do componente, e devem reportar de volta a `ack` propriedade relatada:

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

O dispositivo gémeo para componentes mostra as secções desejadas e comunicadas da seguinte forma:

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

Os modelos sem componentes recebem o nome de comando, uma vez que foi invocado pelo serviço.

Os modelos com componentes receberão o nome de comando pré-fixado com o componente e o `*` separador.

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

## <a name="interact-with-the-device"></a>Interaja com o dispositivo 

O IoT Plug and Play permite-lhe utilizar dispositivos que anunciaram o seu ID do modelo com o seu hub IoT. Por exemplo, pode aceder diretamente às propriedades e comandos de um dispositivo.

Para utilizar um dispositivo IoT Plug and Play que esteja ligado ao seu hub IoT, utilize a API IoT Hub REST ou um dos SDKs ioT. Os exemplos a seguir usam a API IoT Hub REST. A versão atual da API é `2020-05-31-preview` . Apêndice `?api-version=2020-05-31` às suas chamadas DE REPOUSO PI.

Se o seu dispositivo termóstato for `t-123` chamado, obtém todas as propriedades em todas as interfaces implementadas pelo seu dispositivo com uma chamada REST API GET:

```REST
GET /digitalTwins/t-123
```

Esta chamada incluirá a propriedade Json `$metadata.$model` com o ID do modelo anunciado pelo dispositivo.

Todas as propriedades em todas as interfaces são acedidas com o `GET /DigitalTwin/{device-id}` modelo DE API REST onde `{device-id}` está o identificador do dispositivo:

```REST
GET /digitalTwins/{device-id}
```

Pode ligar diretamente para os comandos do dispositivo IoT Plug and Play. Se o `Thermostat` componente do dispositivo tiver um `t-123` `restart` comando, pode chamá-lo com uma chamada REST API POST:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

De uma forma mais geral, os comandos podem ser chamados através deste modelo de API REST:

- `device-id`: o identificador do dispositivo.
- `component-name`: o nome da interface a partir da secção de implementações no modelo de capacidade do dispositivo.
- `command-name`: o nome do comando.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre modelação de dispositivos, aqui estão alguns recursos adicionais:

- [Linguagem de definição de gémeos digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK de Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Componentes do modelo](./concepts-components.md)
