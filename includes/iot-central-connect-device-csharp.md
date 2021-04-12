---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 0383949dd9b0aeaa185613474d91d0416169ab1f
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491148"
---
## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa dos seguintes recursos:

* Uma aplicação Azure IoT Central criada usando o modelo **de aplicação personalizado.** Para obter mais informações, veja [criar um início rápido da aplicação](../articles/iot-central/core/quick-deploy-iot-central.md). O pedido deve ter sido criado em ou depois de 14 de julho de 2020.
* Uma máquina de desenvolvimento com [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/).
* Uma cópia local do [repositório Do Microsoft Azure IoT samples para C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub que contém o código de amostra. Utilize este link para descarregar uma cópia do repositório: [Descarregue ZIP](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Em seguida, desaperte o ficheiro para um local adequado na sua máquina local.

## <a name="review-the-code"></a>Rever o código

Na cópia do repositório Microsoft Azure IoT Samples for C# que descarregou anteriormente, abra o ficheiro de solução *azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln"* ficheiro de solução no Visual Studio. No **Solution Explorer**, expanda os *ficheiros PnpDeviceSamples > TemperatureController* e abra o *Programa.cs* e *Controlo de TemperaturaS.cs* ficheiros para visualizar o código desta amostra.

Quando executar a amostra para ligar à IoT Central, utiliza o Serviço de Provisionamento de Dispositivos (DPS) para registar o dispositivo e gerar uma cadeia de ligação. A amostra recolhe a informação de ligação DPS de que necessita do ambiente.

No *programa.cs,* o `Main` método chama `SetupDeviceClientAsync` a:

* Utilize o ID do modelo `dtmi:com:example:TemperatureController;2` quando forissar o dispositivo com DPS. A IoT Central utiliza o ID do modelo para identificar ou gerar o modelo do dispositivo para este dispositivo. Para saber mais, consulte [Associar um dispositivo com um modelo de dispositivo](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Crie um **dispositivo Desaquiente** para ligar à IoT Central.

```csharp
private static async Task<DeviceClient> SetupDeviceClientAsync(Parameters parameters, CancellationToken cancellationToken)
{
  DeviceClient deviceClient;
  switch (parameters.DeviceSecurityType.ToLowerInvariant())
  {
    case "dps":
      DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, cancellationToken);
      var authMethod = new DeviceAuthenticationWithRegistrySymmetricKey(dpsRegistrationResult.DeviceId, parameters.DeviceSymmetricKey);
      deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
      break;

    case "connectionstring":
      // ...

    default:
      // ...
  }
  return deviceClient;
}
```

O método principal cria então uma instância **TemperatureControllerSample** e chama o `PerformOperationsAsync` método para lidar com as interações com a IoT Central.

No *.cs,* o `PerformOperationsAsync` método:

* Define um manipulador para o comando **de reinicialização** no componente predefinido.
* Define os manipuladores para os comandos **GetMaxMinReport** nos dois componentes do termóstato.
* Define os manipuladores para receber atualizações de propriedade de temperatura-alvo nos dois componentes do termóstato.
* Envia atualizações iniciais de propriedade de informação do dispositivo.
* Envia periodicamente telemetria de temperatura dos dois componentes do termóstato.
* Envia periodicamente telemetria do conjunto de trabalho do componente predefinido.
* Envia a temperatura máxima desde o último reboot sempre que uma nova temperatura máxima é atingida nos dois componentes do termóstato.

```csharp
public async Task PerformOperationsAsync(CancellationToken cancellationToken)
{
  await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);

  // For a component-level command, the command name is in the format "<component-name>*<command-name>".
  await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommand, Thermostat1, cancellationToken);
  await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommand, Thermostat2, cancellationToken);

  await _deviceClient.SetDesiredPropertyUpdateCallbackAsync(SetDesiredPropertyUpdateCallback, null, cancellationToken);
  _desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
  _desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

  await UpdateDeviceInformationAsync(cancellationToken);
  await SendDeviceSerialNumberAsync(cancellationToken);

  bool temperatureReset = true;
  _maxTemp[Thermostat1] = 0d;
  _maxTemp[Thermostat2] = 0d;

  while (!cancellationToken.IsCancellationRequested)
  {
    if (temperatureReset)
    {
      // Generate a random value between 5.0°C and 45.0°C for the current temperature reading for each "Thermostat" component.
      _temperature[Thermostat1] = Math.Round(s_random.NextDouble() * 40.0 + 5.0, 1);
      _temperature[Thermostat2] = Math.Round(s_random.NextDouble() * 40.0 + 5.0, 1);
    }

    await SendTemperatureAsync(Thermostat1, cancellationToken);
    await SendTemperatureAsync(Thermostat2, cancellationToken);
    await SendDeviceMemoryAsync(cancellationToken);

    temperatureReset = _temperature[Thermostat1] == 0 && _temperature[Thermostat2] == 0;
    await Task.Delay(5 * 1000);
  }
}
```

O `SendTemperatureAsync` método mostra como o dispositivo envia a telemetria de temperatura de um componente para a IoT Central. O `SendTemperatureTelemetryAsync` método usa a classe para construir a `PnpConvention` mensagem:

```csharp
private async Task SendTemperatureAsync(string componentName, CancellationToken cancellationToken)
{
  await SendTemperatureTelemetryAsync(componentName, cancellationToken);

  double maxTemp = _temperatureReadingsDateTimeOffset[componentName].Values.Max<double>();
  if (maxTemp > _maxTemp[componentName])
  {
    _maxTemp[componentName] = maxTemp;
    await UpdateMaxTemperatureSinceLastRebootAsync(componentName, cancellationToken);
  }
}

private async Task SendTemperatureTelemetryAsync(string componentName, CancellationToken cancellationToken)
{
  const string telemetryName = "temperature";
  double currentTemperature = _temperature[componentName];
  using Message msg = PnpConvention.CreateMessage(telemetryName, currentTemperature, componentName);

  await _deviceClient.SendEventAsync(msg, cancellationToken);

  if (_temperatureReadingsDateTimeOffset.ContainsKey(componentName))
  {
    _temperatureReadingsDateTimeOffset[componentName].TryAdd(DateTimeOffset.UtcNow, currentTemperature);
  }
  else
  {
    _temperatureReadingsDateTimeOffset.TryAdd(
      componentName,
      new Dictionary<DateTimeOffset, double>
      {
        { DateTimeOffset.UtcNow, currentTemperature },
      });
  }
}
```

O `UpdateMaxTemperatureSinceLastRebootAsync` método envia uma `maxTempSinceLastReboot` atualização de propriedade para a IoT Central. Este método utiliza a `PnpConvention` classe para criar o patch:

```csharp
private async Task UpdateMaxTemperatureSinceLastRebootAsync(string componentName, CancellationToken cancellationToken)
{
  const string propertyName = "maxTempSinceLastReboot";
  double maxTemp = _maxTemp[componentName];
  TwinCollection reportedProperties = PnpConvention.CreateComponentPropertyPatch(componentName, propertyName, maxTemp);

  await _deviceClient.UpdateReportedPropertiesAsync(reportedProperties, cancellationToken);
}
```

O `TargetTemperatureUpdateCallbackAsync` método trata da atualização da propriedade da temperatura-alvo da IoT Central. Este método utiliza a `PnpConvention` classe para ler a mensagem de atualização da propriedade e construir a resposta:

```csharp
private async Task TargetTemperatureUpdateCallbackAsync(TwinCollection desiredProperties, object userContext)
{
  const string propertyName = "targetTemperature";
  string componentName = (string)userContext;

  bool targetTempUpdateReceived = PnpConvention.TryGetPropertyFromTwin(
    desiredProperties,
    propertyName,
    out double targetTemperature,
    componentName);
  if (!targetTempUpdateReceived)
  {
      return;
  }

  TwinCollection pendingReportedProperty = PnpConvention.CreateComponentWritablePropertyResponse(
      componentName,
      propertyName,
      targetTemperature,
      (int)StatusCode.InProgress,
      desiredProperties.Version);

  await _deviceClient.UpdateReportedPropertiesAsync(pendingReportedProperty);

  // Update Temperature in 2 steps
  double step = (targetTemperature - _temperature[componentName]) / 2d;
  for (int i = 1; i <= 2; i++)
  {
      _temperature[componentName] = Math.Round(_temperature[componentName] + step, 1);
      await Task.Delay(6 * 1000);
  }

  TwinCollection completedReportedProperty = PnpConvention.CreateComponentWritablePropertyResponse(
      componentName,
      propertyName,
      _temperature[componentName],
      (int)StatusCode.Completed,
      desiredProperties.Version,
      "Successfully updated target temperature");

  await _deviceClient.UpdateReportedPropertiesAsync(completedReportedProperty);
}

```

O `HandleMaxMinReportCommand` método trata dos comandos dos componentes chamados da IoT Central:

```csharp
private Task<MethodResponse> HandleMaxMinReportCommand(MethodRequest request, object userContext)
{
    try
    {
        string componentName = (string)userContext;
        DateTime sinceInUtc = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
        var sinceInDateTimeOffset = new DateTimeOffset(sinceInUtc);

        if (_temperatureReadingsDateTimeOffset.ContainsKey(componentName))
        {

            Dictionary<DateTimeOffset, double> allReadings = _temperatureReadingsDateTimeOffset[componentName];
            Dictionary<DateTimeOffset, double> filteredReadings = allReadings.Where(i => i.Key > sinceInDateTimeOffset)
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

Para executar a aplicação da amostra no Estúdio Visual:

1. No **Solution Explorer**, selecione o ficheiro de projeto **PnpDeviceSamples > TemperatureController.**

1. Navegue para **project > TemperatureController Properties > Debug**. Em seguida, adicione as seguintes variáveis ambientais ao projeto:

    | Name | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | O valor de âmbito de identificação que fez anteriormente. |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | dispositivo de amostra-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | O valor da chave do dispositivo gerado que fez anteriormente. |

Agora pode executar e depurar a amostra no Estúdio Visual.

A seguinte saída mostra o dispositivo a registar-se e a ligar-se à IoT Central. A amostra começa a enviar telemetria:

```output
[03/31/2021 14:43:17]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Press Control+C to quit the sample.
[03/31/2021 14:43:17]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set up the device client.
[03/31/2021 14:43:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Initializing via DPS
[03/31/2021 14:43:27]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set handler for 'reboot' command.
[03/31/2021 14:43:27]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Connection status change registered - status=Connected, reason=Connection_Ok.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set handler for "getMaxMinReport" command.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set handler to receive 'targetTemperature' updates.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component = 'deviceInformation', properties update is complete.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - { "serialNumber": "SR-123456" } is complete.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - component="thermostat1", { "temperature": 34.2 } in °C.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 34.2 } in °C is complete.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - component="thermostat2", { "temperature": 25.1 } in °C.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat2", { "maxTempSinceLastReboot": 25.1 } in °C is complete.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - {"workingSet":31412} in KB.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Pode ver como o dispositivo responde a comandos e atualizações de propriedade:

```output
[03/31/2021 14:47:00]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Command: Received - component="thermostat2", generating max, min and avg temperature report since 31/03/2021 06:00:00.
[03/31/2021 14:47:00]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Command: component="thermostat2", MaxMinReport since 31/03/2021 06:00:00: maxTemp=36.4, minTemp=36.4, avgTemp=36.4, startTime=31/03/2021 14:46:33, endTime=31/03/2021 14:46:55

...

[03/31/2021 14:46:36]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Received - component="thermostat1", { "targetTemperature": 67°C }.
[03/31/2021 14:46:36]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat1", {"targetTemperature": 67 } in °C is InProgress.
[03/31/2021 14:46:49]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat1", {"targetTemperature": 67 } in °C is Completed
[03/31/2021 14:46:49]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - component="thermostat1", { "temperature": 67 } in °C.
```
