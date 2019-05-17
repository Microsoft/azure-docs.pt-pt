---
title: Monitorizar o Batch com o Azure Application Insights | Documentos da Microsoft
description: Saiba como instrumentar uma aplicação do Azure Batch .NET usando a biblioteca do Azure Application Insights.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: c527b0b10a2b9a351b242d0858fdbe64687970a7
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595297"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorizar e depurar uma aplicação .NET do Batch do Azure com o Application Insights

[O Application Insights](../azure-monitor/app/app-insights-overview.md) fornece uma forma elegante e eficiente para os desenvolvedores a monitorizar e depurar aplicações implementadas nos serviços do Azure. Utilize o Application Insights para contadores do monitor de desempenho e exceções, bem como meio de seu código com métricas personalizadas e rastreamento de eventos. Integração do Application Insights com a sua aplicação do Azure Batch permite-lhe informações mais detalhadas sobre comportamentos e investigar problemas em tempo quase real.

Este artigo mostra como adicionar e configurar a biblioteca do Application Insights na sua solução do Azure Batch .NET e instrumentar o código da aplicação. Ela também mostra as formas de monitorizar a sua aplicação através do portal do Azure e criar dashboards personalizados. Application Insights de suporte em outras linguagens, veja a [documentação de linguagens, plataformas e integrações](../azure-monitor/app/platforms.md).

Um exemplo c# de solução com o código que acompanhará este artigo está disponível no [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Este exemplo adiciona o código de instrumentação do Application Insights para o [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) exemplo. Se não estiver familiarizado com esse exemplo, tente criar e executar TopNWords pela primeira vez. Isso ajudará a compreender um fluxo de trabalho do Batch básico do processamento de um conjunto de blobs de entrada em paralelo em vários nós de computação. 

> [!TIP]
> Como alternativa, configure a sua solução do Batch para apresentar dados do Application Insights, como contadores de desempenho da VM no Explorador do Batch. [Explorador do batch](https://github.com/Azure/BatchExplorer) é uma ferramenta de cliente autónoma gratuita e rica em funcionalidades, para ajudar a criar, depurar e monitorizar aplicações do Azure Batch. Transfira um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows. Consulte a [repositório de informações de batch](https://github.com/Azure/batch-insights) para passos rápidos ativar a dados do Application Insights no Explorador do Batch. 
>

## <a name="prerequisites"></a>Pré-requisitos
* [Visual Studio 2017 ou posterior](https://www.visualstudio.com/vs)

* [Conta do batch e a conta de armazenamento ligada](batch-account-create-portal.md)

* [Recurso do Application Insights](../azure-monitor/app/create-new-resource.md )
  
   * Utilizar o portal do Azure para criar um Application Insights *recursos*. Selecione o *gerais* **tipo de aplicação**.

   * Copiar o [chave de instrumentação](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) do portal. É necessário neste artigo.
  
  > [!NOTE]
  > Pode estar [cobrado](https://azure.microsoft.com/pricing/details/application-insights/) para os dados armazenados no Application Insights. Isto inclui o diagnóstico e monitorização dos dados discutidos neste artigo.
  > 

## <a name="add-application-insights-to-your-project"></a>Adicionar as Informações da Aplicação ao seu projeto

O **Windowsserver** pacote NuGet e suas dependências são necessárias para seu projeto. Adicionar ou restaurá-las ao projeto de seu aplicativo. Para instalar o pacote, utilize o `Install-Package` comando ou o Gestor de pacotes NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Referenciar o Application Insights através da aplicação .NET, utilizando o **applicationinsights** espaço de nomes.

## <a name="instrument-your-code"></a>Instrumentar o seu código

Para instrumentar o seu código, sua solução tem de criar um Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). No exemplo, o TelemetryClient carrega a respetiva configuração a partir da [applicationinsights. config](../azure-monitor/app/configuration-with-applicationinsights-config.md) ficheiro. Certifique-se de que atualizar o applicationinsights. config nos seguintes projetos com a sua chave de instrumentação do Application Insights: Microsoft.Azure.Batch.Samples.TelemetryStartTask e TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Também adicione a chave de instrumentação no ficheiro TopNWords.cs.

O exemplo na TopNWords.cs utiliza as seguintes [chamadas de instrumentação](../azure-monitor/app/api-custom-events-metrics.md) da API do Application Insights:
* `TrackMetric()` -Controla há muito tempo, em média, um nó de computação necessário para transferir o ficheiro de texto necessário.
* `TrackTrace()` -Adiciona chamadas de depuração para o seu código.
* `TrackEvent()` -Roteiros interessante eventos para capturar.

Neste exemplo propositadamente deixa o processamento de exceções. Em vez disso, o Application Insights automaticamente relatórios de exceções sem tratamento, que melhora significativamente a experiência de depuração. 

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Auxiliar do inicializador de telemetria de lote do Azure
Quando o relatório de telemetria para um determinado servidor e instância, o Application Insights utiliza o nome de função de VM do Azure e a VM para os valores predefinidos. No contexto do Azure Batch, o exemplo mostra como utilizar o nome do conjunto e o nome do nó de computação em vez disso. Utilize um [inicializador de telemetria](../azure-monitor/app/api-filtering-sampling.md#add-properties) para substituir os valores predefinidos. 

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

Para ativar o inicializador de telemetria, o ficheiro applicationinsights. config no projeto TopNWordsSample inclui o seguinte:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Atualizar a tarefa e tarefas para incluir os binários do Application Insights

Na ordem do Application Insights para serem executados corretamente em nós de computação, certificar-se de que os binários são colocados corretamente. Adicione os binários necessários a coleção de ficheiros de recursos de sua tarefa, para que eles ser descarregados no momento que a tarefa ser executada. Os seguintes fragmentos são semelhantes ao código no Job.cs.

Primeiro, crie uma lista estática de ficheiros do Application Insights para carregar.

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

Em seguida, crie os ficheiros de testes que são utilizados pela tarefa.
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

O `FileToStage` método é uma função de auxiliar no código de exemplo que permite-lhe para facilmente carregar um ficheiro de disco local para um blob de armazenamento do Azure. Cada ficheiro mais tarde é transferido para um nó de computação e referenciado por uma tarefa.

Por fim, adicione as tarefas ao trabalho e incluem os binários necessários do Application Insights.
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

## <a name="view-data-in-the-azure-portal"></a>Ver dados no portal do Azure

Agora que configurou o trabalho e tarefas para utilizar o Application Insights, execute o trabalho de exemplo no seu conjunto. Navegue para o portal do Azure e abra o recurso do Application Insights que aprovisionou. Depois do conjunto está aprovisionado, deve começar ver o fluxo de dados e obter a sessão iniciada. O restante deste artigo aborda apenas algumas funcionalidades do Application Insights, mas fique à vontade explorar o conjunto completo de recursos.

### <a name="view-live-stream-data"></a>Ver dados de transmissão em direto

Para ver registos de rastreio no seu recurso de informações de aplicações, clique em **Live Stream**. Captura de ecrã seguinte mostra como ver dados ao vivo, proveniente de nós de computação no conjunto, por exemplo a utilização da CPU por nó de computação.

![Live transmitir dados do nó de computação](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Ver registos de rastreio

Para ver registos de rastreio no seu recurso de informações de aplicações, clique em **pesquisa**. Esta vista mostra uma lista de dados de diagnóstico capturados pelo Application Insights, incluindo exceções, eventos e rastreios. 

Captura de ecrã seguinte mostra como um rastreio único para uma tarefa é iniciado e consultado mais tarde para fins de depuração.

![Imagem de registos de rastreio](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Ver exceções não processadas

As capturas de ecrã seguinte mostra como o Application Insights regista exceções acionadas a partir da sua aplicação. Neste caso, dentro de segundos do aplicativo gera a exceção, pode explorar uma exceção específica e diagnosticar o problema.

![Exceções não processadas](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Tempo de transferência de blob de medida

Métricas personalizadas também são uma ferramenta valiosa no portal. Por exemplo, pode exibir o tempo médio, que demorou cada nó de computação para transferir o ficheiro de texto necessário estava processar.

Para criar um gráfico de exemplo:
1. No recurso do Application Insights, clique em **Explorador de métricas** > **adicionar gráfico**.
2. Clique em **editar** no gráfico que foi adicionado.
2. Atualize os detalhes do gráfico da seguinte forma:
   * Definir **tipo de gráfico** ao **Grid**.
   * Definir **agregação** ao **médio**.
   * Definir **Agrupar por** ao **NodeId**.
   * Na **métricas**, selecione **personalizada** > **Blob download em segundos**.
   * Exibição de ajuste **paleta de cores** à sua escolha. 

![Tempo de transferência de blob por nó](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Nós de computação de monitor continuamente

Talvez tenha notado que todas as métricas, incluindo os contadores de desempenho, apenas são registadas quando as tarefas estão em execução. Este comportamento é útil pois limita a quantidade de dados que registra em log Application Insights. No entanto, há casos em que sempre deseja monitorar os nós de computação. Por exemplo, eles poderiam ser executados trabalho em segundo plano que não está a ser agendado por meio do serviço de Batch. Neste caso, configure um processo de monitoramento para execução durante o ciclo de vida de nó de computação. 

Uma forma de conseguir este comportamento é gerar um processo que carrega a biblioteca do Application Insights e é executado em segundo plano. No exemplo, a tarefa de início carrega os binários na máquina e mantém um processo em execução indefinidamente. Configure o ficheiro de configuração do Application Insights para que este processo emitir dados adicionais que está interessado, como contadores de desempenho.

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
> Para aumentar a capacidade de gerenciamento da sua solução, pode agrupar o assembly num [pacote de aplicação](./batch-application-packages.md). Em seguida, para implementar o pacote de aplicação automaticamente seus conjuntos, adicione uma referência de pacote de aplicação para a configuração do conjunto.
>

## <a name="throttle-and-sample-data"></a>Dados de exemplo e limitação 

Devido à natureza em grande escala de aplicações do Azure Batch em execução na produção, pode querer limitar a quantidade de dados recolhidos pelo Application Insights para gerir os custos. Ver [amostragem no Application Insights](../azure-monitor/app/sampling.md) para alguns mecanismos atingir esse objetivo.


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [Application Insights](../azure-monitor/app/app-insights-overview.md).

* Application Insights de suporte em outras linguagens, veja a [documentação de linguagens, plataformas e integrações](../azure-monitor/app/platforms.md).


