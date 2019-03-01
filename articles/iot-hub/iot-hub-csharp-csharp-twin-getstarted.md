---
title: Introdução aos duplos de dispositivo do IoT Hub do Azure (.NET/.NET) | Documentos da Microsoft
description: Como utilizar o gémeos de dispositivo do IoT Hub do Azure para adicionar etiquetas e, em seguida, utilizar uma consulta do IoT Hub. Utilizar o Azure IoT device SDK para .NET para implementar a aplicação de dispositivo simulado e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que adiciona as etiquetas e executa a consulta do IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: robin.shahan
ms.openlocfilehash: 63ec161f2f0d8be4572acf456c81e19ca75bd856
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010740"
---
# <a name="get-started-with-device-twins-netnet"></a>Introdução aos dispositivos duplos (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terá estas aplicações de consola .NET:

* **CreateDeviceIdentity**, uma aplicação .NET que cria uma identidade de dispositivo e a chave de segurança associada para ligar a aplicação de dispositivo simulado.

* **AddTagsAndQuery**, uma aplicação de back-end de .NET que adiciona as etiquetas e consultas de gémeos de dispositivo.

* **ReportConnectivity**, uma aplicação de dispositivo do .NET que simula um dispositivo que liga ao seu hub IoT com a identidade de dispositivo que criou anteriormente e reporta sua condição de conectividade.

> [!NOTE]
> O artigo [do Azure IoT SDKs](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivos e back-end.
> 

Para concluir este tutorial, precisa do seguinte:

* O Visual Studio 2017.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Criar a aplicação de serviço

Nesta secção, vai criar uma aplicação de consola do .NET (usando a linguagem c#) adiciona os metadados de localização para o dispositivo duplo associado **myDeviceId**. Ele consulta, em seguida, os dispositivos duplos armazenados no hub IoT selecionar os dispositivos localizados nos Estados Unidos e, em seguida, aqueles que comunicou uma conexão por celular.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **AddTagsAndQuery**.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. No Solution Explorer, clique com botão direito a **AddTagsAndQuery** projeto e, em seguida, clique em **gerir pacotes NuGet...** .

3. Na **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **Microsoft.Azure.Devices**. Selecione **instale** para instalar o **Microsoft.Azure.Devices** empacotamento e a aceitar os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [SDK do serviço do Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet pacote e suas dependências.
   
    ![Janela Gestor de Pacote NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção anterior.

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
   
    O **RegistryManager** classe expõe todos os métodos necessários para interagir com dispositivos duplos do serviço. O código anterior primeiro inicializa a **registryManager** de objeto, em seguida, obtém o dispositivo duplo **myDeviceId**e, finalmente, atualiza as marcas com as informações de localização pretendida.
   
    Depois de atualizar, ele executa duas consultas: o primeiro seleciona apenas os dispositivos duplos de dispositivos localizados no **Redmond43** plant e o segundo Refina a consulta para selecionar apenas os dispositivos que também estão ligados através de rede celular.
   
    Tenha em atenção que o código anterior, quando cria o **consulta** de objeto, especifica um número máximo de documentos devolvidos. O **consulta** objeto contém um **HasMoreResults** propriedade booleana que pode usar para invocar o **GetNextAsTwinAsync** métodos múltiplas vezes para obter todos os resultados. Um método chamado **GetNextAsJson** está disponível para os resultados que são não dispositivos duplos, por exemplo, os resultados de consultas de agregação.

7. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. No Solution Explorer, abra o **definir projetos de arranque...**  e certifique-se de que o **ação** para **AddTagsAndQuery** projeto é **iniciar**. Compilar a solução.

9. Execute esta aplicação ao clicar na **AddTagsAndQuery** projeto e selecionando **depurar**, seguido **iniciar nova instância**. Deverá ver um dispositivo nos resultados para fazer a consulta para todos os dispositivos localizados num **Redmond43** e nenhuma durante a consulta que restringe os resultados para os dispositivos que utilizam uma rede celular.
   
    ![Resultados da consulta na janela](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

A secção seguinte, vai criar uma aplicação de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação de dispositivo

Nesta secção, vai criar uma aplicação de consola .NET que liga ao seu hub como **myDeviceId**e, em seguida, atualiza as respetivas propriedades comunicadas para conter as informações que está ligado através de uma rede celular.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **ReportConnectivity**.
   
    ![Nova aplicação de dispositivo Visual c# Windows clássico](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. No Solution Explorer, clique com botão direito a **ReportConnectivity** projeto e, em seguida, clique em **gerir pacotes NuGet...** .

3. Na **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **Microsoft.Azure.Devices.Client**. Selecione **instale** para instalar o **Microsoft.Azure.Devices.Client** empacotamento e a aceitar os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [do Azure IoT device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet pacote e suas dependências.
   
    ![Aplicação de cliente de janela do Gestor de pacotes NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação de dispositivo que anotou na secção anterior.

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

    O **cliente** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do dispositivo. O código mostrado acima, inicializa os **cliente** objeto e, em seguida, obtém o dispositivo duplo **myDeviceId**.

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

   O código acima atualizações **myDeviceId**do comunicado a propriedade com as informações de conectividade.

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

9. No Solution Explorer, abra o **definir projetos de arranque...**  e certifique-se de que o **ação** para **ReportConnectivity** projeto é **iniciar**. Compilar a solução.

10. Execute esta aplicação ao clicar na **ReportConnectivity** projeto e selecionando **depurar**, seguido **iniciar nova instância**. Deverá ver obter as informações de duplo e, em seguida, envio de conectividade como um *reportado propriedade*.
   
    ![Executar a aplicação de dispositivo para a conectividade de relatório](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Agora que o dispositivo comunicado suas informações de conectividade, deverá aparecer em ambas as consultas. Executar o .NET **AddTagsAndQuery** app para executar as consultas novamente. Neste momento **myDeviceId** deve aparecer em ambos os resultados da consulta.
   
    ![Conectividade do dispositivo comunicada com êxito](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionados metadados do dispositivo como etiquetas a partir de uma aplicação de back-end e escreveu uma aplicação de dispositivo simulado para informações de conectividade do dispositivo de relatório no dispositivo duplo. Também aprendeu como consultar essas informações usando a linguagem de consulta do Hub de IoT de tipo SQL.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md) tutorial,

* configurar dispositivos com as propriedades pretendidas do duplo do dispositivo com o [utilização pretendida propriedades para configurar dispositivos](tutorial-device-twins.md) tutorial,

* Controlar dispositivos interativamente (como ativar um fã de uma aplicação controlada pelo utilizador) com o [utilizar métodos diretos](quickstart-control-device-dotnet.md) tutorial.
