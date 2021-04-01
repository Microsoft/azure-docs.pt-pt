---
title: Agendar postos de trabalho com a Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Como agendar um trabalho do Azure IoT Hub para invocar um método direto em vários dispositivos. Utiliza o dispositivo Azure IoT SDK para .NET para implementar as aplicações simuladas do dispositivo e uma aplicação de serviço para executar o trabalho.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 0391afe5562238f488fde7e50d914c46a5c32944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142465"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Horários e trabalhos de transmissão (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Use o Azure IoT Hub para agendar e rastrear trabalhos que atualizam milhões de dispositivos. Utilizar empregos para:

* Atualizar as propriedades pretendidas

* Etiquetas de atualização

* Invocar métodos diretos

Um trabalho envolve uma destas ações e acompanha a execução contra um conjunto de dispositivos que é definido por uma consulta de dois dispositivos. Por exemplo, uma aplicação back-end pode usar um trabalho para invocar um método direto em 10.000 dispositivos que reiniciam os dispositivos. Especifica o conjunto de dispositivos com uma consulta dupla do dispositivo e agenda o trabalho para ser executado num futuro próximo. O trabalho acompanha o progresso à medida que cada um dos dispositivos recebe e executa o método direto de reinicialização.

Para saber mais sobre cada uma destas capacidades, consulte:

* Dispositivo twin e propriedades: [Começar com os gémeos do dispositivo](iot-hub-csharp-csharp-twin-getstarted.md) e [tutorial: Como usar propriedades gémeas do dispositivo](tutorial-device-twins.md)

* Métodos diretos: [Guia de desenvolvimento do IoT Hub - métodos diretos](iot-hub-devguide-direct-methods.md) e [tutorial: Utilize métodos diretos](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostrar-lhe como:

* Crie uma aplicação para dispositivos que implemente um método direto chamado **LockDoor**, que pode ser chamado pela aplicação back-end.

* Crie uma aplicação back-end que cria um trabalho para ligar para o método direto **LockDoor** em vários dispositivos. Outro trabalho envia atualizações de propriedade desejadas para vários dispositivos.

No final deste tutorial, tem duas aplicações de consola .NET (C#):

* **SimularDeviceMethods**. Esta aplicação conecta-se ao seu hub IoT e implementa o método direto **LockDoor.**

* **AgendarJob**. Esta aplicação utiliza trabalhos para ligar para o método direto **LockDoor** e atualizar as propriedades desejadas pelo dispositivo em vários dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, cria-se uma aplicação de consola .NET que responde a um método direto chamado pela solução back end.

1. No Visual Studio, **selecione Criar um novo projeto** e, em seguida, escolha o modelo de projeto da App consola **(.NET Framework).** Selecione **Seguinte** para continuar.

1. Em **Configurar o seu novo projeto**, nomeie o projeto *SimulateDeviceMethods* e, em seguida, selecione **Create**.

    ![Configure o seu projeto SimulateDeviceMethods](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. No Solution Explorer, clique com o botão direito no projeto **SimulateDeviceMethods** e, em seguida, selecione **Gerir pacotes NuGet**.

1. No **NuGet Package Manager**, selecione **Procurar** e escolher **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

    ![Aplicativo de cliente de janela do Gestor de pacotes NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet [do dispositivo Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e às suas dependências.

1. Adicione as `using` seguintes declarações no topo do ficheiro **.cs Programa:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do espaço reservado pela cadeia de ligação do dispositivo que observou na secção anterior:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Adicione o seguinte código para implementar o método direto no dispositivo:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Adicione o seguinte método para implementar o dispositivo de escuta gémeos no dispositivo:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Por fim, adicione o seguinte código ao método **Principal** para abrir a ligação ao seu hub IoT e rubricar o ouvinte do método:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Poupe o seu trabalho e construa a sua solução.

> [!NOTE]
> Para manter as coisas simples, este tutorial não implementa nenhuma política de retíria. No código de produção, deve implementar políticas de repetição (como a repetição de ligação), tal como sugerido no [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Agendar empregos para chamar um método direto e enviar atualizações gémeas do dispositivo

Nesta secção, cria-se uma aplicação de consola .NET (utilizando C#) que utiliza trabalhos para ligar para o método direto **LockDoor** e enviar atualizações de propriedade desejadas para vários dispositivos.

1. No Estúdio Visual, selecione **File**  >  **New**  >  **Project**. In **Create a new project**, escolha a App consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto**, nomeie o projeto *ScheduleJob*. Para **solução**, escolha **Adicionar à solução** e, em seguida, selecione **Criar**.

    ![Nome e configuração do projeto ScheduleJob](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. No Solution Explorer, clique com o botão direito no projeto **ScheduleJob** e, em seguida, **selecione Gerir pacotes NuGet**.

1. No Gestor de **Pacotes NuGet**, selecione **Procurar,** procurar e escolher **Microsoft.Azure.Devices**, em seguida, selecione **Instalar**.

   Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet [do serviço Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e às suas dependências.

1. Adicione as `using` seguintes declarações no topo do ficheiro **.cs Programa:**

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adicione a seguinte `using` declaração se ainda não estiver presente nas declarações padrão.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua os espaços reservados pela cadeia de ligação IoT Hub que copiou anteriormente na cadeia de [ligação do hub IoT](#get-the-iot-hub-connection-string) e no nome do seu dispositivo.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Adicione o seguinte método à classe **Programa:**

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Adicione o seguinte método à classe **Programa:**

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Adicione outro método à classe **Programa:**

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Para obter mais informações sobre a sintaxe de consulta, consulte [o idioma de consulta IoT Hub](./iot-hub-devguide-query-language.md).
    >

1. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Poupe o seu trabalho e construa a sua solução.

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. No Visual Studio Solution Explorer, clique com o botão direito na sua solução e, em seguida, selecione **set StartUp Projects**.

1. Selecione **o Common Properties** Startup  >  **Project** e, em seguida, selecione **Vários projetos de startups**.

1. Certifique-se de que `SimulateDeviceMethods` está no topo da lista seguido por `ScheduleJob` . Desacione ambas as suas ações para **Iniciar** e selecionar **OK**.

1. Executar os projetos clicando **em Iniciar** ou ir ao menu **Debug** e clicar **em Iniciar Debugging**.

   Vê-se a saída tanto do dispositivo como das aplicações back-end.

    ![Executar as aplicações para agendar empregos](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do dispositivo Twin.

* Para continuar a trabalhar com o IoT Hub e padrões de gestão de dispositivos como o controlo remoto sobre a atualização do firmware aéreo, leia [Tutorial: Como fazer uma atualização de firmware](tutorial-firmware-update.md).

* Para aprender sobre a implementação de IA para dispositivos de borda com Azure IoT Edge, consulte [Começar com IoT Edge](../iot-edge/quickstart-linux.md).