---
title: Monitorar o uso de recursos e as métricas de consulta para um serviço de pesquisa-Azure Search
description: Habilite o registro em log, obtenha métricas de atividade de consulta, uso de recursos e outros dados do sistema de um serviço de Azure Search.
author: HeidiSteen
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: heidist
ms.openlocfilehash: d0c93d941047413c5056b3718f57b360357affbd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327148"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Monitorar o consumo de recursos e a atividade de consulta no Azure Search

Na página Visão geral do seu serviço de Azure Search, você pode exibir dados do sistema sobre o uso de recursos, métricas de consulta e a quantidade de cota disponível para criar mais índices, indexadores e fontes de dados. Você também pode usar o portal para configurar o log Analytics ou outro recurso usado para a coleta de dados persistentes. 

A configuração de logs é útil para autodiagnóstico e preservação do histórico operacional. Internamente, os logs existem no back-end por um curto período de tempo, suficiente para investigação e análise se você arquivar um tíquete de suporte. Se você quiser controlar e acessar as informações de log, deverá configurar uma das soluções descritas neste artigo.

Neste artigo, saiba mais sobre suas opções de monitoramento, como habilitar o log e o armazenamento de log e como exibir o conteúdo do log.

## <a name="metrics-at-a-glance"></a>Métricas em um relance

As seções de **uso** e **monitoramento** criadas na página Visão geral relatam sobre o consumo de recursos e as métricas de execução de consulta. Essas informações ficam disponíveis assim que você começa a usar o serviço, sem nenhuma configuração necessária. Esta página é atualizada a cada poucos minutos. Se você estiver finalizando decisões sobre [qual camada usar para cargas de trabalho de produção](search-sku-tier.md)ou se deseja [ajustar o número de réplicas e partições ativas](search-capacity-planning.md), essas métricas podem ajudá-lo com essas decisões mostrando como os recursos são consumidos rapidamente e quão bem a configuração atual manipula a carga existente.

A guia **uso** mostra a disponibilidade de recursos em relação aos [limites](search-limits-quotas-capacity.md)atuais. A ilustração a seguir é para o serviço gratuito, que está limitado a três objetos de cada tipo e 50 MB de armazenamento. Um serviço básico ou Standard tem limites mais altos e, se você aumentar as contagens de partição, o armazenamento máximo será proporcionalmente.

![Status de uso relativo a limites efetivos]@no__t status de 1Usage relativo a limites efetivos @ no__t-2

## <a name="queries-per-second-qps-and-other-metrics"></a>Consultas por segundo (QPS) e outras métricas

A guia **monitoramento** mostra as médias de movimentação para métricas como consultas de pesquisa *por segundo* (QPS), agregadas por minuto. 
A *latência de pesquisa* é a quantidade de tempo que o serviço de pesquisa precisou para processar consultas de pesquisa, agregadas por minuto. A *porcentagem de consultas de pesquisa limitadas* (não mostradas) é a porcentagem de consultas de pesquisa que foram limitadas, também agregadas por minuto.

Esses números são aproximados e visam fornecer uma ideia geral de como o sistema está atendendo às solicitações. O QPS real pode ser maior ou menor do que o número relatado no Portal.

Atividade de consultas ![por segundo de atividades](./media/search-monitor-usage/monitoring-tab.png "por segundo")

## <a name="activity-logs"></a>Registos de atividade

O **log de atividades** coleta informações de Azure Resource Manager. Exemplos de informações encontradas no log de atividades incluem criar ou excluir um serviço, atualizar um grupo de recursos, verificar a disponibilidade do nome ou obter uma chave de acesso de serviço para lidar com uma solicitação. 

Você pode acessar o **log de atividades** no painel de navegação esquerdo ou em notificações na barra de comandos da janela superior ou na página **diagnosticar e solucionar problemas** .

Para tarefas em serviço como criar um índice ou excluir uma fonte de dados, você verá notificações genéricas como "obter chave de administração" para cada solicitação, mas não a ação específica em si. Para esse nível de informação, você deve habilitar uma solução de monitoramento de complemento.

## <a name="add-on-monitoring-solutions"></a>Soluções de monitoramento de complemento

Azure Search não armazena nenhum dado além dos objetos que ele gerencia, o que significa que os dados de log têm que ser armazenados externamente. Você pode configurar qualquer um dos recursos abaixo se desejar manter os dados de log. 

A tabela a seguir compara as opções de armazenamento de logs e a adição de monitoramento detalhado de operações de serviço e de cargas de trabalho de consulta por meio de Application Insights.

| Resource | Utilizado para |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | Eventos registrados em log e métricas de consulta, com base nos esquemas abaixo, correlacionados com eventos de usuário em seu aplicativo. Essa é a única solução que leva as ações do usuário ou sinais em conta, mapeando eventos da pesquisa iniciada pelo usuário, em oposição a solicitações de filtro enviadas pelo código do aplicativo. Para usar essa abordagem, copie e cole o código de instrumentação em seus arquivos de origem para rotear informações de solicitação para Application Insights. Para obter mais informações, consulte [análise de tráfego de pesquisa](search-traffic-analytics.md). |
| [Registos do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Eventos registrados e métricas de consulta, com base nos esquemas abaixo. Os eventos são registrados em um espaço de trabalho Log Analytics. Você pode executar consultas em um espaço de trabalho para retornar informações detalhadas do log. Para obter mais informações, consulte Introdução [aos logs de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Armazenamento de blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Eventos registrados e métricas de consulta, com base nos esquemas abaixo. Os eventos são registrados em um contêiner de BLOB e armazenados em arquivos JSON. Use um editor de JSON para exibir o conteúdo do arquivo.|
| [Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/) | Eventos registrados em log e métricas de consulta, com base nos esquemas documentados neste artigo. Escolha esta opção como um serviço de coleta de dados alternativo para logs muito grandes. |

Os logs de Azure Monitor e o armazenamento de BLOBs estão disponíveis como um serviço gratuito para que você possa experimentá-lo gratuitamente pelo tempo de vida da sua assinatura do Azure. Application Insights é livre para se inscrever e usar desde que o tamanho dos dados do aplicativo esteja sob determinados limites (consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para obter detalhes).

A próxima seção orienta você pelas etapas de habilitar e usar o armazenamento de BLOBs do Azure para coletar e acessar dados de log criados por Azure Search operações.

## <a name="enable-logging"></a>Ativar registo

O registro em log para cargas de trabalho de indexação e consulta é desativado por padrão e depende de soluções complementares para a infraestrutura de log e o armazenamento externo de longo prazo. Por si só, os únicos dados persistentes em Azure Search são os objetos que ele cria e gerencia, de modo que os logs devem ser armazenados em outro lugar.

Nesta seção, você aprenderá a usar o armazenamento de BLOBs para armazenar eventos registrados e dados de métricas.

1. [Crie uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) se você ainda não tiver uma. Você pode colocá-lo no mesmo grupo de recursos que Azure Search para simplificar a limpeza mais tarde se quiser excluir todos os recursos usados neste exercício.

   Sua conta de armazenamento deve existir na mesma região que Azure Search.

2. Abra a página Visão geral do serviço de pesquisa. No painel de navegação esquerdo, role para baixo até **monitoramento** e clique em **habilitar monitoramento**.

   ![Habilitar monitoramento](./media/search-monitor-usage/enable-monitoring.png "habilitar monitoramento")

3. Escolha os dados que você deseja exportar: Logs, métricas ou ambos. Você pode copiá-lo para uma conta de armazenamento, enviá-lo para um hub de eventos ou exportá-lo para Azure Monitor logs.

   Para o armazenamento de arquivamento no BLOB, somente a conta de armazenamento deve existir. Contêineres e blobs serão criados conforme necessário quando os dados do log forem exportados.

   ![Configurar arquivo morto de armazenamento de blob](./media/search-monitor-usage/configure-blob-storage-archive.png "configurar arquivo morto de armazenamento de BLOBs")

4. Salve o perfil.

5. Teste o log criando ou excluindo objetos (cria eventos de log) e enviando consultas (gera métricas). 

O registro em log é habilitado quando você salva o perfil. Os contêineres são criados somente quando há uma atividade para log ou medida. Quando os dados são copiados para uma conta de armazenamento, os dados são formatados como JSON e colocados em dois contêineres:

* insights-logs-operationlogs: para os registos de tráfego de pesquisa
* as métricas-insights-pt1m: para métricas

**It leva uma hora antes que os contêineres apareçam no armazenamento de BLOBs. Há um blob, por hora, por contêiner.**

Você pode usar [Visual Studio Code](#download-and-open-in-visual-studio-code) ou outro editor de JSON para exibir os arquivos. 

### <a name="example-path"></a>Caminho de exemplo

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Esquema de registo
Os blobs que contêm os logs de tráfego do serviço de pesquisa são estruturados conforme descrito nesta seção. Cada blob tem um objeto raiz chamado **registros** contendo uma matriz de objetos de log. Cada blob contém registros para todas as operações que ocorreram durante a mesma hora.

| Name | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| hora |datetime |"2018-12-07T00:00:43.6872559Z" |TimeStamp da operação |
| resourceId |cadeia |"/ SUBSCRIÇÕES/11111111-1111-1111-1111-111111111111 /<br/>PADRÃO/RESOURCEGROUPS/FORNECEDORES /<br/> MICROSOFT. PESQUISA/SEARCHSERVICES/SEARCHSERVICE" |O ResourceId |
| operationName |cadeia |"Query.Search" |O nome da operação |
| operationVersion |Cadeia de caracteres |"2019-05-06" |A api-version utilizada |
| categoria |cadeia |"OperationLogs" |constante |
| resultType |cadeia |"Êxito" |Valores possíveis: Êxito ou falha |
| resultSignature |int |200 |Código de resultado HTTP |
| durationMS |int |50 |Duração da operação em milissegundos |
| propriedades |objeto |consulte a tabela seguinte |Objeto que contém dados específicos da operação |

**Esquema de propriedades**

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| Descrição |cadeia |"Obter /indexes('content')/docs" |Ponto final da operação |
| Consulta |Cadeia de caracteres |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Os parâmetros de consulta |
| Documentos |int |42 |Número de documentos processados |
| indexName |cadeia |"testindex" |Nome do índice associado à operação |

## <a name="metrics-schema"></a>Esquema de métricas

As métricas são capturadas para solicitações de consulta.

| Name | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| resourceId |cadeia |"/ SUBSCRIÇÕES/11111111-1111-1111-1111-111111111111 /<br/>PADRÃO/RESOURCEGROUPS/FORNECEDORES /<br/>MICROSOFT. PESQUISA/SEARCHSERVICES/SEARCHSERVICE" |o id de recurso |
| MetricName |cadeia |"Latência" |o nome da métrica |
| hora |datetime |"2018-12-07T00:00:43.6872559Z" |timestamp da operação |
| média |int |64 |O valor médio dos exemplos não processados no intervalo de tempo de métrica |
| mínimo |int |37 |O valor mínimo dos exemplos não processados no intervalo de tempo de métrica |
| máximo |int |78 |O valor máximo das amostras não processados no intervalo de tempo de métrica |
| total |int |258 |O valor total dos exemplos não processados no intervalo de tempo de métrica |
| count |int |4 |O número de amostras não processados, utilizado para gerar a métrica |
| intervalo de agregação |cadeia |"PT1M" |O intervalo de agregação da métrica no ISO 8601 |

Todas as métricas são comunicadas em intervalos de um minuto. Cada medição expõe valores mínimos, máximo e médios por minuto.

Para a métrica de SearchQueriesPerSecond, o valor mais baixo para consultas de pesquisa por segundo que foram registadas durante esse minuto é mínimo. O mesmo se aplica ao valor máximo. Média, é a agregação em minuto completo.
Pense neste cenário, durante um minuto: um segundo alta isto é carregar o máximo para SearchQueriesPerSecond, seguido de 58 segundos da carga média, e, finalmente, um segundo, com apenas uma consulta, que é o mínimo.

Para ThrottledSearchQueriesPercentage, mínimo, máximo, média e total, todos têm o mesmo valor: a percentagem de consultas de pesquisa que eram limitados, do número total de consultas de pesquisa durante um minuto.

## <a name="download-and-open-in-visual-studio-code"></a>Baixar e abrir no Visual Studio Code

Você pode usar qualquer editor de JSON para exibir o arquivo de log. Se você não tiver uma, recomendamos [Visual Studio Code](https://code.visualstudio.com/download).

1. Em portal do Azure, abra sua conta de armazenamento. 

2. No painel de navegação esquerdo, clique em **BLOBs**. Você deve ver **insights-logs-operationlogs** e **insights-métricas-PT1M**. Esses contêineres são criados por Azure Search quando os dados de log são exportados para o armazenamento de BLOBs.

3. Clique na hierarquia de pastas até chegar ao arquivo. JSON.  Use o menu de contexto para baixar o arquivo.

Depois que o arquivo for baixado, abra-o em um editor de JSON para exibir o conteúdo.

## <a name="use-system-apis"></a>Usar APIs do sistema
A API REST do Azure Search e o SDK do .NET fornecem acesso programático às métricas de serviço, informações de índice e indexador e contagens de documentos.

* [Obter estatísticas de serviços](/rest/api/searchservice/get-service-statistics)
* [Obter estatísticas de índice](/rest/api/searchservice/get-index-statistics)
* [Contagem de documentos](/rest/api/searchservice/count-documents)
* [Obter estado do indexador](/rest/api/searchservice/get-indexer-status)

Para ativar a utilizar o PowerShell ou a CLI do Azure, consulte a documentação [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Passos seguintes

[Gerencie seu serviço de pesquisa no Microsoft Azure](search-manage.md) para obter mais informações sobre a administração e o [desempenho e a otimização](search-performance-optimization.md) do serviço para diretrizes de ajuste.