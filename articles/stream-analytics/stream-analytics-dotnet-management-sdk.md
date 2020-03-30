---
title: Gestão .NET SDK para Azure Stream Analytics
description: Inicie-se com stream analytics management .NET SDK. Aprenda a configurar e gerir trabalhos de análise. Criar um projeto, entradas, saídas e transformações.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 20be2c56635faa4f77ae8e8e6afc3c1ece6d4942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426265"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Gestão .NET SDK: Criar e executar trabalhos de análise utilizando a API Azure Stream Analytics para .NET
Aprenda a configurar e executar trabalhos de análise utilizando o Stream Analytics API para .NET utilizando o SDK de Gestão .NET. Criar um projeto, criar fontes de entrada e produção, transformações e iniciar e parar empregos. Para os seus trabalhos de análise, pode transmitir dados a partir do armazenamento blob ou de um centro de eventos.

Consulte a documentação de referência de [gestão para a API Stream Analytics para .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

O Azure Stream Analytics é um serviço totalmente gerido que fornece baixa latência, altamente disponível, escalável, processamento complexo de eventos sobre dados de streaming na nuvem. O Stream Analytics permite que os clientes estabeleçam trabalhos de streaming para analisar os fluxos de dados, e permite-lhes conduzir perto de análises em tempo real.  

> [!NOTE]
> Atualizámos o código da amostra neste artigo com a versão Azure Stream Analytics Management .NET SDK v2.x. Para o código da amostra utilizando a versão SDK (1.x) utilização, consulte [Utilize a Gestão .NET SDK v1.x para Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, deve ter os seguintes requisitos:

* Instale o Visual Studio 2019 ou 2015.
* Descarregue e [instale Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Crie um Grupo de Recursos Azure na sua subscrição. O exemplo seguinte é uma amostra do script Azure PowerShell. Para obter informações da Azure PowerShell, consulte Instalar e configurar a [PowerShell Azure;](/powershell/azure/overview)  

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

* Configurar um objetivo de origem de entrada e saída para o trabalho ligar.

## <a name="set-up-a-project"></a>Configurar um projeto
Para criar um trabalho de análise, utilize o Stream Analytics API para .NET, primeiro configurar o seu projeto.

1. Crie uma aplicação de consola Visual Studio C# .NET.
2. Na consola do Gestor de Pacotes, execute os seguintes comandos para instalar os pacotes NuGet. O primeiro é o Azure Stream Analytics Management .NET SDK. A segunda é para autenticação do cliente Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Adicione a seguinte secção **de aplicaçõesDefinições** no ficheiro App.config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Substitua os valores por **SubscriçãoId** e **ActiveDirectoryTenantId** pela sua subscrição Azure e iDs de inquilino. Pode obter estes valores executando o seguinte cmdlet Azure PowerShell:

   ```powershell
      Get-AzureAccount
   ```

4. Adicione a seguinte referência no seu ficheiro .csproj:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Adicione as seguintes declarações **utilizando** o ficheiro fonte (Program.cs) no projeto:
   
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

6. Adicione um método de ajudante de autenticação:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Criar um cliente de gestão Stream Analytics
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

O valor da variável **resourceGroupName** deve ser o mesmo que o nome do grupo de recursos que criou ou escolheu nas etapas pré-requisitos.

Para automatizar o aspeto de apresentação credencial da criação de emprego, consulte a Autenticação de um diretor de serviço com o Gestor de [Recursos Azure.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

As restantes secções deste artigo assumem que este código está no início do método **Principal.**

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics
O seguinte código cria um trabalho stream analytics sob o grupo de recursos que definiu. Você adicionará uma entrada, saída e transformação ao trabalho mais tarde.

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

## <a name="create-a-stream-analytics-input-source"></a>Criar uma fonte de entrada stream Analytics
O seguinte código cria uma fonte de entrada Stream Analytics com o tipo de fonte de entrada blob e a serialização de CSV. Para criar uma fonte de entrada de hub de eventos, utilize **eventHubStreamInputUseSource** em vez de **BlobStreamInputUseDataSource**. Da mesma forma, pode personalizar o tipo de serialização da fonte de entrada.

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

As fontes de entrada, quer a partir do armazenamento Blob quer de um centro de eventos, estão ligadas a um trabalho específico. Para utilizar a mesma fonte de entrada para diferentes trabalhos, deve voltar a ligar para o método e especificar um nome de trabalho diferente.

## <a name="test-a-stream-analytics-input-source"></a>Teste uma fonte de entrada stream Analytics
O método **TestConnection** testa se o trabalho de Stream Analytics é capaz de se ligar à fonte de entrada, bem como outros aspetos específicos do tipo de origem de entrada. Por exemplo, na fonte de entrada blob que criou num passo anterior, o método verificará se o nome da conta de Armazenamento e o par de chaves podem ser usados para ligar à conta de Armazenamento, bem como verificar se o recipiente especificado existe.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Criar um alvo de saída stream Analytics
Criar um alvo de saída é semelhante à criação de uma fonte de entrada stream Analytics. Tal como as fontes de entrada, os alvos de saída estão ligados a um trabalho específico. Para utilizar o mesmo objetivo de saída para diferentes trabalhos, deve voltar a ligar para o método e especificar um nome de trabalho diferente.

O código seguinte cria um alvo de saída (base de dados Azure SQL). Pode personalizar o tipo de dados e/ou tipo de serialização do alvo de saída.

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

## <a name="test-a-stream-analytics-output-target"></a>Teste um alvo de saída de Stream Analytics
Um alvo de saída stream Analytics também tem o método **TestConnection** para testar ligações.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Criar uma transformação stream analytics
O código seguinte cria uma transformação stream analytics com a consulta "select * from Input" e especifica a atribuição de uma unidade de streaming para o trabalho de Stream Analytics. Para obter mais informações sobre o ajuste das unidades de streaming, consulte [os trabalhos da Scale Azure Stream Analytics](stream-analytics-scale-jobs.md).

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
Depois de criar um trabalho stream analytics e a sua entrada(s), output(s) e transformação, você pode iniciar o trabalho chamando o método **Iniciar.**

O seguinte código de amostra inicia um trabalho de Stream Analytics com uma hora de início de saída personalizada definida para 12 de dezembro de 2012, 12:12:12 UTC:

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
Pode parar um trabalho de Streaming Analytics, ligando para o método **Stop.**

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Eliminar um trabalho de Stream Analytics
O método **Delete** eliminará o trabalho, bem como os subrecursos subjacentes, incluindo os inputs, a produção ou a transformação do trabalho.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Obter suporte
Para mais assistência, experimente o nosso [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
Aprendeu o básico de usar um SDK .NET para criar e executar trabalhos de análise. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
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
