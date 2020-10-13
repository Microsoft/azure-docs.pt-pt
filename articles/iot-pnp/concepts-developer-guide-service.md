---
title: Guia de desenvolvedores de serviços - IoT Plug and Play ! Microsoft Docs
description: Descrição de IoT Plug and Play para desenvolvedores de serviços
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2a61eefc9c065253bdac11665f0135e493584c0d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945112"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guia de desenvolvedores de serviço ioT plug e play

O IoT Plug and Play permite-lhe construir dispositivos inteligentes que anunciam as suas capacidades para aplicações Azure IoT. Os dispositivos IoT Plug e Play não necessitam de configuração manual quando um cliente os liga a aplicações IoT Plug e Play.

O IoT Plug and Play permite-lhe utilizar dispositivos que anunciaram o seu ID do modelo com o seu hub IoT. Por exemplo, pode aceder diretamente às propriedades e comandos de um dispositivo.

Para utilizar um dispositivo IoT Plug and Play que esteja ligado ao seu hub IoT, utilize um dos SDKs de serviço IoT ou o IoT Hub REST API:

## <a name="service-sdks"></a>SDKs de Serviço

Utilize os SDKs de serviço Azure IoT na sua solução para interagir com dispositivos e módulos. Por exemplo, pode utilizar os SDKs de serviço para ler e atualizar propriedades gémeas e invocar comandos. As línguas apoiadas incluem C#, Java, Node.js e Python.

Os SDKs de serviço permitem-lhe aceder a informações do dispositivo a partir de uma solução, como um ambiente de trabalho ou uma aplicação web. Os SDKs de serviço incluem dois espaços de nome e modelos de objetos que pode usar para recuperar o ID do modelo:

- Cliente de serviço Iot Hub. Este serviço expõe o ID do modelo como uma propriedade gémea do dispositivo.

- Cliente de serviço digital Twins. A nova API digital twins opera no modelo [Digital Twins Definition Language (DTDL)](concepts-digital-twin.md) que constrói componentes, propriedades e comandos. As APIs Digitais Twin facilitam aos construtores de soluções a criação de soluções IoT Plug e Play.

| Plataforma | Cliente de serviço IoT Hub | Cliente de serviço Digital Twins |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [Documentação](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices) <br/> [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Documentação](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Amostras](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Documentação](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Documentação](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [Documentação](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Documentação](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="iot-hub-service-client-examples"></a>Exemplos de clientes de serviço IoT Hub

Esta secção mostra exemplos C# utilizando o cliente de serviço IoT Hub e as classes **RegistryManager** e **ServiceClient.** Utiliza a classe **RegistryManager** para interagir com o estado do dispositivo utilizando gémeos do dispositivo. Também pode utilizar a classe **RegistryManager** para [consultar registos](..\iot-hub\iot-hub-devguide-query-language.md) de dispositivos no seu Hub IoT. Utilize a classe **ServiceClient** para chamar comandos no dispositivo. O modelo [DTDL](concepts-digital-twin.md) para o dispositivo define as propriedades e comanda os instrumentos do dispositivo. Nos fragmentos de código, a `deviceTwinId` variável detém o ID do dispositivo IoT Plug and Play registado no seu hub IoT.

### <a name="get-the-device-twin-and-model-id"></a>Obtenha o dispositivo twin e model ID

Para obter o iD duplo e modelo do dispositivo IoT Plug and Play que está ligado ao seu hub IoT:

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(parameters.HubConnectionString);

Twin twin = await registryManager.GetTwinAsync(deviceTwinId);
Console.WriteLine($"Device twin: \n{JsonConvert.SerializeObject(twin, Formatting.Indented)}");
Console.WriteLine($"Model ID: {twin.ModelId}.");
```

### <a name="update-device-twin"></a>Atualização do dispositivo twin

O seguinte código snippet mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A amostra mostra como precisa de obter o gémeo antes de `ETag` atualizá-lo. A propriedade é definida no componente predefinido do dispositivo:

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired["targetTemperature"] = desiredTargetTemperature;

Console.WriteLine($"Update the targetTemperature property to {desiredTargetTemperature}.");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);
```

O seguinte corte mostra como atualizar a `targetTemperature` propriedade em um componente. A amostra mostra como precisa de obter o gémeo antes de `ETag` atualizá-lo. A propriedade é definida na interface **Termóstato1:**

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

var twinPatch = CreatePropertyPatch("targetTemperature", desiredTargetTemperature, "thermostat1");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);

// ...

private static Twin CreatePropertyPatch(string propertyName, object propertyValue, string componentName)
{
    var twinPatch = new Twin();
    twinPatch.Properties.Desired[componentName] = new
    {
        __t = "c"
    };
    twinPatch.Properties.Desired[componentName][propertyName] = JsonConvert.SerializeObject(propertyValue);
    return twinPatch;
}
```

Para uma propriedade num componente, o patch de propriedade parece o seguinte exemplo:

```json
{
"sampleComponentName":
  {
    "__t": "c",
    "samplePropertyName": 20
  }
}
```

### <a name="call-command"></a>Chamar comando

O seguinte corte mostra como invocar o `getMaxMinReport` comando definido num componente predefinido:

```csharp
ServiceClient serviceClient = ServiceClient.CreateFromConnectionString(parameters.HubConnectionString);

var commandInvocation = new CloudToDeviceMethod("getMaxMinReport") { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
  CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);

  Console.WriteLine($"Command getMaxMinReport was invoked." +
      $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine($"Unable to execute command getMaxMinReport on {deviceTwinId}.";
}
```

O seguinte corte mostra como chamar o `getMaxMinReport` comando de um componente. O comando é definido na interface **Termóstato1:**

```csharp
// Create command name to invoke for component. The command is formatted as <component name>*<command name>
string commandToInvoke = "thermostat1*getMaxMinReport";
var commandInvocation = new CloudToDeviceMethod(commandToInvoke) { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
    CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);
    Console.WriteLine($"Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
}
```

## <a name="digital-twin-examples"></a>Exemplos gémeos digitais

Utiliza-se a classe **DigitalTwinClient** para interagir com o estado do dispositivo utilizando gémeos digitais. O modelo [DTDL](concepts-digital-twin.md) para o dispositivo define as propriedades e comanda os instrumentos do dispositivo.

Esta secção mostra exemplos C# utilizando a API dos Gémeos Digitais. Os seguintes snippets utilizam as seguintes classes para representar o gémeo digital dos dispositivos do termóstato e do controlador de temperatura:

```csharp
using Microsoft.Azure.Devices.Serialization;
using Newtonsoft.Json;
using System;

namespace Microsoft.Azure.Devices.Samples
{
  internal class ThermostatTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new ThermostatMetadata Metadata { get; set; }

    [JsonProperty("maxTempSinceLastReboot")]
    public double? MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public double? TargetTemperature { get; set; }
  }

  internal class ThermostatMetadata : DigitalTwinMetadata
  {
    [JsonProperty("maxTempSinceLastReboot")]
    public ReportedPropertyMetadata MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public WritableProperty TargetTemperature { get; set; }
  }

  internal class ReportedPropertyMetadata
  {
    [JsonProperty("lastUpdateTime")]
    public DateTimeOffset LastUpdateTime { get; set; }
  }

  internal class TemperatureControllerTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new TemperatureControllerMetadata Metadata { get; set; }

    [JsonProperty("serialNumber")]
    public string SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public ThermostatTwin Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public ThermostatTwin Thermostat2 { get; set; }
  }

  internal class TemperatureControllerMetadata : DigitalTwinMetadata
  {
    [JsonProperty("serialNumber")]
    public ReportedPropertyMetadata SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public WritableProperty Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public WritableProperty Thermostat2 { get; set; }
  }
}
```

A `digitalTwinId` variável detém o ID do dispositivo IoT Plug and Play registado no seu hub IoT.

### <a name="get-the-digital-twin-and-model-id"></a>Obtenha o twin digital e o ID do modelo

Para obter o twin digital e o iD do ioT Plug and Play que se conecta ao seu hub IoT:

```csharp
DigitalTwinClient digitalTwinClient = DigitalTwinClient.CreateFromConnectionString(parameters.HubConnectionString);
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
ThermostatTwin thermostatTwin = getDigitalTwinResponse.Body;
Console.WriteLine($"Model ID: {thermostatTwin.Metadata.ModelId}.");
Console.WriteLine($"Digital Twin: \n{JsonConvert.SerializeObject(thermostatTwin, Formatting.Indented)}");
```

### <a name="update-digital-twin"></a>Atualizar twin digital

O seguinte código snippet mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A propriedade é definida no componente predefinido do dispositivo:

```csharp
var updateOperation = new UpdateOperationsUtility();

int desiredTargetTemperature = 60;

// Get the current value of the targetTemperature property
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
double? currentTargetTemperature = getDigitalTwinResponse.Body.TargetTemperature;

// Has the targetTemperature property previously been set?
if (currentTargetTemperature != null)
{
  // Update the existing property
  // Prepend the property path with a '/'
  updateOperation.AppendReplacePropertyOp($"/targetTemperature", desiredTargetTemperature);
}
else
{
  // Add a new property
  // Prepend the property path with a '/'
  updateOperation.AppendAddPropertyOp($"/targetTemperature", desiredTargetTemperature);
}

// Update the targetTemperature property on the digital twin
HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

O seguinte corte mostra como atualizar a `targetTemperature` propriedade em um componente. A propriedade é definida no componente **Termóstato1:**

```csharp
int desiredTargetTemperature = 60;

var updateOperation = new UpdateOperationsUtility();

// Look at when the property was updated and what was it set to.
HttpOperationResponse<TemperatureControllerTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
  .GetDigitalTwinAsync<TemperatureControllerTwin>(digitalTwinId);

ThermostatTwin thermostat1 = getDigitalTwinResponse.Body.Thermostat1;

if (thermostat1 != null)
{
  // Thermostat1 is present in the TemperatureController twin. You can add/replace the component-level property "targetTemperature"
  double? currentComponentTargetTemperature = getDigitalTwinResponse.Body.Thermostat1.TargetTemperature;
  if (currentComponentTargetTemperature != null)
  {
      DateTimeOffset targetTemperatureDesiredLastUpdateTime = getDigitalTwinResponse.Body.Thermostat1.Metadata.TargetTemperature.LastUpdateTime;

      // The property path to be replaced should be prepended with a '/'
      updateOperation.AppendReplacePropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
  else
  {
      // The property path to be added should be prepended with a '/'
      updateOperation.AppendAddPropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
}
else
{
    // Thermostat1 is not present in the TemperatureController twin. Add the component.
    var componentProperty = new Dictionary<string, object> { { "targetTemperature", desiredTargetTemperature }, { "$metadata", new object() } };

    // The property path to be replaced should be prepended with a '/'
    updateOperation.AppendAddComponentOp("/thermostat1", componentProperty);
}

HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

### <a name="call-command"></a>Chamar comando

O seguinte corte mostra como invocar o `getMaxMinReport` comando definido num componente predefinido:

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
  HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
    .InvokeCommandAsync(digitalTwinId, "getMaxMinReport", JsonConvert.SerializeObject(since));

  Console.WriteLine($"Command getMaxMinReport was invoked. \nDevice returned status: {invokeCommandResponse.Body.Status}." +
    $"\nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
  if (e.Response.StatusCode == HttpStatusCode.NotFound)
  {
    Console.WriteLine($"Unable to execute command getMaxMinReport on {digitalTwinId}.");
  }
}
```

O seguinte corte mostra como chamar o `getMaxMinReport` comando de um componente. O comando é definido na interface **Termóstato1:**

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
    HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
        .InvokeComponentCommandAsync(digitalTwinId, "thermostat1", "getMaxMinReport", JsonConvert.SerializeObject(since));

    Console.WriteLine("Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {invokeCommandResponse.Body.Status}. \nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
    if (e.Response.StatusCode == HttpStatusCode.NotFound)
    {
        Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
    }
}
```

## <a name="rest-api"></a>API REST

Os exemplos a seguir utilizam a API IoT Hub REST para interagir com um dispositivo IoT Plug and Play ligado. A versão atual da API é `2020-09-30` . Apêndice `?api-version=2020-09-30` às suas chamadas DE REPOUSO PI.

> [!NOTE]
> Os gémeos módulos não são atualmente suportados pela `digitalTwins` API.

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

## <a name="read-device-telemetry"></a>Ler telemetria do dispositivo

Os dispositivos IoT Plug e Play enviam a telemetria definida no modelo DTDL para o IoT Hub. Por padrão, o IoT Hub encaminha a telemetria para um ponto final do Event Hubs onde pode consumi-la. Para saber mais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais](../iot-hub/iot-hub-devguide-messages-d2c.md).

O seguinte corte de código mostra como ler a telemetria a partir do ponto final padrão do Event Hubs. O código deste corte é retirado do quickstart IoT Hub [Enviar telemetria de um dispositivo para um hub IoT e lê-lo com uma aplicação de back-end](../iot-hub/quickstart-send-telemetry-dotnet.md):

```csharp
await using EventHubConsumerClient consumer = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, connectionString, EventHubName);

Console.WriteLine("Listening for messages on all partitions");

try
{
    await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync(cancellationToken))
    {
        Console.WriteLine("Message received on partition {0}:", partitionEvent.Partition.PartitionId);

        string data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
        Console.WriteLine("\t{0}:", data);

        Console.WriteLine("Application properties (set by device):");
        foreach (var prop in partitionEvent.Data.Properties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }

        Console.WriteLine("System properties (set by IoT Hub):");
        foreach (var prop in partitionEvent.Data.SystemProperties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }
    }
}
catch (TaskCanceledException)
{
    // This is expected when the token is signaled; it should not be considered an
    // error in this scenario.
}
```

A saída a seguir do código anterior mostra a telemetria de temperatura enviada pelo dispositivo **Thermostat** IoT Plug and Play que só tem o componente predefinido. A `dt-dataschema` propriedade do sistema mostra o ID do modelo:

```cmd/sh
Message received on partition 1:
        { "temperature": 25.5 }:
Application properties (set by device):
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:30:58
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:Thermostat;1
        content-type: application/json
        content-encoding: utf-8
```

A seguinte saída do código anterior mostra a telemetria de temperatura enviada pelo dispositivo IoT Plug and Play **do Controlador de Temperatura** multi-componente. A `dt-subject` propriedade do sistema mostra o nome do componente que enviou a telemetria. Neste exemplo, os dois componentes são `thermostat1` e `thermostat2` tal como definidos no modelo DTDL. A `dt-dataschema` propriedade do sistema mostra o ID do modelo:

```cmd/sh
Message received on partition 1:
        {"temperature":11.1}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat1
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
Message received on partition 1:
        {"temperature":41.2}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat2
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-device-twin-change-notifications"></a>Leia notificações de alteração dupla do dispositivo

Pode configurar o IoT Hub para gerar notificações de alterações gémeas do dispositivo para encaminhar para um ponto final suportado. Para saber mais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais > eventos de não telemetria](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no corte de código C# anterior gera a seguinte saída quando o IoT Hub gera notificações de alteração dupla do dispositivo para um dispositivo termóstato sem componente. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Message received on partition 1:
        {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":9.6,"$metadata":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z"}},"$version":2}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:17:41.7408552+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:17:41
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 61394e8ba7d
        content-type: application/json
        content-encoding: utf-8
```

O código mostrado no corte de código C# anterior gera a seguinte saída quando o IoT Hub gera notificações de alteração dupla do dispositivo para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente do termóstato gera notificações. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Message received on partition 1:
        {"version":5,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":9.6},"$metadata":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","thermostat1":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","__t":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"}}},"$version":4}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:27:59.5159720+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:27:59
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 615051f364e
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-digital-twin-change-notifications"></a>Leia notificações digitais de mudança de gémeos

Pode configurar o IoT Hub para gerar notificações digitais de alteração dupla para encaminhar para um ponto final suportado. Para saber mais, consulte [o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais > eventos de não telemetria](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no corte de código C# anterior gera a seguinte saída quando o IoT Hub gera notificações digitais de alteração dupla para um dispositivo termóstato sem componente. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-06T10:39:16.0209836Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":34.9}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:39:16.0209836+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:39:16
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 6169857bf8c
        content-type: application/json-patch+json
        content-encoding: utf-8
```

O código mostrado no código C# anterior gera a seguinte saída quando o IoT Hub gera notificações digitais de alteração dupla para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente do termóstato gera notificações. As propriedades da aplicação `iothub-message-schema` e `opType` dar-lhe informações sobre o tipo de notificação de alteração:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-06T10:41:44.8312666Z"}},"maxTempSinceLastReboot":29.1}}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:41:44.8312666+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:41:44
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 616f108f0e3
        content-type: application/json-patch+json
        content-encoding: utf-8
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu sobre modelação de dispositivos, aqui estão alguns recursos adicionais:

- [Linguagem de definição de gémeos digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK de Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Componentes do modelo](./concepts-components.md)
- [Instale e utilize as ferramentas de autoria DTDL](howto-use-dtdl-authoring-tools.md)
