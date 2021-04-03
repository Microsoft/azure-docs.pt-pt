---
title: Identidade do módulo Azure IoT Hub & módulo gémeo (portal e .NET)
description: Saiba como criar a identidade de módulo e atualizar o módulo duplo com o portal e o .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 0d16d7a6dbf903ae790015c12e3d34e4a2a553d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139340"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Introdução à identidade de módulo e ao módulo duplo do Hub IoT com o portal e o dispositivo .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> As [identidades de módulo e os módulos duplos](iot-hub-devguide-module-twins.md) assemelham-se à identidade do dispositivo e ao dispositivo duplo do Hub IoT do Azure, exceto no facto de oferecerem melhor granularidade. Enquanto a identidade do dispositivo Azure IoT Hub e o dispositivo twin permitem que a aplicação de back-end configurar um dispositivo e dar visibilidade sobre as condições do dispositivo, uma identidade de módulo e módulo twin fornecem estas capacidades para componentes individuais de um dispositivo. Em dispositivos capazes com múltiplos componentes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware, identidades de módulos e gémeos módulos permitem configuração e condições isoladas para cada componente.
>

Neste tutorial, irá aprender:

* Como criar uma identidade de módulo no portal.

* Como utilizar um SDK de dispositivo .NET para atualizar o módulo twin do seu dispositivo.

> [!NOTE]
> Para obter informações sobre os SDKs Azure IoT que pode utilizar para construir ambas as aplicações para executar em dispositivos e a sua solução back end, consulte [Azure IoT SDKs](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-a-hub"></a>Criar um hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Registar um novo dispositivo no centro

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Criar uma identidade de módulo no portal

Numa identidade do dispositivo, pode criar até 20 identidades de módulo. Para adicionar uma identidade, siga estes passos:

1. Para o dispositivo que criou na secção anterior, escolha **Adicionar Identidade do Módulo** para criar a sua primeira identidade de módulo.

1. Insira o nome *myFirstModule*. Guarde a identidade do módulo.

    ![Adicionar identidade de módulo](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    A sua nova identidade do módulo aparece na parte inferior do ecrã. Selecione-o para ver os detalhes da identidade do módulo.

    ![Ver detalhes de identidade do módulo](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Guarde a **cadeia Connect - tecla primária**. Utilize-o na secção seguinte para configurar o módulo no dispositivo.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo duplo com o SDK de dispositivo .NET

Criou a identidade de módulo com êxito no seu Hub IoT. Vamos tentar comunicar com a cloud a partir do seu dispositivo simulado. Quando uma identidade de módulo é criada, é criado implicitamente um módulo duplo no Hub IoT. Nesta secção, irá criar uma aplicação de consola .NET no seu dispositivo simulado que atualiza as propriedades reportadas do módulo duplo.

### <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Para criar uma aplicação que atualize as propriedades reportadas por twin módulo, siga estes passos:

1. No Visual Studio, **selecione Criar um novo projeto,** em seguida, escolha a App de Consola **(.NET Framework)** e selecione **Next**.

1. Em **Configurar o seu novo projeto,** insira *UpdateModuleTwinReportedProperties* como **o nome do Projeto**. Selecione **Criar** para continuar.

    ![Configure que você é um projeto de estúdio visual](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Instale o mais recente Azure IoT Hub .NET dispositivo SDK

A identidade do módulo e o módulo gémeo estão em pré-visualização pública. Só está disponível nos SDKs do dispositivo de pré-lançamento IoT Hub. Para instalá-lo, siga estes passos:

1. No Estúdio Visual, open **Tools**  >  **NuGet Package Manager**  >  **Gerencie pacotes nuget para solução**.

1. **Selecione Procurar** e, em seguida, selecione **Incluir pré-relançar**. Pesquisa rumo *a Microsoft.Azure.Devices.Client*. Selecione a versão mais recente e instale.

    ![Instalar pré-visualização do serviço SDK do Azure IoT Hub .NET](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Agora, tem acesso a todas as funcionalidades de módulo.

### <a name="get-your-module-connection-string"></a>Obtenha a sua cadeia de ligação módulo

Precisa da cadeia de ligação do módulo para a sua aplicação de consola. Siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue para o seu hub IoT e selecione **Dispositivos IoT**. Abra **o meuFirstDevice** e verá que **o meuFirstModule** foi criado com sucesso.

1. Selecione **myFirstModule** em **Identificações de Módulos**. Em **Detalhes de Identidade do Módulo,** copie a cadeia de **ligação (tecla primária)**.

    ![Detalhe do módulo no portal do Azure](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Criar app de consola UpdateModuleTwinReportedProperties

Para criar a sua aplicação, siga estes passos:

1. Adicione as `using` seguintes declarações no topo do ficheiro **.cs Programa:**

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

4. Por último, substitua o método **principal** pelo seguinte código:

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
  
  Pode construir e executar esta aplicação utilizando **f5.**

Este código de exemplo mostra como obter o módulo duplo e atualizar as propriedades reportadas com o protocolo AMQP. Na pré-visualização pública, o protocolo AMQP só é suportado para operações de módulo duplo.

## <a name="next-steps"></a>Passos seguintes

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução à identidade de módulo e ao módulo duplo do Hub IoT com a cópia de segurança e o dispositivo .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Introdução ao IoT Edge](../iot-edge/quickstart-linux.md)