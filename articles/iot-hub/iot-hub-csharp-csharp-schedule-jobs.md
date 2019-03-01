---
title: Agendar tarefas com o Azure IoT Hub (.NET/.NET) | Documentos da Microsoft
description: Como agendar um trabalho do IoT Hub do Azure para invocar um método direto em vários dispositivos. Utilizar o Azure IoT device SDK para .NET para implementar as aplicações de dispositivo simulado e uma aplicação de serviço para executar a tarefa.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: robin.shahan
ms.openlocfilehash: b39d7d4af4c476778665e844f647beecf20e3d9f
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008887"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Agendar e difundir tarefas (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Utilize o IoT Hub do Azure para agendar e monitorizar tarefas que atualizar milhões de dispositivos. Utilize tarefas para:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Uma tarefa encapsula uma destas ações e controla a execução com um conjunto de dispositivos que é definido por uma consulta do dispositivo duplo. Por exemplo, uma aplicação de back-end pode utilizar uma tarefa para invocar um método direto num 10 000 dispositivos que reinicia os dispositivos. Especifique o conjunto de dispositivos com uma consulta do dispositivo duplo e agendar a tarefa seja executada num período futuro. O progresso da tarefa roteiros como cada um dos dispositivos receber e executar o método direto de reinício.

Para saber mais sobre cada uma destas capacidades, consulte:

* Dispositivo duplo e propriedades: [Introdução aos dispositivos duplos](iot-hub-csharp-csharp-twin-getstarted.md) e [Tutorial: Como utilizar propriedades dos dispositivos duplos](tutorial-device-twins.md)

* Métodos diretos: [Guia do programador do IoT Hub - métodos diretos](iot-hub-devguide-direct-methods.md) e [Tutorial: Utilizar métodos diretos](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostrar-lhe como:

* Criar uma aplicação de dispositivo que implementa um método direto chamado **LockDoor** que pode ser chamada por aplicação de back-end.

* Criar uma aplicação de back-end que cria uma tarefa para chamar o **LockDoor** método direto em vários dispositivos. Outra tarefa envia atualizações de propriedade pretendida para vários dispositivos.

No final deste tutorial, tem duas aplicações de consola .NET (c#):

**SimulateDeviceMethods** que liga ao seu IoT hub e implementa o **LockDoor** método direto.

**ScheduleJob** que utiliza tarefas para chamar o **LockDoor** método direto e atualizar as propriedades dos dispositivos duplos desejado em vários dispositivos.

Para concluir este tutorial, precisa do seguinte:

* O Visual Studio 2017.
* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, vai criar uma aplicação de consola .NET que responde a um método direto chamado pela solução de back-end.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **SimulateDeviceMethods**.
   
    ![Nova aplicação de dispositivo Visual c# Windows clássico](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. No Solution Explorer, clique com botão direito a **SimulateDeviceMethods** projeto e, em seguida, clique em **gerir pacotes NuGet...** .

3. Na **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **Microsoft.Azure.Devices.Client**. Selecione **instale** para instalar o **Microsoft.Azure.Devices.Client** empacotamento e a aceitar os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [do Azure IoT device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet pacote e suas dependências.
   
    ![Aplicação de cliente de janela do Gestor de pacotes NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação de dispositivo que anotou na secção anterior:

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

7. Adicione o seguinte para implementar o serviço de escuta de gémeos de dispositivo no dispositivo:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Por fim, adicione o seguinte código para o **Main** método para abrir a ligação ao seu hub IoT e inicializar o serviço de escuta do método:
   
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
        
9. Guarde o seu trabalho e criar a sua solução.         

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como tentativas de ligação), como sugerido no artigo [processamento de erros transitórios](/azure/architecture/best-practices/transient-faults).
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Programar tarefas para chamar um método direto e enviar atualizações de dispositivo duplo

Nesta secção, vai criar uma aplicação de consola do .NET (usando a linguagem c#) utiliza tarefas para chamar o **LockDoor** método direto e enviar atualizações de propriedade pretendida para vários dispositivos.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **ScheduleJob**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. No Solution Explorer, clique com botão direito a **ScheduleJob** projeto e, em seguida, clique em **gerir pacotes NuGet...** .

3. Na **Gestor de pacotes NuGet** janela, selecione **procurar**, procure **Microsoft.Azure.Devices**, selecione **instalar** para instalar o **Microsoft.Azure.Devices** empacotamento e a aceitar os termos de utilização. Este passo transfere, instala e adiciona uma referência para o [SDK do serviço do Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet pacote e suas dependências.

    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adicione o seguinte `using` instrução se já não estão presentes nas instruções padrão.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Adicione os seguintes campos à classe **Programa**. Substitua os marcadores de posição pela cadeia de ligação do IoT Hub para o hub que criou na secção anterior e o nome do seu dispositivo.

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

9. Adicione outro método para o **programa** classe:

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
    > Para obter mais informações sobre a sintaxe de consulta, consulte [linguagem de consulta do IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
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

11. Guarde o seu trabalho e criar a sua solução. 

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. No Explorador de soluções do Visual Studio, sua solução com o botão direito e, em seguida, clique em **criar**. **Vários projetos de arranque**. Certifique-se `SimulateDeviceMethods` na parte superior da lista seguida `ScheduleJob`. Definir as respetivas ações **começar** e clique em **OK**.

2. Execute os projetos, clicando em **começar** ou vá para o **depurar** menu e clique em **iniciar depuração**.

3. Pode ver o resultado de dispositivos e aplicações de back-end.

    ![Executar as aplicações para agendar tarefas](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou uma tarefa para agendar um método direto a um dispositivo e a atualização das propriedades do dispositivo duplo.

Para continuar a introdução ao IoT Hub e padrões de gestão de dispositivos como remota sobre a atualização de firmware do ar, leia [Tutorial: Como fazer uma atualização de firmware](tutorial-firmware-update.md).

Para saber mais sobre implementar o AI em dispositivos de ponta com o Azure IoT Edge, veja [introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).