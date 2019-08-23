---
title: Introdução ao gerenciamento de dispositivos do Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT do Azure para iniciar uma reinicialização remota do dispositivo. Use o SDK do dispositivo IoT do Azure para .NET para implementar um aplicativo de dispositivo simulado que inclui um método direto e o SDK do serviço IoT do Azure para .NET para implementar um aplicativo de serviço que invoca o método direto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 5a0be55df9be67fcf6ff5d53e18e3eb2b0e69d7f
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904467"
---
# <a name="get-started-with-device-management-net"></a>Introdução ao gerenciamento de dispositivos (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Use o portal do Azure para criar um hub IoT e criar uma identidade de dispositivo no Hub IoT.

* Crie um aplicativo de dispositivo simulado que contenha um método direto que reinicie esse dispositivo. Os métodos diretos são invocados da nuvem.

* Crie um aplicativo de console .NET que chama o método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

No final deste tutorial, terá duas aplicações de consola .NET:

* **SimulateManagedDevice**. Esse aplicativo se conecta ao Hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e informa a hora da última reinicialização.

* **TriggerReboot**. Esse aplicativo chama um método direto no aplicativo de dispositivo simulado, exibe a resposta e exibe as propriedades relatadas atualizadas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Visual Studio.

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você cria um aplicativo de console do .NET, C#usando o, que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo de dispositivos para descobrir a hora da última reinicialização para esse dispositivo.

1. No Visual Studio, selecione **criar um novo projeto**.

1. Em **criar um novo projeto**, localize e selecione o modelo de projeto **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Em **configurar seu novo projeto**, nomeie o projeto *TriggerReboot*e selecione .NET Framework versão 4.5.1 ou posterior. Selecione **Criar**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **TriggerReboot** e selecione **gerenciar pacotes NuGet**.

1. Selecione **procurar**e, em seguida, pesquise e selecione **Microsoft. Azure.** Devices. Selecione **instalar** para instalar o pacote **Microsoft. Azure.** Devices.

    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.

1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o `{iot hub connection string}` valor do espaço reservado pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Adicione o seguinte método à classe **Programa**.  Esse código obtém o dispositivo "fileplace" para o dispositivo de reinicialização e gera as propriedades relatadas.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Adicione o seguinte método à classe **Programa**.  Esse código inicia a reinicialização no dispositivo usando um método direto.

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

1. Por fim, adicione as seguintes linhas ao método **Main**:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Selecione **criar** > **solução de compilação**.

> [!NOTE]
> Este tutorial executa apenas uma única consulta para as propriedades relatadas do dispositivo. No código de produção, recomendamos a sondagem para detectar alterações nas propriedades relatadas.

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, pode:

* Crie um aplicativo de console .NET que responda a um método direto chamado pela nuvem.

* Disparar uma reinicialização de dispositivo simulado.

* Use as propriedades relatadas para habilitar consultas de dispositivo de dispositivos para identificar os dispositivos e quando eles foram reinicializados pela última vez.

Para criar o aplicativo de dispositivo simulado, siga estas etapas:

1. No Visual Studio, na solução TriggerReboot que você já criou, selecione **arquivo** > **novo** > **projeto**. Em **criar um novo projeto**, localize e selecione o modelo de projeto **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Em **configurar seu novo projeto**, nomeie o projeto *SimulateManagedDevice*e, para **solução**, selecione **Adicionar à solução**. Selecione **Criar**.

    ![Nomeie e adicione seu projeto à solução](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no novo projeto **SimulateManagedDevice** e selecione **gerenciar pacotes NuGet**.

1. Selecione **procurar**e, em seguida, pesquise e selecione **Microsoft. Azure. Devices. Client**. Selecione **Instalar**.

    ![Aplicativo cliente da janela do Gerenciador de pacotes NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do dispositivo IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e suas dependências.

1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o `{device connection string}` valor do espaço reservado pela cadeia de conexão do dispositivo que você anotou anteriormente em [registrar um novo dispositivo no Hub IOT](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Adicione o seguinte para implementar o método direto no dispositivo:

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

1. Por fim, adicione o seguinte código ao método **Main** para abrir a conexão ao seu hub IOT e inicializar o ouvinte do método:

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

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em sua solução e selecione **definir projetos de inicialização**.

1. Para o**projeto de inicialização**de **Propriedades** > comuns, selecione **projeto de inicialização única**e, em seguida, selecione o projeto **SimulateManagedDevice** . Selecione **OK** para guardar as alterações.

1. Selecione **criar** > **solução de compilação**.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como uma retirada exponencial), conforme sugerido em [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Executar as aplicações

Agora você está pronto para executar os aplicativos.

1. Para executar o aplicativo de dispositivo do .NET **SimulateManagedDevice**, em Gerenciador de soluções, clique com o botão direito do mouse no projeto **SimulateManagedDevice** , selecione **depurar**e, em seguida, selecione **Iniciar nova instância**. O aplicativo deve começar a escutar chamadas de método de seu hub IoT.

1. Depois que o dispositivo estiver conectado e aguardando invocações de método, clique com o botão direito do mouse no projeto **TriggerReboot** , selecione **depurar**e, em seguida, selecione **Iniciar nova instância**.

   Você deve ver "reinicializando!" escrito no console do **SimulatedManagedDevice** e as propriedades relatadas do dispositivo, que incluem a hora da última reinicialização, gravada no console do **TriggerReboot** .

    ![Execução do aplicativo de serviço e dispositivo](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
