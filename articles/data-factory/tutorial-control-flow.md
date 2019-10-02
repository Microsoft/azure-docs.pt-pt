---
title: Ramificação no pipeline do Azure Data Factory | Microsoft Docs
description: Saiba como controlar o fluxo de dados no Azure Data Factory através de atividades de ramificação e encadeamento.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 9/27/2019
ms.openlocfilehash: 5b9be86b0a3d17c9c325b565979fccbec92f5733
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815883"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Atividades de ramificação e encadeamento num pipeline do Data Factory

Neste tutorial, você cria um pipeline de Data Factory que apresenta alguns recursos de fluxo de controle. Esse pipeline copia de um contêiner no armazenamento de BLOBs do Azure para outro contêiner na mesma conta de armazenamento. Se a atividade de cópia for bem-sucedida, o pipeline enviará os detalhes da operação de cópia bem-sucedida em um email. Essas informações podem incluir a quantidade de dados gravados. Se a atividade de cópia falhar, ela enviará detalhes da falha de cópia, como a mensagem de erro, em um email. Ao longo do tutorial, vai ver como passar os parâmetros.

Este gráfico fornece uma visão geral do cenário:

![Descrição geral](media/tutorial-control-flow/overview.png)

Este tutorial mostra como realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma fábrica de dados
> * Criar um serviço ligado do Armazenamento do Azure
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para atividades posteriores
> * Usar passagem de parâmetro e variáveis do sistema
> * Iniciar um execução de pipeline
> * Monitorizar o pipeline e execuções de atividades

Este tutorial utiliza o .NET SDK. Você pode usar outros mecanismos para interagir com Azure Data Factory. Para obter Data Factory guias de início rápido, consulte [início rápido de 5 minutos](https://docs.microsoft.com/azure/data-factory/#5-minute-quickstarts).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Conta de armazenamento do Azure. Você usa o armazenamento de BLOBs como um armazenamento de dados de origem. Se você não tiver uma conta de armazenamento do Azure, consulte [criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).
* Gerenciador de Armazenamento do Azure. Para instalar essa ferramenta, consulte [Gerenciador de armazenamento do Azure](https://storageexplorer.com/).
* Banco de dados SQL do Azure. Você usa o banco de dados como um armazenamento de dado de coletor. Se você não tiver um banco de dados SQL do Azure, consulte [criar um banco de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md).
* Visual Studio. Este artigo usa o Visual Studio 2019.
* SDK do .NET do Azure. Baixe e instale o [SDK do .net do Azure](https://azure.microsoft.com/downloads/).

Para obter uma lista de regiões do Azure nas quais Data Factory está disponível no momento, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados e as computações podem estar em outras regiões. Os repositórios incluem o armazenamento do Azure e o banco de dados SQL do Azure. As computações incluem o HDInsight, que Data Factory usa.

Crie um aplicativo conforme descrito em [criar um aplicativo Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Atribua o aplicativo à função **colaborador** seguindo as instruções no mesmo artigo. Você precisará de vários valores para as partes posteriores deste tutorial, como **ID de aplicativo (cliente)** e **ID de diretório (locatário)** .

### <a name="create-a-blob-table"></a>Criar uma tabela de BLOBs

1. Abra um editor de texto. Copie o texto a seguir e salve-o localmente como *Input. txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Abra Gerenciador de Armazenamento do Azure. Expanda sua conta de armazenamento. Clique com o botão direito do mouse em **contêineres de blob** e selecione **criar contêiner de blob**.
1. Nomeie o novo contêiner *adfv2branch* e selecione **carregar** para adicionar o arquivo *Input. txt* ao contêiner.

## Criar projeto do Visual Studio<a name="create-visual-studio-project"></a>

Criar um C# aplicativo de console .net:

1. Inicie o Visual Studio e selecione **criar um novo projeto**.
1. Em **criar um novo projeto**, escolha **aplicativo de console (.NET Framework)** para C# e selecione **Avançar**.
1. Nomeie o projeto *ADFv2BranchTutorial*.
1. Selecione **.NET versão 4.5.2** ou superior e, em seguida, selecione **criar**.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

1. Selecione **ferramentas** > **Gerenciador**depacotesNuGetconsoledoGerenciadordepacotes. > 
1. No **console do Gerenciador de pacotes**, execute os seguintes comandos para instalar pacotes. Consulte [pacote do NuGet Microsoft. Azure. Management. datafactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) para obter detalhes.

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Criar um cliente de fábrica de dados

1. Abra *Program.cs* e adicione as seguintes instruções:

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

1. Adicione essas variáveis estáticas à classe `Program`. Substitua os marcadores de posição pelos seus próprios valores.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Adicione o seguinte código ao método `Main`. Esse código cria uma instância da classe `DataFactoryManagementClient`. Em seguida, use esse objeto para criar data factory, serviço vinculado, conjuntos de valores e pipeline. Você também pode usar esse objeto para monitorar os detalhes da execução do pipeline.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Adicione um método `CreateOrUpdateDataFactory` ao arquivo *Program.cs* :

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Adicione a seguinte linha ao método `Main` que cria uma data factory:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure

1. Adicione um método `StorageLinkedServiceDefinition` ao arquivo *Program.cs* :

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Adicione a seguinte linha ao método `Main` que cria um serviço vinculado do armazenamento do Azure:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Para obter mais informações sobre propriedades e detalhes com suporte, consulte [Propriedades do serviço vinculado](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta seção, você cria dois conjuntos de valores, um para a origem e um para o coletor.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Criar um conjunto de um DataSet para um blob do Azure de origem

Adicione um método que cria um *conjunto de um DataSet de blob do Azure*. Para obter mais informações sobre propriedades e detalhes com suporte, consulte [Propriedades do conjunto de dados de blob do Azure](connector-azure-blob-storage.md#dataset-properties).

Adicione um método `SourceBlobDatasetDefinition` ao arquivo *Program.cs* :

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Defina um conjunto de dados que represente os dados de origem no Blob do Azure. Esse conjunto de conjuntos de blob refere-se ao serviço vinculado do armazenamento do Azure com suporte na etapa anterior. O conjunto de conjuntos de blob descreve o local do blob do qual copiar: *FolderPath* e *filename*.

Observe o uso de parâmetros para o *FolderPath*. `sourceBlobContainer` é o nome do parâmetro e a expressão é substituída pelos valores passados na execução do pipeline. A sintaxe para definir os parâmetros é `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Criar um conjunto de um DataSet para um blob do Azure do coletor

1. Adicione um método `SourceBlobDatasetDefinition` ao arquivo *Program.cs* :

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Adicione o código a seguir ao método `Main` que cria os conjuntos de fontes de blob do Azure e de coletor.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Criar uma C# classe: EmailRequest

Em seu C# projeto, crie uma classe chamada `EmailRequest`. Essa classe define quais propriedades o pipeline envia na solicitação de corpo ao enviar um email. Neste tutorial, o pipeline envia quatro propriedades do pipeline para o e-mail:

* mensagem. Corpo do email. Para uma cópia bem-sucedida, essa propriedade contém a quantidade de dados gravados. Para uma cópia com falha, essa propriedade contém detalhes do erro.
* Nome do data Factory. Nome do data factory.
* Nome do pipeline. Nome do pipeline.
* Distância. Parâmetro que passa. Esta propriedade especifica o recetor do e-mail.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Criar pontos finais de fluxo de trabalho de e-mail

Para acionar o envio de uma mensagem de e-mail, utilize [Logic Apps](../logic-apps/logic-apps-overview.md) para definir o fluxo de trabalho. Para obter detalhes sobre como criar um fluxo de trabalho de aplicativos lógicos, consulte [como criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Fluxo de trabalho de e-mail de êxito

No [portal do Azure](https://portal.azure.com), crie um fluxo de trabalho de aplicativos lógicos chamado *CopySuccessEmail*. Defina o gatilho do fluxo de trabalho como `When an HTTP request is received`. Para o acionador do pedido, preencha o `Request Body JSON Schema` com o seguinte JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

O fluxo de trabalho é semelhante ao exemplo a seguir:

![Fluxo de trabalho de e-mail de êxito](media/tutorial-control-flow/success-email-workflow-trigger.png)

Esse conteúdo JSON se alinha com a classe `EmailRequest` criada na seção anterior.

Adicione uma ação de `Office 365 Outlook – Send an email`. Para a ação **enviar um email** , personalize como você deseja formatar o email, usando as propriedades passadas no esquema JSON do **corpo** da solicitação. Segue-se um exemplo:

![Designer de aplicativo lógico – ação enviar email](media/tutorial-control-flow/customize-send-email-action.png)

Depois de salvar o fluxo de trabalho, copie e salve o valor da **URL Post http** do gatilho.

## <a name="fail-email-workflow"></a>Fluxo de trabalho de e-mail de falha

Clone **CopySuccessEmail** como outro fluxo de trabalho de aplicativos lógicos chamado *CopyFailEmail*. No acionador do pedido, o `Request Body JSON schema` é o mesmo. Altere o formato do e-mail, como o `Subject`, para adaptar a um e-mail de falha. Segue-se um exemplo:

![Designer de aplicativo lógico-fluxo de trabalho do email de falha](media/tutorial-control-flow/fail-email-workflow.png)

Depois de salvar o fluxo de trabalho, copie e salve o valor da **URL Post http** do gatilho.

Agora você deve ter duas URLs de fluxo de trabalho, como os exemplos a seguir:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Criar um pipeline

Volte para seu projeto no Visual Studio. Agora, adicionaremos o código que cria um pipeline com uma atividade de cópia e a propriedade `DependsOn`. Neste tutorial, o pipeline contém uma atividade, uma atividade de cópia, que usa o conjunto de conjuntos de blob como uma origem e outro conjunto de um banco de uma como coletor. Se a atividade de cópia for bem-sucedida ou falhar, ela chamará diferentes tarefas de email.

Neste pipeline, utiliza as seguintes funcionalidades:

* Parâmetros
* Atividade da Web
* Dependência das atividades
* Usando a saída de uma atividade como uma entrada para outra atividade

1. Adicione este método ao seu projeto. As seções a seguir fornecem mais detalhes.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Adicione a seguinte linha ao método `Main` que cria o pipeline:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parâmetros

A primeira seção de nosso código de pipeline define parâmetros.

* `sourceBlobContainer`. O conjunto de fonte de blob de origem consome esse parâmetro no pipeline.
* `sinkBlobContainer`. O conjunto de coleta de blob do coletor consome esse parâmetro no pipeline.
* `receiver`. As duas atividades da Web no pipeline que enviam emails de êxito ou falha para o receptor usam esse parâmetro.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Atividade da Web

A atividade da Web permite uma chamada para qualquer ponto de extremidade REST. Para obter mais informações sobre a atividade, consulte [atividade da Web no Azure data Factory](control-flow-web-activity.md). Esse pipeline usa uma atividade da Web para chamar o fluxo de trabalho de email dos aplicativos lógicos. Você cria duas atividades da Web: uma que chama o fluxo de trabalho `CopySuccessEmail` e outra que chama o `CopyFailWorkFlow`.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

Na propriedade `Url`, Cole os pontos de extremidade da **URL http post** dos fluxos de trabalho dos seus aplicativos lógicos. Na propriedade `Body`, passe uma instância da classe `EmailRequest`. O pedido de e-mail contém as seguintes propriedades:

* mensagem. Passa o valor de `@{activity('CopyBlobtoBlob').output.dataWritten`. Acessa uma propriedade da atividade de cópia anterior e passa o valor de `dataWritten`. Para o caso de falha, passe a saída de erro em vez de `@{activity('CopyBlobtoBlob').error.message`.
* Nome do Data Factory. Passa o valor de `@{pipeline().DataFactory}` essa variável do sistema permite que você acesse o nome correspondente do data factory. Para obter uma lista de variáveis do sistema, consulte [variáveis do sistema](control-flow-system-variables.md).
* Nome do pipeline. Passa o valor de `@{pipeline().Pipeline}`. Essa variável de sistema permite que você acesse o nome do pipeline correspondente.
* Distância. Passa o valor de `"@pipeline().parameters.receiver"`. Acessa os parâmetros de pipeline.

Esse código cria uma nova dependência de atividade que depende da atividade de cópia anterior.

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código a seguir ao método `Main` que dispara uma execução de pipeline.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};
 
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Classe principal

O método final `Main` deve ser semelhante a este.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Compile e execute o seu programa para acionar uma execução de pipeline!

## <a name="monitor-a-pipeline-run"></a>Monitorizar uma execução de pipeline

1. Adicione o seguinte código ao método `Main`:

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    Esse código verifica continuamente o status da execução até que ela termine de copiar os dados.

1. Adicione o seguinte código ao método `Main` que recupera os detalhes da execução da atividade de cópia, por exemplo, tamanho dos dados lidos/gravados:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Executar o código

Crie e inicie a aplicação e, em seguida, verifique a execução de pipeline.

O aplicativo exibe o progresso da criação de data factory, serviço vinculado, conjuntos de aplicativos, pipeline e execução de pipeline. Em seguida, verifica o estado de execução do pipeline. Aguarde até ver os detalhes da execução da atividade de cópia com o tamanho dos dados lidos/escritos. Em seguida, use ferramentas como Gerenciador de Armazenamento do Azure para verificar se o blob foi copiado para *outputBlobPath* de *inputBlobPath* conforme especificado em variáveis.

Sua saída deve se parecer com o exemplo a seguir:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Passos seguintes

Você fez as seguintes tarefas neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados
> * Criar um serviço ligado do Armazenamento do Azure
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para atividades posteriores
> * Usar passagem de parâmetro e variáveis do sistema
> * Iniciar um execução de pipeline
> * Monitorizar o pipeline e execuções de atividades

Agora você pode continuar na seção conceitos para obter mais informações sobre Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines e atividades](concepts-pipelines-activities.md)