---
title: Agendar trabalhos com o Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como agendar um trabalho do Hub IoT do Azure para invocar um método direto em vários dispositivos. Use o SDK do dispositivo IoT do Azure para .NET para implementar os aplicativos de dispositivo simulado e um aplicativo de serviço para executar o trabalho.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: 3594828ff3a79242e1cfd4663c415d8de502a329
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872774"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Agendar e difundir trabalhos (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Use o Hub IoT do Azure para agendar e acompanhar trabalhos que atualizam milhões de dispositivos. Usar trabalhos para:

* Atualizar as propriedades pretendidas
* Atualizar marcas
* Invocar métodos diretos

Um trabalho encapsula uma dessas ações e controla a execução em relação a um conjunto de dispositivos que é definido por uma consulta de dispositivo. Por exemplo, um aplicativo de back-end pode usar um trabalho para invocar um método direto em 10.000 dispositivos que reinicia os dispositivos. Especifique o conjunto de dispositivos com uma consulta de dispositivo de dispositivos e agende o trabalho para ser executado no futuro. O trabalho acompanha o progresso à medida que cada um dos dispositivos recebe e executa o método direto de reinicialização.

Para saber mais sobre cada um desses recursos, consulte:

* Dispositivos e propriedades do dispositivo: [Introdução ao dispositivo gêmeos](iot-hub-csharp-csharp-twin-getstarted.md) e [tutorial: Como usar as propriedades de dispositivo.](tutorial-device-twins.md)

* Métodos diretos: [Guia do desenvolvedor do Hub IOT – métodos diretos](iot-hub-devguide-direct-methods.md) e [tutorial: Usar métodos diretos](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostrar-lhe como:

* Crie um aplicativo de dispositivo que implementa um método direto chamado **LockDoor** que pode ser chamado pelo aplicativo de back-end.

* Crie um aplicativo de back-end que cria um trabalho para chamar o método direto **LockDoor** em vários dispositivos. Outro trabalho envia as atualizações de propriedade desejadas para vários dispositivos.

No final deste tutorial, você tem dois aplicativos de console .NETC#():

**SimulateDeviceMethods** que se conecta ao seu hub IOT e implementa o método direto **LockDoor** .

**ScheduleJob** que usa trabalhos para chamar o método direto **LockDoor** e atualizar as propriedades desejadas do dispositivo em vários dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Visual Studio. Este tutorial usa o Visual Studio 2017.

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta seção, você cria um aplicativo de console .NET que responde a um método direto chamado pelo back-end da solução.

1. No Visual Studio, adicione um projeto C# de área de trabalho clássica do Visual Windows a uma solução nova ou existente usando o modelo de projeto de **aplicativo de console** . Nomeie o projeto **SimulateDeviceMethods**.

    ![Novo aplicativo C# de dispositivo clássico do Visual Windows](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)

2. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulateDeviceMethods** e selecione **gerenciar pacotes NuGet...** .

3. Na janela **Gerenciador de pacotes NuGet** , selecione **procurar** e procure **Microsoft. Azure. Devices. Client**. Selecione **instalar** para instalar o pacote **Microsoft. Azure. Devices. Client** e aceite os termos de uso. Este procedimento baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do dispositivo IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e suas dependências.

    ![Aplicativo cliente da janela do Gerenciador de pacotes NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do espaço reservado pela cadeia de conexão do dispositivo que você anotou na seção anterior:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Adicione o seguinte para implementar o método direto no dispositivo:

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

7. Adicione o seguinte para implementar o ouvinte de dispositivo gêmeos no dispositivo:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Por fim, adicione o seguinte código ao método **Main** para abrir a conexão ao seu hub IOT e inicializar o ouvinte do método:

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

9. Salve seu trabalho e compile sua solução.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como repetição de conexão), conforme sugerido no artigo [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Agendar trabalhos para chamar um método direto e enviar atualizações do dispositivo

Nesta seção, você cria um aplicativo de console do .NET ( C#usando) que usa trabalhos para chamar o método direto **LockDoor** e enviar as atualizações de propriedade desejadas para vários dispositivos.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **ScheduleJob**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ScheduleJob** e selecione **gerenciar pacotes NuGet...** .

3. Na janela **Gerenciador de pacotes NuGet** , selecione **procurar**, procure **Microsoft. Azure.** Devices, selecione **instalar** para instalar o pacote **Microsoft. Azure.** Devices e aceite os termos de uso. Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.

    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adicione a instrução `using` a seguir se ainda não estiver presente nas instruções padrão.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Adicione os seguintes campos à classe **Programa**. Substitua os espaços reservados pela cadeia de conexão do Hub IoT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string) e o nome do seu dispositivo.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. Adicione o seguinte método à classe **Programa**:

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

8. Adicione o seguinte método à classe **Programa**:

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

9. Adicione outro método à classe **programa** :

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
    > Para obter mais informações sobre a sintaxe de consulta, consulte [linguagem de consulta do Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    >

10. Por fim, adicione as seguintes linhas ao método **Main**:

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

11. Salve seu trabalho e compile sua solução.

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. No Visual Studio Gerenciador de Soluções, clique com o botão direito do mouse em sua solução e selecione **definir projetos de inicialização**. Em seguida, selecione **vários projetos de inicialização**. Verifique se **SimulateDeviceMethods** está na parte superior da lista, seguido por **ScheduleJob**. Defina as ações a serem **iniciadas** e selecione **OK**.

2. Execute os projetos selecionando **Iniciar** ou acessando o menu **depurar** e selecionando **Iniciar Depuração**.

3. Você vê a saída do dispositivo e dos aplicativos de back-end.

    ![Executar os aplicativos para agendar trabalhos](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você usou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades de FileUp do dispositivo.

Para continuar a introdução aos padrões do Hub IOT e do gerenciamento de dispositivos, como remoto pela atualização do firmware [do ar, leia tutorial: Como fazer uma atualização](tutorial-firmware-update.md)de firmware.

Para saber mais sobre como implantar o ia em dispositivos de borda com o Azure IoT Edge, consulte [introdução ao IOT Edge](../iot-edge/tutorial-simulate-device-linux.md).
