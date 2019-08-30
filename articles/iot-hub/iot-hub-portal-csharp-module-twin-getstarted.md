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
ms.date: 08/20/2019
ms.openlocfilehash: 395b1fd3301925db0607f775c6b7367979ba367b
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147423"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Introdução à identidade de módulo e ao módulo duplo do Hub IoT com o portal e o dispositivo .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Embora a identidade do dispositivo do Hub IoT do Azure e o dispositivo de dispositivos de apoio habilitem o aplicativo de back-end para configurar um dispositivo e fornecer visibilidade sobre as condições do dispositivo, uma identidade de módulo e um módulo inserir fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, como dispositivos baseados no sistema operacional ou dispositivos de firmware, as identidades de módulo e o módulo gêmeos permitem a configuração isolada e as condições para cada componente.
>

Neste tutorial, irá aprender:

* Como criar uma identidade de módulo no Portal.

* Como usar um SDK de dispositivo .NET para atualizar o módulo de atualização do seu dispositivo.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar ambos os aplicativos para execução em dispositivos e o back-end da solução, consulte [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-a-hub"></a>Criar um hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Registrar um novo dispositivo no Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Criar uma identidade de módulo no portal

Numa identidade do dispositivo, pode criar até 20 identidades de módulo. Para adicionar uma identidade, siga estas etapas:

1. Para o dispositivo que você criou na seção anterior, escolha **Adicionar identidade do módulo** para criar sua primeira identidade do módulo.

1. Insira o nome *myFirstModule*. Salve a identidade do módulo.

    ![Adicionar identidade de módulo](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    A nova identidade do módulo aparece na parte inferior da tela. Selecione-o para ver os detalhes de identidade do módulo.

    ![Consulte os detalhes de identidade do módulo](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Salve a **cadeia de conexão-chave primária**. Você o usará na próxima seção para configurar seu módulo no dispositivo.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo duplo com o SDK de dispositivo .NET

Criou a identidade de módulo com êxito no seu Hub IoT. Vamos tentar comunicar com a cloud a partir do seu dispositivo simulado. Quando uma identidade de módulo é criada, é criado implicitamente um módulo duplo no Hub IoT. Nesta secção, irá criar uma aplicação de consola .NET no seu dispositivo simulado que atualiza as propriedades reportadas do módulo duplo.

### <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Para criar um aplicativo que atualiza as propriedades relatadas do módulo de atualização, siga estas etapas:

1. No Visual Studio, selecione **criar um novo projeto**, escolha **aplicativo de console (.NET Framework)** e selecione **Avançar**.

1. Em **configurar seu novo projeto**, insira *UpdateModuleTwinReportedProperties* como o **nome do projeto**. Selecione **criar** para continuar.

    ![Configurar você é um projeto do Visual Studio](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Instalar o SDK do dispositivo .NET do Hub IoT do Azure mais recente

A identidade do módulo e o módulo "/" está em visualização pública. Ele só está disponível nos SDKs do dispositivo de pré-lançamento do Hub IoT. Para instalá-lo, siga estas etapas:

1. No Visual Studio, abra **ferramentas** > **Gerenciador** > de pacotes NuGet**gerenciar pacotes NuGet para solução**.

1. Selecione **procurar**e, em seguida, selecione **incluir pré-lançamento**. Procure *Microsoft. Azure. Devices. Client*. Selecione a versão mais recente e instale.

    ![Instalar a versão prévia do SDK do serviço .NET do Hub IoT do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Agora, tem acesso a todas as funcionalidades de módulo.

### <a name="get-your-module-connection-string"></a>Obter a cadeia de conexão do módulo

Você precisa da cadeia de conexão do módulo para seu aplicativo de console. Siga estes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue até o Hub IoT e selecione **dispositivos IOT**. Abra **myFirstDevice** e você verá que o **myFirstModule** foi criado com êxito.

1. Selecione **myFirstModule** em **identidades de módulo**. Em **detalhes de identidade do módulo**, copie a **cadeia de conexão (chave primária)** .

    ![Detalhe do módulo no portal do Azure](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Criar aplicativo de console UpdateModuleTwinReportedProperties

Para criar seu aplicativo, siga estas etapas:

1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do módulo.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Adicione o seguinte método **OnDesiredPropertyChanged** à classe **Program**:

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

4. Por fim, substitua o método **Main** pelo código a seguir:

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
  
  Você pode criar e executar esse aplicativo usando **F5**.

Este código de exemplo mostra como obter o módulo duplo e atualizar as propriedades reportadas com o protocolo AMQP. Na pré-visualização pública, o protocolo AMQP só é suportado para operações de módulo duplo.

## <a name="next-steps"></a>Passos Seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução à identidade do módulo do Hub IoT e ao módulo "cópia" usando o backup do .NET e o dispositivo .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
