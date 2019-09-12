---
title: Conectar um Raspberry Pi ao seu aplicativo de IoT Central doC#Azure () | Microsoft Docs
description: Como desenvolvedor de dispositivos, como conectar um Raspberry Pi ao aplicativo IoT Central do Azure usando C#o.
author: viv-liu
ms.author: viviali
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7a66925dceee4bf90bc6a5cd155f99347bbd124e
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886011"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Conectar um Raspberry Pi ao seu aplicativo de IoT Central doC#Azure ()

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como desenvolvedor de dispositivos, conectar um Raspberry Pi ao seu aplicativo Microsoft Azure IoT Central usando a linguagem C# de programação.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa dos seguintes componentes:

* Um aplicativo IoT Central do Azure criado no modelo de aplicativo **devkits de exemplo** . Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
* Um dispositivo Raspberry Pi que executa o sistema operacional Raspbian. O Raspberry Pi deve ser capaz de se conectar à Internet. Para obter mais informações, consulte [Configurando o Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>Aplicativo **devkits de exemplo**

Um aplicativo criado no modelo de aplicativo **devkits de exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as seguintes características:

- Telemetria, que inclui as seguintes medidas que o dispositivo coletará:
  - Humidade
  - Temperatura
  - Pressão
  - Magnetômetro (X, Y, Z)
  - Acelerômetro (X, Y, Z)
  - Giroscópio (X, Y, Z)
- Definições
  - Volta
  - Atual
  - Velocidade do ventilador
  - Alternância de IR.
- properties
  - Propriedade de dispositivo de número de matriz
  - Propriedade de nuvem de local

Para obter os detalhes completos da configuração do modelo de dispositivo, consulte os [detalhes do modelo de dispositivo do Raspberry Pi](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo IoT Central do Azure, adicione um dispositivo real do modelo de dispositivo **Raspberry Pi** . Anote os detalhes de conexão do dispositivo (**ID do escopo**, **ID do dispositivo**e **chave primária**). Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo IOT central do Azure](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Criar seu aplicativo .NET

Você cria e testa o aplicativo do dispositivo no computador desktop.

Para concluir as etapas a seguir, você pode usar Visual Studio Code. Para obter mais informações, consulte [trabalhando C#com ](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Se preferir, você pode concluir as etapas a seguir usando um editor de código diferente.

1. Para inicializar o projeto do .NET e adicionar os pacotes do NuGet necessários, execute os seguintes comandos:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. Abra a `pisample` pasta em Visual Studio Code. Em seguida, abra o arquivo de projeto **pisample. csproj** . Adicione a `<RuntimeIdentifiers>` marca mostrada no trecho a seguir:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Seu número de versão do pacote **Microsoft. Azure. Devices. Client** pode ser maior do que o mostrado.

1. Salve **pisample. csproj**. Se Visual Studio Code solicitar que você execute o comando de restauração, escolha **restaurar**.

1. Abra **Program.cs** e substitua o conteúdo pelo código a seguir:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > Você atualiza o espaço `{your device connection string}` reservado na próxima etapa.

## <a name="run-your-net-application"></a>Executar seu aplicativo .NET

Adicione a cadeia de conexão específica do dispositivo ao código para o dispositivo autenticar com o Azure IoT Central. Siga estas instruções para [gerar a cadeia de conexão do dispositivo](howto-generate-connection-string.md) usando a **ID do escopo**, a **ID do dispositivo**e a **chave primária** anotada anteriormente.

1. Substitua `{your device connection string}` no arquivo **Program.cs** pela cadeia de conexão que você gerou.

1. Execute o seguinte comando em seu ambiente de linha de comando:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Copie a `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` pasta para o dispositivo Raspberry Pi. Você pode usar o comando **SCP** para copiar os arquivos, por exemplo:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Para obter mais informações, consulte [acesso remoto do Raspberry Pi](https://www.raspberrypi.org/documentation/remote-access/).

1. Entre no dispositivo Raspberry Pi e execute os seguintes comandos em um shell:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Em seu Raspberry Pi, execute os seguintes comandos:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![O programa começa](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. No aplicativo IoT Central do Azure, você pode ver como o código em execução no Raspberry Pi interage com o aplicativo:

   * Na página **medidas** do seu dispositivo real, você pode ver a telemetria.
   * Na página **Propriedades** , você pode ver o valor da propriedade número de **dado** relatado.
   * Na página **configurações** , você pode alterar várias configurações no Raspberry Pi, como tensão e velocidade do ventilador.

     A captura de tela a seguir mostra o Raspberry Pi que recebe a alteração de configuração:

     ![Raspberry Pi recebe alteração de configuração](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Detalhes do modelo de dispositivo Raspberry Pi

Um aplicativo criado no modelo de aplicativo **devkits de exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as seguintes características:

### <a name="telemetry-measurements"></a>Medições de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| humidade       | %      | 0       | 100     | 0              |
| Temp           | °     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| acelerômetro | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Definições

Configurações numéricas

| Display name | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Volta      | setVoltage | V | 0              | 0       | 240     | 0       |
| Atual      | SetCurrent | 2,0  | 0              | 0       | 100     | 0       |
| Velocidade do ventilador    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Alternar configurações

| Display name | Nome do campo | Em texto | Fora do texto | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATIVADO      | OFF      | Desativado     |

### <a name="properties"></a>properties

| Type            | Display name | Nome do campo | Tipo de dados                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| Propriedade do dispositivo | Número do chip   | dieNumber  | number                                 |
| Location        | Location     | location   | {Lat: float, Long: float, Alt?: float} |

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu como conectar um Raspberry Pi ao seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo IOT.
