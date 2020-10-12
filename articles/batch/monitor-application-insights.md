---
title: Monitor Batch com Insights de Aplicação Azure
description: Saiba como instrumentar uma aplicação Azure Batch .NET utilizando a biblioteca Azure Application Insights.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 04/05/2018
ms.openlocfilehash: d06e2b61725f05d025acd8a2995ea041f138ae4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933567"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitore e depure uma aplicação Azure Batch .NET com Insights de Aplicação

[A Application Insights](../azure-monitor/app/app-insights-overview.md) fornece uma forma elegante e poderosa de os desenvolvedores monitorizarem e depurarem aplicações implantadas nos serviços da Azure. Utilize insights de aplicação para monitorizar os contadores de desempenho e exceções, bem como instrumento o seu código com métricas e rastreios personalizados. A integração de Insights de Aplicação com a sua aplicação Azure Batch permite-lhe obter informações profundas sobre comportamentos e investigar problemas em tempo quase real.

Este artigo mostra como adicionar e configurar a biblioteca Application Insights na sua solução Azure Batch .NET e instrumento o seu código de aplicação. Também mostra formas de monitorizar a sua aplicação através do portal Azure e construir dashboards personalizados. Para o suporte de Insights de Aplicação em outras línguas, olhe para a [documentação de línguas, plataformas e integrações.](../azure-monitor/app/platforms.md)

Uma solução de amostra C# com código para acompanhar este artigo está disponível no [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Este exemplo adiciona o código de instrumentação application Insights ao exemplo [do TopNWords.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Se não está familiarizado com esse exemplo, tente construir e executar o TopNWords primeiro. Ao fazê-lo, irá ajudá-lo a compreender um fluxo básico de trabalho do Lote de processamento de um conjunto de bolhas de entrada paralelamente em múltiplos nós de computação. 

> [!TIP]
> Como alternativa, configuure a sua solução Batch para exibir dados de Insights de Aplicação, tais como contadores de desempenho VM no Batch Explorer. [O Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta cliente independente e independente, com destaque para ajudar a criar, depurar e monitorizar as aplicações do Azure Batch. Transfira um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows. Consulte o [repo de insights de lote](https://github.com/Azure/batch-insights) para obter passos rápidos para ativar os dados de Insights de Aplicação no Batch Explorer. 
>

## <a name="prerequisites"></a>Pré-requisitos
* [Estúdio Visual 2017 ou mais tarde](https://www.visualstudio.com/vs)

* [Conta de lote e conta de armazenamento ligada](batch-account-create-portal.md)

* [Recurso do Application Insights](../azure-monitor/app/create-new-resource.md )
  
   * Utilize o portal Azure para criar um *recurso*Application Insights . Selecione o **tipo de Aplicação** *Geral* .

   * Copie a [chave de instrumentação](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) do portal. É necessário mais tarde neste artigo.
  
  > [!NOTE]
  > Pode ser [cobrado](https://azure.microsoft.com/pricing/details/application-insights/) pelos dados armazenados no Application Insights. Isto inclui os dados de diagnóstico e monitorização discutidos neste artigo.
  > 

## <a name="add-application-insights-to-your-project"></a>Adicionar Insights de Aplicação ao seu projeto

O pacote **Microsoft.ApplicationInsights.WindowsServer** NuGet e as suas dependências são necessários para o seu projeto. Adicione ou devolva ao projeto da sua aplicação. Para instalar a embalagem, utilize o gestor de `Install-Package` pacotes de comando ou NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Insights de aplicação de referência da sua aplicação .NET utilizando o espaço de nome **Microsoft.ApplicationInsights.**

## <a name="instrument-your-code"></a>Instrumento o seu código

Para instrumentar o seu código, a sua solução precisa de criar um [Telemetria de](/dotnet/api/microsoft.applicationinsights.telemetryclient)Insights de Aplicação . No exemplo, o TelemetriaClient carrega a sua configuração a partir do ficheiro [ApplicationInsights.config.](../azure-monitor/app/configuration-with-applicationinsights-config.md) Certifique-se de atualizar ApplicationInsights.config nos seguintes projetos com a sua chave de instrumentação Application Insights: Microsoft.Azure.Batch. Samples.TelemetryStartTask e TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Adicione também a chave de instrumentação no ficheiro TopNWords.cs.

O exemplo em TopNWords.cs utiliza as seguintes chamadas de [instrumentação](../azure-monitor/app/api-custom-events-metrics.md) da API application insights:
* `TrackMetric()` - Rastreia o tempo, em média, de um nó computacional para descarregar o ficheiro de texto necessário.
* `TrackTrace()` - Adiciona chamadas de depuragem ao seu código.
* `TrackEvent()` - Rastreia eventos interessantes para capturar.

Este exemplo deixa propositadamente de fora o tratamento de exceções. Em vez disso, o Application Insights reporta automaticamente exceções não manipuladas, o que melhora significativamente a experiência de depuragem. 

O seguinte corte ilustra como utilizar estes métodos.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Ajudante de inicializador de telemetria Azure Batch
Ao reportar telemetria para um determinado servidor e exemplo, o Application Insights utiliza o nome Azure VM Role e VM para os valores predefinidos. No contexto do Azure Batch, o exemplo mostra como usar o nome da piscina e o nome do nó de computação. Utilize um [inicializador de telemetria](../azure-monitor/app/api-filtering-sampling.md#add-properties) para anular os valores predefinidos. 

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

Para ativar o inicializador de telemetria, o ficheiro ApplicationInsights.config no projeto TopNWordsSample inclui o seguinte:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Atualizar o trabalho e as tarefas para incluir binários application insights

Para que os Insights de Aplicação corram corretamente nos seus nós de computação, certifique-se de que os binários estão corretamente colocados. Adicione os binários necessários à recolha de ficheiros de recursos da sua tarefa para que sejam descarregados no momento em que a sua tarefa executa. Os seguintes cortes são semelhantes ao código em Job.cs.

Em primeiro lugar, crie uma lista estática de ficheiros Application Insights para carregar.

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

Em seguida, crie os ficheiros de preparação que são utilizados pela tarefa.
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

O `FileToStage` método é uma função de ajudante na amostra de código que lhe permite carregar facilmente um ficheiro do disco local para uma bolha de armazenamento Azure. Cada ficheiro é posteriormente descarregado para um nó de computação e referenciado por uma tarefa.

Por fim, adicione as tarefas ao trabalho e inclua os binários de Insights de Aplicação necessários.
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

## <a name="view-data-in-the-azure-portal"></a>Ver dados no portal Azure

Agora que configuraste o trabalho e as tarefas para usar o Application Insights, corre o trabalho de exemplo na tua piscina. Navegue até ao portal Azure e abra o recurso Application Insights que aprovisionou. Depois de o pool ser a provisionado, deve começar a ver os dados a fluir e a ser registados. O resto deste artigo aborda apenas algumas funcionalidades do Application Insights, mas sinta-se livre para explorar o conjunto completo de funcionalidades.

### <a name="view-live-stream-data"></a>Ver dados de transmissão ao vivo

Para visualizar registos de vestígios no seu recurso Applications Insights, clique em **Live Stream**. A imagem que se segue mostra como visualizar dados ao vivo provenientes dos nós de computação na piscina, por exemplo, o uso do CPU por nó de computação.

![Dados do nó de computação em direto](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Ver registos de vestígios

Para visualizar registos de vestígios no seu recurso 'Insights' de Aplicações, clique em **Procurar**. Esta visão mostra uma lista de dados de diagnóstico capturados por Application Insights, incluindo vestígios, eventos e exceções. 

A imagem que se segue mostra como um único vestígio para uma tarefa é registado e posteriormente questionado para fins de depuragem.

![Imagem de registos de traços](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Ver exceções não manipuladas

As imagens seguintes mostram como o Application Insights regista exceções lançadas da sua aplicação. Neste caso, a poucos segundos da aplicação que lança a exceção, pode perfurar uma exceção específica e diagnosticar o problema.

![Exceções não processadas](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Medir o tempo de descarregamento do blob

As métricas personalizadas também são uma ferramenta valiosa no portal. Por exemplo, pode exibir o tempo médio que cada nó de cálculo demorou a descarregar o ficheiro de texto necessário que estava a processar.

Para criar um gráfico de amostragem:
1. No seu recurso 'Insights de Aplicação', clique em **Gráfico de Métricas Explorer**  >  **Add**.
2. Clique em **Editar** na tabela que foi adicionada.
2. Atualize os detalhes do gráfico da seguinte forma:
   * Definir **tipo de gráfico** para **grelha**.
   * Definir **agregação** para **média**.
   * Set **Group by** to **NodeId**.
   * Em **Metrics**, selecione **Download Custom**  >  **Blob em segundos**.
   * Ajuste a **paleta de cores do** ecrã à sua escolha. 

![Tempo de download de blob por nó](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Monitorize os nosdes computacional continuamente

Pode ter reparado que todas as métricas, incluindo contadores de desempenho, só são registadas quando as tarefas estão em execução. Este comportamento é útil porque limita a quantidade de dados que a Application Insights regista. No entanto, há casos em que gostaria sempre de monitorizar os nós computacional. Por exemplo, podem estar a executar trabalhos de fundo que não são programados através do serviço Batch. Neste caso, crie um processo de monitorização para executar a vida útil do nó de computação. 

Uma forma de alcançar este comportamento é desovar um processo que carrega a biblioteca Application Insights e funciona em segundo plano. No exemplo, a tarefa inicial carrega as binários na máquina e mantém um processo em funcionamento indefinidamente. Configure o ficheiro de configuração do Application Insights para este processo para emitir dados adicionais que lhe interessam, como contadores de desempenho.

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
> Para aumentar a gestão da sua solução, pode agregar o conjunto num [pacote de aplicações.](./batch-application-packages.md) Em seguida, para implantar o pacote de aplicações automaticamente nas suas piscinas, adicione uma referência de pacote de aplicação à configuração da piscina.
>

## <a name="throttle-and-sample-data"></a>Dados de aceleração e amostra 

Devido à natureza em larga escala das aplicações do Azure Batch em execução em produção, é melhor limitar a quantidade de dados recolhidos pela Application Insights para gerir os custos. Consulte [a amostragem em Insights de Aplicação](../azure-monitor/app/sampling.md) para obter alguns mecanismos para o conseguir.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a Aplicação Insights](../azure-monitor/app/app-insights-overview.md).

* Para o suporte de Insights de Aplicação em outras línguas, olhe para a [documentação de línguas, plataformas e integrações.](../azure-monitor/app/platforms.md)


