---
title: Consultas de lista eficientes de design
description: Aumente o desempenho filtrando as suas consultas ao solicitar informações sobre recursos do Batch, como piscinas, empregos, tarefas e nós de computação.
ms.topic: how-to
ms.date: 06/18/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3a767cc8ae3c8c48e1e40e0735c33fa807ba0015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88933519"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Criar consultas para listar os recursos do Batch de forma eficiente

Quase todas as aplicações do Batch necessitam de efetuar algum tipo de monitorização ou outra operação que questione o serviço Batch, muitas vezes em intervalos regulares. Por exemplo, para determinar se existem algumas tarefas em fila restantes num trabalho, deve obter dados sobre todas as tarefas no trabalho. Para determinar o estado dos nós na sua piscina, você deve obter dados sobre cada nó na piscina. Este artigo explica como executar tais consultas da forma mais eficiente.

Pode aumentar o desempenho da sua aplicação Azure Batch reduzindo a quantidade de dados que são devolvidos pelo serviço quando consulta empregos, tarefas, nó de cálculo e outros recursos com a biblioteca [Batch .NET.](/dotnet/api/microsoft.azure.batch)

> [!NOTE]
> O serviço Batch fornece suporte API para os cenários comuns de contagem de tarefas em um trabalho, e contando nós de computação em lote de lote. Em vez de utilizar uma consulta de lista para estes, pode ligar para as operações [Get Task Counts](/rest/api/batchservice/job/gettaskcounts) e List Pool [Node Counts.](/rest/api/batchservice/account/listpoolnodecounts) Estas operações são mais eficientes do que uma consulta de lista, mas devolvem informações mais limitadas que podem nem sempre estar atualizadas. Para obter mais informações, consulte [as tarefas count e os nós de cálculo por estado.](batch-get-resource-counts.md)

## <a name="specify-a-detail-level"></a>Especificar um nível de detalhe

Numa aplicação de lote de produção, entidades como empregos, tarefas e nómada podem numerar milhares. Quando solicita informações sobre estes recursos, uma quantidade potencialmente grande de dados deve "atravessar o fio" do serviço Batch para a sua aplicação em cada consulta. Ao limitar o número de itens e o tipo de informação que é devolvido por uma consulta, pode aumentar a velocidade das suas consultas e, portanto, o desempenho da sua aplicação.

Este snippet de código API [lote .NET](/dotnet/api/microsoft.azure.batch) lista *todas as* tarefas que estão associadas a um trabalho, juntamente com *todas as* propriedades de cada tarefa:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

No entanto, pode realizar uma consulta de lista muito mais eficiente, aplicando um "nível de detalhe" à sua consulta. Fá-lo fornecendo um objeto [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) ao método [JobOperations.ListTasks.](/dotnet/api/microsoft.azure.batch.joboperations) Este snippet devolve apenas as propriedades de ID, linha de comando e informação do nó de cálculo das tarefas concluídas:

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

Neste cenário de exemplo, se houver milhares de tarefas no trabalho, os resultados da segunda consulta serão normalmente devolvidos muito mais rapidamente do que o primeiro. Mais informações sobre a utilização do ODATADetailLevel quando listar itens com o Lote .NET API estão incluídos [abaixo](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Recomendamos vivamente que forneça sempre um objeto ODATADetailLevel às suas chamadas de lista de API .NET para garantir a máxima eficiência e desempenho da sua aplicação. Ao especificar um nível de detalhe, pode ajudar a reduzir os tempos de resposta do serviço do Batch, melhorar a utilização da rede e minimizar o uso da memória pelas aplicações do cliente.

## <a name="filter-select-and-expand"></a>Filtrar, selecionar e expandir

As APIs [de Lote .NET](/dotnet/api/microsoft.azure.batch) e [Lote REST](/rest/api/batchservice/) fornecem a capacidade de reduzir tanto o número de itens que são devolvidos numa lista, como a quantidade de informação que é devolvida para cada um. Fá-lo especificando **o filtro,** **selecione** e **expanda as cordas** ao executar consultas de lista.

### <a name="filter"></a>Filtro

A corda do filtro é uma expressão que reduz o número de itens que são devolvidos. Por exemplo, pode listar apenas as tarefas de execução para um trabalho, ou listar apenas nós de computação que estejam prontos para executar tarefas.

A cadeia de filtro é constituída por uma ou mais expressões, com uma expressão que consiste num nome de propriedade, operador e valor. As propriedades que podem ser especificadas são específicas de cada tipo de entidade que consulta, assim como os operadores que são suportados para cada imóvel.  Expressões múltiplas podem ser combinadas utilizando os operadores lógicos `and` e `or` .

Este exemplo lista apenas as tarefas de "renderização" em execução: `(state eq 'running') and startswith(id, 'renderTask')` .

### <a name="select"></a>Selecione

A cadeia selecionada limita os valores de propriedade que são devolvidos para cada item. Você especifica uma lista de nomes de propriedade separados por vírgula, e apenas esses valores de propriedade são devolvidos para os itens nos resultados da consulta. Pode especificar qualquer uma das propriedades para o tipo de entidade que está a consultar.

Este exemplo de cadeia selecionada especifica que apenas três valores de propriedade devem ser devolvidos para cada tarefa: `id, state, stateTransitionTime` .

### <a name="expand"></a>Expandir

O string de expansão reduz o número de chamadas API que são necessárias para obter determinadas informações. Quando utiliza uma cadeia de expansão, mais informações sobre cada item podem ser obtidas com uma única chamada API. Em vez de obter primeiro a lista de entidades, depois solicitar informações para cada item da lista, utiliza uma cadeia de expansão para obter a mesma informação numa única chamada API, ajudando a melhorar o desempenho reduzindo as chamadas API.

Semelhante à cadeia selecionada, a cadeia de expansão controla se certos dados estão incluídos nos resultados da consulta da lista. Quando todas as propriedades são necessárias e nenhuma cadeia selecionada é especificada, o string de expansão *deve* ser usado para obter informações estatísticas. Se uma cadeia selecionada for utilizada para obter um subconjunto de propriedades, `stats` então pode ser especificada na cadeia selecionada, e a cadeia de expansão não precisa de ser especificada.

A cadeia de expansão só é suportada quando é usada na listagem de empregos, horários de trabalho, tarefas e piscinas. Atualmente, apenas suporta informação estatística.

Este exemplo expande a cadeia especifica que as informações estatísticas devem ser devolvidas para cada item da lista: `stats` .

> [!NOTE]
> Ao construir qualquer um dos três tipos de cadeias de consulta (filtrar, selecionar e expandir), deve certificar-se de que os nomes da propriedade e o caso coincidem com os dos seus homólogos de elementos DEAPI REST. Por exemplo, ao trabalhar com a classe .NET [CloudTask,](/dotnet/api/microsoft.azure.batch.cloudtask) deve especificar **o estado** em vez de **Estado**, mesmo que a propriedade .NET seja [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Consulte as tabelas abaixo para mapear propriedades entre as APIs .NET e REST.

### <a name="rules-for-filter-select-and-expand-strings"></a>Regras para filtrar, selecionar e expandir cordas

- Os nomes de propriedades no filtro, selecione e expanda as cordas devem aparecer como fazem no [Lote REST](/rest/api/batchservice/) API, mesmo quando utilizar [Batch .NET](/dotnet/api/microsoft.azure.batch) ou um dos outros SDKs de lote.
- Todos os nomes de propriedade são sensíveis a casos, mas os valores de propriedade são insensíveis.
- As cordas data/hora podem ser um de dois formatos, e devem ser precedidas com `DateTime` .
  
  - Exemplo do formato W3C-DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Exemplo de formato RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- As cordas booleanas são `true` `false` ou.
- Se for especificado um imóvel ou operador inválido, resultará um `400 (Bad Request)` erro.

## <a name="efficient-querying-in-batch-net"></a>Consulta eficiente em Lote .NET

Dentro do [Lote .NET](/dotnet/api/microsoft.azure.batch) API, a classe [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) é utilizada para fornecer filtros, selecione e expanda as cordas para listar as operações. A classe ODataDetailLevel tem três propriedades de cordas públicas que podem ser especificadas no construtor, ou definidas diretamente no objeto. Em seguida, passa o objeto ODataDetailLevel como parâmetro para as várias operações da lista, tais como [ListPools,](/dotnet/api/microsoft.azure.batch.pooloperations) [ListJobs](/dotnet/api/microsoft.azure.batch.joboperations)e [ListTasks](/dotnet/api/microsoft.azure.batch.joboperations).

- [ODATADetailLevel.FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause): Limite o número de itens que são devolvidos.
- [ODATADetailLevel.SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause): Especifique quais os valores de propriedade devolvidos com cada item.
- [ODATADetailLevel.ExpandClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.expandclause): Recupere dados para todos os itens numa única chamada API em vez de chamadas separadas para cada item.

O seguinte corte de código utiliza o Batch .NET API para consultar eficazmente o serviço Batch para as estatísticas de um conjunto específico de piscinas. Neste cenário, o utilizador do Batch tem piscinas de teste e de produção. Os IDs do pool de teste são pré-fixados com "teste", e os IDs do pool de produção são prefixados com "prod". No snippet, *myBatchClient* é um exemplo devidamente inicializado da classe [BatchClient.](/dotnet/api/microsoft.azure.batch.batchclient)

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
> Um caso de [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) que está configurado com cláusulas Select e Expand pode também ser passado para métodos de obter apropriados, tais como [PoolOperations.GetPool,](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__)para limitar a quantidade de dados que são devolvidos.

## <a name="batch-rest-to-net-api-mappings"></a>Lote REST para mapeamentos de API .NET

Os nomes de propriedade no filtro, selecionam e expandem as cordas devem refletir os seus homólogos da API REST, tanto em nome como em caso. As tabelas abaixo fornecem mapeamentos entre as contrapartidas .NET e REST API.

### <a name="mappings-for-filter-strings"></a>Mapeamentos para cordas de filtro

- **.NET métodos de lista**: Cada um dos métodos de API .NET nesta coluna aceita um objeto [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) como parâmetro.
- **Pedidos de lista REST**: Cada página de API REST ligada a esta coluna contém uma tabela que especifica as propriedades e operações que são permitidas em cadeias *de filtro.* Utiliza estes nomes e operações de propriedade quando constrói uma cadeia [ODATADetailLevel.FilterClause.](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause)

| Métodos de lista .NET | Pedidos de lista REST |
| --- | --- |
| [Operações de certificado.ListCertificates](/dotnet/api/microsoft.azure.batch.certificateoperations) |[Listar os certificados numa conta](/rest/api/batchservice/certificate/list) |
| [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask) |[Listar os ficheiros associados a uma tarefa](/rest/api/batchservice/file/listfromtask) |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus](/dotnet/api/microsoft.azure.batch.joboperations) |[Listar o estado das tarefas de preparação de emprego e de libertação de emprego para um trabalho](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) |
| [JobOperations.ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) |[Listar os empregos numa conta](/rest/api/batchservice/job/list) |
| [JobOperations.ListNodeFiles](/dotnet/api/microsoft.azure.batch.joboperations) |[Listar os ficheiros num nó](/rest/api/batchservice/file/listfromcomputenode) |
| [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) |[Listar as tarefas associadas a um trabalho](/rest/api/batchservice/task/list) |
| [JobScheduleOperations.ListJobSchedules](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Listar os horários de trabalho numa conta](/rest/api/batchservice/jobschedule/list) |
| [JobScheduleOperations.ListJobs](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Listar os empregos associados a um horário de trabalho](/rest/api/batchservice/job/listfromjobschedule) |
| [PoolOperations.ListComputeNodes](/dotnet/api/microsoft.azure.batch.pooloperations) |[Listar os nó de computação numa piscina](/rest/api/batchservice/computenode/list) |
| [PoolOperations.ListPools](/dotnet/api/microsoft.azure.batch.pooloperations) |[Listar as piscinas numa conta](/rest/api/batchservice/pool/list) |

### <a name="mappings-for-select-strings"></a>Mapeamentos para cadeias selecionadas

- **Tipos de lote .NET**: Tipos de API lote .NET.
- **Rest As entidades API**: Cada página desta coluna contém uma ou mais tabelas que listam os nomes de propriedade rest API para o tipo. Estes nomes de propriedade são usados quando constrói cordas *selecionadas.* Utiliza estes mesmos nomes de propriedade quando constrói uma cadeia [ODATADetailLevel.SelectClause.](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause)

| Tipos de lote .NET | Rest API entidades |
| --- | --- |
| [Certificado](/dotnet/api/microsoft.azure.batch.certificate) |[Obtenha informações sobre um certificado](/rest/api/batchservice/certificate/get) |
| [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) |[Obtenha informações sobre um trabalho](/rest/api/batchservice/job/get) |
| [CloudJobSchedule](/dotnet/api/microsoft.azure.batch.cloudjobschedule) |[Obtenha informações sobre um horário de trabalho](/rest/api/batchservice/jobschedule/get) |
| [ComputeNode](/dotnet/api/microsoft.azure.batch.computenode) |[Obtenha informações sobre um nó](/rest/api/batchservice/computenode/get) |
| [CloudPool](/dotnet/api/microsoft.azure.batch.cloudpool) |[Obtenha informações sobre uma piscina](/rest/api/batchservice/pool/get) |
| [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) |[Obtenha informações sobre uma tarefa](/rest/api/batchservice/task/get) |

## <a name="example-construct-a-filter-string"></a>Exemplo: construa uma corda de filtro

Quando construir uma cadeia de filtros para [ODATADetailLevel.FilterClause,](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause)consulte a tabela acima em "Mappings for filter strings" para encontrar a página de documentação DA API REST que corresponda à operação da lista que deseja executar. Encontrará as propriedades filtradas e os seus operadores suportados na primeira tabela multi-sobrancelha nessa página. Se desejar recuperar todas as tarefas cujo código de saída não era zero, por exemplo, esta linha na [Lista as tarefas associadas a um trabalho](/rest/api/batchservice/task/list) especifica a cadeia de propriedade aplicável e os operadores admissíveis:

| Propriedade | Operações permitidas | Tipo |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Assim, a cadeia de filtro para listar todas as tarefas com um código de saída não-zero seria:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemplo: construa uma corda selecionada

Para construir [ODATADetailLevel.SelectClause,](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause)consulte a tabela acima em "Mappings for select strings" e navegue na página REST API que corresponde ao tipo de entidade que está a listar. Encontrará as propriedades selecionáveis e os seus operadores suportados na primeira tabela multi-sobrancelha nessa página. Se desejar recuperar apenas o ID e a linha de comando para cada tarefa numa lista, por exemplo, encontrará estas linhas na tabela aplicável na [Obter informações sobre uma tarefa:](/rest/api/batchservice/task/get)

| Propriedade | Tipo | Notas |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

A cadeia selecionada para incluir apenas o ID e a linha de comando com cada tarefa listada seria:

`id, commandLine`

## <a name="code-samples"></a>Exemplos de código

### <a name="efficient-list-queries-code-sample"></a>Amostra de código de lista eficiente

O projeto de amostra [EfficientListQueries](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries) no GitHub mostra como a consulta de listas eficiente pode afetar o desempenho numa aplicação. Esta aplicação de consola C# cria e adiciona um grande número de tarefas a um trabalho. Em seguida, faz múltiplas chamadas para o método [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) e passa objetos [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) que estão configurados com diferentes valores de propriedade para variar a quantidade de dados a devolver. Produz uma produção semelhante à seguinte:

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

Como mostrado nos tempos decorridos, é possível reduzir consideravelmente os tempos de resposta de consulta limitando as propriedades e o número de itens que são devolvidos. Você pode encontrar este e outros projetos de amostra no repositório [de amostras de azure-lot](https://github.com/Azure-Samples/azure-batch-samples) no GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Biblioteca batchmetrics e amostra de código

Para além da amostra de código EfficientListQueries acima, o projeto de amostra [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) demonstra como monitorizar eficazmente o progresso do trabalho do Azure Batch utilizando a API do lote.

A amostra [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) inclui um projeto de biblioteca de classe .NET que pode incorporar nos seus próprios projetos, e um simples programa de linha de comando para exercer e demonstrar o uso da biblioteca.

A aplicação da amostra no âmbito do projeto demonstra as seguintes operações:

1. Selecionando atributos específicos para descarregar apenas as propriedades que precisa
2. Filtrar os tempos de transição do Estado para descarregar apenas alterações desde a última consulta

Por exemplo, o seguinte método aparece na biblioteca BatchMetrics. Devolve um ODATADetailLevel que especifica que apenas os `id` imóveis e `state` propriedades devem ser obtidos para as entidades que são consultadas. Especifica ainda que apenas as entidades cujo estado mudou desde o parâmetro especificado `DateTime` devem ser devolvidas.

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

- Saiba como maximizar o [uso do recurso de computação Azure Batch com tarefas de nó simultâneos](batch-parallel-node-tasks.md). Alguns tipos de cargas de trabalho podem beneficiar da execução de tarefas paralelas em nós de computação maiores (mas menos) de computação. Confira o cenário de [exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para mais detalhes sobre tal cenário.
- Saiba como [monitorizar as soluções do Batch contando tarefas e acenos por estado](batch-get-resource-counts.md)


[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_ctor]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_expand]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_filter]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_select]: /dotnet/api/microsoft.azure.batch.odatadetaillevel

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[rest_list_certs]: /rest/api/batchservice/certificate/list
[rest_list_compute_nodes]: /rest/api/batchservice/computenode/list
[rest_list_job_schedules]: /rest/api/batchservice/jobschedule/list
[rest_list_jobprep_status]: /rest/api/batchservice/job/listpreparationandreleasetaskstatus
[rest_list_jobs]: /rest/api/batchservice/job/list
[rest_list_nodefiles]: /rest/api/batchservice/file/listfromcomputenode
[rest_list_pools]: /rest/api/batchservice/pool/list
[rest_list_schedule_jobs]: /rest/api/batchservice/job/listfromjobschedule
[rest_list_task_files]: /rest/api/batchservice/file/listfromtask
[rest_list_tasks]: /rest/api/batchservice/task/list

[rest_get_cert]: /rest/api/batchservice/certificate/get
[rest_get_job]: /rest/api/batchservice/job/get
[rest_get_node]: /rest/api/batchservice/computenode/get
[rest_get_pool]: /rest/api/batchservice/pool/get
[rest_get_schedule]: /rest/api/batchservice/jobschedule/get
[rest_get_task]: /rest/api/batchservice/task/get

[net_cert]: /dotnet/api/microsoft.azure.batch.certificate
[net_job]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_node]: /dotnet/api/microsoft.azure.batch.computenode
[net_pool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_schedule]: /dotnet/api/microsoft.azure.batch.cloudjobschedule
[net_task]: /dotnet/api/microsoft.azure.batch.cloudtask

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
