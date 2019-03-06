---
title: 'Tutorial: Criar um pipeline com atividade de cópia com a .NET API | Documentos da Microsoft'
description: Neste tutorial, vai criar um pipeline do Azure Data Factory com uma Atividade de Cópia através da API .NET.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ac0ef463f05c2f361c75827e300b2866e16dfef1
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446984"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Tutorial: Criar um pipeline com atividade de cópia com a .NET API
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

Neste artigo, vai aprender a utilizar a [API .NET](https://portal.azure.com) para criar uma fábrica de dados com um pipeline que copia dados de um armazenamento de blobs do Azure para uma base de dados do SQL Azure. Se não estiver familiarizado com o Azure Data Factory, leia o artigo [Introduction to Azure Data Factory](data-factory-introduction.md) (Introdução ao Azure Data Factory) antes de fazer este tutorial.   

Neste tutorial, vai criar um pipeline com uma atividade no mesmo: Atividade de cópia. A Atividade de Cópia copia dados de um arquivo de dados suportado para um arquivo de dados sink suportado. Para obter uma lista dos arquivos de dados suportados como origens e sinks, veja [Supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Arquivos de dados suportados). A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Para obter mais informações sobre a Atividade de Cópia, veja [Data Movement Activities](data-factory-data-movement-activities.md) (Atividades de Movimento de Dados).

Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [Multiple activities in a pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (Várias atividades num pipeline). 

> [!NOTE] 
> Para obter toda a documentação sobre a API .NET do Data Factory, veja [Data Factory .NET API Reference](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1) (Referência da API .NET do Data Factory).
> 
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Para obter um tutorial sobre como transformar dados com o Azure Data Factory, veja [Tutorial: Criar um pipeline para transformar dados com o cluster do Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Leia a [Descrição Geral e Pré-requisitos do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter uma descrição geral do tutorial e concluir os passos de **pré-requisitos**.
* Visual Studio 2012 ou 2013 ou 2015
* Transferir e instalar o [SDK .NET do Azure](https://azure.microsoft.com/downloads/)
* Azure PowerShell. Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps) para instalar o Azure PowerShell no seu computador. Utilize o Azure PowerShell para criar uma aplicação do Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Criar uma Aplicação no Azure Active Directory
Crie uma aplicação no Azure Active Directory, crie um principal de serviço para a aplicação e atribua-a à função **Contribuinte do Data Factory**.

1. Inicie o **Azure PowerShell**.
2. Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no portal do Azure.

    ```PowerShell
    Connect-AzAccount
    ```
3. Execute o comando seguinte para ver todas as subscrições para esta conta.

    ```PowerShell
    Get-AzSubscription
    ```
4. Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua subscrição do Azure.

    ```PowerShell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > Tome nota do **SubscriptionId** e **TenantId** a partir do resultado deste comando.

5. Crie um grupo de recursos do Azure com o nome **ADFTutorialResourceGroup**, executando o comando seguinte no PowerShell.

    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Se o grupo de recursos já existir, especifique se pretende atualizá-lo (Y) ou mantê-lo como está (N).

    Se utilizar um grupo de recursos diferente, terá de utilizar o nome do grupo de recursos em vez de ADFTutorialResourceGroup neste tutorial.
6. Crie uma aplicação do Azure Active Directory.

    ```PowerShell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Se obtiver o seguinte erro, especifique um URL diferente e execute o comando novamente.
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. Crie o principal de serviço do AD.

    ```PowerShell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Adicione o principal de serviço à função **Contribuinte do Data Factory**.

    ```PowerShell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Obtenha o ID da aplicação.

    ```PowerShell
    $azureAdApplication 
    ```
    Tome nota do ID da aplicação (applicationID) a partir do resultado.

Deve obter os quatro valores seguintes destes passos:

* ID do inquilino
* ID da subscrição
* ID da aplicação
* Palavra-passe (especificada no primeiro comando)

## <a name="walkthrough"></a>Instruções
1. Com o Visual Studio 2012/2013/2015, crie uma aplicação de consola de C# .NET.
   1. Inicie o **Visual Studio** 2012/2013/2015.
   2. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**.
   3. Expanda **Modelos** e selecione **Visual C#**. Nestas instruções, utiliza C#, mas pode utilizar qualquer linguagem .NET.
   4. Selecione **Aplicação de Consola** na lista de tipos de projeto à direita.
   5. Introduza **DataFactoryAPITestApp** para o Nome.
   6. Selecione **C:\ADFGetStarted** para a Localização.
   7. Clique em **OK** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **Gestor de Pacotes NuGet** e clique em **Consola do Gestor de Pacotes**.
3. Na **Consola do Gestor de Pacotes**, realize os seguintes passos:
   1. Execute o seguinte comando para instalar o pacote do Data Factory: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Execute o seguinte comando para instalar o pacote do Azure Active Directory (utilize a API do Azure Active Directory no código): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Adicione a secção **appSetttings** seguinte ao ficheiro **App.config**. Estas definições são utilizadas pelo método auxiliar: **GetAuthorizationHeader**.

    Substitua os valores de **&lt;ID da aplicação&gt;**, **&lt;Palavra-passe&gt;**, **&lt;ID da subscrição&gt;** e **&lt;ID de inquilino&gt;** pelos seus próprios valores.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. Adicione o seguinte ao **utilizar** instruções para o ficheiro de origem (Program.cs) no projeto.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```

6. Adicione o código seguinte, que cria uma instância de classe **DataPipelineManagementClient**, ao método **Principal**. Utilize este objeto para criar uma fábrica de dados, um serviço ligado, conjuntos de dados de entrada e de saída e um pipeline. Utilize-o também para monitorizar setores de um conjunto de dados no tempo de execução.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Substitua o valor do **resourceGroupName** pelo nome do seu grupo de recursos do Azure.
   >
   > Atualize o nome da fábrica de dados (dataFactoryName) para que seja exclusivo. O nome da fábrica de dados deve ser globalmente exclusivo. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.

7. Adicione o código seguinte, que cria uma **fábrica de dados**, ao método **Principal**.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```

    Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma Atividade do Ramo de Registo do HDInsight para executar um Script de Ramo de Registo para transformar dados de entrada em dados de saída do produto. Comecemos este passo com a criação da fábrica de dados.
8. Adicione o código seguinte, que cria um **serviço ligado do Armazenamento do Azure**, ao método **Principal**.

   > [!IMPORTANT]
   > Substitua **storageaccountname** e **accountkey** pelo nome e chave da sua conta de Armazenamento do Azure.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

    Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste tutorial, não vai utilizar serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Vai utilizar dois arquivos de dados do tipo Armazenamento do Azure (origem) e Base de Dados SQL do Azure (destino). 

    Portanto, cria dois serviços ligados com o nome AzureStorageLinkedService e AzureSqlLinkedService dos tipos: AzureStorage e AzureSqlDatabase.  

    O AzureStorageLinkedService liga a sua conta do Armazenamento do Azure à fábrica de dados. Esta conta de armazenamento é aquela em que criou um contentor e para a qual carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
9. Adicione o código seguinte, que cria um **serviço ligado SQL do Azure**, ao método **Principal**.

   > [!IMPORTANT]
   > Substitua **servername**, **databasename**, **username** e **password** pelos nomes do seu servidor, base de dados, utilizador e palavra-passe do SQL do Azure.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

    O AzureSqlLinkedService liga a sua base de dados SQL do Azure à fábrica de dados. Os dados copiados do armazenamento de blobs são armazenados nesta base de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou a tabela emp nesta base de dados.
10. Adicione o código seguinte, que cria **conjuntos de dados de entrada e saída**, ao método **Principal**.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "InputDataset";
    string Dataset_Destination = "OutputDataset";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },
                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```
    
    No passo anterior, criou serviços ligados para ligar a sua conta de Armazenamento do Azure e uma base de dados SQL do Azure à fábrica de dados. Neste passo, vai definir dois conjuntos de dados, com os nomes InputDataset e OutputDataset, que representam os dados de entrada e saída que estão armazenados nos arquivos de dados referenciados por AzureStorageLinkedService e AzureSqlLinkedService, respetivamente.

    O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Além disso, o conjunto de dados de blobs de entrada (InputDataset) especifica o contentor e a pasta que contém os dados de entrada.  

    Do mesmo modo, o serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. E o conjunto de dados da tabela SQL de saída (OututDataset) especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados.

    Neste passo, vai criar um conjunto de dados com o nome InputDataset que aponta para um ficheiro de blobs (emp.text) na pasta raiz de um contentor de blobs (adftutorial) no Armazenamento do Azure, representado pelo serviço ligado AzureStorageLinkedService. Se não especificar um valor para fileName (ou se o ignorar), os dados de todos os blobs da pasta de entrada são copiados para o destino. Neste tutorial, vai especificar um valor para fileName.    

    Neste passo, vai criar um conjunto de dados de saída com o nome **OutputDataset**. Este conjunto de dados aponta para uma tabela SQL na Base de Dados SQL do Azure, representada por **AzureSqlLinkedService**.
11. Adicione o código seguinte, que **cria e ativa um pipeline**, ao método **Principal**. Neste passo, vai criar um pipeline com uma **atividade de cópia** que utiliza **InputDataset** como entrada e **OutputDataset** como saída.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    }
                }
            }
        });
    ```

    Tenha em atenção os seguintes pontos:
   
    - Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**. Para obter mais informações sobre a atividade de cópia, veja [Data movement activities](data-factory-data-movement-activities.md) (Atividades de movimento de dados). Nas soluções do Data Factory, também pode utilizar [Data transformation activities](data-factory-data-transformation-activities.md) (Atividades de transformação de dados).
    - A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**. 
    - Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. Para obter uma lista completa dos arquivos de dados que a atividade de cópia suporta como origens e sinks, veja [supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (arquivos de dados suportados). Para saber como utilizar um arquivo de dados suportado específico como origem/sink, clique na ligação na tabela.  
   
    Atualmente, é o conjunto de dados de saída que controla a agenda. Neste tutorial, o conjunto de dados de saída está configurado para produzir um setor uma vez por hora. As horas de início e de fim do pipeline têm um dia de diferença, ou seja, 24 horas. Desta forma, o pipeline produz 24 setores de conjuntos de dados de saída.
12. Adicione o código seguinte ao método **Principal** para obter o estado de um setor de dados do conjunto de dados de saída. Neste exemplo, é esperado apenas um setor.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");        
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. Adicione o código seguinte, para obter os detalhes de execução de um setor de dados, ao método **Principal**.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. Adicione o método de ajuda seguinte, utilizado pelo método **Principal**, à classe de **Programa**.

    > [!NOTE] 
    > Quando copiar e colar o código a seguir, certifique-se de que o código copiado está ao mesmo nível que o método Main.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. No Explorador de Soluções, expanda o projeto (DataFactoryAPITestApp), clique com o botão direito do rato em **Referências** e clique em **Adicionar Referência**. Selecione a caixa de verificação da assemblagem **System.Configuration**. e clique em **OK**.
16. Crie a aplicação da consola. Clique em **Criar** no menu e clique em **Criar Solução**.
17. Confirme se existe pelo menos um ficheiro no contentor **adftutorial** do seu armazenamento de blobs do Azure. Se não existir, crie um ficheiro **Emp.txt** no Bloco de Notas com o seguinte conteúdo e carregue-o para o contentor adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
18. Execute o exemplo, clicando em **Depurar** -> **Iniciar Depuração** no menu. Quando vir **A obter detalhes da execução de um setor de dados**, aguarde alguns minutos e prima **ENTER**.
19. Utilize o portal do Azure para verificar se a fábrica de dados **APITutorialFactory** foi criada com os artefactos seguintes:
   * Serviço ligado: **LinkedService_AzureStorage**
   * Conjunto de dados: **InputDataset** e **OutputDataset**.
   * Pipeline: **PipelineBlobSample**
20. Verifique se os registos dos dois colaboradores são criados na tabela **emp** na base de dados SQL do Azure especificada.

## <a name="next-steps"></a>Passos Seguintes
Para obter toda a documentação sobre a API .NET do Data Factory, veja [Data Factory .NET API Reference](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1) (Referência da API .NET do Data Factory).

Neste tutorial, utilizou o armazenamento de blobs do Azure como arquivo de dados de origem e uma base de dados SQL do Azure como arquivo de dados de destino numa operação de cópia. A tabela seguinte disponibiliza uma lista dos arquivos de dados que a atividade de cópia suporta como origens e destinos: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber como copiar dados de/para um arquivo de dados, clique na ligação relativa a esse arquivo na tabela.

