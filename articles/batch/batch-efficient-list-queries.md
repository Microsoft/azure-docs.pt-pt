---
title: Consultas de lista eficientes de design - Lote Azure / Microsoft Docs
description: Aumente o desempenho filtrando as suas consultas ao solicitar informações sobre recursos do Lote, como piscinas, empregos, tarefas e nós de computação.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: df923ac479ce5f5a3668c18c616b11348dc6c0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022244"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Criar consultas para listar os recursos do Lote de forma eficiente

Aqui você vai aprender a aumentar o desempenho da sua aplicação Azure Batch reduzindo a quantidade de dados que é devolvido pelo serviço quando você consulta empregos, tarefas, nós de computação e outros recursos com a biblioteca [Batch .NET.][api_net]

Quase todas as aplicações do Lote precisam de realizar algum tipo de monitorização ou outra operação que questione o serviço De lote, muitas vezes em intervalos regulares. Por exemplo, para determinar se há alguma tarefa em fila que permaneça num trabalho, deve obter dados sobre todas as tarefas no trabalho. Para determinar o estado dos nós na sua piscina, você deve obter dados de cada nó na piscina. Este artigo explica como executar tais consultas da forma mais eficiente.

> [!NOTE]
> O serviço Batch fornece suporte especial da API para os cenários comuns de contagem de tarefas num trabalho, e contando nós de computação em pool Batch. Em vez de utilizar uma consulta de lista para estes, pode ligar para as operações [get task counts][rest_get_task_counts] e list Pool [Node Counts.][rest_get_node_counts] Estas operações são mais eficientes do que uma consulta de lista, mas devolvem informações mais limitadas. Consulte [as tarefas do Conde e calcule os nódosos por estado.](batch-get-resource-counts.md) 


## <a name="meet-the-detaillevel"></a>Conheça o DetailLevel
Numa aplicação de lote de produção, entidades como empregos, tarefas e nós de computação podem ser numeradas em milhares. Quando solicita informações sobre estes recursos, uma quantidade potencialmente grande de dados deve "cruzar o fio" do serviço 'Lote' para a sua aplicação em cada consulta. Limitando o número de itens e tipo de informação que é devolvido por uma consulta, pode aumentar a velocidade das suas consultas e, portanto, o desempenho da sua aplicação.

Este [código De lote .NET][api_net] API lista todas *as* tarefas associadas a um trabalho, juntamente com *todas as* propriedades de cada tarefa:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

No entanto, pode realizar uma consulta de lista muito mais eficiente, aplicando um "nível de detalhe" à sua consulta. Faça-o fornecendo um objeto [ODATADetailLevel][odata] ao método [JobOperations.ListTasks.][net_list_tasks] Este corte devolve apenas as propriedades de informação do ID, linha de comando e computação das tarefas concluídas:

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

Neste cenário de exemplo, se houver milhares de tarefas no trabalho, os resultados da segunda consulta serão normalmente devolvidos muito mais rapidamente do que o primeiro. Mais informações sobre a utilização do ODATADetailLevel quando listar itens com a API do Lote .NET está incluída [abaixo](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Recomendamos vivamente que forneça *sempre* um objeto ODATADetailLevel às chamadas da sua lista .NET API para garantir a máxima eficiência e desempenho da sua aplicação. Especificando um nível de detalhe, pode ajudar a reduzir os tempos de resposta do serviço do Batch, melhorar a utilização da rede e minimizar o uso da memória pelas aplicações do cliente.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrar, selecionar e expandir
As APIs de [Lote .NET][api_net] e [Lote REST][api_rest] fornecem a capacidade de reduzir tanto o número de itens que são devolvidos numa lista, como a quantidade de informação que é devolvida para cada um. Fá-lo especificando **filtro**, **selecione**e **expanda cordas** ao executar consultas de lista.

### <a name="filter"></a>Filtro
A cadeia do filtro é uma expressão que reduz o número de itens que são devolvidos. Por exemplo, enumerar apenas as tarefas de execução para um trabalho, ou listar apenas nós de cálculo que estejam prontos para executar tarefas.

* A cadeia de filtros consiste numa ou mais expressões, com expressão que consiste num nome de propriedade, operador e valor. As propriedades que podem ser especificadas são específicas de cada tipo de entidade que consulta, assim como os operadores que são suportados por cada imóvel.
* As expressões múltiplas podem ser `and` `or`combinadas utilizando os operadores lógicos e .
* Este exemplo de cadeia de filtro selelista apenas as tarefas de "renderização" em execução: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Selecione
A cadeia selecionada limita os valores de propriedade que são devolvidos para cada item. Especifica uma lista de nomes de propriedade, e apenas esses valores de propriedade são devolvidos para os itens nos resultados da consulta.

* A cadeia selecionada consiste numa lista separada de nomes de propriedade. Pode especificar qualquer uma das propriedades para o tipo de entidade que está a consultar.
* Este exemplo seleciona a cadeia especifica que apenas `id, state, stateTransitionTime`três valores de propriedade devem ser devolvidos para cada tarefa: .

### <a name="expand"></a>Expandir
A cadeia de expansão reduz o número de chamadas API que são necessárias para obter certas informações. Quando utiliza uma cadeia de expansão, mais informações sobre cada item podem ser obtidas com uma única chamada API. Em vez de obter primeiro a lista de entidades, depois solicitando informações para cada item da lista, você usa uma cadeia de expansão para obter a mesma informação numa única chamada API. Menos chamadas aPi significa melhor desempenho.

* Semelhante à cadeia selecionada, a cadeia de expansão controla se determinados dados estão incluídos nos resultados da consulta da lista.
* A cadeia de expansão só é suportada quando é usada na listagem de empregos, horários de trabalho, tarefas e piscinas. Atualmente, apenas apoia a informação estatística.
* Quando todas as propriedades são necessárias e nenhuma cadeia selecionada é especificada, a cadeia de expansão *deve* ser usada para obter informações estatísticas. Se for utilizada uma cadeia selecionada para obter `stats` um subconjunto de propriedades, então pode ser especificado na cadeia selecionada e a cadeia de expansão não precisa de ser especificada.
* Este exemplo expande a cadeia especifica que as informações `stats`estatísticas devem ser devolvidas para cada item da lista: .

> [!NOTE]
> Ao construir qualquer um dos três tipos de cordas de consulta (filtro, selecione e expandir), deve certificar-se de que os nomes de propriedade e o caso correspondem aos dos seus elementos REST API. Por exemplo, ao trabalhar com a classe [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) .NET, deve especificar **estado** em vez de **Estado**, mesmo que a propriedade .NET seja [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Consulte as tabelas abaixo para mapeamento de propriedades entre as APIs .NET e REST.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regras para filtro, selecione e expanda cordas
* As propriedades nomes no filtro, selecionam e expandem as cordas devem aparecer como fazem no [Lote REST][api_rest] API - mesmo quando utiliza o [Batch .NET][api_net] ou um dos outros SDKs de Lote.
* Todos os nomes de propriedade são sensíveis a casos, mas os valores da propriedade são insensíveis.
* As cordas data/hora podem ser um de dois formatos, e devem ser precedidas com `DateTime`.
  
  * Exemplo de formato W3C-DTF:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Exemplo de formato RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Cordas booleanas `true` são `false`ou.
* Se for especificado um imóvel `400 (Bad Request)` ou operador inválido, resultará um erro.

## <a name="efficient-querying-in-batch-net"></a>Consulta eficiente em Lote .NET
Dentro do [Lote .NET][api_net] API, a classe [ODATADetailLevel][odata] é utilizada para fornecer filtro, selecionar e expandir cordas para listar operações. A classe ODataDetailLevel tem três propriedades de cadeia pública que podem ser especificadas no construtor, ou definidas diretamente no objeto. Em seguida, passa o objeto ODataDetailLevel como parâmetro para as várias operações da lista, tais como [ListPools,][net_list_pools] [ListJobs][net_list_jobs]e [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata]. [FilterClause][odata_filter]: Limite o número de itens que são devolvidos.
* [ODATADetailLevel][odata]. [SelectClause][odata_select]: Especifique quais os valores de propriedade devolvidos com cada item.
* [ODATADetailLevel][odata]. [Cláusula de expansão][odata_expand]: Recupere dados para todos os itens numa única chamada API em vez de chamadas separadas para cada item.

O seguinte código de corte utiliza o Batch.NET API para consultar eficientemente o serviço Batch para as estatísticas de um conjunto específico de piscinas. Neste cenário, o utilizador do Batch tem piscinas de teste e de produção. Os IDs do grupo de teste são pré-fixados com "teste", e os IDs do pool de produção são pré-fixados com "prod". No snippet, *myBatchClient* é uma instância devidamente inicializada da classe [BatchClient.](/dotnet/api/microsoft.azure.batch.batchclient)

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
> Uma instância de [ODATADetailLevel][odata] que está configurada com cláusulas Select e Expandir também pode ser passada para métodos apropriados Get, tais como [PoolOperations.GetPool,](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__)para limitar a quantidade de dados que são devolvidos.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Lote REPOUSO para mapeamento de .NET API
Os nomes de propriedade no filtro, selecione e expandam cordas *devem* refletir os seus homólogos rest API, tanto no nome como no caso. As tabelas abaixo fornecem mapeamentos entre os congéneres .NET e REST API.

### <a name="mappings-for-filter-strings"></a>Mapeamentos para cordas de filtro
* **métodos de lista .NET:** Cada um dos métodos .NET API nesta coluna aceita um objeto [ODATADetailLevel][odata] como parâmetro.
* **Pedidos da lista DE REPOUSO**: Cada página REST API ligada a esta coluna contém uma tabela que especifica as propriedades e operações que são permitidas nas cordas do *filtro.* Utilizará estes nomes e operações de propriedade quando construir uma cadeia [ODATADetailLevel.FilterClause.][odata_filter]

| .MÉTODOS de lista .NET | Pedidos de lista de REPOUSO |
| --- | --- |
| [CertificadoSOperações.ListCertificates][net_list_certs] |[Enumerar os certificados numa conta][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Enumerar os ficheiros associados a uma tarefa][rest_list_task_files] |
| [Operações de Emprego.ListJobPreparationandreleaseTaskstatus][net_list_jobprep_status] |[Enumerar o estado das tarefas de preparação de emprego e de libertação de emprego para um emprego][rest_list_jobprep_status] |
| [Operações de Emprego.ListJobs][net_list_jobs] |[Enumerar os empregos numa conta][rest_list_jobs] |
| [Operações de Emprego.ListNodeFiles][net_list_nodefiles] |[Lista rumine os ficheiros num nó][rest_list_nodefiles] |
| [Operações de Emprego.ListTasks][net_list_tasks] |[Enumerar as tarefas associadas a um trabalho][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Enumerar os horários de trabalho numa conta][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Enumerar os empregos associados a um horário de trabalho][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Enumere os nódosos computacionais em uma piscina][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Listar as piscinas numa conta][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapeamentos para cordas selecionadas
* **Tipos de lote .NET**: Tipos de API do lote .NET.
* **Entidades REST API**: Cada página desta coluna contém uma ou mais tabelas que listam os nomes de propriedade da API REST para o tipo. Estes nomes de propriedade são usados quando se *constroem* cordas selecionadas. Utilizará estes mesmos nomes de propriedade quando construir uma cadeia [ODATADetailLevel.SelectClause.][odata_select]

| Tipos de lote .NET | Entidades rest API |
| --- | --- |
| [Certificado][net_cert] |[Obtenha informações sobre um certificado][rest_get_cert] |
| [CloudJob][net_job] |[Obtenha informações sobre um trabalho][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Obtenha informações sobre um horário de trabalho][rest_get_schedule] |
| [ComputeNode][net_node] |[Obtenha informações sobre um nó][rest_get_node] |
| [CloudPool][net_pool] |[Obtenha informações sobre uma piscina][rest_get_pool] |
| [CloudTask][net_task] |[Obtenha informações sobre uma tarefa][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exemplo: construir uma cadeia de filtro
Quando construir uma cadeia de filtros para [ODATADetailLevel.FilterClause,][odata_filter]consulte a tabela acima em "Mapeamentos para cordas de filtro" para encontrar a página de documentação REST API que corresponda ao funcionamento da lista que pretende realizar. Encontrará as propriedades filtradas e os seus operadores suportados na primeira tabela multisrow nessa página. Se desejar recuperar todas as tarefas cujo código de saída não fosse zero, por exemplo, esta linha na [Lista das tarefas associadas][rest_list_tasks] a um trabalho especifica a cadeia de propriedade aplicável e os operadores admissíveis:

| Propriedade | Operações permitidas | Tipo |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Assim, a cadeia de filtros para listar todas as tarefas com um código de saída não zero seria:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemplo: construir uma corda selecionada
Para construir [ODATADetailLevel.SelectClause,][odata_select]consulte a tabela acima em "Mapeamentos para selecionar cordas" e navegue para a página REST API que corresponda ao tipo de entidade que está a listar. Encontrará as propriedades selecionáveis e os seus operadores suportados na primeira tabela multisrow nessa página. Se desejar recuperar apenas a linha de identificação e comando para cada tarefa de uma lista, por exemplo, encontrará estas linhas na tabela aplicável sobre [Obter informações sobre uma tarefa:][rest_get_task]

| Propriedade | Tipo | Notas |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

A cadeia selecionada para incluir apenas o ID e a linha de comando com cada tarefa listada seria:

`id, commandLine`

## <a name="code-samples"></a>Exemplos de código
### <a name="efficient-list-queries-code-sample"></a>Lista eficiente consulta amostra de código
Confira o projeto de amostra de [EfficientListQueries][efficient_query_sample] no GitHub para ver quão eficiente a consulta de lista pode afetar o desempenho numa aplicação. Esta aplicação de consola C# cria e adiciona um grande número de tarefas a um trabalho. Em seguida, faz várias chamadas para o método [JobOperations.ListTasks][net_list_tasks] e passa objetos [ODATADetailLevel][odata] que são configurados com diferentes valores de propriedade para variar a quantidade de dados a devolver. Produz uma saída semelhante à seguinte:

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

Como mostrado nos tempos decorridos, você pode reduzir muito os tempos de resposta de consulta limitando as propriedades e o número de itens que são devolvidos. Você pode encontrar este e outros projetos de amostra no repositório [de amostras de lote azul][github_samples] no GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Biblioteca batchMetrics e amostra de código
Além da amostra de código EfficientListQueries acima, pode encontrar o projeto [BatchMetrics][batch_metrics] no repositório GitHub [de amostras de lote azul.][github_samples] O projeto de amostra BatchMetrics demonstra como monitorizar eficientemente o progresso do trabalho do Lote Azure utilizando a API do lote.

A amostra [BatchMetrics][batch_metrics] inclui um projeto de biblioteca de classe .NET que pode incorporar nos seus próprios projetos, e um simples programa de linha de comando para exercitar e demonstrar o uso da biblioteca.

A aplicação da amostra no âmbito do projeto demonstra as seguintes operações:

1. Selecionar atributos específicos para descarregar apenas as propriedades que necessita
2. Filtragem nos tempos de transição do Estado para descarregar apenas alterações desde a última consulta

Por exemplo, o seguinte método aparece na biblioteca BatchMetrics. Devolve um ODATADetailLevel que especifica `id` que `state` apenas as propriedades e propriedades devem ser obtidas para as entidades que são consultadas. Especifica ainda que apenas as entidades cujo `DateTime` Estado tenha mudado, uma vez que o parâmetro especificado deve ser devolvido.

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
### <a name="parallel-node-tasks"></a>Tarefas paralelas do nó
Maximizar O uso de [recursos computacionais do Lote Maximize Com tarefas de nó simultânea](batch-parallel-node-tasks.md) é outro artigo relacionado com o desempenho da aplicação do Lote. Alguns tipos de cargas de trabalho podem beneficiar da execução de tarefas paralelas em nós maiores mas menos-- computados. Confira o cenário de [exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para mais detalhes sobre tal cenário.


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
