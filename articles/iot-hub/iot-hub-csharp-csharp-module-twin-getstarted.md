---
title: Introdução à identidade de módulo e ao módulo duplo do Hub IoT do Azure (.NET) | Microsoft Docs
description: Saiba como criar a identidade de módulo e atualizar o módulo duplo com os SDKs do IoT para .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: ea42a459365848386082470deb5120e09cb92421
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668159"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Introdução à identidade e módulo do módulo do Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo duplo do Hub IoT do Azure permitem que a aplicação de back-end configure um dispositivo e conferem visibilidade às condições do dispositivo, uma identidade de módulo e o módulo duplo fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, permitem a configuração e condições isoladas para cada componente.

No final deste tutorial, terá duas aplicações de consola .NET:

* **CreateIdentities**, que cria uma identidade de dispositivo, uma identidade de módulo e a chave de segurança associada para ligar os clientes do dispositivo e do módulo.

* **UpdateModuleTwinReportedProperties**, que envia as propriedades reportadas do módulo duplo atualizadas para o Hub IoT.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar ambos os aplicativos para execução em dispositivos e o back-end da solução, consulte [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).

Para concluir este tutorial, precisa do seguinte:

* Visual Studio.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo duplo com o SDK de dispositivo .NET

Nesta secção, vai criar uma aplicação de consola .NET no seu dispositivo simulado que atualiza as propriedades reportadas do módulo duplo.

1. **Criar um projeto do Visual Studio:** No Visual Studio, adicione um projeto C# de área de trabalho clássica do Visual Windows à solução existente usando o modelo de projeto **aplicativo de console (.NET Framework)** . Certifique-se de que tem a versão 4.6.1 ou posterior do .NET Framework. Atribua o nome **UpdateModuleTwinReportedProperties** ao projeto.

    ![Criar um projeto do Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

2. **Instale o SDK do dispositivo .NET do Hub IoT do Azure mais recente:** A identidade do módulo e o módulo "/" está em visualização pública. Ele só está disponível nos SDKs de dispositivo de pré-lançamento do Hub IoT. No Visual Studio, abra tools > Nuget package manager > manage Nuget packages for solution. Procure Microsoft.Azure.Devices.Client. Certifique-se de que selecionou a caixa de verificação Include prerelease. Selecione a versão mais recente e instale. Agora, tem acesso a todas as funcionalidades de módulo. 

    ![Instalar o SDK de serviço .NET do Hub IoT do Azure V1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **Obtenha a cadeia de conexão do módulo** – agora se você entrar no [portal do Azure](https://portal.azure.com/). Navegue até ao seu Hub IoT e clique em Dispositivos IoT. Localize myFirstDevice, abra-o e você verá que o myFirstModule foi criado com êxito. Copie a cadeia de ligação do módulo. É necessária para o próximo passo.

    ![Detalhe do módulo no portal do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

4. **Criar aplicativo de console UpdateModuleTwinReportedProperties**

    Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do módulo.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    Adicione o seguinte método **OnDesiredPropertyChanged** à classe **Program**:

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

    Por fim, adicione as seguintes linhas ao método **Main**:

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

5. Além do método **Main** acima, você pode adicionar um bloco de código abaixo para enviar o evento ao Hub IOT do seu módulo:

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações. No Visual Studio, no Explorador de Soluções, clique com o botão direito do rato na sua solução e, em seguida, clique em **Definir projetos de Arranque**. Selecione **Vários projetos de arranque** e, em seguida, selecione **Iniciar** como ação para a aplicação de consola. Prima F5 para iniciar a aplicação.

## <a name="next-steps"></a>Passos seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução ao gerenciamento de dispositivos](iot-hub-node-node-device-management-get-started.md)
* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
