---
title: Recolher dados de registo
titleSuffix: Azure Cognitive Search
description: Colete e analise dados de registo permitindo uma definição de diagnóstico e, em seguida, use a Linguagem de Consulta Kusto para explorar resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462373"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Recolher e analisar dados de registo para pesquisa cognitiva azure

Os registos de diagnóstico ou operacionais fornecem informações sobre as operações detalhadas da Pesquisa Cognitiva Azure e são úteis para monitorizar os processos de serviço e carga de trabalho. Internamente, existem registos no backend por um curto período de tempo, suficientes para investigação e análise se você arquivar um bilhete de apoio. No entanto, se pretender auto-orientação sobre dados operacionais, deve configurar uma definição de diagnóstico para especificar onde as informações de registo são recolhidas.

A configuração de registos é útil para diagnósticos e preservação do histórico operacional. Depois de ativar a exploração madeireira, pode executar consultas ou construir relatórios para análise estruturada.

A tabela que se segue enumera as opções de recolha e persistência de dados.

| Recurso | Utilizado para |
|----------|----------|
| [Enviar para Log Analytics espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Eventos e métricas são enviados para um espaço de trabalho log Analytics, que pode ser consultado no portal para devolver informações detalhadas. Para uma introdução, consulte [Começar com registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Arquivo com armazenamento Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Os eventos e métricas são arquivados num contentor Blob e armazenados em ficheiros JSON. Os registos podem ser bastante granulares (por hora/minuto), úteis para pesquisar um incidente específico, mas não para investigação em aberto. Utilize um editor da JSON para visualizar um ficheiro de registo bruto ou Power BI para agregar e visualizar dados de registo.|
| [Stream to Event Hub](https://docs.microsoft.com/azure/event-hubs/) | Eventos e métricas são transmitidos para um serviço Azure Event Hubs. Escolha isto como um serviço alternativo de recolha de dados para registos muito grandes. |

Tanto os registos do Monitor Azure como o armazenamento blob estão disponíveis como um serviço gratuito para que possa experimentá-lo gratuitamente durante a vida útil da sua subscrição Azure. Os Insights de Aplicação são livres de se inscreverem e utilizarem desde que o tamanho dos dados da aplicação esteja abaixo de determinados limites (consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para mais detalhes).

## <a name="prerequisites"></a>Pré-requisitos

Se estiver a utilizar o Log Analytics ou o Azure Storage, pode criar recursos com antecedência.

+ [Criar um espaço de trabalho de análise de registo](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Ativar a recolha de dados

As definições de diagnóstico especificam como os eventos e métricas registados são recolhidos.

1. Em **Monitorização**, selecione **Definições de diagnóstico**.

   ![Definições de diagnóstico](./media/search-monitor-usage/diagnostic-settings.png "Definições de diagnóstico")

1. Selecione **+ Adicione a definição de diagnóstico**

1. Verifique **o Log Analytics,** selecione o seu espaço de trabalho e selecione **OperationLogs** e **AllMetrics**.

   ![Configure a recolha de dados](./media/search-monitor-usage/configure-storage.png "Configure a recolha de dados")

1. Guarde a definição.

1. Depois de ativada a exploração madeireira, utilize o seu serviço de pesquisa para começar a gerar registos e métricas. Levará algum tempo até que eventos e métricas registados fiquem disponíveis.

Para o Log Analytics, serão vários minutos antes de os dados estarem disponíveis, após o que poderá executar consultas kusto para devolver dados. Para mais informações, consulte os pedidos de [consulta do Monitor](search-monitor-logs.md).

Para o armazenamento blob, demora uma hora até que os recipientes apareçam no armazenamento blob. Há uma bolha, por hora, por recipiente. Os recipientes só são criados quando existe uma atividade para registar ou medir. Quando os dados são copiados para uma conta de armazenamento, os dados são formatados como JSON e colocados em dois recipientes:

+ insights-logs-operationlogs: para registos de tráfego de pesquisa
+ insights-metrics-pt1m: para métricas

## <a name="query-log-information"></a>Informação de registo de consulta

Nos registos de diagnóstico, duas tabelas contêm registos e métricas para a Pesquisa Cognitiva Azure: **AzureDiagnostics** e **AzureMetrics**.

1. Sob **monitorização,** selecione **Registos**.

1. Introduza **a AzureMetrics** na janela de consulta. Faça esta simples consulta para se familiarizar com os dados recolhidos nesta tabela. Percorra a mesa para ver métricas e valores. Note a contagem de recordes no topo, e se o seu serviço estiver a recolher métricas há algum tempo, é melhor ajustar o intervalo de tempo para obter um conjunto de dados manejável.

   ![Tabela AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabela AzureMetrics")

1. Introduza a seguinte consulta para devolver um conjunto de resultados tabular.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Repita os passos anteriores, começando pela **AzureDiagnostics** para devolver todas as colunas para fins informísticos, seguida de uma consulta mais seletiva que extrai informações mais interessantes.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Mesa AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Mesa AzureDiagnostics")

## <a name="log-schema"></a>Esquema de log

As estruturas de dados que contêm dados de registo de pesquisa cognitiva Azure estão em conformidade com o esquema abaixo. 

Para o armazenamento blob, cada bolha tem um objeto de raiz chamado **registos** contendo uma variedade de objetos de madeira. Cada bolha contém registos de todas as operações que ocorreram durante a mesma hora.

A tabela seguinte é uma lista parcial de campos comuns à exploração madeireira de diagnóstico.

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| tempoGerado |datetime |"2018-12-07T00:00:43.6872559Z" |Carimbo de tempo da operação |
| resourceId |string |"/SUBSCRIÇÕES/11111111-1111-1111-1111-1111111111/<br/>GRUPOS DE RECURSOS/DEFAULT/FORNECEDORES/<br/> A MICROSOFT. PESQUISA/SERVIÇOS DE PESQUISA/SERVIÇO DE PESQUISA" |O seu ResourceId |
| operationName |string |"Consulta.Pesquisa" |O nome da operação |
| operationVersion |string |"2019-05-06" |A versão api utilizada |
| categoria |string |"OperationLogs" |constante |
| resultType |string |"Sucesso" |Valores possíveis: Sucesso ou Falha |
| resultSignature |int |200 |Código de resultados http |
| duraçãoMS |int |50 |Duração da operação em milissegundos |
| propriedades |objeto |ver a tabela seguinte |Objeto que contenha dados específicos da operação |

### <a name="properties-schema"></a>Esquema de propriedades

As propriedades abaixo são específicas da Pesquisa Cognitiva Azure.

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| Description_s |string |"GET/indexes('content')/docs" |O ponto final da operação |
| Documents_d |int |42 |Número de documentos processados |
| IndexName_s |string |"Índice de teste" |Nome do índice associado à operação |
| Query_s |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Os parâmetros de consulta |

## <a name="metrics-schema"></a>Esquema de métricas

As métricas são capturadas para pedidos de consulta e medida em intervalos de um minuto. Cada métrica expõe valores mínimos, máximos e médios por minuto. Para mais informações, consulte os pedidos de [consulta do Monitor](search-monitor-queries.md).

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIÇÕES/11111111-1111-1111-1111-1111111111/<br/>GRUPOS DE RECURSOS/DEFAULT/FORNECEDORES/<br/>A MICROSOFT. PESQUISA/SERVIÇOS DE PESQUISA/SERVIÇO DE PESQUISA" |o seu ID de recurso |
| nome métrico |string |"Latência" |o nome da métrica |
| hora |datetime |"2018-12-07T00:00:43.6872559Z" |o carimbo temporal da operação |
| média |int |64 |O valor médio das amostras cruas no intervalo de tempo métrico, unidades em segundos ou percentuais, dependendo da métrica. |
| mínimo |int |37 |O valor mínimo das amostras cruas no intervalo de tempo métrico, unidades em segundos. |
| máximo |int |78 |O valor máximo das amostras cruas no intervalo de tempo métrico, unidades em segundos.  |
| total |int |258 |O valor total das amostras cruas no intervalo de tempo métrico, unidades em segundos.  |
| count |int |4 |O número de métricas emitidas de um nó para o tronco dentro do intervalo de um minuto.  |
| grão de tempo |string |"PT1M" |O grão de tempo da métrica na ISO 8601. |

É comum que as consultas executem em milissegundos, por isso só as consultas que medem como segundos aparecerão em métricas como qPS.

Para as consultas de **pesquisa por segunda** métrica, o mínimo é o valor mais baixo para consultas de pesquisa por segundo que foi registado durante esse minuto. O mesmo se aplica ao valor máximo. Média, é o agregado durante todo o minuto. Por exemplo, dentro de um minuto, poderá ter um padrão como este: um segundo de carga alta que é o máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga média, e finalmente um segundo com apenas uma consulta, que é o mínimo.

Para consultas de **pesquisa aceleradas Percentagem**, mínimo, máximo, média e total, todas têm o mesmo valor: a percentagem de consultas de pesquisa que foram estranguladas, do número total de consultas de pesquisa durante um minuto.

## <a name="view-raw-log-files"></a>Ver ficheiros de registo crus

O armazenamento de blob é usado para arquivar ficheiros de registo. Pode utilizar qualquer editor da JSON para visualizar o ficheiro de registo. Se não tiver um, recomendamos o [Código do Estúdio Visual.](https://code.visualstudio.com/download)

1. No portal Azure, abra a sua conta de Armazenamento. 

2. No painel de navegação à esquerda, clique em **Blobs**. Deve ver **insights-logs-operationlogs** e **insights-metrics-pt1m**. Estes recipientes são criados pela Azure Cognitive Search quando os dados de registo são exportados para o armazenamento blob.

3. Clique na hierarquia da pasta até chegar ao ficheiro .json.  Utilize o menu de contexto para descarregar o ficheiro.

Assim que o ficheiro for descarregado, abra-o num editor da JSON para ver os conteúdos.

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, reveja os fundamentos da monitorização do serviço de pesquisa para saber mais sobre toda a gama de capacidades de supervisão.

> [!div class="nextstepaction"]
> [Monitorizar operações e atividades em Pesquisa Cognitiva Azure](search-monitor-usage.md)