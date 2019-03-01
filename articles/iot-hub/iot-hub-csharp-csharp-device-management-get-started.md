---
title: Introdução à gestão de dispositivos do IoT Hub do Azure (.NET/.NET) | Documentos da Microsoft
description: Como utilizar a gestão de dispositivos do IoT Hub do Azure para iniciar um reinício do dispositivo remoto. Utilizar o Azure IoT device SDK para .NET para implementar uma aplicação de dispositivo simulado que inclui um método direto e o serviço de IoT do Azure SDK para .NET implementar um serviço de aplicações que invoca o método direto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robin.shahan
ms.openlocfilehash: 7e7d6fad4268200e5410eb3d3ba3c1e2df06e967
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008615"
---
# <a name="get-started-with-device-management-netnet"></a>Introdução à gestão de dispositivos (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o portal do Azure para criar um IoT Hub e criar uma identidade de dispositivo no IoT hub.

* Crie uma aplicação de dispositivo simulado que contém um método direto que reinicia o que o dispositivo. Os métodos diretos são invocados a partir da cloud.

* Crie uma aplicação de consola .NET que chama o método direto de reinício no aplicativo do dispositivo simulado através do IoT hub.

No final deste tutorial, terá duas aplicações de consola .NET:

* **SimulateManagedDevice**, que liga ao seu hub IoT com a identidade de dispositivo que criou anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e reporta o tempo para a última reinicialização.

* **TriggerReboot**, que chama um método direto na aplicação do dispositivo simulado, apresenta a resposta e apresenta a atualização de propriedades comunicadas.

Para concluir este tutorial, precisa do seguinte:

* O Visual Studio 2017.

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-find-include-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Acionar a reinicialização remota no dispositivo com um método direto

Nesta secção, vai criar uma aplicação de consola do .NET (usando a linguagem c#) inicia um reinício remoto num dispositivo através de um método direto. A aplicação utiliza consultas de dispositivo duplo para detetar a última hora de reinício desse dispositivo.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows a uma solução nova, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Nomeie o projeto **TriggerReboot**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. No Solution Explorer, clique com botão direito a **TriggerReboot** projeto e, em seguida, clique em **gerir pacotes NuGet**.

3. Na **Gestor de pacotes NuGet** janela, selecione **procurar**, procure **Microsoft.Azure.Devices**, selecione **instalar** para instalar o **Microsoft.Azure.Devices** empacotamento e a aceitar os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [SDK do serviço do Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet pacote e suas dependências.

    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Adicione os seguintes campos à classe **Programa**. Substitua o valor de marcador de posição pela cadeia de ligação do IoT Hub para o hub que criou na secção "Criar um hub IoT". 
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Adicione o seguinte método à classe **Programa**.  Este código obtém o dispositivo duplo para o dispositivo ser reiniciado e devolve as propriedades comunicadas.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Adicione o seguinte método à classe **Programa**.  Esse código inicia a reinicialização no dispositivo com um método direto.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

7. Por fim, adicione as seguintes linhas ao método **Main**:
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. Compilar a solução.

> [!NOTE]
> Este tutorial executa apenas uma única consulta para as propriedades comunicadas do dispositivo. No código de produção, recomendamos que a consulta para detectar alterações nas propriedades comunicadas.

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, vai fazer o seguinte:

* Crie uma aplicação de consola .NET que responde a um método direto chamado pela cloud.

* Acione um reinício do dispositivo simulado.

* Utilize as propriedades comunicadas para ativar as consultas de gémeos de dispositivo identificar os dispositivos e quando elas foram reiniciadas pela última vez.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **SimulateManagedDevice**.
   
    ![Nova aplicação de dispositivo Visual c# Windows clássico](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. No Solution Explorer, clique com botão direito a **SimulateManagedDevice** projeto e, em seguida, clique em **gerir pacotes NuGet...** .

3. Na **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **Microsoft.Azure.Devices.Client**. Selecione **instale** para instalar o **Microsoft.Azure.Devices.Client** empacotamento e a aceitar os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [do Azure IoT device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet pacote e suas dependências.
   
    ![Aplicação de cliente de janela do Gestor de pacotes NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação de dispositivo que anotou na secção anterior.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. Adicione o seguinte para implementar o método direto no dispositivo:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. Por fim, adicione o seguinte código para o **Main** método para abrir a ligação ao seu hub IoT e inicializar o serviço de escuta do método:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```
        
8. No Explorador de Soluções do Visual Studio, clique com o botão direito do rato na sua solução e, em seguida, clique em **Definir Projetos de Arranque...**. Selecione **projeto de arranque único**e, em seguida, selecione a **SimulateManagedDevice** projeto no menu pendente. Compilar a solução.       

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [processamento de erros transitórios](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. Para executar a aplicação de dispositivo do .NET **SimulateManagedDevice**. Com o botão direito a **SimulateManagedDevice** projeto, selecione **depurar**e, em seguida, selecione **iniciar nova instância**. Deve começar a ouvir as chamadas de método do seu hub IoT. 

2. Agora que o dispositivo está ligado e à espera de invocações de método, executar o .NET **TriggerReboot** aplicação para invocar o método de reinício no aplicativo do dispositivo simulado. Para tal, clique com botão direito a **TriggerReboot** projeto, selecione **depurar**e, em seguida, selecione **iniciar nova instância**. Deverá ver "Reinício!" escritas no **SimulatedManagedDevice** consola e as propriedades reportadas do dispositivo, que incluem o último reinício tempo, escrito no **TriggerReboot** consola.
   
    ![Executar a aplicação de serviço e dispositivo](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
