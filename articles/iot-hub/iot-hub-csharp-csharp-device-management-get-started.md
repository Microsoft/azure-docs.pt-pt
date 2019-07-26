---
title: Introdução ao gerenciamento de dispositivos do Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT do Azure para iniciar uma reinicialização remota do dispositivo. Use o SDK do dispositivo IoT do Azure para .NET para implementar um aplicativo de dispositivo simulado que inclui um método direto e o SDK do serviço IoT do Azure para .NET para implementar um aplicativo de serviço que invoca o método direto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robinsh
ms.openlocfilehash: 40db247dba1d55b5121f95a4d69ca853f3d7ee56
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404576"
---
# <a name="get-started-with-device-management-netnet"></a>Introdução ao gerenciamento de dispositivos (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Use o portal do Azure para criar um hub IoT e criar uma identidade de dispositivo no Hub IoT.

* Crie um aplicativo de dispositivo simulado que contenha um método direto que reinicie esse dispositivo. Os métodos diretos são invocados da nuvem.

* Crie um aplicativo de console .NET que chama o método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

No final deste tutorial, terá duas aplicações de consola .NET:

* **SimulateManagedDevice**, que se conecta ao seu hub IOT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e informa a hora da última reinicialização.

* **TriggerReboot**, que chama um método direto no aplicativo de dispositivo simulado, exibe a resposta e exibe as propriedades relatadas atualizadas.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você cria um aplicativo de console do .NET ( C#usando) que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo de dispositivos para descobrir a hora da última reinicialização para esse dispositivo.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows a uma solução nova, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)** . Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Nomeie o projeto **TriggerReboot**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **TriggerReboot** e clique em **gerenciar pacotes NuGet**.

3. Na janela **Gerenciador de pacotes NuGet** , selecione **procurar**, procure **Microsoft. Azure.** Devices, selecione **instalar** para instalar o pacote **Microsoft. Azure.** Devices e aceite os termos de uso. Este procedimento baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.

    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Adicione o seguinte método à classe **Programa**.  Esse código obtém o dispositivo "fileplace" para o dispositivo de reinicialização e gera as propriedades relatadas.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Adicione o seguinte método à classe **Programa**.  Esse código inicia a reinicialização no dispositivo usando um método direto.

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
> Este tutorial executa apenas uma única consulta para as propriedades relatadas do dispositivo. No código de produção, recomendamos a sondagem para detectar alterações nas propriedades relatadas.

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, vai fazer o seguinte:

* Crie um aplicativo de console .NET que responda a um método direto chamado pela nuvem.

* Disparar uma reinicialização de dispositivo simulado.

* Use as propriedades relatadas para habilitar consultas de dispositivo de dispositivos para identificar os dispositivos e quando eles foram reinicializados pela última vez.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **SimulateManagedDevice**.
   
    ![Novo aplicativo C# de dispositivo clássico do Visual Windows](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulateManagedDevice** e clique em **gerenciar pacotes NuGet...** .

3. Na janela **Gerenciador de pacotes NuGet** , selecione **procurar** e procure **Microsoft. Azure. Devices. Client**. Selecione **instalar** para instalar o pacote **Microsoft. Azure. Devices. Client** e aceite os termos de uso. Este procedimento baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do dispositivo IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e suas dependências.
   
    ![Aplicativo cliente da janela do Gerenciador de pacotes NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do espaço reservado pela cadeia de conexão do dispositivo que você anotou na seção anterior.

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

7. Por fim, adicione o seguinte código ao método **Main** para abrir a conexão ao seu hub IOT e inicializar o ouvinte do método:

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
        
8. No Explorador de Soluções do Visual Studio, clique com o botão direito do rato na sua solução e, em seguida, clique em **Definir Projetos de Arranque...** . Selecione **único projeto de inicialização**e, em seguida, selecione o projeto **SimulateManagedDevice** no menu suspenso. Compilar a solução.       

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. Para executar o aplicativo de dispositivo .NET **SimulateManagedDevice**. Clique com o botão direito do mouse no projeto **SimulateManagedDevice** , selecione **depurar**e selecione **Iniciar nova instância**. Ele deve começar a escutar chamadas de método de seu hub IoT. 

2. Agora que o dispositivo está conectado e aguardando invocações de método, execute o aplicativo .NET **TriggerReboot** para invocar o método de reinicialização no aplicativo de dispositivo simulado. Para fazer isso, clique com o botão direito do mouse no projeto **TriggerReboot** , selecione **depurar**e, em seguida, selecione **Iniciar nova instância**. Você deve ver "reinicializando!" escrito no console do **SimulatedManagedDevice** e as propriedades relatadas do dispositivo, que incluem a hora da última reinicialização, gravada no console do **TriggerReboot** .
   
    ![Execução do aplicativo de serviço e dispositivo](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
