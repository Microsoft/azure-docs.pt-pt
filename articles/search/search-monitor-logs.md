---
title: Recolher dados de registo
titleSuffix: Azure Cognitive Search
description: Recolher e analisar dados de registo, permitindo uma definição de diagnóstico e, em seguida, usar a Linguagem de Consulta de Kusto para explorar resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 52230d6b13c4210e0ff8e85d0a3efe39af55f6e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935063"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Recolher e analisar dados de registo para a pesquisa cognitiva do Azure

Os registos de diagnóstico ou operacionais fornecem informações detalhadas sobre as operações detalhadas da Pesquisa Cognitiva do Azure e são úteis para monitorizar o serviço e os processos de carga de trabalho. Internamente, existem algumas informações do sistema sobre o backend por um curto período de tempo, suficiente para investigação e análise se você arquivar um bilhete de apoio. No entanto, se pretender a auto-orientação sobre os dados operacionais, deverá configurar uma definição de diagnóstico para especificar onde as informações de registo são recolhidas.

O registo de diagnóstico é ativado através da integração com [o Azure Monitor](../azure-monitor/index.yml). 

Quando configurar a registo de diagnóstico, ser-lhe-á pedido que especifique um mecanismo de armazenamento. A tabela a seguir enumera opções de recolha e persistência de dados.

| Recurso | Utilizado para |
|----------|----------|
| [Enviar para a área de trabalho do Log Analytics](../azure-monitor/learn/tutorial-resource-logs.md) | Eventos e métricas são enviados para um espaço de trabalho Log Analytics, que pode ser consultado no portal para devolver informações detalhadas. Para uma introdução, consulte [Começar com os registos do Azure Monitor](../azure-monitor/log-query/get-started-portal.md) |
| [Arquivo com armazenamento Blob](../storage/blobs/storage-blobs-overview.md) | Os eventos e métricas são arquivados num recipiente Blob e armazenados em ficheiros JSON. Os troncos podem ser bastante granulares (à hora/minuto), úteis para pesquisar um incidente específico, mas não para investigação aberta. Utilize um editor JSON para visualizar um ficheiro de log cru ou Power BI para agregar e visualizar dados de registo.|
| [Stream para Centro de Eventos](../event-hubs/index.yml) | Eventos e métricas são transmitidos para um serviço Azure Event Hubs. Escolha isto como um serviço alternativo de recolha de dados para registos muito grandes. |

## <a name="prerequisites"></a>Pré-requisitos

Crie recursos com antecedência para que possa selecionar um ou mais ao configurar a sessão de diagnóstico.

+ [Criar um espaço de trabalho de análise de log analytics](../azure-monitor/learn/quick-create-workspace.md)

+ [Criar uma conta de armazenamento](../storage/common/storage-account-create.md)

+ [Criar um Centro de Eventos](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>Ativar a recolha de dados

As definições de diagnóstico especificam como os eventos e métricas registados são recolhidos.

1. Em **Monitorização**, selecione **Definições de diagnóstico**.

   ![Definições de diagnóstico](./media/search-monitor-usage/diagnostic-settings.png "Definições de diagnóstico")

1. Selecione **+ Adicionar definição de diagnóstico**

1. Verifique **o Log Analytics,** selecione o seu espaço de trabalho e selecione **OperationLogs** e **AllMetrics**.

   ![Configurar a recolha de dados](./media/search-monitor-usage/configure-storage.png "Configurar a recolha de dados")

1. Guarde a regulação.

1. Depois de ter sido ativado o registo, utilize o seu serviço de pesquisa para começar a gerar registos e métricas. Levará tempo até que os eventos registados e as métricas estejam disponíveis.

Para o Log Analytics, serão vários minutos antes de os dados estarem disponíveis, após o que poderá executar consultas kusto para devolver dados. Para obter mais informações, consulte [os pedidos de consulta do Monitor](search-monitor-logs.md).

Para o armazenamento blob, leva uma hora até que os recipientes apareçam no armazenamento Blob. Há uma bolha, por hora, por contentor. Os contentores só são criados quando há uma atividade para registar ou medir. Quando os dados são copiados para uma conta de armazenamento, os dados são formatados como JSON e colocados em dois contentores:

+ insights-logs-operaçãologs: para registos de tráfego de pesquisa
+ insights-metrics-pt1m: para métricas

## <a name="query-log-information"></a>Informações de registo de consultas

Duas tabelas contêm troncos e métricas para Azure Cognitive Search: **AzureDiagnostics** e **AzureMetrics**.

1. Em **Monitorização**, **selecione Logs**.

1. Introduza **AzureMetrics** na janela de consulta. Faça esta simples consulta para conhecer os dados recolhidos nesta tabela. Percorra a mesa para ver métricas e valores. Note a contagem de registos no topo, e se o seu serviço estiver a recolher métricas há algum tempo, talvez queira ajustar o intervalo de tempo para obter um conjunto de dados manejável.

   ![Tabela AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabela AzureMetrics")

1. Introduza a seguinte consulta para devolver um conjunto de resultados tabular.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Repita os passos anteriores, começando com **a AzureDiagnostics** para devolver todas as colunas para fins informativos, seguida de uma consulta mais seletiva que extrai informações mais interessantes.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabela AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabela AzureDiagnostics")

## <a name="kusto-query-examples"></a>Exemplos de consulta de Kusto

Se for habilitado a fazer login de diagnóstico, pode consultar **a AzureDiagnostics** para uma lista de operações que funcionavam no seu serviço e quando. Também pode correlacionar atividade para investigar alterações no desempenho.

#### <a name="example-list-operations"></a>Exemplo: Lista de operações 

Devolva uma lista de operações e uma contagem de cada uma.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Exemplo: Correlacionar operações

Correlacionar o pedido de consulta com as operações de indexação, e tornar os pontos de dados através de um gráfico de tempo para ver as operações coincidirem.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>Operações registadas

Os eventos registados capturados pelo Azure Monitor incluem os relacionados com a indexação e consultas. A tabela **AzureDiagnostics** no Log Analytics recolhe dados operacionais relacionados com consultas e indexação.

| OperationName | Descrição |
|---------------|-------------|
| Estatísticas de Serviço | Esta operação é uma chamada de rotina para [obter estatísticas de serviços,](/rest/api/searchservice/get-service-statistics)chamadas direta ou implicitamente para preencher uma página de visão geral do portal quando esta é carregada ou atualizada. |
| Consulta.Pesquisa |  Pedidos de consulta contra um índice Consulte [consultas do Monitor](search-monitor-queries.md) para obter informações sobre consultas registadas.|
| Indexação.Índice  | Esta operação é uma chamada para [adicionar, atualizar ou eliminar documentos.](/rest/api/searchservice/addupdate-or-delete-documents) |
| índices. Protótipo | Este é um índice criado pelo assistente de dados de importação. |
| Indexadores.Criar | Crie um indexante explicitamente ou implicitamente através do assistente de Dados de Importação. |
| Indexadores.Get | Devolve o nome de um indexante sempre que o indexante é executado. |
| Indexadores.Estado | Devolve o estado de um indexante sempre que o indexante é executado. |
| DataSources.Get | Devolve o nome da fonte de dados sempre que um indexante é executado.|
| Índices.Get | Devolve o nome de um índice sempre que um indexante é executado. |

## <a name="log-schema"></a>Esquema de registo

Se estiver a construir relatórios personalizados, as estruturas de dados que contêm dados de registo de pesquisa cognitiva do Azure estão em conformidade com o esquema abaixo. Para o armazenamento blob, cada bolha tem um objeto de raiz chamado **registos** que contém uma matriz de objetos de log. Cada bolha contém registos de todas as operações que ocorreram durante a mesma hora.

A tabela a seguir é uma lista parcial de campos comuns à exploração de recursos.

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| tempoGerado |datetime |"2018-12-07T00:00:43.6872559Z" |Timetamp da operação |
| resourceId |string |"/ASSINATURAS/11111111-1111-1111-1111-1111-111111111111/<br/>GRUPOS DE RECURSOS/INCUMPRIMENTO/FORNECEDORES/<br/> A MICROSOFT. SEARCH/SEARCHSERVICES/SEARCHSERVICE" |O seu ResourceId |
| operationName |string |"Consulta.Pesquisa" |O nome da operação |
| operationVersion |string |"2020-06-30" |A versão api usada |
| categoria |string |"OperaçãoLogs" |constante |
| resultType |string |"Sucesso" |Valores possíveis: Sucesso ou Falha |
| resultSignature |int |200 |Código de resultados HTTP |
| durações |int |50 |Duração da operação em milissegundos |
| propriedades |objeto |ver a tabela seguinte |Objeto contendo dados específicos da operação |

### <a name="properties-schema"></a>Esquema de propriedades

As propriedades abaixo são específicas da Pesquisa Cognitiva Azure.

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| Description_s |string |"GET /indexes ('content')/docs" |O ponto final da operação |
| Documents_d |int |42 |Número de documentos processados |
| IndexName_s |string |"índice de teste" |Nome do índice associado à operação |
| Query_s |string |"?search=AzureSearch&$count=true&api-version=2020-06-30" |Os parâmetros de consulta |

## <a name="metrics-schema"></a>Esquema de métricas

As métricas são capturadas para pedidos de consulta e medidas em intervalos de um minuto. Cada métrica expõe valores mínimos, máximos e médios por minuto. Para obter mais informações, consulte [os pedidos de consulta do Monitor](search-monitor-queries.md).

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| resourceId |string |"/ASSINATURAS/11111111-1111-1111-1111-1111-111111111111/<br/>GRUPOS DE RECURSOS/INCUMPRIMENTO/FORNECEDORES/<br/>A MICROSOFT. SEARCH/SEARCHSERVICES/SEARCHSERVICE" |seu ID recurso |
| nome métrico |string |"Latência" |o nome da métrica |
| hora |datetime |"2018-12-07T00:00:43.6872559Z" |a estada de tempo da operação |
| média |int |64 |O valor médio das amostras brutas no intervalo de tempo métrico, unidades em segundos ou percentagem, dependendo da métrica. |
| mínimo |int |37 |O valor mínimo das amostras brutas no intervalo de tempo métrico, unidades em segundos. |
| máximo |int |78 |O valor máximo das amostras brutas no intervalo de tempo métrico, unidades em segundos.  |
| total |int |258 |O valor total das amostras brutas no intervalo de tempo métrico, unidades em segundos.  |
| count |int |4 |O número de métricas emitidas de um nó para o log dentro do intervalo de um minuto.  |
| timegrain |string |"PT1M" |O grão de tempo da métrica na ISO 8601. |

É comum que as consultas executem em milissegundos, por isso apenas as consultas que medem como segundos aparecerão em métricas como QPS.

Para a **métrica de Pesquisa Por Segundo,** o mínimo é o valor mais baixo para consultas de pesquisa por segundo que foi registado durante esse minuto. O mesmo se aplica ao valor máximo. Média, é o agregado em todo o minuto. Por exemplo, dentro de um minuto, pode ter um padrão como este: um segundo de carga alta que é o máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga média, e finalmente um segundo com apenas uma consulta, que é o mínimo.

Para **a Percentagem de Consultas de Pesquisa Throttled**, mínima, máxima, média e total, todas têm o mesmo valor: a percentagem de consultas de pesquisa que foram estrangulada, do número total de consultas de pesquisa durante um minuto.

## <a name="view-raw-log-files"></a>Ver ficheiros de registo bruto

O armazenamento de blob é utilizado para arquivar ficheiros de registo. Pode utilizar qualquer editor JSON para visualizar o ficheiro de registo. Se não tiver um, recomendamos [código de estúdio visual.](https://code.visualstudio.com/download)

1. No portal Azure, abra a sua conta de Armazenamento. 

2. No painel de navegação à esquerda, clique em **Blobs**. Você deve ver **insights-logs-operationlogs** e **insights-metrics-pt1m**. Estes recipientes são criados pela Azure Cognitive Search quando os dados de registo são exportados para o armazenamento blob.

3. Clique na hierarquia da pasta até chegar ao ficheiro .json.  Utilize o menu de contexto para descarregar o ficheiro.

Assim que o ficheiro for descarregado, abra-o num editor da JSON para ver o conteúdo.

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, reveja os fundamentos da monitorização do serviço de pesquisa para saber mais sobre toda a gama de capacidades de supervisão.

> [!div class="nextstepaction"]
> [Monitorizar operações e atividades em Azure Cognitive Search](search-monitor-usage.md)