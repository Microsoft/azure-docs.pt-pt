---
title: Recolher dados de registo
titleSuffix: Azure Cognitive Search
description: Colete e analise dados de registo permitindo uma definição de diagnóstico e, em seguida, use a Linguagem de Consulta Kusto para explorar resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 2849dc94f1c45dda3da09120adebba6e004eb96b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211181"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Recolher e analisar dados de registo para pesquisa cognitiva azure

Os registos de diagnóstico ou operacionais fornecem informações sobre as operações detalhadas da Pesquisa Cognitiva Azure e são úteis para monitorizar os processos de serviço e carga de trabalho. Internamente, existem registos no backend por um curto período de tempo, suficientes para investigação e análise se você arquivar um bilhete de apoio. No entanto, se pretender auto-orientação sobre dados operacionais, deve configurar uma definição de diagnóstico para especificar onde as informações de registo são recolhidas. 

A configuração de registos é útil para diagnósticos e preservação do histórico operacional. Depois de ativar a exploração madeireira, pode executar consultas ou construir relatórios para análise estruturada.

A tabela que se segue enumera as opções de recolha e persistência de dados.

| Recurso | Utilizado para |
|----------|----------|
| [Enviar para Log Analytics espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Eventos registados e métricas de consulta, com base nos esquemas abaixo. Os eventos estão registados num espaço de trabalho do Log Analytics. Utilizando o Log Analytics, pode executar consultas para devolver informações detalhadas. Para mais informações, consulte [Começar com registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Arquivo com armazenamento Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Eventos registados e métricas de consulta, com base nos esquemas abaixo. Os eventos são registados num contentor Blob e armazenados em ficheiros JSON. Os registos podem ser bastante granulares (por hora/minuto), úteis para pesquisar um incidente específico, mas não para investigação em aberto. Utilize um editor da JSON para ver um ficheiro de registo.|
| [Stream to Event Hub](https://docs.microsoft.com/azure/event-hubs/) | Eventos registados e métricas de consulta, com base nos esquemas documentados neste artigo. Escolha isto como um serviço alternativo de recolha de dados para registos muito grandes. |

Tanto os registos do Monitor Azure como o armazenamento blob estão disponíveis como um serviço gratuito para que possa experimentá-lo gratuitamente durante a vida útil da sua subscrição Azure. Os Insights de Aplicação são livres de se inscreverem e utilizarem desde que o tamanho dos dados da aplicação esteja abaixo de determinados limites (consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para mais detalhes).

## <a name="prerequisites"></a>Pré-requisitos

Se estiver a utilizar o Log Analytics ou o Azure Storage, pode criar recursos com antecedência.

+ [Criar um espaço de trabalho de análise de registo](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Crie uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) de armazenamento se necessitar de um arquivo de registo.

## <a name="create-a-log"></a>Criar um tronco

As definições de diagnóstico definem a recolha de dados. Uma definição especifica como e o que é recolhido. 

1. Sob **monitorização,** selecione **definições de diagnóstico**.

   ![Definições de diagnóstico](./media/search-monitor-usage/diagnostic-settings.png "Definições de diagnóstico")

1. Selecione **+ Adicione a definição de diagnóstico**

1. Escolher os dados que pretende exportar: registos, métricas ou ambos. Pode recolher dados numa conta de armazenamento, um espaço de trabalho de análise de registo, ou transmiti-lo para o Event Hub.

   Recomenda-se a análise de registo porque pode consultar o espaço de trabalho no portal.

   Se também estiver a utilizar o armazenamento blob, os contentores e as bolhas serão criados quando os dados de registo forem exportados.

   ![Configure a recolha de dados](./media/search-monitor-usage/configure-storage.png "Configure a recolha de dados")

1. Guarde a definição.

1. Teste criando ou apagando objetos (cria eventos de registo) e submetendo consultas (gera métricas). 

No armazenamento blob, os recipientes só são criados quando há uma atividade para log ou medir. Quando os dados são copiados para uma conta de armazenamento, os dados são formatados como JSON e colocados em dois recipientes:

* insights-logs-operationlogs: para os registos de tráfego de pesquisa
* as métricas-insights-pt1m: para métricas

**Demora uma hora até que os contentores apareçam no armazém da Blob. Há uma bolha, por hora, por recipiente.**

Os registos são arquivados por cada hora em que a atividade ocorre. O caminho seguinte é um exemplo de um ficheiro de registo criado a 12 de janeiro de 2020 às 9h00. onde cada `/` é uma pasta: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2020/m=01/d=12/h=09/m=00/name=PT1H.json`

## <a name="log-schema"></a>Esquema de registo

As estruturas de dados que contêm dados de registo de pesquisa cognitiva Azure estão em conformidade com o esquema abaixo. 

Para o armazenamento blob, cada bolha tem um objeto de raiz chamado **registos** contendo uma variedade de objetos de madeira. Cada bolha contém registos de todas as operações que ocorreram durante a mesma hora.

A tabela seguinte é uma lista parcial de campos comuns à exploração madeireira de diagnóstico.

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| tempoGerado |datetime |"2018-12-07T00:00:43.6872559Z" |TimeStamp da operação |
| resourceId |string |"/ SUBSCRIÇÕES/11111111-1111-1111-1111-111111111111 /<br/>PADRÃO/RESOURCEGROUPS/FORNECEDORES /<br/> MICROSOFT. PESQUISA/SEARCHSERVICES/SEARCHSERVICE" |O ResourceId |
| operationName |string |"Query.Search" |O nome da operação |
| operationVersion |string |"2019-05-06" |A api-version utilizada |
| categoria |string |"OperationLogs" |constante |
| resultType |string |"Êxito" |Valores possíveis: êxito ou falha |
| resultSignature |int |200 |Código de resultado HTTP |
| durationMS |int |50 |Duração da operação em milissegundos |
| propriedades |objeto |consulte a tabela seguinte |Objeto que contém dados específicos da operação |

### <a name="properties-schema"></a>Esquema de propriedades

As propriedades abaixo são específicas da Pesquisa Cognitiva Azure.

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| Description_s |string |"Obter /indexes('content')/docs" |Ponto final da operação |
| Documents_d |int |42 |Número de documentos processados |
| IndexName_s |string |"Índice de teste" |Nome do índice associado à operação |
| Query_s |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Os parâmetros de consulta |

## <a name="metrics-schema"></a>Esquema de métricas

As métricas são capturadas para pedidos de consulta e medida em intervalos de um minuto. Cada medição expõe valores mínimos, máximo e médios por minuto. Para mais informações, consulte os pedidos de [consulta do Monitor](search-monitor-queries.md).

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| resourceId |string |"/ SUBSCRIÇÕES/11111111-1111-1111-1111-111111111111 /<br/>PADRÃO/RESOURCEGROUPS/FORNECEDORES /<br/>MICROSOFT. PESQUISA/SEARCHSERVICES/SEARCHSERVICE" |o seu ID de recurso |
| metricName |string |"Latência" |o nome da métrica |
| hora |datetime |"2018-12-07T00:00:43.6872559Z" |timestamp da operação |
| média |int |64 |O valor médio das amostras cruas no intervalo de tempo métrico, unidades em segundos ou percentuais, dependendo da métrica. |
| mínimo |int |37 |O valor mínimo das amostras cruas no intervalo de tempo métrico, unidades em segundos. |
| máximo |int |78 |O valor máximo das amostras cruas no intervalo de tempo métrico, unidades em segundos.  |
| total |int |258 |O valor total das amostras cruas no intervalo de tempo métrico, unidades em segundos.  |
| count |int |4 |O número de métricas emitidas de um nó para o tronco dentro do intervalo de um minuto.  |
| intervalo de agregação |string |"PT1M" |O grão de tempo da métrica na ISO 8601. |

É comum que as consultas executem em milissegundos, por isso só as consultas que medem como segundos aparecerão em métricas como qPS.

Para as consultas de **pesquisa por segunda** métrica, o mínimo é o valor mais baixo para consultas de pesquisa por segundo que foi registado durante esse minuto. O mesmo se aplica ao valor máximo. Média, é a agregação em minuto completo. Por exemplo, dentro de um minuto, poderá ter um padrão como este: um segundo de carga alta que é o máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga média, e finalmente um segundo com apenas uma consulta, que é o mínimo.

Para consultas de **pesquisa aceleradas Percentagem**, mínimo, máximo, média e total, todas têm o mesmo valor: a percentagem de consultas de pesquisa que foram estranguladas, do número total de consultas de pesquisa durante um minuto.

## <a name="view-log-files"></a>Ver ficheiros de registo

O armazenamento de blob é usado para arquivar ficheiros de registo. Pode utilizar qualquer editor da JSON para visualizar o ficheiro de registo. Se não tiver um, recomendamos o [Código do Estúdio Visual.](https://code.visualstudio.com/download)

1. No portal Azure, abra a sua conta de Armazenamento. 

2. No painel de navegação à esquerda, clique em **Blobs**. Deve ver **insights-logs-operationlogs** e **insights-metrics-pt1m**. Estes recipientes são criados pela Azure Cognitive Search quando os dados de registo são exportados para o armazenamento blob.

3. Clique na hierarquia da pasta até chegar ao ficheiro .json.  Utilize o menu de contexto para descarregar o ficheiro.

Assim que o ficheiro for descarregado, abra-o num editor da JSON para ver os conteúdos.

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, reveja os fundamentos da monitorização do serviço de pesquisa para saber mais sobre toda a gama de capacidades de supervisão.

> [!div class="nextstepaction"]
> [Monitorizar operações e atividades em Pesquisa Cognitiva Azure](search-monitor-usage.md)