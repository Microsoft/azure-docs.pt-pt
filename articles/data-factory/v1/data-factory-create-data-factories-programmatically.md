---
title: Criar pipelines de dados com o SDK .NET do Azure | Documentos da Microsoft
description: Saiba como criar programaticamente, monitorizar e gerir fábricas de dados do Azure com o SDK do Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8f333b626fa51fa60f80350547ee53f346d6cc3a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436772"
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Criar, monitorizar e gerir fábricas de dados do Azure com o SDK .NET do Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

## <a name="overview"></a>Descrição geral
Pode criar, monitorizar e gerir fábricas de dados do Azure programaticamente usando o SDK .NET do Data Factory. Este artigo contém um passo a passo que pode seguir para criar uma aplicação de consola .NET de exemplo que cria e monitora uma fábrica de dados. 

> [!NOTE]
> Este artigo não abrange toda a API .NET do Data Factory. Ver [referência de API .NET do Data Factory](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1) para documentação abrangente sobre a .NET API do Data Factory. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Visual Studio 2012 ou 2013 ou 2015
* Transfira e instale [SDK .NET do Azure](https://azure.microsoft.com/downloads/).
* Azure PowerShell. Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalar o Azure PowerShell no seu computador. Utilize o Azure PowerShell para criar uma aplicação do Azure Active Directory.

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
    $azureAdApplication = New-AzADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
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
Passo a passo, vai criar uma fábrica de dados com um pipeline que contém uma atividade de cópia. A atividade de cópia copia dados a partir de uma pasta no armazenamento de Blobs do Azure para outra pasta num armazenamento de Blobs do mesmo. 

A Atividade de Cópia executa o movimento de dados no Azure Data Factory. A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

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
4. Substitua o conteúdo do **App. config** arquivo no projeto com o seguinte conteúdo: 
    
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
5. No ficheiro App. config, atualize os valores para  **&lt;ID da aplicação&gt;**,  **&lt;palavra-passe&gt;**,  **&lt;subscrição ID&gt;**, e **&lt;ID de inquilino&gt;** pelos seus próprios valores.
6. Adicione as seguintes **usando** instruções para o **Program.cs** arquivo no projeto.

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

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Substitua o valor do **resourceGroupName** pelo nome do seu grupo de recursos do Azure. Pode criar um grupo de recursos utilizando o [New-AzureResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet.
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
9. Adicione o código seguinte, que cria **conjuntos de dados de entrada e saída**, ao método **Principal**.

    O **FolderPath** o blob de entrada está definida como **adftutorial /** em que **adftutorial** é o nome do contentor no armazenamento de Blobs. Se este contentor não existe no armazenamento de Blobs do Azure, criar um contentor com este nome: **adftutorial** e carregar um ficheiro de texto para o contentor.

    FolderPath para o blob de saída está definido como: **adftutorial/apifactoryoutput / {segmente}** onde **setor** dinamicamente é calculado com base no valor de **SliceStart** ( Comece a data e hora de cada setor.)

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
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
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
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
10. Adicione o código seguinte, que **cria e ativa um pipeline**, ao método **Principal**. Este pipeline tem um **CopyActivity** que assume **BlobSource** como uma origem e **BlobSink** como um sink.

    A Atividade de Cópia executa o movimento de dados no Azure Data Factory. A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
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
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
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
    
                },
            }
        }
    });
    ```
12. Adicione o código seguinte ao método **Principal** para obter o estado de um setor de dados do conjunto de dados de saída. Há apenas uma setor esperado neste exemplo.

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
13. **(opcional)**  Adicione o seguinte código para obter detalhes de execução de um setor de dados para o **Main** método.

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
        });
    
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
14. Adicione o método de ajuda seguinte, utilizado pelo método **Principal**, à classe de **Programa**. Esse método será exibida uma caixa de diálogo que lhe permite fornecer **nome de utilizador** e **palavra-passe** que utiliza para iniciar sessão no portal do Azure.

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

15. No Solution Explorer, expanda o projeto: **DataFactoryAPITestApp**, clique com botão direito **referências**e clique em **Add Reference**. Selecione a caixa de verificação para `System.Configuration` assembly e clique em **OK**.
15. Crie a aplicação da consola. Clique em **Criar** no menu e clique em **Criar Solução**.
16. Confirme se existe pelo menos um ficheiro no contentor adftutorial no seu armazenamento de Blobs do Azure. Caso contrário, crie o ficheiro Emp. txt no bloco de notas com o seguinte conteúdo e carregue-o para o contentor adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
17. Execute o exemplo, clicando em **Depurar** -> **Iniciar Depuração** no menu. Quando vir **A obter detalhes da execução de um setor de dados**, aguarde alguns minutos e prima **ENTER**.
18. Utilize o portal do Azure para verificar se a fábrica de dados **APITutorialFactory** foi criada com os artefactos seguintes:
    * Serviço ligado: **AzureStorageLinkedService**
    * Conjunto de dados: **DatasetBlobSource** e **DatasetBlobDestination**.
    * Pipeline: **PipelineBlobSample**
19. Certifique-se de que é criado um ficheiro de saída na **apifactoryoutput** pasta na **adftutorial** contentor.

## <a name="get-a-list-of-failed-data-slices"></a>Obter uma lista de setores de dados com falhas 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>Passos Seguintes
Veja o exemplo seguinte para criar um pipeline com o SDK de .NET que copia dados de um armazenamento de Blobs do Azure para uma base de dados SQL do Azure: 

- [Criar um pipeline para copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-activity-tutorial-using-dotnet-api.md)
