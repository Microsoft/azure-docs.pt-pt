---
title: Inicie-se com a gestão de dispositivos Azure IoT Hub (.NET/.NET) [ Microsoft Docs
description: Como utilizar a gestão do dispositivo Azure IoT Hub para iniciar um reboot remoto do dispositivo. Utiliza o dispositivo Azure IoT SDK para a implementação de uma aplicação de dispositivo simulado que inclui um método direto e o serviço Azure IoT SDK para .NET implementar uma aplicação de serviço que invoque o método direto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 3cc74faa39b21b1ab275149db4f85de8f55fd07e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733479"
---
# <a name="get-started-with-device-management-net"></a>Começar com a gestão do dispositivo (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o portal Azure para criar um hub IoT e criar uma identidade de dispositivo no seu hub IoT.

* Crie uma aplicação simulada de dispositivo que contenha um método direto que reinicie esse dispositivo. Os métodos diretos são invocados da nuvem.

* Crie uma aplicação de consola .NET que chame o método direct reboot na aplicação simulada do dispositivo através do seu hub IoT.

No final deste tutorial, terá duas aplicações de consola .NET:

* **Simular O Dispositivo Gerido**. Esta aplicação liga-se ao seu hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula um reboot físico e relata o tempo para o último reboot.

* **TriggerReboot**. Esta aplicação chama um método direto na aplicação simulada do dispositivo, exibe a resposta e exibe as propriedades reportadas atualizadas.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Desencadear um reboot remoto no dispositivo utilizando um método direto

Nesta secção, cria-se uma aplicação de consola .NET, utilizando C#, que inicia uma reinicialização remota num dispositivo utilizando um método direto. A aplicação utiliza consultas de gémeos dispositivos para descobrir o último tempo de reinício para esse dispositivo.

1. No Estúdio Visual, selecione **Criar um novo projeto.**

1. Em **Criar um novo projeto,** encontre e selecione o modelo de projeto da App consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** nomeie o projeto *TriggerReboot*e selecione .NET Framework versão 4.5.1 ou posterior. Selecione **Criar**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. No **Solution Explorer,** clique à direita no projeto **TriggerReboot** e, em seguida, selecione **Gerir pacotes NuGet**.

1. Selecione **Browse,** em seguida, procure e selecione **Microsoft.Azure.Devices**. Selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.**

    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet do [serviço Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e às suas dependências.

1. Adicione as `using` seguintes declarações na parte superior do ficheiro **Program.cs:**

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Adicione os seguintes campos à classe **Programa**. Substitua `{iot hub connection string}` o valor do espaço reservado pela cadeia de ligação IoT Hub que copiou anteriormente na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Adicione o seguinte método à classe **Programa**.  Este código fica com o dispositivo gémeo para o dispositivo de reinicialização e produz as propriedades reportadas.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Adicione o seguinte método à classe **Programa**.  Este código inicia o reboot no dispositivo utilizando um método direto.

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

1. Selecione **Build Build** > **Solution**.

> [!NOTE]
> Este tutorial realiza apenas uma única consulta para as propriedades reportadas do dispositivo. No código de produção, recomendamos que as sondagens detetem alterações nas propriedades reportadas.

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, pode:

* Crie uma aplicação de consola .NET que responda a um método direto chamado pela nuvem.

* Desencadear uma simulação de reinicialização do dispositivo.

* Utilize as propriedades reportadas para permitir que as consultas gémeas do dispositivo identifiquem os dispositivos e quando foram reiniciadas pela última vez.

Para criar a aplicação simulada do dispositivo, siga estes passos:

1. No Estúdio Visual, na solução TriggerReboot já criou, selecione **File** > **New** > **Project**. Em **Criar um novo projeto,** encontre e selecione o modelo de projeto da App consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** nomeie o projeto *Simular ManagedDevice*, e para **Solução,** selecione **Adicionar à solução**. Selecione **Criar**.

    ![Nomeie e adicione o seu projeto à solução](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. No Solution Explorer, clique no novo projeto **SimulateManagedDevice** e, em seguida, selecione **Gerir pacotes NuGet**.

1. Selecione **Browse,** em seguida, procure e selecione **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

    ![Aplicativo cliente de cliente de janela nuGet Package Manager](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet do [dispositivo Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e às suas dependências.

1. Adicione as `using` seguintes declarações na parte superior do ficheiro **Program.cs:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua `{device connection string}` o valor do espaço reservado pela cadeia de ligação do dispositivo que observou anteriormente no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

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

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Por fim, adicione o seguinte código ao método **Principal** para abrir a ligação ao seu hub IoT e inicializar o ouvinte do método:

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

1. No Solution Explorer, clique à direita na sua solução e, em seguida, selecione **Definir Projetos StartUp**.

1. Para **o** > Common Properties**Startup Project,** selecione um projeto de arranque **único**e, em seguida, selecione o projeto **SimulamanagedDevice.** Selecione **OK** para guardar as alterações.

1. Selecione **Build Build** > **Solution**.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar políticas de retry (como um backoff exponencial), como sugerido no manuseamento de [falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Executar as aplicações

Está pronto para executar as aplicações.

1. Para executar a aplicação de dispositivo .NET **SimularManagedDevice**, no Solution Explorer, clique no direito do projeto **SimulateManagedDevice,** selecione **Debug**, e, em seguida, selecione **Iniciar uma nova instância**. A aplicação deve começar a ouvir chamadas de métodos do seu hub IoT.

1. Depois disso, o dispositivo está ligado e à espera de invocações de métodos, clique à direita no projeto **TriggerReboot,** selecione **Debug**, e, em seguida, selecione **Iniciar uma nova instância**.

   Devias ver "Reiniciar!" escrita na consola **SimuladManagedDevice** e nas propriedades reportadas do dispositivo, que incluem o último tempo de reinício, escrito na consola **TriggerReboot.**

    ![Execução de aplicativo de serviço e dispositivo](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
