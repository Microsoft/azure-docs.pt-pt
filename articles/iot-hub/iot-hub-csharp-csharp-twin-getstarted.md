---
title: Começa com os gémeos Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Como usar gémeos de dispositivo Azure IoT Hub para adicionar tags e, em seguida, usar uma consulta IoT Hub. Utiliza o dispositivo Azure IoT SDK para .NET para implementar a aplicação de dispositivo simulado e o serviço Azure IoT SDK para .NET para implementar uma aplicação de serviço que adiciona as tags e executa a consulta IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 267a69486dc91ef95c0de704346eeb1d1780ef48
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89013763"
---
# <a name="get-started-with-device-twins-net"></a>Começar com os gémeos do dispositivo (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Neste tutorial, cria-se estas aplicações de consola .NET:

* **AddTagsAndQuery**. Esta aplicação de back-end adiciona etiquetas e consultas de dispositivo gémeos.

* **ReportConnectivity**. Esta aplicação de dispositivo simula um dispositivo que se conecta ao seu hub IoT com a identidade do dispositivo criada anteriormente, e relata a sua condição de conectividade.

> [!NOTE]
> O artigo [Azure IoT SDKs](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs Azure IoT que você pode usar para construir tanto dispositivos como aplicações de back-end.
>

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar a app de serviço

Nesta secção, cria-se uma aplicação de consola .NET, utilizando C#, que adiciona metadados de localização ao dispositivo twin associado ao **myDeviceId**. Em seguida, consulta os gémeos do dispositivo armazenados no hub IoT, selecionando os dispositivos localizados nos EUA, e depois os que reportaram uma ligação celular.

1. No Visual Studio, **selecione Criar um novo projeto.** In **Create new project**, selecione Console App **(.NET Framework)** e, em seguida, selecione **Next**.

1. In **Configure o seu novo projeto**, nomeie o projeto **AddTagsAndQuery**.

    ![Configure o seu projeto AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. No Solution Explorer, clique com o botão direito no projeto **AddTagsAndQuery** e, em seguida, **selecione Gerir pacotes NuGet**.

1. **Selecione Procurar** e procurar e selecione **Dispositivos Microsoft.Azure..** Selecione **Instalar**.

    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet [do serviço Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e às suas dependências.

1. Adicione as `using` seguintes declarações no topo do ficheiro **.cs Programa:**

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Adicione os seguintes campos à classe **Programa**. `{iot hub connection string}`Substitua-a pela cadeia de ligação IoT Hub que copiou na [cadeia de ligação do hub IoT](#get-the-iot-hub-connection-string).

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

    A classe **RegistryManager** expõe todos os métodos necessários para interagir com os gémeos do dispositivo do serviço. O código anterior inicializa primeiro o objeto **Descente** de Registo, depois recupera o dispositivo twin para **o myDeviceId**, e finalmente atualiza as suas etiquetas com as informações de localização desejadas.

    Após a atualização, executa duas consultas: a primeira seleciona apenas os gémeos do dispositivo dos dispositivos localizados na fábrica **Redmond43,** e a segunda refina a consulta para selecionar apenas os dispositivos que também estão ligados através da rede celular.

    O código anterior, quando cria o objeto **de consulta,** especifica um número máximo de documentos devolvidos. O objeto **de consulta** contém uma propriedade booleana **HasMoreResults** que você pode usar para invocar os métodos **GetNextAsTwinAsync** várias vezes para recuperar todos os resultados. Um método chamado **GetNextAsJson** está disponível para resultados que não são gémeos de dispositivo, por exemplo, resultados de consultas de agregação.

1. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Execute esta aplicação clicando à direita no projeto **AddTagsAndQuery** e selecione **Debug**, seguido por **Iniciar nova instância**. Deverá ver um dispositivo nos resultados da consulta pedindo todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados a dispositivos que utilizam uma rede celular.

    ![Consulta resulta na janela](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

Na secção seguinte, cria-se uma aplicação para dispositivos que relata as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação do dispositivo

Nesta secção, cria-se uma aplicação de consola .NET que se conecta ao seu hub como **myDeviceId**, e atualiza as suas propriedades reportadas para conter a informação que está ligada através de uma rede celular.

1. No Estúdio Visual, selecione **File**  >  **New**  >  **Project**. In **Create new project**, escolha App Consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto**, nomeie o projeto **ReportConnectivity.** Para **solução**, escolha **Adicionar à solução** e, em seguida, selecione **Criar**.

1. No Solution Explorer, clique com o botão direito no projeto **ReportConnectivity** e, em seguida, **selecione Gerir Pacotes NuGet**.

1. **Selecione Procurar** e procurar e escolher **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

   Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet [do dispositivo Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e às suas dependências.

1. Adicione as `using` seguintes declarações no topo do ficheiro **.cs Programa:**

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Adicione os seguintes campos à classe **Programa**. `{device connection string}`Substitua-a pela cadeia de ligação do dispositivo que notou no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

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

    O objeto **Cliente** expõe todos os métodos necessários para interagir com os gémeos do dispositivo a partir do dispositivo. O código acima indicado inicializa o objeto **Cliente** e, em seguida, recupera o dispositivo twin para **o myDeviceId**.

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

   O código acima atualiza a propriedade reportada do  **myDeviceId** com a informação de conectividade.

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

1. No Solution Explorer, clique com o botão direito na sua solução e selecione **set StartUp Projects**.

1. No **Common Properties** Startup  >  **Project,** selecione **Vários projetos de startups.** Para **ReportConnectivity**, selecione **Start** as **the Action**. Selecione **OK** para guardar as alterações.  

1. Executar esta aplicação clicando à direita no projeto **ReportConnectivity** e selecionando **Debug**, em seguida, **Inicie uma nova instância**. Você deve ver a app recebendo a informação gémea e, em seguida, enviando conectividade como uma **_propriedade relatada_**.

    ![Executar aplicativo de dispositivo para reportar conectividade](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Depois de o dispositivo ter comunicado as suas informações de conectividade, deve aparecer em ambas as consultas.

1. Clique com o direito no projeto **AddTagsAndQuery** e selecione **Debug**  >  **Inicie nova instância** para executar novamente as consultas. Desta vez, **o myDeviceId** deve aparecer em ambos os resultados de consulta.

    ![Conectividade do dispositivo reportada com sucesso](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionou metadados de dispositivo como tags de uma aplicação de back-end, e escreveu uma aplicação de dispositivo simulado para reportar informações de conectividade do dispositivo no twin do dispositivo. Você também aprendeu a consultar esta informação usando a linguagem de consulta IoT Hub semelhante ao SQL.

Pode aprender mais com os seguintes recursos:

* Para aprender a enviar telemetria a partir de dispositivos, consulte a [telemetria Enviar de um dispositivo para um tutorial de hub IoT.](quickstart-send-telemetry-dotnet.md)

* Para aprender a configurar dispositivos utilizando as propriedades desejadas pelo dispositivo Twin, consulte as [propriedades desejadas para configurar os dispositivos](tutorial-device-twins.md) tutoriais.

* Para aprender a controlar os dispositivos de forma interativa, como ligar um ventilador a partir de uma aplicação controlada pelo utilizador, consulte o tutorial [de métodos diretos Use.](quickstart-control-device-dotnet.md)
