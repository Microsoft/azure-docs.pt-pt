---
title: Monitorizar a utilização de recursos e métricas de consulta
titleSuffix: Azure Cognitive Search
description: Ativar a exploração madeireira, obter métricas de atividade de consulta, uso de recursos e outros dados do sistema a partir de um serviço de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7ef868f156ac537cb066f293872f69135c4df25f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059658"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-cognitive-search"></a>Monitorizar o consumo de recursos e a atividade de consulta na Pesquisa Cognitiva do Azure

Na página de visão geral do seu serviço de Pesquisa Cognitiva Azure, pode visualizar dados do sistema sobre o uso de recursos, métricas de consulta e quanto quota está disponível para criar mais índices, indexadores e fontes de dados. Também pode utilizar o portal para configurar a análise de registoou outro recurso utilizado para a recolha persistente de dados. 

A configuração de registos é útil para autodiagnósticos e preservação do histórico operacional. Internamente, existem registos no backend por um curto período de tempo, suficientes para investigação e análise se você arquivar um bilhete de apoio. Se quiser controlar e aceder a informações de log, deverá configurar uma das soluções descritas neste artigo.

Neste artigo, conheça as suas opções de monitorização, como ativar o armazenamento de registos e registos e como visualizar o conteúdo do registo.

## <a name="metrics-at-a-glance"></a>Métricas num ápice

As secções de **utilização** e **monitorização** incorporadas na página overview reportam sobre o consumo de recursos e métricas de execução de consultas. Esta informação fica disponível assim que começar a utilizar o serviço, sem necessidade de configuração. Esta página é refrescada a cada poucos minutos. Se estiver a finalizar decisões sobre [qual o nível a utilizar para cargas](search-sku-tier.md)de trabalho de produção , ou se ajustar [o número de réplicas e divisórias ativas,](search-capacity-planning.md)estas métricas podem ajudá-lo com essas decisões mostrando-lhe a rapidez com que os recursos são consumidos e quão bem a configuração atual lida com a carga existente.

O separador **De Utilização** mostra a disponibilidade de recursos em relação aos [limites](search-limits-quotas-capacity.md)atuais . A seguinte ilustração é para o serviço gratuito, que está limitado a 3 objetos de cada tipo e 50 MB de armazenamento. Um serviço Básico ou Standard tem limites mais elevados, e se aumentar as contagens de partição, o armazenamento máximo sobe proporcionalmente.

![Estado de utilização relativo a limites eficazes](./media/search-monitor-usage/usage-tab.png
 "Estado de utilização relativo a limites eficazes")

## <a name="queries-per-second-qps-and-other-metrics"></a>Consultas por segundo (QPS) e outras métricas

O separador **monitora** mostra médias móveis para métricas como consultas de pesquisa *por segundo* (QPS), agregadas por minuto. 
*A latência* de pesquisa é a quantidade de tempo que o serviço de pesquisa necessário para processar consultas de pesquisa, agregadas por minuto. *A percentagem* de consultas de pesquisa acelerada (não mostrada) é a percentagem de consultas de pesquisa que foram estranguladas, também agregadas por minuto.

Estes números são aproximados e destinam-se a dar-lhe uma ideia geral de quão bem o seu sistema está a servir pedidos. O QPS real pode ser superior ou inferior ao número reportado no portal.

![Consultas por segunda atividade](./media/search-monitor-usage/monitoring-tab.png "Consultas por segunda atividade")

## <a name="activity-logs"></a>Registos de atividade

O **registo de atividade** recolhe informações do Gestor de Recursos Azure. Exemplos de informações encontradas no registo de Atividades incluem a criação ou a locação de um serviço, a atualização de um grupo de recursos, a verificação da disponibilidade de nomes ou a obtenção de uma chave de acesso ao serviço para lidar com um pedido. 

Pode aceder ao **registo de atividade** a partir do painel de navegação à esquerda, ou a partir de Notificações na barra de comando da janela superior, ou da página diagnosticar e resolver **problemas.**

Para tarefas em serviço como criar um índice ou apagar uma fonte de dados, você verá notificações genéricas como "Obter Chave de Administrador" para cada pedido, mas não a ação específica em si. Para este nível de informação, deve ativar uma solução de monitorização adicionais.

## <a name="add-on-monitoring-solutions"></a>Soluções de monitorização adicionais

A Pesquisa Cognitiva Azure não armazena dados para além dos objetos que gere, o que significa que os dados de registo têm de ser armazenados externamente. Pode configurar qualquer um dos recursos abaixo se quiser persistir os dados de registo. 

O quadro seguinte compara as opções de armazenamento de registos e a monitorização aprofundada das operações de serviço e as cargas de trabalho de consulta através do Application Insights.

| Recurso | Utilizado para |
|----------|----------|
| [Registos do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Eventos registados e métricas de consulta, com base nos esquemas abaixo. Os eventos estão registados num espaço de trabalho do Log Analytics. Pode fazer consultas contra um espaço de trabalho para devolver informações detalhadas do registo. Para mais informações, consulte [Começar com registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Armazenamento de blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Eventos registados e métricas de consulta, com base nos esquemas abaixo. Os eventos são registados num contentor Blob e armazenados em ficheiros JSON. Utilize um editor da JSON para ver o conteúdo dos ficheiros.|
| [Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/) | Eventos registados e métricas de consulta, com base nos esquemas documentados neste artigo. Escolha isto como um serviço alternativo de recolha de dados para registos muito grandes. |

Tanto os registos do Monitor Azure como o armazenamento blob estão disponíveis como um serviço gratuito para que possa experimentá-lo gratuitamente durante a vida útil da sua subscrição Azure. Os Insights de Aplicação são livres de se inscreverem e utilizarem desde que o tamanho dos dados da aplicação esteja abaixo de determinados limites (consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para mais detalhes).

A secção seguinte percorre-o através dos passos de habilitação e utilização do armazenamento Azure Blob para recolher e aceder aos dados de registo criados pelas operações de Pesquisa Cognitiva Azure.

## <a name="enable-logging"></a>Ativar o registo

O registo de cargas horárias de indexação e consulta está desligado por defeito e depende de soluções adicionais tanto para infraestruturas de exploração como para armazenamento externo a longo prazo. Por si só, os únicos dados persistidos na Pesquisa Cognitiva Azure são os objetos que cria e gere, pelo que os registos devem ser armazenados em outro lugar.

Nesta secção, você aprenderá a usar o armazenamento Blob para armazenar eventos registados e dados de métricas.

1. [Crie uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) de armazenamento se ainda não tiver uma. Pode colocá-lo no mesmo grupo de recursos que a Azure Cognitive Search para simplificar a limpeza mais tarde se pretender eliminar todos os recursos utilizados neste exercício.

   A sua conta de armazenamento deve existir na mesma região que a Pesquisa Cognitiva Azure.

2. Abra a sua página de visão geral do serviço de pesquisa. No painel de navegação à esquerda, desloque-se para **baixo** até monitorizar e clique em **definições de diagnóstico**.

   ![Definições de diagnóstico](./media/search-monitor-usage/diagnostic-settings.png "Definições de diagnóstico")

3. **Selecione Adicionar definição de diagnóstico**

4. Escolher os dados que pretende exportar: registos, métricas ou ambos. Pode copiá-lo para uma conta de armazenamento, enviá-la para um centro de eventos ou exportá-la para registos do Monitor Azure.

   Para o armazenamento de arquivo para blob, apenas a conta de armazenamento deve existir. Os recipientes e as bolhas serão criados quando os dados de registo forem exportados.

   ![Configure arquivo de armazenamento de blob](./media/search-monitor-usage/configure-blob-storage-archive.png "Configure arquivo de armazenamento de blob")

5. Salvar o perfil

6. Teste a exploração de madeira criando ou apagando objetos (cria eventos de registo) e submetendo consultas (gera métricas). 

O registo é ativado assim que guardar o perfil. Os recipientes só são criados quando existe uma atividade para registar ou medir. Quando os dados são copiados para uma conta de armazenamento, os dados são formatados como JSON e colocados em dois recipientes:

* insights-logs-operationlogs: para os registos de tráfego de pesquisa
* as métricas-insights-pt1m: para métricas

**Demora uma hora até que os contentores apareçam no armazém da Blob. Há uma bolha, por hora, por recipiente.**

Pode utilizar o [Visual Studio Code](#download-and-open-in-visual-studio-code) ou outro editor da JSON para visualizar os ficheiros. 

### <a name="example-path"></a>Caminho de exemplo

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Esquema de registo
As bolhas que contêm os registos de tráfego do serviço de pesquisa estão estruturadas como descrito nesta secção. Cada bolha tem um objeto de raiz chamado **registos** contendo uma série de objetos de madeira. Cada bolha contém registos de todas as operações que ocorreram durante a mesma hora.

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| hora |datetime |"2018-12-07T00:00:43.6872559Z" |TimeStamp da operação |
| resourceId |string |"/ SUBSCRIÇÕES/11111111-1111-1111-1111-111111111111 /<br/>PADRÃO/RESOURCEGROUPS/FORNECEDORES /<br/> MICROSOFT. PESQUISA/SEARCHSERVICES/SEARCHSERVICE" |O ResourceId |
| operationName |string |"Query.Search" |O nome da operação |
| operationVersion |string |"2019-05-06" |A api-version utilizada |
| categoria |string |"OperationLogs" |constante |
| resultType |string |"Êxito" |Valores possíveis: êxito ou falha |
| resultSignature |int |200 |Código de resultado HTTP |
| durationMS |int |50 |Duração da operação em milissegundos |
| propriedades |objeto |consulte a tabela seguinte |Objeto que contém dados específicos da operação |

**Esquema de propriedades**

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| Descrição |string |"Obter /indexes('content')/docs" |Ponto final da operação |
| Consulta |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Os parâmetros de consulta |
| Documentos |int |42 |Número de documentos processados |
| indexName |string |"testindex" |Nome do índice associado à operação |

## <a name="metrics-schema"></a>Esquema de métricas

As métricas são capturadas para pedidos de consulta.

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| resourceId |string |"/ SUBSCRIÇÕES/11111111-1111-1111-1111-111111111111 /<br/>PADRÃO/RESOURCEGROUPS/FORNECEDORES /<br/>MICROSOFT. PESQUISA/SEARCHSERVICES/SEARCHSERVICE" |o seu ID de recurso |
| metricName |string |"Latência" |o nome da métrica |
| hora |datetime |"2018-12-07T00:00:43.6872559Z" |timestamp da operação |
| média |int |64 |O valor médio dos exemplos não processados no intervalo de tempo de métrica |
| mínimo |int |37 |O valor mínimo dos exemplos não processados no intervalo de tempo de métrica |
| máximo |int |78 |O valor máximo das amostras não processados no intervalo de tempo de métrica |
| total |int |258 |O valor total dos exemplos não processados no intervalo de tempo de métrica |
| count |int |4 |O número de amostras não processados, utilizado para gerar a métrica |
| intervalo de agregação |string |"PT1M" |O intervalo de agregação da métrica no ISO 8601 |

Todas as métricas são comunicadas em intervalos de um minuto. Cada medição expõe valores mínimos, máximo e médios por minuto.

Para a métrica de SearchQueriesPerSecond, o valor mais baixo para consultas de pesquisa por segundo que foram registadas durante esse minuto é mínimo. O mesmo se aplica ao valor máximo. Média, é a agregação em minuto completo.
Pense neste cenário, durante um minuto: um segundo alta isto é carregar o máximo para SearchQueriesPerSecond, seguido de 58 segundos da carga média, e, finalmente, um segundo, com apenas uma consulta, que é o mínimo.

Para ThrottledSearchQueriesPercentage, mínimo, máximo, média e total, todos têm o mesmo valor: a percentagem de consultas de pesquisa que eram limitados, do número total de consultas de pesquisa durante um minuto.

## <a name="download-and-open-in-visual-studio-code"></a>Descarregue e abra no Código do Estúdio Visual

Pode utilizar qualquer editor da JSON para visualizar o ficheiro de registo. Se não tiver um, recomendamos o [Código do Estúdio Visual.](https://code.visualstudio.com/download)

1. No portal Azure, abra a sua conta de Armazenamento. 

2. No painel de navegação à esquerda, clique em **Blobs**. Deve ver **insights-logs-operationlogs** e **insights-metrics-pt1m**. Estes recipientes são criados pela Azure Cognitive Search quando os dados de registo são exportados para o armazenamento blob.

3. Clique na hierarquia da pasta até chegar ao ficheiro .json.  Utilize o menu de contexto para descarregar o ficheiro.

Assim que o ficheiro for descarregado, abra-o num editor da JSON para ver os conteúdos.

## <a name="use-system-apis"></a>Utilização de APIs do sistema
Tanto a API de Pesquisa Cognitiva Azure como o .NET SDK fornecem acesso programático às métricas de serviço, informações indexadas e indexantes, e contagens de documentos.

* [Obter Estatísticas de Serviços](/rest/api/searchservice/get-service-statistics)
* [Obter Estatísticas de Índices](/rest/api/searchservice/get-index-statistics)
* [Documentos de Contagem](/rest/api/searchservice/count-documents)
* [Obter estatuto de indexante](/rest/api/searchservice/get-indexer-status)

Para permitir a utilização do PowerShell ou do Azure CLI, consulte a documentação [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Passos seguintes

[Gerencie o seu serviço de Pesquisa no Microsoft Azure](search-manage.md) para obter mais informações sobre a administração de serviços e [desempenho e otimização](search-performance-optimization.md) para orientação de afinação.
