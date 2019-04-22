---
title: Criar consultas de lista eficientes - Azure Batch | Documentos da Microsoft
description: Aumentar o desempenho ao filtrar suas consultas quando solicitar informações sobre recursos do Batch como conjuntos, trabalhos, tarefas e nós de computação.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: ff3e95a603b8f9a188c7839578cd12287935de90
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918540"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Criar consultas para listar recursos do Batch de forma eficiente

Aqui aprenderá como aumentar o desempenho da sua aplicação do Azure Batch, reduzindo a quantidade de dados que são devolvidos pelo serviço quando consultar tarefas, tarefas, nós de computação e outros recursos com o [.NET do Batch] [ api_net] biblioteca.

Quase todas as aplicações do Batch tem de executar algum tipo de monitorização ou por outra operação que consulta o serviço Batch, muitas vezes em intervalos regulares. Por exemplo, para determinar se existem quaisquer tarefas em fila restante de uma tarefa, tem de obter dados em todas as tarefas no trabalho. Para determinar o estado de nós do conjunto, tem de obter dados em cada nó no conjunto. Este artigo explica como executar estas consultas da forma mais eficiente.

> [!NOTE]
> O serviço de Batch fornece suporte de API especial para os cenários comuns de tarefas num trabalho de contagem e contagem de nós de computação num conjunto do Batch. Em vez de usar uma consulta de lista para elas, pode chamar o [obter contagens de tarefas] [ rest_get_task_counts] e [contagens de nó de conjunto de lista] [ rest_get_node_counts] operações. Essas operações são mais eficientes do que uma consulta de lista, mas o retorno limitado mais informações. Ver [contagem de tarefas e nós pelo Estado de computação](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Conheça o DetailLevel
Num aplicativo de Batch de produção, as entidades, como trabalhos, tarefas e nós de computação podem chegar em milhares. Quando solicitar informações sobre estes recursos, uma quantidade potencialmente grande de dados tem "cruzada conexão" do serviço Batch para seu aplicativo em cada consulta. Ao limitar o número de itens e o tipo de informação devolvido por uma consulta, pode aumentar a velocidade das suas consultas e, portanto, o desempenho da sua aplicação.

Isso [.NET do Batch] [ api_net] listas de Trecho de código de API *cada* tarefa que está associada uma tarefa, juntamente com *todos os* das propriedades de cada tarefa:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

No entanto, pode executar uma consulta de lista muito mais eficiente, aplicando um "nível de detalhe" a sua consulta. Para fazer isso, fornecendo uma [ODATADetailLevel] [ odata] objeto para o [JobOperations.ListTasks] [ net_list_tasks] método. Este trecho de código devolve apenas o ID de linha de comandos e propriedades de informações do nó de computação de tarefas concluídas:

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

Neste cenário de exemplo, se houver milhares de tarefas no trabalho, os resultados da consulta do segundo normalmente retornará muito mais rápido do que o primeiro. Obter mais informações sobre como utilizar ODATADetailLevel quando listar itens com a API .NET do Batch estão incluídas [abaixo](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> É altamente recomendável que *sempre* fornecer um objeto de ODATADetailLevel para suas chamadas de lista da .NET API para garantir a máxima eficiência e desempenho da sua aplicação. Ao especificar um nível de detalhe, pode ajudar a reduzir os tempos de resposta do serviço Batch, melhorar a utilização de rede e minimizar a utilização de memória por aplicações cliente.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrar, selecione e expanda
O [.NET do Batch] [ api_net] e [REST do Batch] [ api_rest] APIs oferecem a capacidade de reduzir o número de itens que são retornados numa lista, bem como a quantidade de informação devolvido para cada um. Fazê-lo especificando **filtro**, **selecione**, e **expanda cadeias de caracteres** ao efetuar consultas de lista.

### <a name="filter"></a>Filtro
A cadeia de filtro é uma expressão que reduz o número de itens que são devolvidos. Por exemplo, lista apenas as tarefas em execução para uma tarefa ou listar apenas nós de computação que estão prontos para executar tarefas.

* A cadeia de filtro consiste numa ou mais expressões, com uma expressão que é composta por um nome de propriedade, operador e valor. As propriedades que podem ser especificadas são específicas para cada tipo de entidade que consultar, assim como os operadores suportados para cada propriedade.
* Várias expressões podem ser combinadas utilizando os operadores lógicos `and` e `or`.
* Neste exemplo filtrar as listas de cadeia de caracteres apenas a execução "compor" tarefas: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Selecione
A seqüência de caracteres select limita os valores de propriedade que são devolvidos para cada item. Especificar uma lista de nomes de propriedade e apenas esses valores de propriedade são retornados para os itens nos resultados da consulta.

* A seqüência de caracteres select consiste numa lista separada por vírgulas de nomes de propriedade. Pode especificar qualquer uma das propriedades para o tipo de entidade que está a consultar.
* Essa seqüência de caracteres select do exemplo Especifica que apenas três valores de propriedade devem ser retornados para cada tarefa: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expandir
A cadeia de expansão reduz o número de chamadas de API que são necessárias para obter determinadas informações. Quando utiliza uma cadeia de expansão, obter mais informações sobre cada item podem ser obtidas com uma única chamada de API. Em vez de primeiro obter a lista de entidades, em seguida, solicitando informações para cada item na lista, utilize uma cadeia de caracteres de expandir para obter as mesmas informações numa única chamada de API. Menos chamadas de API significa um melhor desempenho.

* Semelhante à seqüência de caracteres select, os controles de cadeia de caracteres de expansão se determinados dados são incluídos nos resultados da consulta de lista.
* A cadeia de expansão só é suportada quando é utilizado numa listagem de tarefas, agendamentos de trabalhos, tarefas e agrupamentos. Atualmente, suporta apenas informações de estatísticas.
* Quando todas as propriedades são necessárias e não for especificada nenhuma seqüência de caracteres select, a cadeia de expansão *tem* ser usado para obter informações de estatísticas. Se uma seqüência de caracteres select é utilizada para obter um subconjunto de propriedades, em seguida, `stats` pode ser especificado na seqüência de caracteres select, e a cadeia de expansão não precisa de ser especificado.
* Cadeia de caracteres de expandir este exemplo Especifica que as informações de estatísticas devem ser retornadas para cada item na lista: `stats`.

> [!NOTE]
> Ao construir a qualquer um dos tipos de cadeia de caracteres de consulta de três (filtrar, selecione e expanda), tem de garantir que os nomes de propriedade e o caso de corresponder ao de suas contrapartes de elemento da REST API. Por exemplo, ao trabalhar com o .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask) classe, tem de especificar **estado** em vez de **estado**, apesar da propriedade do .NET é [ CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask.state). Consulte a tabela abaixo para mapeamentos de propriedades entre o .NET e REST APIs.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regras de filtro, selecione e expanda as cadeias de caracteres
* Nomes de propriedades no filtro, selecione e expanda as cadeias de caracteres deve aparecer como fazem no [REST do Batch] [ api_rest] API – mesmo quando usa [.NET do Batch] [ api_net]ou um dos outros SDKs do Batch.
* Todos os nomes de propriedade diferenciam maiúsculas de minúsculas, mas os valores de propriedade diferenciam maiúsculas de minúsculas.
* Data/hora cadeias de caracteres pode ser um dos dois formatos e têm de ser precedidas com `DateTime`.
  
  * Exemplo de formato W3C DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Exemplo de formato de RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* São cadeias de caracteres booleanas `true` ou `false`.
* Se for especificada uma propriedade inválida ou o operador, um `400 (Bad Request)` erro irá resultar.

## <a name="efficient-querying-in-batch-net"></a>Eficiente de consulta no .NET do Batch
Dentro de [.NET do Batch] [ api_net] API, o [ODATADetailLevel] [ odata] classe é utilizada para fornecer o filtro, selecione e expanda as cadeias de caracteres para listar operações. A classe de ODataDetailLevel tem três propriedades de cadeia de caracteres público que podem ser especificadas no construtor ou definidas diretamente no objeto. , Em seguida, passar o objeto de ODataDetailLevel como um parâmetro para as várias operações de lista, tal como [ListPools][net_list_pools], [ListJobs][net_list_jobs], e [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[FilterClause][odata_filter]: Limite o número de itens que são devolvidos.
* [ODATADetailLevel][odata].[SelectClause][odata_select]: Especifique os valores de propriedade são devolvidos com cada item.
* [ODATADetailLevel][odata].[ExpandClause][odata_expand]: Obter dados para todos os itens numa única chamada de API em vez de chamadas separadas para cada item.

O fragmento de código seguinte utiliza a API .NET do Batch para consultar o serviço Batch para as estatísticas de um conjunto específico de conjuntos de forma eficaz. Neste cenário, o utilizador de Batch tem conjuntos de teste e produção. O conjunto de teste IDs são prefixados com "teste" e o conjunto de produção IDs são prefixados com "prod". No fragmento, *myBatchClient* é uma instância corretamente inicializada do [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient#microsoft_azure_batch_batchclient) classe.

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
> Uma instância do [ODATADetailLevel] [ odata] que está configurado com Select e cláusulas de expansão podem também ser passadas a métodos de Get apropriados, como [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), para limitar a quantidade de dados que são devolvidos.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>REST para mapeamentos de .NET API do batch
Nomes de propriedade no filtro, selecione e expanda as cadeias de caracteres *tem* refletir suas contrapartes de REST API, no nome e o caso. As tabelas abaixo apresentam mapeamentos entre as contrapartes de .NET e a REST API.

### <a name="mappings-for-filter-strings"></a>Mapeamentos para cadeias de caracteres de filtro
* **Métodos de lista de .NET**: Cada um dos métodos de .NET API nesta coluna aceita uma [ODATADetailLevel] [ odata] objeto como um parâmetro.
* **Pedidos de listas REST**: Cada página de REST API ligada a esta coluna contém uma tabela que especifica as propriedades e operações que são permitidas em *filtro* cadeias de caracteres. Irá utilizar estes nomes de propriedades e operações ao construir um [ODATADetailLevel.FilterClause] [ odata_filter] cadeia de caracteres.

| Métodos de lista de .NET | Pedidos de listas REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Listar os certificados de uma conta][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Listar os ficheiros associados a uma tarefa][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Liste o estado da tarefa de preparação e tarefas de lançamento de uma tarefa][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Listar as tarefas numa conta][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Listar os ficheiros num nó][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Listar as tarefas associadas uma tarefa][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Lista as agendas de trabalho numa conta][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Listar os trabalhos associados uma agenda de trabalho][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Listar os nós de computação num conjunto][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Listar os conjuntos numa conta][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapeamentos para cadeias de caracteres select
* **Tipos .NET do batch**: Tipos de .NET API do batch.
* **Entidades de REST API**: Cada página nesta coluna contém uma ou mais tabelas que listam os nomes das propriedades da REST API para o tipo. Estes nomes de propriedade são utilizados ao construir *selecione* cadeias de caracteres. Irá utilizar estes mesmos nomes de propriedade ao construir um [ODATADetailLevel.SelectClause] [ odata_select] cadeia de caracteres.

| Tipos de .NET do batch | Entidades de REST API |
| --- | --- |
| [Certificado][net_cert] |[Obter informações sobre um certificado][rest_get_cert] |
| [CloudJob][net_job] |[Obter informações sobre uma tarefa][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Obter informações sobre uma agenda de trabalho][rest_get_schedule] |
| [ComputeNode][net_node] |[Obter informações sobre um nó][rest_get_node] |
| [CloudPool][net_pool] |[Obter informações sobre um conjunto][rest_get_pool] |
| [CloudTask][net_task] |[Obter informações sobre uma tarefa][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exemplo: construir uma cadeia de filtro
Quando constrói uma cadeia de filtro para [ODATADetailLevel.FilterClause][odata_filter], consulte a tabela acima em "Mapeamentos para cadeias de caracteres de filtro" para a página de documentação encontrar a API REST, que corresponde do operação de lista que pretende executar. Encontrará as propriedades filtráveis e os operadores suportados na primeira tabela multirow nessa página. Se pretender obter todas as tarefas cujo código de saída foi diferente de zero, por exemplo, esta linha no [lista as tarefas associadas uma tarefa] [ rest_list_tasks] Especifica a cadeia de caracteres de propriedade aplicável e operadores permitidos:

| Propriedade | Operações permitidas | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Portanto, a cadeia de filtro para listar todas as tarefas com um código de saída diferente de zero seria:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemplo: construir uma seqüência de caracteres select
Para construir [ODATADetailLevel.SelectClause][odata_select], consulte a tabela acima em "Mapeamentos para cadeias de caracteres select" e navegue para a página de REST API que corresponde ao tipo de entidade que está a listar. Encontrará as propriedades selecionáveis e os operadores suportados na primeira tabela multirow nessa página. Se desejar recuperar apenas o ID e a linha de comandos para cada tarefa numa lista, por exemplo, irá encontrar estas linhas na tabela aplicável no [obter informações sobre uma tarefa][rest_get_task]:

| Propriedade | Type | Notas |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Em seguida, seria a seqüência de caracteres select para apenas o ID e a linha de comandos com cada tarefa listada, incluindo:

`id, commandLine`

## <a name="code-samples"></a>Exemplos de código
### <a name="efficient-list-queries-code-sample"></a>Exemplo de código de consultas de lista eficientes
Veja a [EfficientListQueries] [ efficient_query_sample] projeto de exemplo no GitHub para ver uma consulta lista como eficiente pode afetar o desempenho de um aplicativo. Esta aplicação de consola em C# cria e adiciona um grande número de tarefas para uma tarefa. Em seguida, faz várias chamadas para o [JobOperations.ListTasks] [ net_list_tasks] método e os passes [ODATADetailLevel] [ odata] objetos que estão configurada com valores de propriedade diferentes para variar a quantidade de dados a serem retornados. Ele produz um resultado semelhante ao seguinte:

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

Como mostra o tempo decorrido, pode reduzir significativamente os tempos de resposta de consulta ao limitar as propriedades e o número de itens que são devolvidos. Pode encontrar este e outros projetos de exemplo na [azure-batch-samples] [ github_samples] repositório no GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Exemplo de código e biblioteca BatchMetrics
Além de EfficientListQueries código de exemplo acima, pode encontrar o [BatchMetrics] [ batch_metrics] projeto o [azure-batch-samples] [ github_samples]Repositório do GitHub. O projeto de exemplo BatchMetrics demonstra como monitorizar o progresso da tarefa do Azure Batch com a API do Batch com eficiência.

O [BatchMetrics] [ batch_metrics] exemplo inclui um projeto de biblioteca de classes .NET que poderá incorporar nos seus próprios projetos e um programa de linha de comandos simples para testar e demonstrar o uso da biblioteca.

O exemplo de aplicativo dentro do projeto demonstra as seguintes operações:

1. Selecionar atributos específicos para transferir apenas as propriedades que precisa
2. Filtrar em tempos de transição de estado para transferir apenas alterações desde a última consulta

Por exemplo, é apresentado o seguinte método na biblioteca do BatchMetrics. Ele retorna um ODATADetailLevel que especifica que apenas os `id` e `state` propriedades devem ser obtidas para as entidades que são consultadas. Também especifica que apenas as entidades cujo estado foi alterado desde especificado `DateTime` parâmetro deve ser devolvido.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Passos Seguintes
### <a name="parallel-node-tasks"></a>Tarefas paralelas nó
[Maximize a utilização de recursos de computação do Azure Batch com tarefas de nós simultâneas](batch-parallel-node-tasks.md) está outro artigo relacionado ao desempenho de aplicações do Batch. Alguns tipos de cargas de trabalho podem beneficiar da execução de tarefas paralelas em maior, mas menos – nós de computação. Veja a [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para obter detalhes sobre um cenário como esse.


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
