---
title: Inicie-se com gémeos de dispositivo Shub Azure IoT (.NET/.NET) [ Microsoft Docs
description: Como utilizar gémeos de dispositivo SoT Hub Azure ioT para adicionar tags e, em seguida, usar uma consulta IoT Hub. Utiliza o dispositivo Azure IoT SDK para a implementação da aplicação simulada do dispositivo e do serviço Azure IoT SDK para .NET implementar uma aplicação de serviço que adiciona as etiquetas e executa a consulta IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733139"
---
# <a name="get-started-with-device-twins-net"></a>Começar com gémeos dispositivo (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Neste tutorial, cria estas aplicações de consola .NET:

* **CreateDeviceIdentity**. Esta aplicação cria uma identidade de dispositivo e uma chave de segurança associada para ligar a aplicação simulada do dispositivo.

* **AddTagsAndQuery**. Esta aplicação back-end adiciona tags e consultas de gémeos dispositivo.

* **ReportConectividade**. Esta aplicação do dispositivo simula um dispositivo que se conecta ao seu hub IoT com a identidade do dispositivo criada anteriormente, e relata a sua condição de conectividade.

> [!NOTE]
> O artigo [Azure IoT SDKs](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs Azure IoT que pode usar para construir aplicações de dispositivos e back-end.
>

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar a app de serviço

Nesta secção, cria-se uma aplicação de consola .NET, utilizando C#, que adiciona metadados de localização ao dispositivo twin associado ao **myDeviceId**. Em seguida, questiona os gémeos do dispositivo armazenados no hub IoT selecionando os dispositivos localizados nos EUA, e depois os que relataram uma ligação celular.

1. No Estúdio Visual, selecione **Criar um novo projeto.** Em **Criar um novo projeto,** selecione App consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Na **Configuração do seu novo projeto,** nomeie o projeto **AddTagsAndQuery**.

    ![Configure o seu projeto AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. No Solution Explorer, clique no projeto **AddTagsAndQuery** e, em seguida, selecione **Gerir pacotes NuGet**.

1. Selecione **Navegar** e procurar e selecionar **Microsoft.Azure.Devices**. Selecione **Instalar**.

    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet do [serviço Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e às suas dependências.

1. Adicione as `using` seguintes declarações na parte superior do ficheiro **Program.cs:**

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua `{iot hub connection string}` com a cadeia de ligação IoT Hub que copiou na cadeia de ligação do [hub IoT](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adicione o seguinte método à classe **Programa:**

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    A classe **RegistryManager** expõe todos os métodos necessários para interagir com gémeos dispositivo sacar do serviço. O código anterior inicialmente inicia o objeto **do RegistryManager,** depois recupera o dispositivo twin para **myDeviceId**, e finalmente atualiza as suas etiquetas com as informações de localização desejadas.

    Após a atualização, executa duas consultas: a primeira seleciona apenas os gémeos dispositivos localizados na fábrica **redmond43,** e o segundo refina a consulta para selecionar apenas os dispositivos que também estão ligados através da rede celular.

    O código anterior, quando cria o objeto **de consulta,** especifica um número máximo de documentos devolvidos. O objeto **de consulta** contém uma propriedade boolean **HasMoreResults** que você pode usar para invocar os métodos **GetNextAsTwinAsync** várias vezes para recuperar todos os resultados. Um método chamado **GetNextAsJson** está disponível para resultados que não são gémeos de dispositivo, por exemplo, resultados de consultas de agregação.

1. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Execute esta aplicação clicando à direita no projeto **AddTagsAndQuery** e selecionando **Debug**, seguido de **Iniciar uma nova instância**. Deve ver um dispositivo nos resultados da consulta que pede todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados a dispositivos que utilizam uma rede celular.

    ![Resultados da consulta na janela](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

Na secção seguinte, cria-se uma aplicação de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação do dispositivo

Nesta secção, cria-se uma aplicação de consola .NET que se conecta ao seu hub como **myDeviceId**, e depois atualiza as suas propriedades reportadas para conter a informação de que está conectado através de uma rede celular.

1. No Estúdio Visual, selecione **File** > **New** > **Project**. Em **Criar um novo projeto,** escolha a App consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Em **Configure o seu novo projeto,** nomeie o projeto **ReportConnectivity**. Para **solução,** escolha **Adicionar à solução**, e, em seguida, selecione **Criar**.

1. No Solution Explorer, clique no projeto **ReportConnectivity** e, em seguida, selecione **Gerir pacotes NuGet**.

1. Selecione **Procurar** e pesquisar e escolher **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

   Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet do [dispositivo Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e às suas dependências.

1. Adicione as `using` seguintes declarações na parte superior do ficheiro **Program.cs:**

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua-a `{device connection string}` pela cadeia de ligação do dispositivo que observou no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Adicione o seguinte método à classe **Programa:**

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    O objeto **Cliente** expõe todos os métodos necessários para interagir com gémeos dispositivos do dispositivo. O código acima indicado inicializa o objeto **Cliente** e, em seguida, recupera o dispositivo twin para **myDeviceId**.

1. Adicione o seguinte método à classe **Programa:**

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   O código acima atualiza a propriedade reportada do **myDeviceId** com a informação de conectividade.

1. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. No Solution Explorer, clique à direita na sua solução e selecione **Definir Projetos StartUp**.

1. No Common **Properties** > **Startup Project,** selecione **Vários projetos de startups.** Para **reportConnectivity,** selecione **Iniciar** como **ação**. Selecione **OK** para guardar as alterações.  

1. Execute esta aplicação clicando no projeto **ReportConnectivity** e selecionando **Debug,** em seguida, **inicie uma nova instância**. Você deve ver a app obtendo a informação gémea, e, em seguida, enviar conectividade como uma ***propriedade reportada***.

    ![Executar aplicativo de dispositivo para relatar conectividade](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Depois de o dispositivo ter comunicado as suas informações de conectividade, deverá aparecer em ambas as consultas.

1. Clique no projeto **AddTagsAndQuery** e selecione **Debug** > **Start nova instância** para executar as consultas novamente. Desta vez, o **myDeviceId** deve aparecer em ambos os resultados da consulta.

    ![Conectividade do dispositivo reportada com sucesso](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionou metadados de dispositivos como tags de uma aplicação de back-end e escreveu uma aplicação simulada de dispositivo para relatar informações de conectividade do dispositivo no dispositivo twin. Também aprendeu a consultar esta informação usando a linguagem de consulta IoT Hub semelhante a SQL.

Pode aprender mais com os seguintes recursos:

* Para aprender a enviar telemetria a partir de dispositivos, consulte a [telemetria Enviar de um dispositivo para um tutorial de hub IoT.](quickstart-send-telemetry-dotnet.md)

* Para aprender a configurar os dispositivos utilizando as propriedades desejadas pelo dispositivo Twin, consulte as [propriedades desejadas para configurar](tutorial-device-twins.md) o tutorial dos dispositivos.

* Para aprender a controlar os dispositivos interativamente, como ligar um ventilador a partir de uma aplicação controlada pelo utilizador, consulte o tutorial de [métodos diretos use.](quickstart-control-device-dotnet.md)
