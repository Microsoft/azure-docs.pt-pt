---
title: Monitorar lote com insights Aplicativo Azure | Microsoft Docs
description: Saiba como instrumentar um aplicativo .NET do lote do Azure usando a biblioteca do Aplicativo Azure insights.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: jushiman
ms.openlocfilehash: c69ef0bf20e2ade15d2278d0fc2fabd75f39153b
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029494"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorar e depurar um aplicativo .NET do lote do Azure com o Application Insights

O [Application insights](../azure-monitor/app/app-insights-overview.md) fornece uma maneira elegante e poderosa para que os desenvolvedores monitorem e depurem aplicativos implantados nos serviços do Azure. Use Application Insights para monitorar contadores de desempenho e exceções, bem como instrumentar seu código com métricas e rastreamento personalizados. A integração do Application Insights ao aplicativo do lote do Azure permite que você tenha informações aprofundadas sobre comportamentos e investigue problemas quase em tempo real.

Este artigo mostra como adicionar e configurar a biblioteca de Application Insights em sua solução .NET do lote do Azure e instrumentar o código do aplicativo. Ele também mostra maneiras de monitorar seu aplicativo por meio do portal do Azure e criar painéis personalizados. Para Application Insights suporte em outros idiomas, examine a [documentação idiomas, plataformas e integrações](../azure-monitor/app/platforms.md).

Um exemplo C# de solução com código para acompanhar este artigo está disponível no [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Este exemplo adiciona Application Insights código de instrumentação ao exemplo [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) . Se você não estiver familiarizado com esse exemplo, tente Compilar e executar o TopNWords primeiro. Isso ajudará você a entender um fluxo de trabalho básico do lote de processamento de um conjunto de blobs de entrada em paralelo em vários nós de computação. 

> [!TIP]
> Como alternativa, configure sua solução de lote para exibir dados de Application Insights, como contadores de desempenho de VM no Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta de cliente autônoma, gratuita e com recursos avançados para ajudar a criar, depurar e monitorar aplicativos do lote do Azure. Transfira um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows. Consulte o [repositório de informações de lote](https://github.com/Azure/batch-insights) para obter etapas rápidas para habilitar Application insights dados em batch Explorer. 
>

## <a name="prerequisites"></a>Pré-requisitos
* [Visual Studio 2017 ou posterior](https://www.visualstudio.com/vs)

* [Conta do lote e conta de armazenamento vinculada](batch-account-create-portal.md)

* [Application Insights recurso](../azure-monitor/app/create-new-resource.md )
  
   * Use o portal do Azure para criar um *recurso*de Application insights. Selecione o **tipo de aplicativo**geral.

   * Copie a [chave de instrumentação](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) do Portal. Ele é necessário mais adiante neste artigo.
  
  > [!NOTE]
  > Você pode ser [cobrado](https://azure.microsoft.com/pricing/details/application-insights/) pelos dados armazenados em Application insights. Isso inclui os dados de diagnóstico e monitoramento discutidos neste artigo.
  > 

## <a name="add-application-insights-to-your-project"></a>Adicionar as Informações da Aplicação ao seu projeto

O pacote NuGet **Microsoft. ApplicationInsights. WindowsServer** e suas dependências são necessários para seu projeto. Adicione ou restaure-os no projeto do seu aplicativo. Para instalar o pacote, use o comando `Install-Package` ou o Gerenciador de pacotes NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Referencie Application Insights de seu aplicativo .NET usando o namespace **Microsoft. ApplicationInsights** .

## <a name="instrument-your-code"></a>Instrumentar seu código

Para instrumentar seu código, sua solução precisa criar um Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). No exemplo, o TelemetryClient carrega sua configuração do arquivo [ApplicationInsights. config](../azure-monitor/app/configuration-with-applicationinsights-config.md) . Certifique-se de atualizar ApplicationInsights. config nos projetos a seguir com sua chave de instrumentação de Application Insights: Microsoft. Azure. Batch. Samples. TelemetryStartTask e TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Além disso, adicione a chave de instrumentação no arquivo TopNWords.cs.

O exemplo em TopNWords.cs usa as seguintes [chamadas de instrumentação](../azure-monitor/app/api-custom-events-metrics.md) da API Application insights:
* `TrackMetric()`-controla quanto tempo, em média, um nó de computação leva para baixar o arquivo de texto necessário.
* `TrackTrace()`-adiciona chamadas de depuração ao seu código.
* `TrackEvent()`-rastreia eventos interessantes a serem capturados.

Este exemplo deixa a manipulação de exceções de propósito. Em vez disso, Application Insights relata automaticamente as exceções sem tratamento, o que melhora significativamente a experiência de depuração. 

O trecho a seguir ilustra como usar esses métodos.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Auxiliar de inicialização de telemetria do lote do Azure
Ao relatar a telemetria para um determinado servidor e instância, Application Insights usa a função de VM do Azure e o nome da VM para os valores padrão. No contexto do lote do Azure, o exemplo mostra como usar o nome do pool e o nome do nó de computação em vez disso. Use um [inicializador de telemetria](../azure-monitor/app/api-filtering-sampling.md#add-properties) para substituir os valores padrão. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Para habilitar o inicializador de telemetria, o arquivo ApplicationInsights. config no projeto TopNWordsSample inclui o seguinte:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Atualizar o trabalho e as tarefas para incluir Application Insights binários

Para que o Application Insights seja executado corretamente em seus nós de computação, verifique se os binários estão posicionados corretamente. Adicione os binários necessários à coleção de arquivos de recursos da tarefa para que eles sejam baixados no momento em que a tarefa for executada. Os trechos a seguir são semelhantes ao código em Job.cs.

Primeiro, crie uma lista estática de arquivos de Application Insights para carregar.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Em seguida, crie os arquivos de preparo usados pela tarefa.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

O método `FileToStage` é uma função auxiliar no exemplo de código que permite que você carregue facilmente um arquivo do disco local para um blob de armazenamento do Azure. Cada arquivo é baixado posteriormente para um nó de computação e referenciado por uma tarefa.

Por fim, adicione as tarefas ao trabalho e inclua os binários de Application Insights necessários.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Exibir dados no portal do Azure

Agora que você configurou o trabalho e as tarefas para usar Application Insights, execute o trabalho de exemplo em seu pool. Navegue até a portal do Azure e abra o recurso de Application Insights que você provisionou. Depois que o pool é provisionado, você deve começar a ver os dados fluindo e registrando-se. O restante deste artigo aborda apenas alguns recursos de Application Insights, mas fique à vontade para explorar o conjunto completo de recursos.

### <a name="view-live-stream-data"></a>Exibir dados de transmissão ao vivo

Para exibir os logs de rastreamento no recurso do Application insights, clique em **Live Stream**. A captura de tela a seguir mostra como exibir dados dinâmicos provenientes dos nós de computação no pool, por exemplo, o uso da CPU por nó de computação.

![Dados do nó de computação de fluxo ao vivo](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Exibir logs de rastreamento

Para exibir os logs de rastreamento no recurso Application insights, clique em **Pesquisar**. Esta exibição mostra uma lista de dados de diagnóstico capturados por Application Insights incluindo rastreamentos, eventos e exceções. 

A captura de tela a seguir mostra como um único rastreamento para uma tarefa é registrado e posteriormente consultado para fins de depuração.

![Imagem de logs de rastreamento](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Exibir exceções sem tratamento

As capturas de tela a seguir mostram como o Application Insights registra em log as exceções geradas do seu aplicativo. Nesse caso, em segundos do aplicativo que lança a exceção, você pode analisar uma exceção específica e diagnosticar o problema.

![Exceções não processadas](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Medir o tempo de download do blob

As métricas personalizadas também são uma ferramenta valiosa no Portal. Por exemplo, você pode exibir o tempo médio que cada nó de computação levou para baixar o arquivo de texto necessário que estava processando.

Para criar um gráfico de exemplo:
1. Em seu recurso de Application Insights, clique em **Metrics Explorer** > **Adicionar gráfico**.
2. Clique em **Editar** no gráfico que foi adicionado.
2. Atualize os detalhes do gráfico da seguinte maneira:
   * Defina o **tipo de gráfico** como **grade**.
   * Defina a **agregação** como **média**.
   * Defina **Agrupar por** para **NodeId**.
   * Em **métricas**, selecione **download de blob**de > **personalizado** em segundos.
   * Ajuste a **paleta de cores** de exibição à sua escolha. 

![Tempo de download do blob por nó](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Monitorar nós de computação continuamente

Você deve ter notado que todas as métricas, incluindo contadores de desempenho, são registradas somente quando as tarefas estão em execução. Esse comportamento é útil porque limita a quantidade de dados que Application Insights logs. No entanto, há casos em que você sempre gostaria de monitorar os nós de computação. Por exemplo, eles podem estar executando o trabalho em segundo plano que não está agendado por meio do serviço de lote. Nesse caso, configure um processo de monitoramento para ser executado durante a vida útil do nó de computação. 

Uma maneira de atingir esse comportamento é gerar um processo que carregue a biblioteca de Application Insights e seja executado em segundo plano. No exemplo, a tarefa inicial carrega os binários no computador e mantém um processo em execução indefinidamente. Configure o arquivo de configuração Application Insights para que esse processo emita dados adicionais nos quais você está interessado, como contadores de desempenho.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Para aumentar a capacidade de gerenciamento de sua solução, você pode agrupar o assembly em um [pacote de aplicativos](./batch-application-packages.md). Em seguida, para implantar o pacote de aplicativos automaticamente em seus pools, adicione uma referência de pacote de aplicativo à configuração do pool.
>

## <a name="throttle-and-sample-data"></a>Limitação e dados de exemplo 

Devido à natureza em larga escala dos aplicativos do lote do Azure em execução na produção, talvez você queira limitar a quantidade de dados coletados por Application Insights para gerenciar os custos. Consulte [amostragem no Application insights](../azure-monitor/app/sampling.md) para ver alguns mecanismos para conseguir isso.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [Application insights](../azure-monitor/app/app-insights-overview.md).

* Para Application Insights suporte em outros idiomas, examine a [documentação idiomas, plataformas e integrações](../azure-monitor/app/platforms.md).


