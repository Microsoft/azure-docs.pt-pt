---
title: Começar c/ Azure IoT Hub identidade do módulo & módulo twin (.NET)
description: Saiba como criar a identidade de módulo e atualizar o módulo duplo com os SDKs do IoT para .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 3d683ada6401c4ba4f2e232948e9712643339fd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533473"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Começa com a identidade do módulo IoT Hub e módulo twin (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo Azure IoT Hub e o dispositivo twin permitem que a aplicação de back-end configurar um dispositivo e dar visibilidade sobre as condições do dispositivo, uma identidade de módulo e módulo twin fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos capazes com múltiplos componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, identidades de módulos e gémeos módulos permitem configuração e condições isoladas para cada componente.

No final deste tutorial, terá duas aplicações de consola .NET:

* **CreateIdentities**. Esta aplicação cria uma identidade do dispositivo, uma identidade de módulo e chave de segurança associada para ligar os clientes do seu dispositivo e módulos.

* **ActualizaçãoModuleTwinReportedProperties**. Esta aplicação envia propriedades atualizadas do módulo twin reportadas para o seu hub IoT.

> [!NOTE]
> Para informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end, veja [Azure IoT SDKs](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-a-hub"></a>Criar um hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo duplo com o SDK de dispositivo .NET

Nesta secção, vai criar uma aplicação de consola .NET no seu dispositivo simulado que atualiza as propriedades reportadas do módulo duplo.

Antes de começar, obtenha a cadeia de ligação do módulo. Inicie sessão no [portal do Azure](https://portal.azure.com/). Navegue para o seu hub e selecione **Dispositivos IoT**. Encontre **o meuFirstDevice.** Selecione **o meuFirstDevice** para o abrir e, em seguida, selecione **o meu FirstModule** para o abrir. Em **Detalhes de Identidade do Módulo,** copie a cadeia de **ligação (chave primária)** quando necessário no seguinte procedimento.

   ![Detalhe do módulo no portal do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. No Visual Studio, adicione um novo projeto à sua solução selecionando **File**  >  **New**  >  **Project**. Na Criação de um novo projeto, selecione **App de Consola (.NET Framework)** e selecione **Next**.

1. Atribua o nome *UpdateModuleTwinReportedProperties* ao projeto. Para **solução**, **selecione Adicionar à solução**. Certifique-se de que tem a versão 4.6.1 ou posterior do .NET Framework.

    ![Criar um projeto do Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Selecione **Criar** para criar o seu projeto.

1. No Estúdio Visual, open **Tools**  >  **NuGet Package Manager**  >  **Gerencie pacotes nuget para solução**. Selecione o separador **Procurar**.

1. Procure e selecione **Microsoft.Azure.Devices.Client**e, em seguida, **selecione Instalar**.

    ![Screenshot que mostra o "Microsoft.Azure.Devices.Client" selecionado e o botão "Instalar" realçado.](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Adicione as `using` seguintes declarações no topo do ficheiro **Program.cs:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do módulo.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Adicione o seguinte método **OnDesiredPropertyChanged** à classe **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
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

1. Adicione as seguintes linhas ao método **principal:**

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

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
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Este código de exemplo mostra como obter o módulo duplo e atualizar as propriedades reportadas com o protocolo AMQP. Na pré-visualização pública, o protocolo AMQP só é suportado para operações de módulo duplo.

1. Opcionalmente, pode adicionar estas declarações ao método **Principal** para enviar um evento para IoT Hub a partir do seu módulo. Coloque estas linhas abaixo do `try catch` bloco.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Agora pode executar as aplicações.

1. No Estúdio Visual, no **Solution Explorer,** clique com o botão direito da sua solução e, em seguida, selecione **Definir projetos startUp**.

1. No âmbito **de Propriedades Comuns,** selecione **Startup Project.**

1. Selecione **vários projetos de startups**e, em seguida, selecione **Iniciar** como ação para as aplicações, e **OK** para aceitar as suas alterações.

1. Prima **F5** para iniciar as aplicações.

## <a name="next-steps"></a>Passos seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Getting started with device management (Introdução à gestão de dispositivos)](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
