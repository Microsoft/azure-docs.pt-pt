---
title: Copiar dados do Armazenamento De Blob Azure para a Base de Dados SQL Azure
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
ms.openlocfilehash: a835e67b1091a55c832955d8dac8615289a6d99e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418701"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copiar dados do Blob do Azure para a Base de Dados SQL do Azure utilizando o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, vai criar um pipeline do Data Factory que copia dados do Armazenamento de Blobs do Azure para a Base de Dados SQL do Azure. O padrão de configuração neste tutorial aplica-se à cópia a partir de um arquivo de dados baseado em ficheiros para um arquivo de dados relacional. Para obter uma lista de lojas de dados suportadas como fontes e pias, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Você toma os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar os serviços ligados Armazenamento do Microsoft Azure e Base de Dados SQL do Azure.
> * Criar os conjuntos de dados Blob do Azure e Base de Dados SQL do Azure.
> * Criar um pipeline que contém uma atividade de cópia.
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Este tutorial utiliza o .NET SDK. Pode utilizar outros mecanismos para interagir com a Azure Data Factory; consulte as amostras em **Quickstarts**.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* *Conta de Armazenamento Azure.* Utilize o armazenamento de blobs como arquivo de dados de *origem*. Se não tiver uma conta de armazenamento Azure, consulte [Criar uma conta de armazenamento de uso geral](../storage/common/storage-account-create.md).
* *Base de Dados Azure SQL*. Pode utilizar a base de dados como arquivo de dados *sink*. Se não tiver uma base de dados Azure SQL, consulte Criar uma base de [dados Azure SQL](../sql-database/sql-database-single-database-get-started.md).
* *Estúdio Visual.* A passagem neste artigo utiliza o Visual Studio 2019.
* *[Azure SDK para .NET](/dotnet/azure/dotnet-tools)*.
* *Aplicação azure Ative Diretório.* Se não tiver uma aplicação azure Ative Diretório, consulte a secção de [aplicação Create a Azure Ative Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) de [Como: Utilize o portal para criar uma aplicação Azure AD](../active-directory/develop/howto-create-service-principal-portal.md). Copie os seguintes valores para utilização em etapas posteriores: **ID de aplicação (cliente),** **chave de autenticação**e **ID do Diretório (inquilino).** Atribuir o pedido ao **papel de Contribuinte** seguindo as instruções no mesmo artigo.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare a sua Base de Dados Azure Blob e Azure SQL para o tutorial, criando um blog de origem e uma mesa SQL afundada.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

Em primeiro lugar, crie uma bolha de origem criando um recipiente e enviando-lhe um ficheiro de texto de entrada:

1. Abra o bloco de notas. Copie o seguinte texto e guarde-o localmente para um ficheiro chamado *inputEmp.txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Utilize uma ferramenta como o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para criar o recipiente *adfv2tutorial* e para carregar o ficheiro *inputEmp.txt* para o recipiente.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela SQL sink

Em seguida, crie uma tabela SQL afundada:

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

2. Permita que os serviços do Azure acedam ao servidor SQL. Certifique-se de que permite o acesso aos serviços Azure no seu servidor Azure SQL para que o serviço Data Factory possa escrever dados para o seu servidor Azure SQL. Para verificar e ativar desta definição, execute os passos seguintes:

    1. Vá ao [portal Azure](https://portal.azure.com) para gerir o seu servidor SQL. Procure e selecione **servidores SQL**.

    2. Selecione o seu servidor.

    3. Sob a rubrica **de Segurança** do servidor SQL, selecione **Firewalls e redes virtuais**.

    4. Na página **Firewall e redes virtuais,** sob **os serviços e recursos do Allow Azure para aceder a este servidor,** selecione **ON**.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Utilizando o Visual Studio, crie uma aplicação de consola C# .NET.

1. Abra o Visual Studio.
2. Na janela **Iniciar,** selecione **Criar um novo projeto**.
3. Na janela Criar uma nova janela de **projeto,** escolha a versão C# da **App consola (.NET Framework)** da lista de tipos de projeto. Em seguida, selecione **Seguinte**.
4. Na **configuração** da sua nova janela de projeto, insira um nome de **Projeto** de *ADFv2Tutorial*. Para **localização,** navegue de e/ou crie o diretório para salvar o projeto. Em seguida, selecione **Criar**. O novo projeto aparece no Estúdio Visual IDE.

## <a name="install-nuget-packages"></a>Instalar pacotes NuGet

Em seguida, instale os pacotes de biblioteca necessários utilizando o gestor de pacotes NuGet.

1. Na barra de menus, escolha **ferramentas** > **NuGet Package Manager** > **Manager Console**.
2. No painel de **consola sinuoso do gestor** de pacotes, execute os seguintes comandos para instalar pacotes. Para obter informações sobre o pacote NuGet da Fábrica de Dados Azure, consulte [Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Criar um cliente de fábrica de dados

Siga estes passos para criar um cliente de fábrica de dados.

1. Abra *Program.cs,* em seguida, `using` sobrepor as declarações existentes com o seguinte código para adicionar referências aos espaços de nome.

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

2. Adicione o seguinte `Main` código ao método que define variáveis. Substitua os 14 espaços reservados com os seus próprios valores.

    Para ver a lista das regiões do Azure em que a Data Factory está atualmente disponível, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/) Na lista de **produtos,** escolha A**Fábrica**de Dados da **Browse** > **Analytics** > . Em seguida, nas **regiões** lista de abandono, escolha as regiões que lhe interessam. Uma grelha aparece com o estado de disponibilidade dos produtos data Factory para as suas regiões selecionadas.

    > [!NOTE]
    > As lojas de dados, como o Azure Storage e o Azure SQL Database, e computagens, como o HDInsight, que a Data Factory utiliza podem estar noutras regiões do que o que escolhepara data Factory.

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

3. Adicione o seguinte `Main` código ao método `DataFactoryManagementClient` que cria uma instância de classe. Utilize este objeto para criar uma fábrica de dados, um serviço ligado, os conjuntos de dados e um pipeline. Também pode utilizar este objeto para monitorizar os detalhes de execução do pipeline.

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

Adicione o seguinte `Main` código ao método que cria uma fábrica de *dados*.

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

Neste tutorial, cria-se dois serviços ligados para a fonte e afunda-se, respectivamente.

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure

Adicione o seguinte `Main` código ao método que cria um serviço ligado ao *Armazenamento Azure*. Para obter informações sobre propriedades e detalhes suportados, consulte propriedades de serviço ligadas a [Azure Blob.](connector-azure-blob-storage.md#linked-service-properties)

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

Adicione o seguinte `Main` código ao método que cria um serviço ligado à Base de *Dados Azure SQL*. Para obter informações sobre propriedades e detalhes suportados, consulte propriedades de serviço ligadas à Base de [Dados Azure SQL](connector-azure-sql-database.md#linked-service-properties).

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

Nesta secção, cria-se dois conjuntos de dados: um para a fonte, outro para a pia.

### <a name="create-a-dataset-for-source-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de origem

Adicione o seguinte `Main` código ao método que cria um conjunto de *dados de blob Azure*. Para obter informações sobre propriedades e detalhes suportados, consulte propriedades de [conjunto de dados Azure Blob](connector-azure-blob-storage.md#dataset-properties).

Defina um conjunto de dados que represente os dados de origem no Blob do Azure. Este conjunto de dados do Blob refere-se ao serviço ligado de Armazenamento do Microsoft Azure que criou no passo anterior e descreve:

- A localização da bolha para `FolderPath` copiar de: e`FileName`
- O formato blob que indica `TextFormat` como analisar o conteúdo: e as suas definições, tais como delimitador de colunas
- A estrutura de dados, incluindo nomes de colunas e tipos de dados, que mapeiam neste exemplo para a tabela SQL do lavatório

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

Adicione o seguinte `Main` código ao método que cria um conjunto de *dados azure SQL Database*. Para obter informações sobre propriedades e detalhes suportados, consulte as propriedades do conjunto de [dados Azure SQL](connector-azure-sql-database.md#dataset-properties)Database .

Defina um conjunto de dados que representa os dados sink na Base de Dados SQL do Azure. Este conjunto de dados refere-se ao serviço ligado à Base de Dados Azure SQL que criou na etapa anterior. Também especifica a tabela SQL que contém os dados copiados.

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

Adicione o seguinte `Main` código ao método que cria um *pipeline com uma atividade de cópia*. Neste tutorial, este pipeline contém `CopyActivity`uma atividade: , que toma como fonte o conjunto de dados blob e o conjunto de dados SQL. Para obter informações sobre detalhes da atividade de cópia, consulte a atividade de Copiar na Fábrica de [Dados Azure](copy-activity-overview.md).

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

Adicione o seguinte `Main` código ao método que *desencadeia uma execução*do gasoduto .

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorizar uma execução de pipeline

Agora insira o código para verificar os estados de execução do gasoduto e para obter detalhes sobre a execução da atividade da cópia.

1. Adicione o seguinte `Main` código ao método para verificar continuamente os estados do gasoduto até que termine de copiar os dados.

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

2. Adicione o seguinte `Main` código ao método que recupera detalhes de execução de atividade de cópia, como o tamanho dos dados que foram lidos ou escritos.

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

Construa a aplicação escolhendo **build build** > **solution**. Em seguida, inicie a aplicação escolhendo **Debug** > **Start Debugging**, e verifique a execução do gasoduto.

A consola imprime o progresso da criação de uma fábrica de dados, do serviço ligado, dos conjuntos de dados, do pipeline e da execução de pipeline. Em seguida, verifica o estado de execução do pipeline. Aguarde até ver a atividade da cópia executar detalhes com o tamanho de leitura/escrita de dados. Em seguida, utilizando ferramentas como o SQL Server Management Studio (SSMS) ou o Visual Studio, pode ligar-se à base de dados Azure SQL do seu destino e verificar se a tabela de destino que especificou contém os dados copiados.

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
> * Criar um oleoduto contendo uma atividade de cópia.
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Avance para o tutorial seguinte para saber como copiar dados do local para a cloud:

> [!div class="nextstepaction"]
>[Copiar dados do local para a cloud](tutorial-hybrid-copy-powershell.md)
