---
title: 'Quickstart: Find available rooms - Azure Digital Twins | Microsoft Docs'
description: Neste início rápido, vai executar dois exemplos de aplicações .NET Core para enviar telemetria simulada de movimento e dióxido de carbono para um espaço no Azure Digital Twins. O objetivo é encontrar salas disponíveis com ar fresco a partir das APIs de Gestão após o processamento calculado na cloud.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 11/12/2019
ms.openlocfilehash: b150167ca6a808e0da337be4a609a21cd974598a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383147"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Quickstart: Find available rooms by using Azure Digital Twins

The Azure Digital Twins service allows you to re-create a digital image of your physical environment. Depois, pode ser notificado por eventos no ambiente e personalizar as respostas para esses eventos.

This quickstart uses [a pair of .NET samples](https://github.com/Azure-Samples/digital-twins-samples-csharp) to digitize an imaginary office building. It shows you how to find available rooms in that building. With Digital Twins, you can associate many sensors with your environment. You also can find out if the air quality of your available room is optimal with the help of a simulated sensor for carbon dioxide. One of the sample applications generates random sensor data to help you visualize this scenario.

O vídeo seguinte apresenta um resumo da configuração do início rápido:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma conta do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

1. The two console applications you run in this quickstart are written by using C#. Install the [.NET Core SDK version 2.1.403 or above](https://www.microsoft.com/net/download) on your development machine. If you have the .NET Core SDK installed, verify the current version of C# on your development machine. Run `dotnet --version` in a command prompt.

1. Download the [sample C# project](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Extract the digital-twins-samples-csharp-master.zip archive.

## <a name="create-a-digital-twins-instance"></a>Criar uma instância do Digital Twins

Create a new instance of Digital Twins in the [portal](https://portal.azure.com) by following the steps in this section.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Definir permissões para a aplicação

This section registers your sample application to Azure Active Directory (Azure AD) so that it can access your Digital Twins instance. If you already have an Azure AD app registration, reuse it for your sample. Make sure that it's configured as described in this section.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Compilar a aplicação

Build the occupancy application by following these steps.

1. Abra uma linha de comandos. Go to the folder where your `digital-twins-samples-csharp-master.zip` files were extracted.
1. Execute `cd occupancy-quickstart/src`.
1. Execute `dotnet restore`.
1. Edite [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) para atualizar as variáveis seguintes:
    - **ClientId**: Enter the Application ID of your Azure AD app registration, noted in the preceding section.
    - **Tenant**: Enter the Directory ID of your Azure AD tenant, also noted in the previous section.
    - **BaseUrl**: The Management API URL of your Digital Twins instance is in the format `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Replace the placeholders in this URL with values for your instance from the previous section.

    Save the updated file.

## <a name="provision-graph"></a>Aprovisionar o gráfico

This step provisions your Digital Twins spatial graph with:

- Several spaces.
- One device.
- Two sensors.
- A custom function.
- One role assignment.

The spatial graph is provisioned by using the [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) file.

1. Execute `dotnet run ProvisionSample`.

    >[!NOTE]
    >The Device Login Azure CLI tool is used to authenticate the user to Azure AD. The user must enter a given code to authenticate by using [the Microsoft login](https://microsoft.com/devicelogin) page. After the code is entered, follow the steps to authenticate. The user must authenticate when the tool is running.

    >[!TIP]
    > When you run this step, make sure your variables were copied properly if the following error message appears: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. The provisioning step might take a few minutes. It also provisions an IoT Hub within your Digital Twins instance. It loops through until the IoT Hub shows Status=`Running`.

    [![Provision the sample - Status=Running](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png)](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png#lightbox)

1. At the end of the execution, copy the `ConnectionString` of the device for use in the device simulator sample. Copy only the string outlined in this image.

    [![Copy the connection string](media/quickstart-view-occupancy-dotnet/digital-twins-connection-string.png)](media/quickstart-view-occupancy-dotnet/digital-twins-connection-string.png#lightbox)

    >[!TIP]
    > You can view and modify your spatial graph using the [Azure Digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer).

Keep the console window open for use again later.

## <a name="send-sensor-data"></a>Enviar dados do sensor

Build and run the sensor simulator device application by following these steps.

1. Open a new command prompt. Go to the project you downloaded in the `digital-twins-samples-csharp-master` folder.
1. Execute `cd device-connectivity`.
1. Execute `dotnet restore`.
1. Edit [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) to update **DeviceConnectionString** with the previous `ConnectionString`. Save the updated file.
1. Run `dotnet run` to start sending sensor data. You see it sent to Digital Twins as shown in the following image.

     [![Device Connectivity](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png#lightbox)

1. Let this simulator run so that you can view results side by side with the next step action. This window shows you the simulated sensor data sent to Digital Twins. The next step queries in real time to find available rooms with fresh air.

    >[!TIP]
    > When you run this step, make sure `DeviceConnectionString` was copied properly if the following error message appears: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Encontrar espaços disponíveis com ar fresco

The sensor sample simulates random data values for two sensors. They're motion and carbon dioxide. Available spaces with fresh air are defined in the sample by no presence in the room. They're also defined by a carbon dioxide level under 1,000 ppm. If the condition isn't fulfilled, the space isn't available or the air quality is poor.

1. Open the command prompt you used to run the provisioning step earlier.
1. Execute `dotnet run GetAvailableAndFreshSpaces`.
1. Look at this command prompt and the sensor data command prompt side by side.

    The sensor data command prompt sends simulated motion and carbon dioxide data to Digital Twins every five seconds. The other command prompt reads the graph in real time to find out available rooms with fresh air based on random simulated data. It displays one of these conditions in near real time based on the sensor data that was sent last:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Get available spaces with fresh air](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png)](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png#lightbox)

To understand what happened in this quickstart and what APIs were called, open [Visual Studio Code](https://code.visualstudio.com/Download) with the code workspace project found in `digital-twins-samples-csharp`. Utilize o seguinte comando:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

The tutorials go deep into the code. They teach you how to modify configuration data and what APIs are called. For more information on Management APIs, go to your Digital Twins Swagger page:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nome | Substituir |
| --- | --- |
| YOUR_INSTANCE_NAME | The name of your Digital Twins instance |
| YOUR_LOCATION | The server region where your instance is hosted |

Or for convenience, browse to [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Limpar recursos

The tutorials go into detail about how to:

- Build an application for facility managers to increase occupant productivity.
- Operate the building more efficiently.

To continue to the tutorials, don't clean up the resources created in this quickstart. If you don't plan to continue, delete all the resources created by this quickstart.

1. Delete the folder that was created when you downloaded the sample repository.
1. From the menu on the left in the [Azure portal](https://portal.azure.com), select **All resources**. Then select your Digital Twins resource. At the top of the **All resources** pane, select **Delete**.

    > [!TIP]
    > If you previously experienced trouble deleting your Digital Twins instance, a service update has been rolled out with the fix. Please retry deleting your instance.

## <a name="next-steps"></a>Passos seguintes

This quickstart used a simple scenario and sample applications to show how Digital Twins can be used to find rooms with good working conditions. For in-depth analysis of this scenario, see this tutorial:

>[!div class="nextstepaction"]
>[Tutorial: Deploy Azure Digital Twins and configure a spatial graph](tutorial-facilities-setup.md) (Tutorial: Implementar o Azure Digital Twins e configurar um gráfico espacial)
