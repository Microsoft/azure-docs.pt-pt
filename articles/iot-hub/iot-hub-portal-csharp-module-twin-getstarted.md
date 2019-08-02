---
title: Introdução à identidade de módulo e ao módulo duplo do Hub IoT do Azure (portal e .NET)| Microsoft Docs
description: Saiba como criar a identidade de módulo e atualizar o módulo duplo com o portal e o .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: cb7b60de3bfcd31b523b9be0fc49a3c621c8446f
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668114"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Introdução à identidade de módulo e ao módulo duplo do Hub IoT com o portal e o dispositivo .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo duplo do Hub IoT do Azure permitem que a aplicação de back-end configure um dispositivo e conferem visibilidade às condições do dispositivo, uma identidade de módulo e o módulo duplo fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, permitem a configuração e condições isoladas para cada componente.
>

Neste tutorial, irá aprender:

1. Como criar uma identidade de módulo no Portal.

2. Como usar um SDK de dispositivo .NET atualize o módulo de atualização do seu dispositivo.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar ambos os aplicativos para execução em dispositivos e o back-end da solução, consulte [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).
>

Para concluir este tutorial, precisa do seguinte:

* Visual Studio.
* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Criar uma identidade de módulo no portal

Numa identidade do dispositivo, pode criar até 20 identidades de módulo. Clique no botão **Adicionar Identidade de Módulo** na parte superior para criar a sua primeira identidade de módulo denominada **myFirstModule**.

  ![Detalhes do dispositivo](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

Guarde e clique na identidade de módulo acabada de criar. Pode ver os detalhes da identidade de módulo. Guarde a Cadeia de ligação - chave primária. Será utilizada na secção seguinte onde configura o módulo no dispositivo.

  ![Detalhes do dispositivo](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo duplo com o SDK de dispositivo .NET

Criou a identidade de módulo com êxito no seu Hub IoT. Vamos tentar comunicar com a cloud a partir do seu dispositivo simulado. Quando uma identidade de módulo é criada, é criado implicitamente um módulo duplo no Hub IoT. Nesta secção, irá criar uma aplicação de consola .NET no seu dispositivo simulado que atualiza as propriedades reportadas do módulo duplo.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

No Visual Studio, adicione um projeto C# de área de trabalho clássica do Visual Windows à solução existente usando o modelo de projeto **aplicativo de console (.NET Framework)** . Certifique-se de que tem a versão 4.6.1 ou posterior do .NET Framework. Atribua o nome **UpdateModuleTwinReportedProperties** ao projeto.

  ![Criar um projeto do Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Instalar o SDK do dispositivo .NET do Hub IoT do Azure mais recente

A identidade do módulo e o módulo "/" está em visualização pública. Ele só está disponível nos SDKs do dispositivo de pré-lançamento do Hub IoT. No Visual Studio, abra tools > Nuget package manager > manage Nuget packages for solution. Procure Microsoft.Azure.Devices.Client. Verifique se você marcou a caixa de seleção incluir pré-lançamento. Selecione a versão mais recente e instale. Agora, tem acesso a todas as funcionalidades de módulo.

  ![Instalar o SDK de serviço .NET do Hub IoT do Azure V1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>Obter a cadeia de conexão do módulo

Inicie sessão no [portal do Azure](https://portal.azure.com/). Navegue até ao seu Hub IoT e clique em Dispositivos IoT. Localize myFirstDevice, abra-o e você verá que o myFirstModule foi criado com êxito. Copie a cadeia de ligação do módulo. É necessária para o próximo passo.

  ![Detalhe do módulo no portal do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Criar aplicativo de console UpdateModuleTwinReportedProperties

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

Já está pronto para executar as aplicações. No Visual Studio, no Explorador de Soluções, clique com o botão direito do rato na sua solução e, em seguida, clique em **Definir projetos de Arranque**. Selecione **Vários projetos de arranque** e, em seguida, selecione **Iniciar** como ação para a aplicação de consola. E pressione F5 para iniciar os dois aplicativos em execução.

## <a name="next-steps"></a>Passos Seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução à identidade do módulo do Hub IoT e ao módulo "cópia" usando o backup do .NET e o dispositivo .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
