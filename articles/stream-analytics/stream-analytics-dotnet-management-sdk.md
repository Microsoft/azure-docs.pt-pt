---
title: Gestão .NET SDK para Azure Stream Analytics
description: Começa com o Stream Analytics Management .NET SDK. Aprenda a montar e executar trabalhos de análise. Criar um projeto, entradas, saídas e transformações.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/12/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9adc4c92e3e637b9d3e18249b5de00782a94baab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232890"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Gestão .NET SDK: Criar e executar trabalhos de análise utilizando a API AZure Stream Analytics para .NET
Saiba como configurar e executar trabalhos de análise utilizando a API stream Analytics para .NET utilizando a Management .NET SDK. Crie um projeto, crie fontes de entrada e saída, transformações e iniciar e parar empregos. Para os seus trabalhos de análise, pode transmitir dados a partir do armazenamento blob ou de um centro de eventos.

Consulte a [documentação de referência de gestão para a API stream Analytics para .NET](/previous-versions/azure/dn889315(v=azure.100)).

O Azure Stream Analytics é um serviço totalmente gerido que fornece um processamento de eventos complexo e de baixa latência, altamente disponível, escalável e complexo sobre os dados de streaming na nuvem. O Stream Analytics permite que os clientes criem trabalhos de streaming para analisar fluxos de dados, e permite-lhes conduzir perto de análises em tempo real.  

> [!NOTE]
> Atualizámos o código de amostra neste artigo com a versão Azure Stream Analytics Management .NET SDK v2.x. Para obter o código de amostra utilizando a versão SDK de utilização (1.x), consulte [utilizar a versão Management .NET SDK v1.x para Stream Analytics]().

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar este artigo, deve ter os seguintes requisitos:

* Instale o Visual Studio 2019 ou 2015.
* Faça o download e instale [o Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Crie um Grupo de Recursos Azure na sua subscrição. O exemplo a seguir é uma amostra do script Azure PowerShell. Para obter informações sobre a Azure PowerShell, consulte [instalar e configurar a Azure PowerShell;](/powershell/azure/)  

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

* Crie uma fonte de entrada e um alvo de saída para o trabalho se ligar.

## <a name="set-up-a-project"></a>Criar um projeto
Para criar um trabalho de análise, utilize a API stream Analytics para .NET, configurar primeiro o seu projeto.

1. Criar uma aplicação de consola Visual Studio C# .NET.
2. Na Consola Gestor de Pacotes, execute os seguintes comandos para instalar os pacotes NuGet. O primeiro é o Azure Stream Analytics Management .NET SDK. A segunda é para autenticação do cliente Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Adicione a seguinte **secção de appsSettings** ao ficheiro App.config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Substitua os valores **de SubscriptionId** e **ActiveDirectoryTenantId** pela subscrição Azure e IDs de inquilino. Pode obter estes valores executando o seguinte cmdlet Azure PowerShell:

   ```powershell
      Get-AzureAccount
   ```

4. Adicione a seguinte referência no seu ficheiro .csproj:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Adicione as **seguintes declarações** ao ficheiro de origem (Programa.cs) no projeto:
   
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

6. Adicione um método de ajuda à autenticação:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Criar um cliente de gestão stream Analytics
Um objeto **StreamAnalyticsManagementClient** permite-lhe gerir o trabalho e os componentes de trabalho, tais como entrada, saída e transformação.

Adicione o seguinte código ao início do método **Principal:**

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

O valor da variável de nome de **recurso doGroup** deve ser o mesmo que o nome do grupo de recursos que criou ou escolheu nos passos pré-requisitos.

Para automatizar o aspeto de apresentação credencial da criação de emprego, consulte a [Autenticação de um diretor de serviço com o Azure Resource Manager.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

As restantes secções deste artigo pressupõem que este código está no início do método **Principal.**

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics
O código a seguir cria um trabalho stream Analytics no grupo de recursos que definiu. Mais tarde, adicionará uma entrada, saída e transformação ao trabalho.

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

## <a name="create-a-stream-analytics-input-source"></a>Criar uma fonte de entrada Stream Analytics
O código a seguir cria uma fonte de entrada Stream Analytics com o tipo de fonte de entrada blob e a serialização do CSV. Para criar uma fonte de entrada de hub de eventos, utilize **o EventHubStreamInputDataSource** em vez de **BlobStreamInputDataSource**. Da mesma forma, pode personalizar o tipo de serialização da fonte de entrada.

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

As fontes de entrada, seja a partir do armazenamento blob ou de um centro de eventos, estão ligadas a um trabalho específico. Para utilizar a mesma fonte de entrada para diferentes trabalhos, deve voltar a ligar para o método e especificar um nome de trabalho diferente.

## <a name="test-a-stream-analytics-input-source"></a>Testar uma fonte de entrada stream Analytics
O método **TestConnection** testa se o trabalho stream Analytics é capaz de ligar à fonte de entrada, bem como outros aspetos específicos do tipo de fonte de entrada. Por exemplo, na fonte de entrada blob que criou num passo anterior, o método verificará se o nome da conta de Armazenamento e o par de chaves podem ser utilizados para ligar à conta de Armazenamento, bem como verificar se o recipiente especificado existe.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```
O resultado da chamada TestConnection é um objeto *ResourceTestResult* que contém duas propriedades:

- *estado*: Pode ser uma das seguintes cordas: ["TestNotAttempted", "TestSucceed", "TestFailed"]
- *erro*: É do tipo ErrorResponse contendo as seguintes propriedades:
   - *código:* uma propriedade requerida de cadeia de tipo. O valor é o padrão System.Net.HttpStatusCode recebido durante os testes.
   - *mensagem:* uma propriedade requerida de tipo de cadeia que representa o erro. 

## <a name="create-a-stream-analytics-output-target"></a>Criar um alvo de saída stream Analytics
Criar um alvo de saída é semelhante à criação de uma fonte de entrada Stream Analytics. Tal como as fontes de entrada, os alvos de saída estão ligados a um trabalho específico. Para utilizar o mesmo alvo de saída para diferentes trabalhos, deve voltar a ligar para o método e especificar um nome de trabalho diferente.

O seguinte código cria um alvo de saída (Base de Dados Azure SQL). Pode personalizar o tipo de dados e/ou tipo de serialização do alvo de saída.

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

## <a name="test-a-stream-analytics-output-target"></a>Teste um alvo de saída stream Analytics
Um alvo de saída stream Analytics também tem o método **TestConnection** para testar ligações.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Criar uma transformação stream Analytics
O código a seguir cria uma transformação stream Analytics com a consulta "select * from Input" e especifica a atribuição de uma unidade de streaming para o trabalho stream Analytics. Para obter mais informações sobre o ajuste das unidades de streaming, consulte [os trabalhos de Scale Azure Stream Analytics](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Tal como a entrada e a saída, uma transformação também está ligada ao trabalho específico do Stream Analytics em que foi criado.

## <a name="start-a-stream-analytics-job"></a>Iniciar uma tarefa do Stream Analytics
Depois de criar um trabalho stream Analytics e seus inputs( s), output(s) e transformação, você pode começar o trabalho chamando o método **Iniciar.**

O seguinte código de amostra inicia um trabalho stream Analytics com uma hora de início de saída personalizada definida para 12 de dezembro de 2012, 12:12:12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Pare um trabalho de Stream Analytics
Pode parar um trabalho de Stream Analytics em execução, chamando o método **Stop.**

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Excluir um trabalho de Stream Analytics
O método **Delete** eliminará o trabalho, bem como os sub-recursos subjacentes, incluindo entradas, output(s) e transformação do trabalho.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para a Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes
Aprendeu o básico de usar um .NET SDK para criar e executar trabalhos de análise. Para saber mais, leia os artigos seguintes:

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Management .NET SDK](/previous-versions/azure/dn889315(v=azure.100)).
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: ../storage/blobs/storage-quickstart-blobs-dotnet.md

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
