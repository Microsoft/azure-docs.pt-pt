---
title: 'Tutorial: Deploy a preview environment and spatial graph - Azure Digital Twins| Microsoft Docs'
description: Learn how to deploy your instance of Azure Digital Twins and configure your spatial resources by using the steps in this tutorial.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 20174a4eafb4e72fb62eeff6df2d129b91016b9e
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383031"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Tutorial: Deploy Azure Digital Twins Preview and configure a spatial graph

You can use the Azure Digital Twins Preview service to bring together people, places, and devices in a coherent spatial system. This series of tutorials demonstrates how to use Azure Digital Twins to detect room occupancy with optimal conditions of temperature and air quality. 

These tutorials will walk you through a .NET console application to build a scenario of an office building. The building has multiple floors and rooms within each floor. The rooms contain devices with attached sensors that detect motion, ambient temperature, and air quality. 

You'll learn how to replicate the physical areas and entities in the building as digital objects by using the Azure Digital Twins service. You'll simulate device events by using another console application. Then, you'll learn how to monitor the events that come from these physical areas and entities in near real time. 

O administrador dos escritórios pode utilizar estas informações para ajudar os funcionários que trabalham neste edifício a reservarem salas de reuniões com as condições ideais. An office facilities manager can use your setup to get usage trends of the rooms, and to monitor working conditions for maintenance purposes.

No primeiro tutorial da série, vai aprender a:

> [!div class="checklist"]
> * Deploy Digital Twins.
> * Grant permissions to your app.
> * Modify a Digital Twins sample app.
> * Provision your building.

Estes tutoriais utilizam e modificam os mesmos exemplos que o artigo [Quickstart to find available rooms](quickstart-view-occupancy-dotnet.md) (Início rápido para descobrir salas disponíveis) utiliza, para proporcionar uma cobertura mais detalhada e aprofundada dos conceitos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se ainda não tiver uma conta do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- The .NET Core SDK. The Azure Digital Twins samples used in these tutorials are written in C#. Make sure to install [.NET Core SDK version 2.1.403 or later](https://www.microsoft.com/net/download) on your development machine to build and run the sample. Check that the right version is installed on your machine by running `dotnet --version` in a command window.

- [Visual Studio Code](https://code.visualstudio.com/), para explorar o código de exemplo. 

## <a name="deploy-digital-twins"></a>Implementar o Digital Twins

Use the steps in this section to create a new instance of the Azure Digital Twins service. Only one instance can be created per subscription. Skip to the next section if you already have one running. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Conceder permissões à sua aplicação

Digital Twins uses [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) to control [read/write access](../active-directory/develop/v1-permissions-and-consent.md) to the service. Any application that needs to connect with your Digital Twins instance must be registered with Azure AD. Os passos nesta secção explicam como registar o exemplo de aplicação.

If you already have an app registration, you can reuse it for your sample. No entanto, leia esta secção para ter a certeza de que esse registo está devidamente configurado.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Configure the Digital Twins sample

This section walks you through an Azure Digital Twins application that communicates with the [Digital Twins REST APIs](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Transferir o exemplo

Se já tiver transferido os exemplos para [Quickstart to find available rooms](quickstart-view-occupancy-dotnet.md) (Início rápido para descobrir salas disponíveis), pode ignorar estes passos.

1. Download the [Digital Twins .NET samples](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extract the contents of the zip folder on your machine.

### <a name="explore-the-sample"></a>Explorar o exemplo

In the extracted sample folder, open the file **digital-twins-samples-csharp\digital-twins-samples.code-workspace** in Visual Studio Code. Inclui dois projetos:

* You can use the provisioning sample **occupancy-quickstart** to configure and provision a [spatial intelligence graph](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). This graph is the digitized image of your physical spaces and the resources in them. It uses an [object model](concepts-objectmodel-spatialgraph.md#digital-twins-object-models), which defines objects for a smart building. For a complete list of Digital Twins objects and REST APIs, visit [this REST API documentation](https://docs.westcentralus.azuresmartspaces.net/management/swagger) or the Management API URL that was created for [your instance](#deploy-digital-twins).

   To explore the sample to see how it communicates with your Digital Twins instance, you can start with the **src\actions** folder. The files in this folder implement the commands that you'll use in these tutorials:
    - The **provisionSample.cs** file shows how to provision your spatial graph.
    - The **getSpaces.cs** file gets information about the provisioned spaces.
    - The **getAvailableAndFreshSpaces.cs** file gets the results of a custom function called a user-defined function.
    - The **createEndpoints.cs** file creates endpoints to interact with other services.

* The simulation sample **device-connectivity** simulates sensor data and sends it to the IoT hub that's provisioned for your Digital Twins instance. You'll use this sample in [the next tutorial after you provision your spatial graph](tutorial-facilities-udf.md#simulate-sensor-data). The sensor and device identifiers that you use to configure this sample should be the same as what you'll use to provision your graph.

### <a name="configure-the-provisioning-sample"></a>Configurar o exemplo de aprovisionamento

1. Open a command window and go to the downloaded sample. Execute o seguinte comando:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Execute o comando seguinte para restaurar as dependências para o exemplo de projeto:

    ```cmd/sh
    dotnet restore
    ```

1. In Visual Studio Code, open the [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) file in the **occupancy-quickstart** project. Update the following values:
   * **ClientId**: Enter the application ID of your Azure AD app registration. You noted this ID in the section where you [set app permissions](#grant-permissions-to-your-app).
   * **Tenant**: Enter the directory ID of your [Azure AD tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). You also noted this ID in the section where you [set app permissions](#grant-permissions-to-your-app).
   * **BaseUrl**: introduza o URL da instância do Digital Twins. To get this URL, replace the placeholders in this URL with values for your instance: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. You can also get this URL by modifying the Management API URL from [the deployment section](#deploy-digital-twins). Replace **swagger/** with **api/v1.0/** .

1. See a list of Digital Twins features that you can explore by using the sample. Execute o seguinte comando:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Understand the provisioning process

Esta secção mostra de que forma é que o exemplo aprovisiona um gráfico espacial de um edifício.

In Visual Studio Code, browse to the **occupancy-quickstart\src\actions** folder and open the file **provisionSample.cs**. Repare na função seguinte:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

This function uses [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) in the same folder. Open this file, and note the hierarchy of an office building: *Venue*, *Floor*, *Area*, and *Rooms*. Qualquer um destes espaços físicos pode ter *dispositivos* e *sensores*. Each entry has a predefined `type`&mdash;for example, Floor, Room.

The sample **yaml** file shows a spatial graph that uses the `Default` Digital Twins object model. This model provides generic names for most types. Generic names are sufficient for a building. Examples are Temperature for SensorDataType, and Map for SpaceBlobType. An example space type is Room with subtypes FocusRoom, ConferenceRoom, and so on. 

Se tiver de criar um gráfico espacial para outro tipo de local, como uma fábrica, poderá ter de utilizar um modelo de objetos diferente. You can find out which models are available to use by running the command `dotnet run GetOntologies` on the command line for the provisioning sample. 

For more information on spatial graphs and object models, read [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Modify the sample spatial graph

The **provisionSample.yaml** file contains the following nodes:

- **resources**: The `resources` node creates an Azure IoT Hub resource to communicate with the devices in your setup. An IoT hub at the root node of your graph can communicate with all the devices and sensors in your graph.  

- **spaces**: no modelo de objetos do Digital Twins, `spaces` representa as localizações físicas. Each space has a `Type`&mdash;for example, Region, Venue, or Customer&mdash;and a friendly `Name`. Spaces can belong to other spaces, creating a hierarchical structure. The provisionSample.yaml file has a spatial graph of an imaginary building. Note the logical nesting of spaces of type `Floor` within `Venue`, `Area` in a floor, and `Room` nodes in an area. 

- **devices**: os espaços podem conter `devices`, que são entidades físicas ou virtuais que gerem vários sensores. For example, a device might be a user’s phone, a Raspberry Pi sensor pod, or a gateway. No edifício imaginário do exemplo, repare que a sala denominada **Focus Room** contém um dispositivo **Raspberry Pi 3 A1**. Cada nó do dispositivo é identificado por um `hardwareId` exclusivo, que está codificado no exemplo. Para configurar este exemplo para uma produção real, substitua estes valores pelos da sua configuração.  

- **sensors**: A device can contain multiple `sensors`. They can detect and record physical changes like temperature, motion, and battery level. Each sensor node is uniquely identified by a `hardwareId`, hardcoded here. For an actual application, replace these by using the unique identifiers of the sensors in your setup. The provisionSample.yaml file has two sensors to record *Motion* and *CarbonDioxide*. Adicione outro sensor para registar a *Temperature* (Temperatura) ao adicionar as linhas seguintes, abaixo das do sensor CarbonDioxide. Note that these are provided in provisionSample.yaml as commented-out lines. You can uncomment them by removing the `#` character in the front of each line. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Make sure the `dataType` and `hardwareId` keys align with the statements above this snippet. Confirme também que o editor não substitui espaços por tabulações. 

Save and close the provisionSample.yaml file. In the next tutorial, you'll add more information to this file, and then provision your Azure Digital Twins sample building.

> [!TIP]
> You can view and modify your spatial graph using the [Azure Digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Limpar recursos

If you want to stop exploring Azure Digital Twins at this point, feel free to delete resources created in this tutorial:

1. From the left menu in the [Azure portal](https://portal.azure.com), select **All resources**, select your Digital Twins resource group, and select **Delete**.

    > [!TIP]
    > If you experienced trouble deleting your Digital Twins instance, a service update has been rolled out with the fix. Please retry deleting your instance.

1. If necessary, delete the sample application on your work machine.

## <a name="next-steps"></a>Passos seguintes

To learn how to implement a custom logic to monitor conditions in your sample building, go to the next tutorial in the series: 
> [!div class="nextstepaction"]
> [Tutorial: Provision your building and monitor working conditions](tutorial-facilities-udf.md) (Tutorial: Aprovisionar o edifício e monitorizar as condições de funcionamento)