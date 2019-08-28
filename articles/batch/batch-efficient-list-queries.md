---
title: Criar consultas de lista eficientes – lote do Azure | Microsoft Docs
description: Aumente o desempenho filtrando suas consultas ao solicitar informações sobre recursos do lote, como pools, trabalhos, tarefas e nós de computação.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 37d34267220cbb7ceabfc823f6facd651969fbd4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095167"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Criar consultas para listar recursos do lote com eficiência

Aqui você aprenderá como aumentar o desempenho do aplicativo do lote do Azure reduzindo a quantidade de dados retornados pelo serviço ao consultar trabalhos, tarefas, nós de computação e outros recursos com a biblioteca .NET do [lote][api_net] .

Quase todos os aplicativos do lote precisam executar algum tipo de monitoramento ou outra operação que consulta o serviço de lote, geralmente em intervalos regulares. Por exemplo, para determinar se há alguma tarefa em fila restante em um trabalho, você deve obter dados em cada tarefa no trabalho. Para determinar o status de nós em seu pool, você deve obter dados em cada nó no pool. Este artigo explica como executar essas consultas da maneira mais eficiente.

> [!NOTE]
> O serviço de lote fornece suporte especial à API para os cenários comuns de contagem de tarefas em um trabalho e a contagem de nós de computação no pool do lote. Em vez de usar uma consulta de lista para elas, você pode chamar as operações [obter contagens de tarefas][rest_get_task_counts] e [contagens de nó de pool de lista][rest_get_node_counts] . Essas operações são mais eficientes do que uma consulta de lista, mas retornam informações mais limitadas. Consulte [contar tarefas e nós de computação por estado](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Conheça o DetailLevel
Em um aplicativo do lote de produção, as entidades, como trabalhos, tarefas e nós de computação, podem ser numeradas em milhares. Quando você solicita informações sobre esses recursos, uma quantidade potencialmente grande de dados deve "cruzar a transmissão" do serviço de lote para seu aplicativo em cada consulta. Ao limitar o número de itens e o tipo de informações retornadas por uma consulta, você pode aumentar a velocidade de suas consultas e, portanto, o desempenho do seu aplicativo.

Este trecho de código de API [.net do lote][api_net] lista *todas* as tarefas associadas a um trabalho, juntamente com *todas* as propriedades de cada tarefa:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

No entanto, você pode executar uma consulta de lista muito mais eficiente aplicando um "nível de detalhe" à sua consulta. Você faz isso fornecendo um objeto [ODATADetailLevel][odata] para o método [JobOperations. ListTasks][net_list_tasks] . Este trecho de código retorna somente as propriedades de ID, linha de comando e informações de nó de computação das tarefas concluídas:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Neste cenário de exemplo, se houver milhares de tarefas no trabalho, os resultados da segunda consulta normalmente serão retornados muito mais rapidamente do que o primeiro. Mais informações sobre como usar o ODATADetailLevel ao listar itens com a API .NET do lote estão incluídas [abaixo](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> É altamente recomendável que você *sempre* forneça um objeto ODATADetailLevel às suas chamadas de lista da API do .net para garantir o máximo de eficiência e desempenho do seu aplicativo. Ao especificar um nível de detalhe, você pode ajudar a reduzir os tempos de resposta do serviço de lote, melhorar a utilização da rede e minimizar o uso de memória por aplicativos cliente.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrar, selecionar e expandir
As APIs do [lote .net][api_net] e [REST do lote][api_rest] fornecem a capacidade de reduzir o número de itens retornados em uma lista, bem como a quantidade de informações retornadas para cada um. Você faz isso especificando **Filtrar**, **selecionar**e **expandir cadeias de caracteres** ao executar consultas de lista.

### <a name="filter"></a>Filtro
A cadeia de caracteres de filtro é uma expressão que reduz o número de itens retornados. Por exemplo, liste apenas as tarefas em execução de um trabalho ou liste somente os nós de computação que estão prontos para executar tarefas.

* A cadeia de caracteres de filtro consiste em uma ou mais expressões, com uma expressão que consiste em um nome de propriedade, operador e valor. As propriedades que podem ser especificadas são específicas para cada tipo de entidade que você consulta, assim como os operadores com suporte para cada propriedade.
* Várias expressões podem ser combinadas usando os operadores `and` lógicos e. `or`
* Esta cadeia de caracteres de filtro de exemplo lista apenas as tarefas de `(state eq 'running') and startswith(id, 'renderTask')`"renderização" em execução:.

### <a name="select"></a>Seleccionar
A cadeia de caracteres Select limita os valores de propriedade que são retornados para cada item. Você especifica uma lista de nomes de propriedade e somente esses valores de propriedade são retornados para os itens nos resultados da consulta.

* A cadeia de caracteres Select consiste em uma lista separada por vírgulas de nomes de propriedade. Você pode especificar qualquer uma das propriedades para o tipo de entidade que você está consultando.
* Este exemplo de cadeia de caracteres SELECT especifica que apenas três valores de propriedade devem ser retornados `id, state, stateTransitionTime`para cada tarefa:.

### <a name="expand"></a>Expandir
A cadeia de caracteres de expansão reduz o número de chamadas de API que são necessárias para obter determinadas informações. Quando você usa uma cadeia de caracteres de expansão, mais informações sobre cada item podem ser obtidas com uma única chamada à API. Em vez de obter primeiro a lista de entidades e, em seguida, solicitar informações para cada item na lista, você usa uma cadeia de caracteres de expansão para obter as mesmas informações em uma única chamada à API. Menos chamadas à API significam melhor desempenho.

* Semelhante à cadeia de caracteres Select, a cadeia de caracteres de expansão controla se determinados dados estão incluídos nos resultados da consulta de lista.
* A cadeia de caracteres de expansão só tem suporte quando usada na listagem de trabalhos, agendas de trabalho, tarefas e pools. Atualmente, ele dá suporte apenas a informações de estatísticas.
* Quando todas as propriedades são necessárias e nenhuma cadeia de caracteres de seleção é especificada, a cadeia de caracteres de expansão *deve* ser usada para obter informações estatísticas. Se uma cadeia de caracteres SELECT for usada para obter um subconjunto de `stats` Propriedades, poderá ser especificado na cadeia de caracteres Select e a cadeia de caracteres de expansão não precisará ser especificada.
* Este exemplo expande a cadeia de caracteres especifica que as informações de estatísticas devem ser retornadas `stats`para cada item na lista:.

> [!NOTE]
> Ao construir qualquer um dos três tipos de cadeia de caracteres de consulta (filtrar, selecionar e expandir), você deve garantir que os nomes de propriedade e maiúsculas e minúsculas correspondam aos seus contrapartes do elemento da API REST. Por exemplo, ao trabalhar com a classe [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) do .net, você deve especificar **estado** em vez de **estado**, mesmo que a propriedade .NET seja [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Consulte as tabelas abaixo para obter mapeamentos de propriedade entre as APIs .NET e REST.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regras para filtrar, selecionar e expandir cadeias de caracteres
* Os nomes das propriedades nas cadeias de caracteres de filtro, seleção e expansão devem aparecer como na API [REST do lote][api_rest] – mesmo quando você usa o [.net do lote][api_net] ou um dos outros SDKs do lote.
* Todos os nomes de propriedade diferenciam maiúsculas de minúsculas, mas os valores de propriedade diferenciam maiúsculas de minúsculas.
* Cadeias de caracteres de data/hora podem ser um dos dois formatos e devem ser `DateTime`precedidas por.
  
  * Exemplo de formato W3C-DTF:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Exemplo de formato RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* As cadeias `true` de caracteres `false`boolianas são ou.
* Se uma propriedade ou um operador inválido for especificado, `400 (Bad Request)` um erro será resultado.

## <a name="efficient-querying-in-batch-net"></a>Consulta eficiente no .NET do lote
Na API [.net do lote][api_net] , a classe [ODATADetailLevel][odata] é usada para fornecer cadeias de caracteres de filtro, seleção e expansão para as operações de lista. A classe ODataDetailLevel tem três propriedades de cadeia de caracteres públicas que podem ser especificadas no construtor ou definidas diretamente no objeto. Em seguida, você passa o objeto ODataDetailLevel como um parâmetro para as várias operações de lista, como [ListPools][net_list_pools], [ListJobs][net_list_jobs]e [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata]. [FilterClause][odata_filter]: Limite o número de itens retornados.
* [ODATADetailLevel][odata]. [SelectClause][odata_select]: Especifique quais valores de propriedade são retornados com cada item.
* [ODATADetailLevel][odata]. [ExpandClause][odata_expand]: Recuperar dados para todos os itens em uma única chamada à API em vez de chamadas separadas para cada item.

O trecho de código a seguir usa a API .NET do lote para consultar com eficiência o serviço de lote para as estatísticas de um conjunto específico de pools. Nesse cenário, o usuário do lote tem os pools de teste e de produção. As IDs do pool de teste são prefixadas com "Test" e as IDs do pool de produção são prefixadas com "Prod". No trecho de código, *myBatchClient* é uma instância corretamente inicializada da classe [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) .

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Uma instância de [ODATADetailLevel][odata] configurada com as cláusulas Select e Expand também pode ser passada para os métodos get apropriados, como [PoolOperations. getpool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), para limitar a quantidade de dados retornados.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Lotes REST em lote para mapeamentos de API .NET
Os nomes de propriedade nas cadeias de caracteres filtrar, selecionar e expandir *devem* refletir suas contrapartes da API REST, tanto no nome quanto no caso. As tabelas a seguir fornecem mapeamentos entre o .NET e as contrapartes da API REST.

### <a name="mappings-for-filter-strings"></a>Mapeamentos para cadeias de caracteres de filtro
* **Métodos de lista do .net**: Cada um dos métodos de API do .NET nesta coluna aceita um objeto [ODATADetailLevel][odata] como um parâmetro.
* **Solicitações da lista REST**: Cada página da API REST vinculada a nesta coluna contém uma tabela que especifica as propriedades e operações que são permitidas em cadeias de caracteres de *filtro* . Você usará esses nomes e operações de propriedade ao construir uma cadeia de caracteres [ODATADetailLevel. FilterClause][odata_filter] .

| Métodos de lista do .NET | Solicitações da lista REST |
| --- | --- |
| [O. ListCertificates][net_list_certs] |[Listar os certificados em uma conta][rest_list_certs] |
| [CloudTask. ListNodeFiles][net_list_task_files] |[Listar os arquivos associados a uma tarefa][rest_list_task_files] |
| [JobOperations. ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Listar o status das tarefas de preparação de trabalho e de liberação de trabalho para um trabalho][rest_list_jobprep_status] |
| [JobOperations. ListJobs][net_list_jobs] |[Listar os trabalhos em uma conta][rest_list_jobs] |
| [JobOperations. ListNodeFiles][net_list_nodefiles] |[Listar os arquivos em um nó][rest_list_nodefiles] |
| [JobOperations. ListTasks][net_list_tasks] |[Listar as tarefas associadas a um trabalho][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Listar as agendas de trabalho em uma conta][rest_list_job_schedules] |
| [JobScheduleOperations. ListJobs][net_list_schedule_jobs] |[Listar os trabalhos associados a uma agenda de trabalho][rest_list_schedule_jobs] |
| [PoolOperations. ListComputeNodes][net_list_compute_nodes] |[Listar os nós de computação em um pool][rest_list_compute_nodes] |
| [PoolOperations. ListPools][net_list_pools] |[Listar os pools em uma conta][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapeamentos para cadeias de caracteres de seleção
* **Tipos de .net do lote**: Tipos de API .NET do lote.
* **Entidades da API REST**: Cada página nesta coluna contém uma ou mais tabelas que listam os nomes de propriedade da API REST para o tipo. Esses nomes de propriedade são usados quando você constrói cadeias de caracteres *Select* . Você usará esses mesmos nomes de propriedade ao construir uma cadeia de caracteres [ODATADetailLevel. SelectClause][odata_select] .

| Tipos de .NET do lote | Entidades da API REST |
| --- | --- |
| [Certificado][net_cert] |[Obter informações sobre um certificado][rest_get_cert] |
| [CloudJob][net_job] |[Obter informações sobre um trabalho][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Obter informações sobre uma agenda de trabalho][rest_get_schedule] |
| [ComputeNode][net_node] |[Obter informações sobre um nó][rest_get_node] |
| [CloudPool][net_pool] |[Obter informações sobre um pool][rest_get_pool] |
| [CloudTask][net_task] |[Obter informações sobre uma tarefa][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exemplo: construir uma cadeia de caracteres de filtro
Ao construir uma cadeia de caracteres de filtro para [ODATADetailLevel. FilterClause][odata_filter], consulte a tabela acima em "mapeamentos para cadeias de caracteres de filtro" para localizar a página de documentação da API REST que corresponde à operação de lista que você deseja executar. Você encontrará as propriedades filtráveis e seus operadores com suporte na primeira tabela multirow nessa página. Se você quiser recuperar todas as tarefas cujo código de saída era diferente de zero, por exemplo, essa linha na [lista as tarefas associadas a um trabalho][rest_list_tasks] especificarão a cadeia de caracteres de propriedade aplicável e os operadores permitidos:

| Propriedade | Operações permitidas | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Assim, a cadeia de caracteres de filtro para listar todas as tarefas com um código de saída diferente de zero seria:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemplo: construir uma cadeia de caracteres Select
Para construir [ODATADetailLevel. SelectClause][odata_select], consulte a tabela acima em "mapeamentos para selecionar cadeias de caracteres" e navegue até a página da API REST que corresponde ao tipo de entidade que você está listando. Você encontrará as propriedades selecionáveis e seus operadores com suporte na primeira tabela de multirow nessa página. Se você quiser recuperar apenas a ID e a linha de comando para cada tarefa em uma lista, por exemplo, você encontrará essas linhas na tabela aplicável em [obter informações sobre uma tarefa][rest_get_task]:

| Propriedade | Type | Notas |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

A cadeia de caracteres SELECT para incluir apenas a ID e a linha de comando com cada tarefa listada seria:

`id, commandLine`

## <a name="code-samples"></a>Exemplos de código
### <a name="efficient-list-queries-code-sample"></a>Exemplo de código de consultas de lista eficientes
Confira o projeto de exemplo [EfficientListQueries][efficient_query_sample] no GitHub para ver como a consulta de lista eficiente pode afetar o desempenho em um aplicativo. Esse C# aplicativo de console cria e adiciona um grande número de tarefas a um trabalho. Em seguida, ele faz várias chamadas para o método [JobOperations. ListTasks][net_list_tasks] e passa objetos [ODATADetailLevel][odata] que são configurados com valores de propriedade diferentes para variar a quantidade de dados a serem retornados. Ele produz uma saída semelhante à seguinte:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Conforme mostrado nos tempos decorridos, você pode reduzir muito os tempos de resposta de consulta limitando as propriedades e o número de itens retornados. Você pode encontrar esse e outros projetos de exemplo no repositório [Azure-batch-Samples][github_samples] no github.

### <a name="batchmetrics-library-and-code-sample"></a>Biblioteca BatchMetrics e exemplo de código
Além do exemplo de código EfficientListQueries acima, você pode encontrar o projeto [BatchMetrics][batch_metrics] no repositório GitHub [Azure-batch-Samples][github_samples] . O projeto de exemplo BatchMetrics demonstra como monitorar com eficiência o progresso do trabalho do lote do Azure usando a API do lote.

O exemplo [BatchMetrics][batch_metrics] inclui um projeto de biblioteca de classes .NET que você pode incorporar em seus próprios projetos e um programa de linha de comando simples para exercitar e demonstrar o uso da biblioteca.

O aplicativo de exemplo dentro do projeto demonstra as seguintes operações:

1. Selecionando atributos específicos para baixar apenas as propriedades necessárias
2. Filtragem em tempos de transição de estado para baixar somente as alterações desde a última consulta

Por exemplo, o método a seguir aparece na biblioteca BatchMetrics. Ele retorna um ODATADetailLevel que especifica que apenas as `id` propriedades `state` e devem ser obtidas para as entidades que são consultadas. Ele também especifica que somente as entidades cujo estado foi alterado desde que `DateTime` o parâmetro especificado deve ser retornado.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Passos seguintes
### <a name="parallel-node-tasks"></a>Tarefas de nó paralelo
[Maximizar o uso de recursos de computação do lote do Azure com tarefas de nó simultâneas](batch-parallel-node-tasks.md) é outro artigo relacionado ao desempenho do aplicativo do lote. Alguns tipos de cargas de trabalho podem se beneficiar da execução de tarefas paralelas em nós maiores, mas menos computacionais. Confira o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para obter detalhes sobre esse cenário.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
