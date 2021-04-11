---
title: Ramificação no gasoduto Azure Data Factory
description: Saiba como controlar o fluxo de dados no Azure Data Factory através de atividades de ramificação e encadeamento.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 83bc90ca251fddd498dd365f1a4b00689a471475
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785670"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Atividades de ramificação e encadeamento num pipeline do Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, cria-se um pipeline data factory que mostra algumas funcionalidades de fluxo de controlo. Este gasoduto copia de um contentor em Azure Blob Storage para outro recipiente na mesma conta de armazenamento. Se a atividade da cópia for bem sucedida, o pipeline envia detalhes da operação de cópia bem sucedida num e-mail. Essa informação pode incluir a quantidade de dados escritos. Se a atividade da cópia falhar, envia detalhes da falha da cópia, como a mensagem de erro, num e-mail. Ao longo do tutorial, vai ver como passar os parâmetros.

Este gráfico fornece uma visão geral do cenário:

![O diagrama mostra o Azure Blob Storage, que é o alvo de uma cópia, que, no sucesso, envia um e-mail com detalhes ou, no fracasso, envia um e-mail com detalhes de erro.](media/tutorial-control-flow/overview.png)

Este tutorial mostra-lhe como fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma fábrica de dados
> * Criar um serviço ligado do Armazenamento do Azure
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para atividades posteriores
> * Utilizar variáveis de passagem de parâmetros e sistemas
> * Iniciar um execução de pipeline
> * Monitorizar o pipeline e execuções de atividades

Este tutorial utiliza o .NET SDK. Pode utilizar outros mecanismos para interagir com a Azure Data Factory. Para arranques rápidos da Data Factory, consulte [quickstarts de 5 minutos](./quickstart-create-data-factory-portal.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Conta de Armazenamento Azure. Utiliza o armazenamento de bolhas como uma loja de dados de origem. Se não tiver uma conta de armazenamento Azure, consulte [criar uma conta de armazenamento](../storage/common/storage-account-create.md).
* Explorador de Armazenamento Azure. Para instalar esta ferramenta, consulte [o Explorador de Armazenamento Azure](https://storageexplorer.com/).
* Base de Dados SQL do Azure. Pode utilizar a base de dados como um arquivo de dados sink. Se não tiver uma base de dados na Base de Dados Azure SQL, consulte a [base de dados Criar uma base de dados na Base de Dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md).
* Visual Studio. Este artigo utiliza o Visual Studio 2019.
* Azure .NET SDK. Faça o download e instale o [Azure .NET SDK](https://azure.microsoft.com/downloads/).

Para obter uma lista das regiões de Azure em que a Data Factory está atualmente disponível, consulte [os Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/) As lojas de dados e os cálculos podem estar noutras regiões. As lojas incluem Azure Storage e Azure SQL Database. Os cálculos incluem HDInsight, que a Data Factory utiliza.

Crie uma aplicação como descrito na [Criação de uma aplicação Azure Ative Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Atribua o pedido à **função Contribuinte** seguindo as instruções no mesmo artigo. Você precisará de vários valores para partes posteriores deste tutorial, tais como **ID de aplicação (cliente)** e **ID de Diretório (inquilino).**

### <a name="create-a-blob-table"></a>Criar uma mesa de bolhas

1. Abra um editor de texto. Copie o seguinte texto e guarde-o localmente como *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Abra o Explorador de Armazenamento Azure. Expanda a sua conta de armazenamento. Clique com o botão direito do rato em **Contentores de Blobs** e selecione **Criar Contentor de Blobs**.
1. Nomeie o novo recipiente *adfv2branch* e **selecione Upload** para adicionar o seu ficheiro *input.txt* ao recipiente.

## <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio<a name="create-visual-studio-project"></a>

Criar uma aplicação de consola C# .NET:

1. Inicie o Visual Studio e selecione **Criar um novo projeto.**
1. In **Create a new project**, escolha a App consola **(.NET Framework)** para C# e selecione **Next**.
1. Nomeie o projeto *ADFv2BranchTutorial*.
1. Selecione **a versão .NET 4.5.2** ou superior e, em seguida, selecione **Criar**.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

1. Selecione **Tools** (Ferramentas)  > **NuGet Package Manager** (Gestor de Pacotes NuGet)  > **Package Manager Console** (Consola do Gestor de Pacotes).
1. Na **Consola Gestor de Pacotes,** executar os seguintes comandos para instalar pacotes. Consulte o [pacote nuget Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) para obter mais detalhes.

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Criar um cliente de fábrica de dados

1. *Programa Aberto.cs* e adicione as seguintes declarações:

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

1. Adicione estas variáveis estáticas à `Program` classe. Substitua os marcadores de posição pelos seus próprios valores.

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

1. Adicione o seguinte código ao método `Main`. Este código cria uma instância de `DataFactoryManagementClient` classe. Em seguida, utilize este objeto para criar fábrica de dados, serviço ligado, conjuntos de dados e oleoduto. Também pode utilizar este objeto para monitorizar os detalhes da execução do gasoduto.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Adicione um `CreateOrUpdateDataFactory` método ao seu ficheiro .cs *Programa:*

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

1. Adicione a seguinte linha ao `Main` método que cria uma fábrica de dados:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure

1. Adicione um `StorageLinkedServiceDefinition` método ao seu ficheiro .cs *Programa:*

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

1. Adicione a seguinte linha ao `Main` método que cria um serviço ligado ao Azure Storage:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Para obter mais informações sobre propriedades e detalhes suportados, consulte [as propriedades do serviço Linked.](connector-azure-blob-storage.md#linked-service-properties)

## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta secção, cria-se dois conjuntos de dados, um para a fonte e outro para a pia.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Criar um conjunto de dados para uma fonte Azure Blob

Adicione um método que cria um *conjunto de dados de bolhas Azure*. Para obter mais informações sobre propriedades e detalhes suportados, consulte [as propriedades do conjunto de dados Azure Blob](connector-azure-blob-storage.md#dataset-properties).

Adicione um `SourceBlobDatasetDefinition` método ao seu ficheiro .cs *Programa:*

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

Defina um conjunto de dados que represente os dados de origem no Blob do Azure. Este conjunto de dados Blob refere-se ao serviço ligado ao Azure Storage suportado no passo anterior. O conjunto de dados Blob descreve a localização da bolha para copiar a partir de: *PastaPath* e *DataName* de Ficheiros .

Note a utilização de parâmetros para o *FolderPath*. `sourceBlobContainer` é o nome do parâmetro e a expressão é substituída pelos valores passados no gasoduto. A sintaxe para definir os parâmetros é `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Crie um conjunto de dados para um azure Blob

1. Adicione um `SourceBlobDatasetDefinition` método ao seu ficheiro .cs *Programa:*

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

1. Adicione o seguinte código ao `Main` método que cria tanto a fonte Azure Blob como conjuntos de dados de sumidouro.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Criar uma classe C#: EmailRequest

No seu projeto C, crie uma classe chamada `EmailRequest` . Esta classe define quais as propriedades que o pipeline envia no pedido do corpo ao enviar um e-mail. Neste tutorial, o pipeline envia quatro propriedades do pipeline para o e-mail:

* Mensagem. Corpo do e-mail. Para uma cópia bem sucedida, esta propriedade contém a quantidade de dados escritos. Para uma cópia falhada, esta propriedade contém detalhes do erro.
* Nome da fábrica de dados. Nome da fábrica de dados.
* Nome do oleoduto. Nome do pipeline.
* O recetor. Parâmetro que passa. Esta propriedade especifica o recetor do e-mail.

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

Para acionar o envio de uma mensagem de e-mail, utilize [Logic Apps](../logic-apps/logic-apps-overview.md) para definir o fluxo de trabalho. Para obter detalhes sobre a criação de um fluxo de trabalho de Apps [Lógicas,](../logic-apps/quickstart-create-first-logic-app-workflow.md)consulte Como criar uma App Lógica.

### <a name="success-email-workflow"></a>Fluxo de trabalho de e-mail de êxito

No [portal Azure,](https://portal.azure.com)crie um fluxo de trabalho de Apps Lógicas chamado *CopySuccessEmail*. Defina o gatilho do fluxo de trabalho como `When an HTTP request is received` . Para o acionador do pedido, preencha o `Request Body JSON Schema` com o seguinte JSON:

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

O seu fluxo de trabalho parece-se com o seguinte exemplo:

![Fluxo de trabalho de e-mail de êxito](media/tutorial-control-flow/success-email-workflow-trigger.png)

Este conteúdo JSON alinha-se com a `EmailRequest` classe que criou na secção anterior.

Adicione uma ação de `Office 365 Outlook – Send an email` . Para enviar uma ação **de e-mail,** personalize como deseja formatar o e-mail, utilizando as propriedades passadas no esquema **do Body** JSON. Eis um exemplo:

![Logic app designer - envie ação de e-mail](media/tutorial-control-flow/customize-send-email-action.png)

Depois de guardar o fluxo de trabalho, copie e guarde o valor **URL HTTP POST** do gatilho.

## <a name="fail-email-workflow"></a>Fluxo de trabalho de e-mail de falha

Clone **CopySuccessEmail** como outro fluxo de trabalho de Apps Lógicas chamado *CopyFailEmail*. No acionador do pedido, o `Request Body JSON schema` é o mesmo. Altere o formato do e-mail, como o `Subject`, para adaptar a um e-mail de falha. Segue-se um exemplo:

![Logic app designer - fail email workflow](media/tutorial-control-flow/fail-email-workflow.png)

Depois de guardar o fluxo de trabalho, copie e guarde o valor **URL HTTP POST** do gatilho.

Deve agora ter dois URLs de fluxo de trabalho, como os seguintes exemplos:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Criar um pipeline

Volte para o seu projeto no Estúdio Visual. Vamos agora adicionar o código que cria um oleoduto com uma atividade de cópia e `DependsOn` propriedade. Neste tutorial, o pipeline contém uma atividade, uma atividade de cópia, que toma o conjunto de dados Blob como fonte e outro conjunto de dados Blob como pia. Se a atividade de cópia tiver sucesso ou falhar, chama diferentes tarefas de e-mail.

Neste pipeline, utiliza as seguintes funcionalidades:

* Parâmetros
* Atividade web
* Dependência das atividades
* Usando a produção de uma atividade como entrada para outra atividade

1. Adicione este método ao seu projeto. As seguintes secções fornecem mais detalhes.

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

1. Adicione a seguinte linha ao `Main` método que cria o gasoduto:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parâmetros

A primeira secção do nosso código de gasoduto define parâmetros.

* `sourceBlobContainer`. O conjunto de dados de bolhas de origem consome este parâmetro no oleoduto.
* `sinkBlobContainer`. O conjunto de dados da bolha da pia consome este parâmetro no oleoduto.
* `receiver`. As duas atividades web no pipeline que enviam e-mails de sucesso ou falha para o recetor usam este parâmetro.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Atividade web

A atividade web permite uma chamada para qualquer ponto final REST. Para obter mais informações sobre a atividade, consulte [a atividade da Web na Azure Data Factory.](control-flow-web-activity.md) Este oleoduto utiliza uma atividade web para chamar o fluxo de trabalho de email da Logic Apps. Cria-se duas atividades web: uma que chama o `CopySuccessEmail` fluxo de trabalho e outra que chama de `CopyFailWorkFlow` .

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

Na `Url` propriedade, cole os pontos finais **DO URL HTTP POST** dos fluxos de trabalho das suas Aplicações Lógicas. Na `Body` propriedade, passe um exemplo da `EmailRequest` classe. O pedido de e-mail contém as seguintes propriedades:

* Mensagem. Passa valor de `@{activity('CopyBlobtoBlob').output.dataWritten` . Acede a um imóvel da anterior atividade de cópia e passa o valor de `dataWritten` . Para o caso de falha, passe a saída de erro em vez de `@{activity('CopyBlobtoBlob').error.message`.
* Nome da fábrica de dados. O valor de passes `@{pipeline().DataFactory}` desta variável do sistema permite-lhe aceder ao nome correspondente da fábrica de dados. Para obter uma lista de variáveis do sistema, consulte [As Variáveis do Sistema.](control-flow-system-variables.md)
* Nome do pipeline. Passa valor de `@{pipeline().Pipeline}` . Esta variável do sistema permite-lhe aceder ao nome do pipeline correspondente.
* O recetor. Passa valor de `"@pipeline().parameters.receiver"` . Acede aos parâmetros do gasoduto.

Este código cria uma nova Dependência de Atividade que depende da atividade de cópia anterior.

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o seguinte código ao `Main` método que aciona uma tubagem.

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

O teu método final `Main` deve ser assim.

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

    Este código verifica continuamente o estado da execução até terminar a cópia dos dados.

1. Adicione o seguinte código ao método que recupera a atividade de `Main` cópias executar detalhes, por exemplo, tamanho dos dados lidos/escritos:

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

A aplicação apresenta o progresso da criação de uma fábrica de dados, serviços ligados, conjuntos de dados, gasoduto e corrida ao gasoduto. Em seguida, verifica o estado de execução do pipeline. Aguarde até ver os detalhes da execução da atividade de cópia com o tamanho dos dados lidos/escritos. Em seguida, utilize ferramentas como o Azure Storage Explorer para verificar se a bolha foi copiada para *a saídaBlobPath* a partir do *inputBlobPath,* tal como especificou em variáveis.

A sua saída deve assemelhar-se à seguinte amostra:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
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

Fez as seguintes tarefas neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados
> * Criar um serviço ligado do Armazenamento do Azure
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para atividades posteriores
> * Utilizar variáveis de passagem de parâmetros e sistemas
> * Iniciar um execução de pipeline
> * Monitorizar o pipeline e execuções de atividades

Pode agora continuar na secção Conceitos para mais informações sobre a Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines e atividades](concepts-pipelines-activities.md)