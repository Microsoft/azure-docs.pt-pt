---
title: SDK de .NET de gestão para o Azure Stream Analytics
description: Introdução ao SDK de .NET de gestão do Stream Analytics. Saiba como configurar e executar tarefas de análise. Crie um projeto, entradas, saídas e transformações.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4fa4a9a8d01d499dc431c8b182401226aa72bf1f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732573"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>SDK de .NET de gestão: Configurar e executar tarefas de análise com a API do Azure Stream Analytics para .NET
Saiba como configurar e executar tarefas de análise com a API do Stream Analytics para .NET com o SDK de .NET de gestão. Configurar um projeto, criar origens de entrada e saídas, transformações e iniciar e parar tarefas. Para as tarefas de análise, pode transmitir dados do armazenamento de BLOBs ou de um hub de eventos.

Consulte a [documentação de referência de gestão para a API do Stream Analytics para .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

O Azure Stream Analytics é um serviço totalmente gerido, fornecendo o processamento de eventos de baixa latência, altamente disponível, escalonável e complexos através de transmissão em fluxo de dados na cloud. Stream Analytics permite aos clientes configurar tarefas de transmissão em fluxo para analisar fluxos de dados e permita que sejam unidade perto de análise em tempo real.  

> [!NOTE]
> Atualizámos o código de exemplo neste artigo com a versão do SDK de .NET de gestão do Azure Stream Analytics v2.x. Para utilizar a versão do SDK lagecy (1.x) utiliza um código de exemplo, veja [utilizar o SDK de .NET de gestão v1.x para o Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* Instale o Visual Studio 2017 ou 2015.
* Transfira e instale [SDK .NET do Azure](https://azure.microsoft.com/downloads/).
* Crie um grupo de recursos do Azure na sua subscrição. Segue-se um exemplo de script do Azure PowerShell. Para obter informações do Azure PowerShell, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Configure uma origem de entrada e de destino de saída para a tarefa ligar a.

## <a name="set-up-a-project"></a>Configure um projeto
Para criar uma tarefa de análise utilizar a API do Stream Analytics para o .NET, primeiro configurar seu projeto.

1. Crie uma aplicação de consola do Visual Studio c# .NET.
2. Na consola do Gestor de pacotes, execute os seguintes comandos para instalar os pacotes de NuGet. A primeira é o SDK de .NET de gestão do Azure Stream Analytics. A segunda é para a autenticação de cliente do Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Adicione as seguintes **appSettings** secção para o ficheiro App. config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Substituir valores para **SubscriptionId** e **ActiveDirectoryTenantId** com os seus IDs de subscrição e de inquilino do Azure. Pode obter estes valores, executando o seguinte cmdlet do PowerShell do Azure:

   ```powershell
      Get-AzureAccount
   ```

4. Adicione a seguinte referência em seu arquivo. csproj:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Adicione as seguintes **usando** instruções para o ficheiro de origem (Program.cs) no projeto:
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Adicione um método de programa auxiliar de autenticação:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Criar um cliente de gestão do Stream Analytics
R **StreamAnalyticsManagementClient** objeto permite-lhe gerir a tarefa e os componentes de tarefa, como entrada, saída e de transformação.

Adicione o seguinte código ao início dos **Main** método:

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

O **resourceGroupName** valor da variável deve ser igual ao nome do grupo de recursos criado ou escolhido nos passos dos pré-requisitos.

Para automatizar o aspecto de apresentação de credencial de criação da tarefa, consulte [autenticar um principal de serviço com o Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

As seções restantes deste artigo partem do princípio de que esse código é no início da **Main** método.

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics
O código a seguir cria uma tarefa do Stream Analytics no grupo de recursos que definiu. Irá adicionar uma entrada, saída e de transformação da tarefa mais tarde.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Criar uma origem de entrada do Stream Analytics
O código a seguir cria uma origem de entrada do Stream Analytics com o tipo de origem de entrada de BLOBs e a serialização de CSV. Para criar uma origem de entrada de hub de eventos, utilize **EventHubStreamInputDataSource** em vez de **BlobStreamInputDataSource**. Da mesma forma, pode personalizar o tipo de serialização da origem de entrada.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Origens de entrada, de armazenamento de BLOBs ou de um hub de eventos estão associadas a uma tarefa específica. Para utilizar a mesma origem de entrada para as diferentes tarefas, tem de chamar o método novamente e especifique um nome de tarefa diferente.

## <a name="test-a-stream-analytics-input-source"></a>Testar uma origem de entrada do Stream Analytics
O **TestConnection** método testa se a tarefa do Stream Analytics é capaz de ligar à origem de entrada, bem como outros aspectos específicos para o tipo de origem de entrada. Por exemplo, na origem entrada de BLOBs que criou no passo anterior, o método irá verificar que o nome da conta de armazenamento e um par de chaves pode ser utilizado para ligar à conta de armazenamento, bem como para verificar que o contentor especificado existe.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Criar um destino de saída do Stream Analytics
A criação de um destino de saída é muito semelhante à criação de uma origem de entrada do Stream Analytics. Como as origens de entrada, saída destinos estão associados a uma tarefa específica. Para utilizar o mesmo destino de saída para as diferentes tarefas, tem de chamar o método novamente e especifique um nome de tarefa diferente.

O código seguinte cria um destino de saída (base de dados SQL do Azure). Pode personalizar o tipo de dados do destino de saída e/ou tipo de serialização.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testar um destino de saída do Stream Analytics
Também tem um destino de saída do Stream Analytics os **TestConnection** método para ligações de teste.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Criar uma transformação do Stream Analytics
O código seguinte cria uma transformação do Stream Analytics com a consulta "selecionar * da entrada" e especifica alocar uma unidade de transmissão em fluxo para a tarefa do Stream Analytics. Para obter mais informações sobre o ajuste de unidades de transmissão em fluxo, consulte [tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Como entrada e saída, uma transformação também está associada à tarefa do Stream Analytics específica, que foi criado em.

## <a name="start-a-stream-analytics-job"></a>Iniciar uma tarefa do Stream Analytics
Depois de criar uma tarefa do Stream Analytics e seu input(s), saída e transformação, pode iniciar a tarefa ao chamar o **iniciar** método.

O seguinte exemplo inicia de código, uma tarefa do Stream Analytics com uma hora de início da saída personalizado definido como 12 de Dezembro de 2012, 12:12:12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Parar uma tarefa do Stream Analytics
Pode parar uma tarefa do Stream Analytics em execução ao chamar o **parar** método.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Eliminar uma tarefa do Stream Analytics
O **eliminar** método irá eliminar a tarefa, bem como os recursos de secundárias subjacentes, incluindo input(s), saída e transformação da tarefa.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Obter suporte
Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes
Que aprendeu as noções básicas da utilização de um SDK .NET para criar e executar tarefas de análise. Para saber mais, consulte o seguinte:

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Management SDK de .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
