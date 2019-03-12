---
title: Introdução à identidade de módulo e ao módulo duplo do Hub IoT do Azure (portal e .NET)| Microsoft Docs
description: Saiba como criar a identidade de módulo e atualizar o módulo duplo com o portal e o .NET.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: f323eb777d98a812ead8dd8ac78ee3d6b36a748a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545825"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Introdução à identidade de módulo e ao módulo duplo do Hub IoT com o portal e o dispositivo .NET

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo duplo do Hub IoT do Azure permitem que a aplicação de back-end configure um dispositivo e conferem visibilidade às condições do dispositivo, uma identidade de módulo e o módulo duplo fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, permitem a configuração e condições isoladas para cada componente.
>

Neste tutorial, irá aprender:

1. como criar uma identidade de módulo no portal. 

2. como utilizar o módulo duplo a partir do seu dispositivo de atualização do SDK de dispositivo de .NET.

> [!NOTE]
> Para informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end, veja [Azure IoT SDKs][lnk-hub-sdks].
>

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Criar uma identidade de módulo no portal

Numa identidade do dispositivo, pode criar até 20 identidades de módulo. Clique no botão **Adicionar Identidade de Módulo** na parte superior para criar a sua primeira identidade de módulo denominada **myFirstModule**. 

  ![Detalhes do dispositivo][9]

Guarde e clique na identidade de módulo acabada de criar. Pode ver os detalhes da identidade de módulo. Guarde a Cadeia de ligação - chave primária. Será utilizada na secção seguinte onde configura o módulo no dispositivo.

  ![Detalhes do dispositivo][12]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo duplo com o SDK de dispositivo .NET

Criou a identidade de módulo com êxito no seu Hub IoT. Vamos tentar comunicar com a cloud a partir do seu dispositivo simulado. Quando uma identidade de módulo é criada, é criado implicitamente um módulo duplo no Hub IoT. Nesta secção, irá criar uma aplicação de consola .NET no seu dispositivo simulado que atualiza as propriedades reportadas do módulo duplo.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

No Visual Studio, adicione um projeto do Visual c# Windows ambiente de trabalho clássico à solução existente, utilizando o **aplicação de consola (.NET Framework)** modelo de projeto. Certifique-se de que tem a versão 4.6.1 ou posterior do .NET Framework. Atribua o nome **UpdateModuleTwinReportedProperties** ao projeto.

  ![Criar um projeto do Visual Studio][13]

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Instalar o dispositivo de Hub de IoT do Azure .NET SDK mais recente

Módulo duplo de identidade e o módulo está em pré-visualização pública. Só está disponível no dispositivo de pré-lançamento de IoT Hub SDKs. No Visual Studio, abra tools > Nuget package manager > manage Nuget packages for solution. Procure Microsoft.Azure.Devices.Client. Certifique-se de que selecionou a caixa de verificação Include prerelease. Selecione a versão mais recente e instale. Agora, tem acesso a todas as funcionalidades de módulo. 

  ![Instalar o SDK de serviço .NET do Hub IoT do Azure V1.16.0-preview-005][14]

## <a name="get-your-module-connection-string"></a>Obter a cadeia de ligação do módulo

Inicie sessão no [portal do Azure][lnk-portal]. Navegue até ao seu Hub IoT e clique em Dispositivos IoT. Encontre myFirstDevice, abra-lo e ver myFirstModule foi criado com êxito. Copie a cadeia de ligação do módulo. É necessária para o próximo passo.

  ![Detalhe do módulo no portal do Azure][15]

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Criar aplicação de consola UpdateModuleTwinReportedProperties

Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do módulo.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

Adicione o seguinte método **OnDesiredPropertyChanged** à classe **Program**:

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
        Console.WriteLine("Sending current time as reported property");
        TwinCollection reportedProperties = new TwinCollection
        {
            ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
        };

        await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
    }
```

Por fim, adicione as seguintes linhas ao método **Main**:

```csharp
static void Main(string[] args)
{
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

        Console.WriteLine("Sending app start time as reported property");
        TwinCollection reportedProperties = new TwinCollection();
        reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

        Client.UpdateReportedPropertiesAsync(reportedProperties);
    }
    catch (AggregateException ex)
    {
        Console.WriteLine("Error in sample: {0}", ex);
    }

    Console.WriteLine("Waiting for Events.  Press enter to exit...");
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

Este código de exemplo mostra como obter o módulo duplo e atualizar as propriedades reportadas com o protocolo AMQP. Na pré-visualização pública, o protocolo AMQP só é suportado para operações de módulo duplo.

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações. No Visual Studio, no Explorador de Soluções, clique com o botão direito do rato na sua solução e, em seguida, clique em **Definir projetos de Arranque**. Selecione **Vários projetos de arranque** e, em seguida, selecione **Iniciar** como ação para a aplicação de consola. E, em seguida, prima F5 para iniciar a ambas as aplicações em execução. 

## <a name="next-steps"></a>Passos Seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução ao IoT Hub módulo identidade e o módulo duplo com cópia de segurança do .NET e o dispositivo de .NET][lnk-csharp-csharp-getstarted]
* [Introdução ao IoT Edge][lnk-iot-edge]


<!-- Images. -->
[9]:./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[12]:./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
