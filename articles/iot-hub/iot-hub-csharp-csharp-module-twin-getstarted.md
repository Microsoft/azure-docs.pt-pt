---
title: Iniciar o módulo Azure IoT Hub identidade & módulo twin (.NET)
description: Saiba como criar a identidade de módulo e atualizar o módulo duplo com os SDKs do IoT para .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp
ms.openlocfilehash: 919d1e37e6066c78e83d58be4fe4667ec67e45ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733379"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Inicie com identidade do módulo IoT Hub e módulo twin (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo Azure IoT Hub e o dispositivo twin permitem que a aplicação back-end configure um dispositivo e forneça visibilidade sobre as condições do dispositivo, uma identidade de módulo e um módulo twin fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos capazes com vários componentes, tais como dispositivos baseados em sistemaoperativo ou dispositivos de firmware, as identidades dos módulos e os gémeos módulos permitem configuração isolada e condições para cada componente.

No final deste tutorial, terá duas aplicações de consola .NET:

* **Criar Identidades**. Esta aplicação cria uma identidade de dispositivo, uma identidade de módulo e uma chave de segurança associada para ligar os seus clientes de dispositivos e módulos.

* **UpdateModuleTwinReportedProperties**. Esta aplicação envia propriedades reportadas de módulos atualizados para o seu hub IoT.

> [!NOTE]
> Para informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end, veja [Azure IoT SDKs](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-a-hub"></a>Criar um hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo duplo com o SDK de dispositivo .NET

Nesta secção, vai criar uma aplicação de consola .NET no seu dispositivo simulado que atualiza as propriedades reportadas do módulo duplo.

Antes de começar, obtenha a corda de ligação do módulo. Inicie sessão no [portal do Azure](https://portal.azure.com/). Navegue para o seu hub e selecione **Dispositivos IoT**. Encontre **o meu Primeiro Dispositivo.** Selecione **o myFirstDevice** para o abrir e, em seguida, selecione **o meuFirstModule** para o abrir. Em Detalhes de Identidade do **Módulo,** copie a cadeia de **ligação (chave primária)** quando necessário no seguinte procedimento.

   ![Detalhe do módulo no portal do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. No Estúdio Visual, adicione um novo projeto à sua solução selecionando **File** > **New** > **Project**. Em Criar um novo projeto, selecione **App consola (.NET Framework)**, e selecione **Next**.

1. Atribua o nome *UpdateModuleTwinReportedProperties* ao projeto. Para **solução,** **selecione Adicionar à solução**. Certifique-se de que tem a versão 4.6.1 ou posterior do .NET Framework.

    ![Criar um projeto do Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Selecione **Criar** para criar o seu projeto.

1. No Estúdio Visual, abrir **ferramentas** > **NuGet Package Manager** > **gere pacotes NuGet para solução**. Selecione o separador **Procurar**.

1. Procure e selecione **Microsoft.Azure.Devices.Client,** e depois selecione **Instalar**.

    ![Instale versão atual do serviço Azure IoT Hub .NET SDK](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Adicione as `using` seguintes declarações na parte superior do ficheiro **Program.cs:**

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

1. Adicione as seguintes linhas ao método **Principal:**

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

1. Opcionalmente, pode adicionar estas declarações ao método **Principal** para enviar um evento para ioT Hub a partir do seu módulo. Coloque estas linhas `try catch` por baixo do bloco.

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

1. No Estúdio Visual, no **Solution Explorer,** clique à direita na sua solução e, em seguida, selecione **definir projetos StartUp**.

1. Em **Propriedades Comuns,** selecione **Projeto de Arranque.**

1. Selecione **Vários projetos**de startups , e, em seguida, selecione **Iniciar** como ação para as aplicações, e **OK** para aceitar as suas alterações.

1. Pressione **f5** para iniciar as aplicações.

## <a name="next-steps"></a>Passos seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Getting started with device management (Introdução à gestão de dispositivos)](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
