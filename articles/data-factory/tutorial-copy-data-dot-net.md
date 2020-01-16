---
title: Copiar dados do armazenamento de BLOBs do Azure para o banco de dados SQL do Azure
description: Este tutorial fornece instruções passo a passo para copiar dados do Armazenamento de Blobs do Azure para a Base de Dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: 93c4f71c762cff3e3f5a01f0e2595f3498f9d38d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977319"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copiar dados do Blob do Azure para a Base de Dados SQL do Azure utilizando o Azure Data Factory

Neste tutorial, vai criar um pipeline do Data Factory que copia dados do Armazenamento de Blobs do Azure para a Base de Dados SQL do Azure. O padrão de configuração neste tutorial aplica-se à cópia a partir de um arquivo de dados baseado em ficheiros para um arquivo de dados relacional. Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores, consulte [formatos e armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Você executará as seguintes etapas neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar os serviços ligados Armazenamento do Microsoft Azure e Base de Dados SQL do Azure.
> * Criar os conjuntos de dados Blob do Azure e Base de Dados SQL do Azure.
> * Criar um pipeline que contém uma atividade de cópia.
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Este tutorial utiliza o .NET SDK. Você pode usar outros mecanismos para interagir com Azure Data Factory; consulte os exemplos em **início rápido**.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* *Conta de Armazenamento do Azure*. Utilize o armazenamento de blobs como arquivo de dados de *origem*. Se você não tiver uma conta de armazenamento do Azure, consulte [criar uma conta de armazenamento de uso geral](../storage/common/storage-account-create.md).
* *Base de Dados SQL do Azure*. Pode utilizar a base de dados como arquivo de dados *sink*. Se você não tiver um banco de dados SQL do Azure, consulte [criar um banco de dados SQL do Azure](../sql-database/sql-database-single-database-get-started.md).
* *Visual Studio*. As instruções neste artigo usam o Visual Studio 2019.
* *[SDK do Azure para .net](/dotnet/azure/dotnet-tools)* .
* *Azure Active Directory aplicativo*. Se você não tiver um aplicativo Azure Active Directory, consulte a seção [criar um aplicativo Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) de [como: usar o portal para criar um aplicativo do Azure ad](../active-directory/develop/howto-create-service-principal-portal.md). Copie os seguintes valores para uso em etapas posteriores: **ID de aplicativo (cliente)** , **chave de autenticação**e **ID de diretório (locatário)** . Atribua o aplicativo à função **colaborador** seguindo as instruções no mesmo artigo.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare seu blob do Azure e o banco de dados SQL do Azure para o tutorial Criando um blog de origem e uma tabela SQL do coletor.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

Primeiro, crie um blob de origem criando um contêiner e carregando um arquivo de texto de entrada para ele:

1. Abra o Bloco de Notas. Copie o texto a seguir e salve-o localmente em um arquivo chamado *inputEmp. txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Use uma ferramenta como [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para criar o contêiner *adfv2tutorial* e carregar o arquivo *inputEmp. txt* para o contêiner.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela SQL sink

Em seguida, crie uma tabela SQL do coletor:

1. Utilize o seguinte script SQL para criar a tabela *dbo.emp* na Base de Dados SQL do Azure.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Permita que os serviços do Azure acedam ao servidor SQL. Certifique-se de permitir o acesso aos serviços do Azure no servidor SQL do Azure para que o serviço de Data Factory possa gravar dados em seu servidor SQL do Azure. Para verificar e ativar desta definição, execute os passos seguintes:

    1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar o SQL Server. Pesquise e selecione **servidores SQL**.

    2. Selecione o servidor.

    3. No título de **segurança** do menu do SQL Server, selecione **firewalls e redes virtuais**.

    4. Na página **Firewall e redes virtuais** , em **permitir que serviços e recursos do Azure acessem este servidor**, selecione **ativado**.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Usando o Visual Studio, crie C# um aplicativo de console .net.

1. Abra o Visual Studio.
2. Na janela **Iniciar** , selecione **criar um novo projeto**.
3. Na janela **criar um novo projeto** , escolha a C# versão do **aplicativo de console (.NET Framework)** na lista de tipos de projeto. Em seguida, selecione **Seguinte**.
4. Na janela **configurar seu novo projeto** , insira um **nome de projeto** de *ADFv2Tutorial*. Para **local**, navegue até e/ou crie o diretório no qual salvar o projeto. Em seguida, selecione **Criar**. O novo projeto aparece no IDE do Visual Studio.

## <a name="install-nuget-packages"></a>Instalar pacotes NuGet

Em seguida, instale os pacotes de biblioteca necessários usando o Gerenciador de pacotes NuGet.

1. Na barra de menus, escolha **ferramentas** > **Gerenciador de pacotes NuGet** > **console do Gerenciador de pacotes**.
2. No painel de **console do Gerenciador de pacotes** , execute os seguintes comandos para instalar pacotes. Para obter informações sobre o Azure Data Factory pacote NuGet, consulte [Microsoft. Azure. Management. datafactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Criar um cliente de fábrica de dados

Siga estas etapas para criar um cliente data factory.

1. Abra *Program.cs*e, em seguida, substitua as instruções de `using` existentes pelo código a seguir para adicionar referências a namespaces.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adicione o código a seguir ao método `Main` que define variáveis. Substitua os 14 espaços reservados pelos seus próprios valores.

    Para ver a lista de regiões do Azure nas quais Data Factory está disponível no momento, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Na lista suspensa **produtos** , escolha **procurar** > **Analytics** > **Data Factory**. Em seguida, na lista suspensa **regiões** , escolha as regiões que lhe interessam. Uma grade é exibida com o status de disponibilidade dos produtos Data Factory para suas regiões selecionadas.

    > [!NOTE]
    > Os armazenamentos de dados, como o armazenamento do Azure e o Azure SQL Database, e as computações, como o HDInsight, que Data Factory usa podem estar em outras regiões do que o que você escolher para Data Factory.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Adicione o código a seguir ao método `Main` que cria uma instância de `DataFactoryManagementClient` classe. Utilize este objeto para criar uma fábrica de dados, um serviço ligado, os conjuntos de dados e um pipeline. Também pode utilizar este objeto para monitorizar os detalhes de execução do pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Adicione o código a seguir ao método `Main` que cria um *Data Factory*.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Criar serviços ligados

Neste tutorial, você cria dois serviços vinculados para a origem e o coletor, respectivamente.

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure

Adicione o código a seguir ao método `Main` que cria um *serviço vinculado do armazenamento do Azure*. Para obter informações sobre propriedades e detalhes com suporte, consulte [Propriedades do serviço vinculado de blob do Azure](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço ligado da Base de Dados SQL do Azure

Adicione o código a seguir ao método `Main` que cria um *serviço vinculado do banco de dados SQL do Azure*. Para obter informações sobre propriedades e detalhes com suporte, consulte [Propriedades do serviço vinculado do banco de dados SQL do Azure](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta seção, você criará dois conjuntos de valores: um para a origem, o outro para o coletor.

### <a name="create-a-dataset-for-source-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de origem

Adicione o código a seguir ao método de `Main` que cria um conjunto de um banco de uma *blob do Azure*. Para obter informações sobre propriedades e detalhes com suporte, consulte [Propriedades do conjunto de dados de blob do Azure](connector-azure-blob-storage.md#dataset-properties).

Defina um conjunto de dados que represente os dados de origem no Blob do Azure. Este conjunto de dados do Blob refere-se ao serviço ligado de Armazenamento do Microsoft Azure que criou no passo anterior e descreve:

- O local do blob do qual copiar: `FolderPath` e `FileName`
- O formato de BLOB que indica como analisar o conteúdo: `TextFormat` e suas configurações, como delimitador de coluna
- A estrutura de dados, incluindo nomes de coluna e tipos de dados, que são mapeados neste exemplo para a tabela SQL do coletor

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Criar um conjunto de dados para o Base de Dados SQL do Azure

Adicione o código a seguir ao método de `Main` que cria um *conjunto de dados do Azure SQL Database*. Para obter informações sobre as propriedades e os detalhes com suporte, consulte [Propriedades do conjunto de dados do Azure SQL Database](connector-azure-sql-database.md#dataset-properties).

Defina um conjunto de dados que representa os dados sink na Base de Dados SQL do Azure. Esse conjunto de dados refere-se ao serviço vinculado do banco de dados SQL do Azure que você criou na etapa anterior. Também especifica a tabela SQL que contém os dados copiados.

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Criar um pipeline

Adicione o código a seguir ao método `Main` que cria um *pipeline com uma atividade de cópia*. Neste tutorial, esse pipeline contém uma atividade: `CopyActivity`, que usa o conjunto de conjuntos de blob como fonte e o conjunto de conteúdo do SQL como coletor. Para obter informações sobre detalhes da atividade de cópia, consulte [atividade de cópia em Azure data Factory](copy-activity-overview.md).

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código a seguir ao método `Main` que *dispara uma execução de pipeline*.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorizar uma execução de pipeline

Agora, insira o código para verificar os Estados de execução do pipeline e obter detalhes sobre a execução da atividade de cópia.

1. Adicione o código a seguir ao método `Main` para verificar continuamente os status da execução do pipeline até que ele termine de copiar os dados.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adicione o código a seguir ao método `Main` que recupera os detalhes da execução da atividade de cópia, como o tamanho dos dados que foram lidos ou gravados.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Executar o código

Compile o aplicativo escolhendo **compilar** > **Compilar solução**. Em seguida, inicie o aplicativo escolhendo **Debug** > **iniciar a depuração**e verificar a execução do pipeline.

A consola imprime o progresso da criação de uma fábrica de dados, do serviço ligado, dos conjuntos de dados, do pipeline e da execução de pipeline. Em seguida, verifica o estado de execução do pipeline. Aguarde até que você veja os detalhes da execução da atividade de cópia com o tamanho de leitura/gravação de dados. Em seguida, usando ferramentas como o SQL Server Management Studio (SSMS) ou o Visual Studio, você pode se conectar ao seu banco de dados SQL do Azure de destino e verificar se a tabela de destino que você especificou contém o dado copiado.

### <a name="sample-output"></a>Resultado da amostra

```json
Creating a data factory AdfV2Tutorial...
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
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
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
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>Passos seguintes

O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar os serviços ligados Armazenamento do Microsoft Azure e Base de Dados SQL do Azure.
> * Criar os conjuntos de dados Blob do Azure e Base de Dados SQL do Azure.
> * Crie um pipeline que contém uma atividade de cópia.
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Avance para o tutorial seguinte para saber como copiar dados do local para a cloud:

> [!div class="nextstepaction"]
>[Copiar dados do local para a cloud](tutorial-hybrid-copy-powershell.md)
