---
title: Introdução à identidade de módulo e ao módulo duplo do Hub IoT do Azure (.NET) | Microsoft Docs
description: Saiba como criar a identidade de módulo e atualizar o módulo duplo com os SDKs do IoT para .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.openlocfilehash: e5d2e485283f71b27bb5e93330a54e1b987044c4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883856"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Introdução à identidade e módulo do módulo do Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Embora a identidade do dispositivo do Hub IoT do Azure e o dispositivo de dispositivos de apoio habilitem o aplicativo de back-end para configurar um dispositivo e fornecer visibilidade sobre as condições do dispositivo, uma identidade de módulo e um módulo inserir fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, como dispositivos baseados no sistema operacional ou dispositivos de firmware, as identidades de módulo e o módulo gêmeos permitem a configuração isolada e as condições para cada componente.

No final deste tutorial, terá duas aplicações de consola .NET:

* **Createidentities**. Esse aplicativo cria uma identidade de dispositivo, uma identidade de módulo e uma chave de segurança associada para conectar seus clientes de módulo e de dispositivo.

* **UpdateModuleTwinReportedProperties**. Este aplicativo envia Propriedades relatadas do módulo atual para o Hub IoT.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar ambos os aplicativos para execução em dispositivos e o back-end da solução, consulte [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Visual Studio.

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-a-hub"></a>Criar um hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo duplo com o SDK de dispositivo .NET

Nesta secção, vai criar uma aplicação de consola .NET no seu dispositivo simulado que atualiza as propriedades reportadas do módulo duplo.

Antes de começar, obtenha a cadeia de conexão do módulo. Inicie sessão no [portal do Azure](https://portal.azure.com/). Navegue até o Hub e selecione **dispositivos IOT**. Localize **myFirstDevice**. Selecione **myFirstDevice** para abri-lo e, em seguida, selecione **myFirstModule** para abri-lo. Em **detalhes de identidade do módulo**, copie a **cadeia de conexão (chave primária)** quando necessário no procedimento a seguir.

   ![Detalhe do módulo no portal do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. No Visual Studio, adicione um novo projeto à sua solução selecionando **arquivo** > **novo** > **projeto**. Em criar um novo projeto, selecione **aplicativo de console (.NET Framework)** e selecione **Avançar**.

1. Atribua o nome *UpdateModuleTwinReportedProperties* ao projeto. Para **solução**, selecione **Adicionar à solução**. Certifique-se de que tem a versão 4.6.1 ou posterior do .NET Framework.

    ![Criar um projeto do Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Selecione **criar** para criar seu projeto.

1. No Visual Studio, abra **ferramentas** > **Gerenciador** > de pacotes NuGet**gerenciar pacotes NuGet para solução**. Selecione o separador **Procurar**.

1. Pesquise e selecione **Microsoft. Azure. Devices. Client**e, em seguida, selecione **instalar**.

    ![Instalar a versão atual do SDK do serviço .NET do Hub IoT do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

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

1. Adicione as seguintes linhas ao método **principal** :

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

1. Opcionalmente, você pode adicionar essas instruções ao método **Main** para enviar um evento ao Hub IOT do seu módulo. Coloque essas linhas abaixo do `try catch` bloco.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Agora você pode executar os aplicativos.

1. No Visual Studio, em **Gerenciador de soluções**, clique com o botão direito do mouse em sua solução e selecione **definir projetos de inicialização**.

1. Em **Propriedades comuns**, selecione **projeto de inicialização.**

1. Selecione **vários projetos de inicialização**e, em seguida, selecione **Iniciar** como a ação para os aplicativos e **OK** para aceitar as alterações.

1. Pressione **F5** para iniciar os aplicativos.

## <a name="next-steps"></a>Passos Seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução ao gerenciamento de dispositivos](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
