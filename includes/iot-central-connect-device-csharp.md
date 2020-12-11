---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/25/2020
ms.openlocfilehash: f4536beae18a50d3e1d42fc1593cf826c94418f8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033891"
---
## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo é necessário o seguinte:

* Uma aplicação Azure IoT Central criada usando o modelo **de aplicação personalizado.** Para obter mais informações, veja [criar um início rápido da aplicação](../articles/iot-central/core/quick-deploy-iot-central.md). O pedido deve ter sido criado em ou depois de 14 de julho de 2020.
* Uma máquina de desenvolvimento com [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/).
* Uma cópia local do [repositório Do Microsoft Azure IoT samples para C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub que contém o código de amostra. Utilize este link para descarregar uma cópia do repositório: [Descarregue ZIP](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Em seguida, desaperte o ficheiro para um local adequado na sua máquina local.

## <a name="review-the-code"></a>Rever o código

Na cópia do repositório Microsoft Azure IoT Samples for C# que descarregou anteriormente, abra o ficheiro do projeto *azure-iot-samples-csharp-master\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* project file in Visual Studio. No projeto **termóstato,** abra os ficheiros *Program.cs* e *ThermostatSample.cs* para visualizar o código desta amostra.

Quando executar a amostra para ligar à IoT Central, utiliza o Serviço de Provisionamento de Dispositivos (DPS) para registar o dispositivo e gerar uma cadeia de ligação. A amostra recolhe a informação de ligação DPS de que necessita do ambiente.

Em *Program.cs,* o `main` método chama `SetupDeviceClientAsync` a:

* Utilize o ID do modelo `dtmi:com:example:Thermostat;1` quando forissar o dispositivo com DPS. A IoT Central utiliza o ID do modelo para identificar ou gerar o modelo do dispositivo para este dispositivo. Para saber mais, consulte [Associar um dispositivo com um modelo de dispositivo](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Crie um **dispositivo Desaquiente** para ligar à IoT Central.

```csharp
private static async Task<DeviceClient> SetupDeviceClientAsync(Parameters parameters, CancellationToken cancellationToken)
{
  DeviceClient deviceClient;
  switch (parameters.DeviceSecurityType.ToLowerInvariant())
  {
    case "dps":
      s_logger.LogDebug($"Initializing via DPS");
      DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, cancellationToken);
      var authMethod = new DeviceAuthenticationWithRegistrySymmetricKey(dpsRegistrationResult.DeviceId, parameters.DeviceSymmetricKey);
      deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
      break;

    case "connectionstring":
        // ...
        break;

    default:
        // ...
  }

  return deviceClient;
}
```

O método principal cria então uma instância **TermóstatoSample** e chama o `PerformOperationsAsync` método para lidar com as interações com a IoT Central.

Em *ThermostatSample.cs,* o `PerformOperationsAsync` método:

* Define um manipulador para receber as atualizações de propriedade desejadas pela temperatura-alvo.
* Define um manipulador para o comando **GetMaxMinReport.**
* Envia periodicamente telemetria de temperatura.
* Envia a temperatura máxima desde o último reboot sempre que se atinja uma nova temperatura máxima.

```csharp
public async Task PerformOperationsAsync(CancellationToken cancellationToken)
{
  await _deviceClient.SetDesiredPropertyUpdateCallbackAsync(TargetTemperatureUpdateCallbackAsync, _deviceClient, cancellationToken);

  await _deviceClient.SetMethodHandlerAsync("getMaxMinReport", HandleMaxMinReportCommand, _deviceClient, cancellationToken);

  bool temperatureReset = true;
  while (!cancellationToken.IsCancellationRequested)
  {
    if (temperatureReset)
    {
      // Generate a random value between 5.0°C and 45.0°C for the current temperature reading.
      _temperature = Math.Round(_random.NextDouble() * 40.0 + 5.0, 1);
      temperatureReset = false;
    }

    await SendTemperatureAsync();
    await Task.Delay(5 * 1000);
  }
}
```

O `SendTemperatureAsync` método mostra como o dispositivo envia a telemetria de temperatura para a IoT Central:

```csharp
private async Task SendTemperatureAsync()
{
  await SendTemperatureTelemetryAsync();

  double maxTemp = _temperatureReadingsDateTimeOffset.Values.Max<double>();
  if (maxTemp > _maxTemp)
  {
    _maxTemp = maxTemp;
    await UpdateMaxTemperatureSinceLastRebootAsync();
  }
}
```

O `UpdateMaxTemperatureSinceLastRebootAsync` método envia uma `maxTempSinceLastReboot` atualização de propriedade para a IoT Central:

```csharp
private async Task UpdateMaxTemperatureSinceLastRebootAsync()
{
  const string propertyName = "maxTempSinceLastReboot";

  var reportedProperties = new TwinCollection();
  reportedProperties[propertyName] = _maxTemp;

  await _deviceClient.UpdateReportedPropertiesAsync(reportedProperties);
}
```

O `TargetTemperatureUpdateCallbackAsync` método trata da atualização da propriedade da temperatura-alvo da IoT Central:

```csharp
private async Task TargetTemperatureUpdateCallbackAsync(TwinCollection desiredProperties, object userContext)
{
    const string propertyName = "targetTemperature";

    (bool targetTempUpdateReceived, double targetTemperature) = GetPropertyFromTwin<double>(desiredProperties, propertyName);
    if (targetTempUpdateReceived)
    {
      string jsonPropertyPending = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.InProgress}, " +
          $"\"av\": {desiredProperties.Version} }} }}";
      var reportedPropertyPending = new TwinCollection(jsonPropertyPending);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedPropertyPending);

      // Update Temperature in 2 steps
      double step = (targetTemperature - _temperature) / 2d;
      for (int i = 1; i <= 2; i++)
      {
        _temperature = Math.Round(_temperature + step, 1);
        await Task.Delay(6 * 1000);
      }

      string jsonProperty = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.Completed}, " +
        $"\"av\": {desiredProperties.Version}, \"ad\": \"Successfully updated target temperature\" }} }}";
      var reportedProperty = new TwinCollection(jsonProperty);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedProperty);
  }
  else
  {
    // ...
  }
}
```

O `HandleMaxMinReportCommand` método lida com o comando chamado da IoT Central:

```csharp
private Task<MethodResponse> HandleMaxMinReportCommand(MethodRequest request, object userContext)
{
  try
  {
    DateTime sinceInUtc = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
    var sinceInDateTimeOffset = new DateTimeOffset(sinceInUtc);

    Dictionary<DateTimeOffset, double> filteredReadings = _temperatureReadingsDateTimeOffset
      .Where(i => i.Key > sinceInDateTimeOffset)
      .ToDictionary(i => i.Key, i => i.Value);

    if (filteredReadings != null && filteredReadings.Any())
    {
      var report = new
      {
        maxTemp = filteredReadings.Values.Max<double>(),
        minTemp = filteredReadings.Values.Min<double>(),
        avgTemp = filteredReadings.Values.Average(),
        startTime = filteredReadings.Keys.Min(),
        endTime = filteredReadings.Keys.Max(),
      };

      byte[] responsePayload = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(report));
      return Task.FromResult(new MethodResponse(responsePayload, (int)StatusCode.Completed));
    }

    return Task.FromResult(new MethodResponse((int)StatusCode.NotFound));
  }
  catch (JsonReaderException ex)
  {
    // ...
  }
}
```

## <a name="get-connection-information"></a>Obter informações da ligação

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Executar o código

Para executar o pedido de amostragem:

1. Abra o ficheiro de projeto *azure-iot-samples-csharp/iot-hub/Samples/device/PnpDeviceSamples/Thermostat/Thermostat.csproj* no Visual Studio.

1. No Estúdio Visual, navegue para **Project > Thermostat Properties > Debug**. Em seguida, adicione as seguintes variáveis ambientais ao projeto:

    | Nome | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | O valor de âmbito de identificação que fez anteriormente. |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | dispositivo de amostra-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | O valor da chave do dispositivo gerado que fez anteriormente. |

Agora pode executar e depurar a amostra no Estúdio Visual.

A seguinte saída mostra o dispositivo a registar-se e a ligar-se à IoT Central. A amostra começa a enviar telemetria:

```output
[11/25/2020 11:07:58]info: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Press Control+C to quit the sample.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set up the device client.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Initializing via DPS
[11/25/2020 11:08:11]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler to receive "targetTemperature" updates.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Connection status change registered - status=Connected, reason=Connection_Ok.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler for "getMaxMinReport" command.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 36.5°C } is Completed.
[11/25/2020 11:08:18]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:23]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:29]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Pode ver como o dispositivo responde a comandos e atualizações de propriedade:

```output
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 19/11/2020 06:30:00.
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: MaxMinReport since 19/11/2020 06:30:00: maxTemp=36.5, minTemp=36.5, avgTemp=36.5, startTime=25/11/2020 11:08:13, endTime=25/11/2020 11:09:51

...

[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Received - { "targetTemperature": 56°C }.
[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is InProgress.
[11/25/2020 11:14:40]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 56°C } is Completed.
[11/25/2020 11:14:43]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is Completed.
```
