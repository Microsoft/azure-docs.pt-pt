---
title: Introdução ao dispositivo gêmeos do Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como usar o dispositivo gêmeos do Hub IoT do Azure para adicionar marcas e, em seguida, usar uma consulta do Hub IoT. Use o SDK do dispositivo IoT do Azure para .NET para implementar o aplicativo de dispositivo simulado e o SDK do serviço IoT do Azure para .NET para implementar um aplicativo de serviço que adiciona as marcas e executa a consulta do Hub IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: robinsh
ms.openlocfilehash: c1b8f60fd155cf9bce0b999da7459299b6f3c7aa
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404521"
---
# <a name="get-started-with-device-twins-netnet"></a>Introdução ao dispositivo gêmeos (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, você terá estes aplicativos de console .NET:

* **CreateDeviceIdentity**, um aplicativo .NET que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o aplicativo do dispositivo simulado.

* **AddTagsAndQuery**, um aplicativo de back-end .NET que adiciona marcas e consultas ao dispositivo gêmeos.

* **ReportConnectivity**, um aplicativo de dispositivo .NET que simula um dispositivo que se conecta ao Hub IOT com a identidade do dispositivo criada anteriormente e relata sua condição de conectividade.

> [!NOTE]
> O artigo [SDKs do IOT do Azure](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs do IOT do Azure que você pode usar para compilar dispositivos e aplicativos de back-end.
> 

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio.
* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo de serviço

Nesta seção, você criará um aplicativo de console do .NET C#(usando) que adiciona metadados de local ao dispositivo de entrelaçamento associado a mydeviceid. Em seguida, ele consulta o dispositivo gêmeos armazenado no Hub IoT selecionando os dispositivos localizados nos EUA e, em seguida, aqueles que relataram uma conexão celular.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **AddTagsAndQuery**.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **AddTagsAndQuery** e clique em **gerenciar pacotes NuGet...** .

3. Na janela **Gerenciador de pacotes NuGet** , selecione **procurar** e procure **Microsoft. Azure.** Devices. Selecione **instalar** para instalar o pacote **Microsoft. Azure.** Devices e aceite os termos de uso. Este procedimento baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.
   
    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

6. Adicione o seguinte método à classe **Programa**:

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
   
    A  classe registrymanager expõe todos os métodos necessários para interagir com o dispositivo gêmeos do serviço. Primeiro, o código anterior Inicializa  o objeto registrymanager e, em seguida, recupera o dispositivo "FileUp" para DeviceID e, por fim, atualiza suas marcas com as informações de localização desejadas.
   
    Após a atualização, ele executa duas consultas: a primeira seleciona apenas o dispositivo gêmeos de dispositivos localizados na fábrica **Redmond43** e o segundo refina a consulta para selecionar apenas os dispositivos que também estão conectados por meio da rede celular.
   
    Observe que o código anterior, quando ele cria o objeto de **consulta** , especifica um número máximo de documentos retornados. O objeto **Query** contém uma propriedade booliana **HasMoreResults** que você pode usar para invocar os métodos **os getnextastwinasync** várias vezes para recuperar todos os resultados. Um método chamado **GetNextAsJson** está disponível para resultados que não são dispositivos gêmeos, por exemplo, resultados de consultas de agregação.

7. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. Na Gerenciador de Soluções, abra **definir projetos de inicialização...** e certifique-se de que a **ação** para o projeto **AddTagsAndQuery** seja **iniciada**. Compilar a solução.

9. Execute esse aplicativo clicando com o botão direito do mouse no projeto **AddTagsAndQuery** e selecionando **depurar**, seguido por **Iniciar nova instância**. Você deve ver um dispositivo nos resultados para a consulta solicitando todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.
   
    ![Resultados da consulta na janela](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

Na próxima seção, você cria um aplicativo de dispositivo que relata as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo

Nesta seção, você cria um aplicativo de console do .NET que se conecta ao seu Hub como DeviceID e, em seguida, atualiza suas propriedades relatadas para conter as informações que ele está conectado usando uma rede de celular.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **ReportConnectivity**.
   
    ![Novo aplicativo C# de dispositivo clássico do Visual Windows](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ReportConnectivity** e clique em **gerenciar pacotes NuGet...** .

3. Na janela **Gerenciador de pacotes NuGet** , selecione **procurar** e procure **Microsoft. Azure. Devices. Client**. Selecione **instalar** para instalar o pacote **Microsoft. Azure. Devices. Client** e aceite os termos de uso. Este procedimento baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do dispositivo IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e suas dependências.
   
    ![Aplicativo cliente da janela do Gerenciador de pacotes NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do espaço reservado pela cadeia de conexão do dispositivo que você anotou na seção anterior.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;
      DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Adicione o seguinte método à classe **Programa**:

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

    O objeto **cliente** expõe todos os métodos necessários para interagir com dispositivos gêmeos do dispositivo. O código mostrado acima Inicializa o objeto **cliente** e, em seguida, recupera o **dispositivo.**

7. Adicione o seguinte método à classe **Programa**:

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

   O código acima atualiza a propriedade relatada de mydeviceid com as informações de conectividade.

8. Por fim, adicione as seguintes linhas ao método **Main**:

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

9. Na Gerenciador de Soluções, abra **definir projetos de inicialização...** e certifique-se de que a **ação** para o projeto **ReportConnectivity** seja **iniciada**. Compilar a solução.

10. Execute esse aplicativo clicando com o botão direito do mouse no projeto **ReportConnectivity** e selecionando **depurar**, seguido por **Iniciar nova instância**. Você deve ver que ele obtém as informações de conexão e, em seguida, enviando a conectividade como uma *Propriedade*relatada.
   
    ![Executar o aplicativo de dispositivo para relatar a conectividade](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Agora que o dispositivo relatou suas informações de conectividade, ele deve aparecer em ambas as consultas. Execute o aplicativo .NET **AddTagsAndQuery** para executar as consultas novamente. Dessa vez  , mydeviceid deve aparecer nos dois resultados da consulta.
   
    ![Conectividade do dispositivo relatada com êxito](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Você adicionou metadados de dispositivo como marcas de um aplicativo de back-end e gravou um aplicativo de dispositivo simulado para relatar informações de conectividade do dispositivo no dispositivo. Você também aprendeu como consultar essas informações usando a linguagem de consulta do Hub IoT do tipo SQL.

Use os seguintes recursos para aprender a:

* Enviar telemetria de dispositivos com o tutorial [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md) ,

* configurar dispositivos usando as propriedades desejadas do dispositivo ' s ' com o tutorial [usar propriedades desejadas para configurar dispositivos](tutorial-device-twins.md) ,

* controlar dispositivos interativamente (como ativar um ventilador de um aplicativo controlado pelo usuário) com o tutorial [usar métodos diretos](quickstart-control-device-dotnet.md) .
