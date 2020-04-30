---
title: Criar fábrica de dados Azure utilizando o .NET SDK
description: Crie uma fábrica de dados do Azure para copiar dados de uma localização no armazenamento de Blobs do Azure para outra localização.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/24/2019
ms.author: jingwang
ms.openlocfilehash: 7f0f18e523368e85d9cea0206e98bb7b1a0e6165
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81419379"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>Quickstart: Criar uma fábrica de dados e um oleoduto utilizando o .NET SDK

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-dot-net.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este início rápido descreve como utilizar o .NET SDK para criar uma fábrica de dados do Azure. O pipeline que criar nesta fábrica de dados **copia** dados de uma pasta para outra pasta num armazenamento de blobs do Azure. Para ter acesso a um tutorial sobre como **transformar** dados com o Azure Data Factory, veja [Tutorial: Transformar dados com o Spark](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Este artigo não disponibiliza uma introdução detalhada do serviço Data Factory. Para obter uma introdução ao serviço Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

A passagem neste artigo utiliza o Visual Studio 2019. Os procedimentos para o Visual Studio 2013, 2015 ou 2017 diferem ligeiramente.

### <a name="azure-net-sdk"></a>SDK do Azure para .NET

Transfira e instale o [SDK do Azure para .NET](https://azure.microsoft.com/downloads/) no seu computador.

## <a name="create-an-application-in-azure-active-directory"></a>Criar uma Aplicação no Azure Active Directory

Das secções de *Como: Utilize o portal para criar uma aplicação e um diretor*de serviço da Azure AD que possam aceder aos recursos, siga as instruções para fazer estas tarefas:

1. Na [Create a Azure Ative Directory, crie](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)uma aplicação que represente a aplicação .NET que está a criar neste tutorial. Para o URL de início de sessão, pode fornecer um URL fictício conforme mostrado no artigo (`https://contoso.org/exampleapp`).
2. Em [Obter valores para iniciar sessão,](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)obtenha o ID da **aplicação** e identificação do **inquilino,** e note estes valores que usa mais tarde neste tutorial. 
3. Em [Certificados e segredos,](../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets)obtenha a chave de **autenticação,** e note este valor que usa mais tarde neste tutorial.
4. Em [Atribuir a aplicação a uma função,](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)atribuir a aplicação ao papel **de Contribuinte** ao nível da subscrição para que a aplicação possa criar fábricas de dados na subscrição.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Em seguida, crie uma aplicação de consola C# .NET no Estúdio Visual:

1. Inicie o **Visual Studio**.
2. Na janela Iniciar, selecione **Criar uma nova** > app de consola de projeto **(.QUADRO NET)**. É necessária a versão 4.5.2 ou superior do .NET.
3. No **nome do Projeto,** insira **ADFv2QuickStart**.
4. Selecione **Create** (Criar) para criar o projeto.

## <a name="install-nuget-packages"></a>Instalar pacotes NuGet

1. Selecione **ferramentas** > **nuget pacote manager** > **consola**.
2. No painel de **consola sinuoso do gestor** de pacotes, execute os seguintes comandos para instalar pacotes. Para mais informações, consulte o [pacote nuget Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Criar um cliente de fábrica de dados

1. Abra **Program.cs** e inclua as seguintes instruções para adicionar referências aos espaços de nomes.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adicione o código seguinte ao método **Principal** que define as variáveis. Substitua os espaços reservados com os seus próprios valores. Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). As lojas de dados (Azure Storage, Azure SQL Database, e muito mais) e computagens (HDInsight e outras) utilizadas pela fábrica de dados podem ser noutras regiões.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```

3. Adicione o seguinte código ao método **Principal** que cria uma instância da classe **DataFactoryManagementClient.** Utilize este objeto para criar uma fábrica de dados, um serviço ligado, conjuntos de dados e um pipeline. Também pode utilizar este objeto para monitorizar os detalhes de execução do pipeline.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Adicione o código seguinte ao método **Main** que cria uma **fábrica de dados**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Criar um serviço ligado

Adicione o código seguinte ao método **Main** que cria um **serviço ligado do Armazenamento do Azure**.

Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste Quickstart, basta criar um serviço ligado ao Armazenamento Azure tanto para a fonte de cópia como para a loja de pias; chama-se "AzureStorageLinkedService" na amostra.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Adicione o código seguinte ao método **Main** que cria um **conjunto de dados do blob do Azure**.

Defina um conjunto de dados que represente os dados a copiar de uma origem para um sink. Neste exemplo, este conjunto de dados de Blobs refere-se ao serviço ligado do Armazenamento do Azure que criou no passo anterior. O conjunto de dados assume um parâmetro cujo valor é definido numa atividade que consome o conjunto de dados. O parâmetro é utilizado para construir o "folderPath" que aponta para onde os dados residem/é armazenado.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Criar um pipeline

Adicione o código seguinte ao método **Main** que cria um **pipeline com uma atividade de cópia**.

Neste exemplo, este gasoduto contém uma atividade e leva dois parâmetros: o caminho da entrada blob e o caminho da bolha de saída. Os valores destes parâmetros são definidos quando o pipeline é acionado/executado. A atividade de cópia refere-se ao mesmo conjunto de dados de blobs criado no passo anterior como entrada e saída. Quando o conjunto de dados é utilizado como conjunto de dados de entrada, o caminho de entrada é especificado. Da mesma forma, quando o conjunto de dados é utilizado como conjunto de dados de saída, o caminho de saída é especificado. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código seguinte ao método **Main** que **aciona uma execução de pipeline**.

Este código também define valores dos parâmetros **inputPath** e **outputPath** especificados no pipeline com os valores reais da fonte e dos caminhos de bolha afundada.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorizar uma execução de pipeline

1. Adicione o código seguinte ao método **Main** para verificar continuamente o estado até terminar de copiar os dados.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. Adicione o seguinte código ao método **Principal** que recupera detalhes de execução de atividade de cópia, como o tamanho dos dados que são lidos ou escritos.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>Executar o código

Crie e inicie a aplicação e, em seguida, verifique a execução de pipeline.

A consola imprime o progresso da criação da fábrica de dados, o serviço ligado, os conjuntos de dados, o pipeline e a execução de pipeline. Em seguida, verifica o estado de execução do pipeline. Aguarde até ver a atividade da cópia executar detalhes com o tamanho dos dados de leitura/escrita. Em seguida, utilize ferramentas como o explorador de [armazenamento Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se a bolha é copiada para "outputBlobPath" a partir de "inputBlobPath", tal como especificado nas variáveis.

### <a name="sample-output"></a>Resultado da amostra

```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verificar a saída

O gasoduto cria automaticamente a pasta de saída no recipiente de bolhas **adftutorial.** Em seguida, copia o ficheiro **emp.txt** da pasta de entrada para a pasta de saída. 

1. No portal Azure, na página do recipiente **adftutorial** em que parou na pasta adicionar uma pasta de entrada e arquivar para a secção de [recipientes blob](#add-an-input-folder-and-file-for-the-blob-container) acima, selecione **Refresh** para ver a pasta de saída. 
2. Na lista de pastas, **selecione a saída**.
3. Confirme se o ficheiro **emp.txt** foi copiado para a pasta de saída. 

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar programáticamente a fábrica de dados, adicione as seguintes linhas de código ao programa: 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Passos seguintes

O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários. 
