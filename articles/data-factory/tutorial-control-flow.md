---
title: Ramificação no pipeline do Azure Data Factory | Microsoft Docs
description: Saiba como controlar o fluxo de dados no Azure Data Factory através de atividades de ramificação e encadeamento.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: shlo
ms.openlocfilehash: d2f892941f9d37dd3d74afe17d7952b404dc709f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551641"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Atividades de ramificação e encadeamento num pipeline do Data Factory
Neste tutorial, vai criar um pipeline do Data Factory que demonstra algumas das funcionalidades de fluxo de controlo. Este pipeline cria uma cópia simples de um contentor do Armazenamento de Blobs do Azure para outro contentor na mesma conta de armazenamento. Se a atividade de cópia for bem sucedida, vai querer enviar detalhes da operação de cópia com êxito (por exemplo, a quantidade de dados escritos) num e-mail de êxito. Se a atividade de cópia falhar, vai querer enviar detalhes da falha de cópia (por exemplo, a mensagem de erro) num e-mail de falha. Ao longo do tutorial, vai ver como passar os parâmetros.

Uma visão geral do cenário: ![Descrição Geral](media/tutorial-control-flow/overview.png)

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um serviço ligado do Armazenamento do Azure
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para atividades posteriores
> * Utilizar passagem de parâmetros e variáveis de sistema
> * Iniciar um execução de pipeline
> * Monitorizar o pipeline e execuções de atividades

Este tutorial utiliza o .NET SDK. Pode utilizar outros mecanismos para interagir com o Azure Data Factory. Veja "Inícios rápidos" no índice.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Conta de Armazenamento do Azure**. Utilize o armazenamento de blobs como arquivo de dados de **origem**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para obter os passos para criar uma.
* **Base de Dados SQL do Azure**. Pode utilizar a base de dados como arquivo de dados **sink**. Se não tiver uma Base de Dados SQL do Azure, veja o artigo [Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para obter os passos para criar uma.
* **Visual Studio** 2013, 2015 ou 2017. As instruções neste artigo utilizam o Visual Studio 2017.
* **Transferir e instalar o [SDK .NET do Azure](https://azure.microsoft.com/downloads/)**.
* **Criar uma Aplicação no Azure Active Directory**, seguindo [estas instruções](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Tome nota dos seguintes valores que vai utilizar em passos posteriores: **ID da aplicação**, **chave de autenticação** e **ID de inquilino**. Atribua a aplicação à função "**Contribuidor**", seguindo as instruções no mesmo artigo.

### <a name="create-blob-table"></a>Criar a tabela de blobs

1. Inicie o Bloco de notas. Copie o texto seguinte e guarde-o como **input.txt** no disco.

    ```
    John|Doe
    Jane|Doe
    ```
2. Utilize ferramentas como o [Explorador de Armazenamento do Microsoft Azure](https://storageexplorer.com/) para criar o contentor **adfv2branch** e carregar o ficheiro **input.txt** para o contentor.

## <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio

Com o Visual Studio 2015/2017, crie uma aplicação de consola de C# .NET.

1. Inicie o **Visual Studio**.
2. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**. É necessária a versão 4.5.2 ou superior do .NET.
3. Selecione **Consola de Aplicação** -> **de Visual C# (.NET Framework)** na lista de tipos de projeto à direita.
4. Introduza **ADFv2BranchTutorial** para o Nome.
5. Clique em **OK** para criar o projeto.

## <a name="install-nuget-packages"></a>Instalar pacotes NuGet

1. clique em **Ferramentas** -> **Gestor de Pacotes NuGet** -> **Consola de Gestor de Pacotes**.
2. Na **Package Manager Console**, execute os seguintes comandos para instalar pacotes. Consulte a [pacote de nuget Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) com detalhes.

    ```
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager
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

2. Adicione estas variáveis estáticas à **Classe do programa**. Substitua os marcadores de posição pelos seus próprios valores. Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

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

3. Adicione o código seguinte ao método **Main** que cria uma instância da classe **DataFactoryManagementClient**. Utilize este objeto para criar uma fábrica de dados, um serviço ligado, conjuntos de dados e um pipeline. Também pode utilizar este objeto para monitorizar os detalhes de execução do pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Crie uma função “CreateOrUpdateDataFactory” no ficheiro Program.cs:

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



Adicione o código seguinte ao método **Principal** que cria uma **fábrica de dados**. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure
Crie uma função “StorageLinkedServiceDefinition” no ficheiro Program.cs:

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
Adicione o código seguinte ao método **Main** que cria um **serviço ligado do Armazenamento do Azure**. Saiba mais a partir da secção [Propriedades do serviço ligado do Blob do Azure](connector-azure-blob-storage.md#linked-service-properties) sobre detalhes e propriedades suportadas.

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta secção, pode criar dois conjuntos de dados: um para a origem e outro para o sink. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de origem
Adicione o código seguinte ao método **Main** que cria um **conjunto de dados do blob do Azure**. Saiba mais a partir da secção [Propriedades do conjunto de dados do Blob do Azure](connector-azure-blob-storage.md#dataset-properties) sobre detalhes e propriedades suportadas.

Defina um conjunto de dados que represente os dados de origem no Blob do Azure. Este conjunto de dados do Blob refere-se ao serviço ligado de Armazenamento do Microsoft Azure que criou no passo anterior e descreve:

- A localização do blob a copiar de: **FolderPath** e **FileName**;
- Repare na utilização de parâmetros para FolderPath. "sourceBlobContainer" é o nome do parâmetro e a expressão é substituída pelos valores transmitidos na execução do pipeline. A sintaxe para definir os parâmetros é `@pipeline().parameters.<parameterName>`

Crie uma função “SourceBlobDatasetDefinition” no ficheiro Program.cs

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

### <a name="create-a-dataset-for-sink-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de sink

Crie uma função “SourceBlobDatasetDefinition” no ficheiro Program.cs

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

Adicione o código seguinte ao método **Principal**, que cria os conjuntos de dados de origem e sink do Blob do Azure. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Criar um C# classe: EmailRequest
No projeto C#, crie uma classe com o nome **EmailRequest**. Esta classe define as propriedades que o pipeline envia no corpo do pedido ao enviar uma mensagem de e-mail. Neste tutorial, o pipeline envia quatro propriedades do pipeline para o e-mail:

- **Mensagem**: o corpo da mensagem de e-mail. No caso de uma cópia com êxito, esta propriedade contém detalhes da execução (número de dados escritos). No caso de uma cópia falhada, esta propriedade contém detalhes do erro.
- **Nome da fábrica de dados**: o nome da fábrica de dados
- **Nome do pipeline**: o nome do pipeline
- **Recetor**: Parâmetro é transmitido. Esta propriedade especifica o recetor do e-mail.

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
Para acionar o envio de uma mensagem de e-mail, utilize [Logic Apps](../logic-apps/logic-apps-overview.md) para definir o fluxo de trabalho. Para obter detalhes sobre a criação de um fluxo de trabalho de Aplicação Lógica, veja [Como criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

### <a name="success-email-workflow"></a>Fluxo de trabalho de e-mail de êxito 
Crie um fluxo de trabalho de Aplicação Lógica com o nome `CopySuccessEmail`. Definir o acionador do fluxo de trabalho como `When an HTTP request is received` e adicione uma ação de `Office 365 Outlook – Send an email`.

![Fluxo de trabalho de e-mail de êxito](media/tutorial-control-flow/success-email-workflow.png)

Para o acionador do pedido, preencha o `Request Body JSON Schema` com o seguinte JSON:

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
Este está alinhado com a classe **EmailRequest** que criou na secção anterior. 

O Pedido deve ter o seguinte aspeto no Estruturador da Aplicação Lógica:

![Estruturador da Aplicação Lógica - pedido](media/tutorial-control-flow/logic-app-designer-request.png)

Para a ação **Enviar E-mail**, personalize como pretende formatar o e-mail, utilizando as propriedades transmitidas no esquema JSON do Corpo do pedido. Segue-se um exemplo:

![Estruturador da Aplicação Lógica - ação enviar e-mail](media/tutorial-control-flow/send-email-action.png)

Anote o URL do pedido HTTP Post para o fluxo de trabalho de e-mail de êxito:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Fluxo de trabalho de e-mail de falha 
Clone o **CopySuccessEmail** e crie outro fluxo de trabalho de Logic Apps de **CopyFailEmail**. No acionador do pedido, o `Request Body JSON schema` é o mesmo. Altere simplesmente o formato da sua mensagem de e-mail, como o `Subject` para adaptar a um e-mail de falha. Segue-se um exemplo:

![Estruturador da Aplicação Lógica - fluxo de trabalho de e-mail de falha](media/tutorial-control-flow/fail-email-workflow.png)

Anote o URL do pedido HTTP Post para o fluxo de trabalho de e-mail de falha:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Agora, deve ter dois URL de fluxo de trabalho:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>Criar um pipeline
Adicione o código seguinte ao método Main que cria um pipeline com uma atividade de cópia e a propriedade dependsOn. Neste tutorial, o pipeline contém uma atividade: atividade de cópia, que aceita o conjunto de dados do Blob como uma origem e outro conjunto de dados do Blob como um sink. Mediante o êxito ou a falha da atividade de cópia, invoca tarefas de e-mail diferentes.

Neste pipeline, utiliza as seguintes funcionalidades:

- Parâmetros
- Atividade Web
- Dependência das atividades
- Utilizar um resultado de uma atividade como entrada para a atividade subsequente

Vamos dividir o pipeline seguinte, secção a secção:

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
Adicione o código seguinte ao método **Principal** que cria o pipeline:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>Parâmetros
A primeira secção do nosso pipeline define os parâmetros. 

- sourceBlobContainer – parâmetro no pipeline consumido pelo conjunto de dados do blob de origem.
- sinkBlobContainer – parâmetro no pipeline consumido pelo conjunto de dados do blob de sink.
- recetor – este parâmetro é utilizado por duas atividades Web no pipeline que envia e-mails de êxito ou falha para o recetor cujo endereço de e-mail é especificado por este parâmetro.


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Atividade Web
A Atividade Web permite uma chamada para qualquer ponto final REST. Para obter mais informações sobre a atividade, veja [Atividade Web](control-flow-web-activity.md). Este pipeline utiliza uma Atividade Web para chamar o fluxo de trabalho de e-mail do Logic Apps. Vai criar duas atividades Web: uma que chama o fluxo de trabalho **CopySuccessEmail** e uma chama o **CopyFailWorkFlow**.

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
Na propriedade "Url", cole os pontos finais do URL do pedido do seu fluxo de trabalho do Logic Apps em conformidade. Na propriedade "Body", transmita uma instância da classe "EmailRequest". O pedido de e-mail contém as seguintes propriedades:

- Mensagem – que transmite o valor de `@{activity('CopyBlobtoBlob').output.dataWritten`. Acede a uma propriedade da atividade de cópia anterior e transmite o valor de dataWritten. Para o caso de falha, passe a saída de erro em vez de `@{activity('CopyBlobtoBlob').error.message`.
- Nome da Fábrica de Dados – que transmite o valor de `@{pipeline().DataFactory}`. Esta é uma variável do sistema, que lhe permite aceder ao nome da fábrica de dados correspondente. Para obter uma lista de variáveis do sistema, veja o artigo [Variáveis do Sistema](control-flow-system-variables.md).
- Nome do pipeline – que transmite o valor de `@{pipeline().Pipeline}`. Também se trata de uma variável do sistema, que lhe permite aceder ao nome do pipeline correspondente. 
- Recetor – transmite o valor de "\@pipeline().parameters.receiver"). acedendo aos parâmetros do pipeline.
 
Este código cria um nova Dependência da Atividade, dependendo da atividade de cópia anterior com êxito.

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline
Adicione o código seguinte ao método **Main** que **aciona uma execução de pipeline**.

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
O método Principal final deve ter este aspeto. Compile e execute o seu programa para acionar uma execução de pipeline!

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

## <a name="monitor-a-pipeline-run"></a>Monitorizar uma execução de pipeline
1. Adicione o código seguinte ao método **Main** para verificar continuamente o estado da execução do pipeline até terminar de copiar os dados.

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

2. Adicione o código seguinte ao método **Main** que obtém os detalhes da execução da atividade de cópia; por exemplo, o tamanho dos dados lidos/escritos.

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
A consola imprime o progresso da criação da fábrica de dados, o serviço ligado, os conjuntos de dados, o pipeline e a execução de pipeline. Em seguida, verifica o estado de execução do pipeline. Aguarde até ver os detalhes da execução da atividade de cópia com o tamanho dos dados lidos/escritos. Em seguida, utilize ferramentas como o explorador de Armazenamento do Azure para verificar se o blob é copiado para "outputBlobPath" a partir de "inputBlobPath", conforme especificou nas variáveis.

**Resultado do exemplo:**

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

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um serviço ligado do Armazenamento do Azure
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para atividades posteriores
> * Utilizar passagem de parâmetros e variáveis de sistema
> * Iniciar um execução de pipeline
> * Monitorizar o pipeline e execuções de atividades

Agora, pode avançar para a secção Conceitos para obter mais informações sobre o Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines e atividades](concepts-pipelines-activities.md)
