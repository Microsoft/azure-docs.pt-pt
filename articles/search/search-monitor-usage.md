---
title: Monitorizar operações e atividades
titleSuffix: Azure Cognitive Search
description: Ativar a exploração madeireira, obter métricas de atividade de consulta, uso de recursos e outros dados do sistema a partir de um serviço de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462331"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorizar operações e atividades de Pesquisa Cognitiva Azure

Este artigo introduz a monitorização ao nível do serviço (recurso), ao nível da carga de trabalho (consultas e indexação), e sugere um quadro para monitorizar o acesso dos utilizadores.

Em todo o espectro, você usará uma combinação de infraestruturas incorporadas e serviços fundacionais como o Azure Monitor, bem como apis de serviço que devolvem estatísticas, contagens e estado. Compreender o alcance das capacidades pode ajudá-lo a construir um ciclo de feedback para que possa resolver os problemas à medida que eles emergem.

## <a name="use-azure-monitor"></a>Utilizar o Azure Monitor

Muitos serviços, incluindo a Pesquisa Cognitiva Azure, alavancam o [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/) para alertas, métricas e dados de diagnóstico de registo. Para a Pesquisa Cognitiva Azure, a infraestrutura de monitorização incorporada é utilizada principalmente para monitorização ao nível de recursos (saúde do serviço) e monitorização de [consultas.](search-monitor-queries.md)

A imagem que se segue ajuda a localizar as funcionalidades do Monitor Azure no portal.

+ O separador de **monitorização,** localizado na página geral principal, mostra métricas-chave num ápice.
+ **O registo de atividades**, logo abaixo da visão geral, reporta sobre ações ao nível dos recursos: notificações de pedido de saúde de serviço e api chave de api.
+ **A monitorização,** mais abaixo na lista, fornece alertas configuráveis, métricas e registos de diagnóstico. Crie isto quando precisar. Uma vez recolhidos e armazenados os dados, pode consultar ou visualizar a informação para obter informações.

![Integração do Monitor Azure num serviço de pesquisa](./media/search-monitor-usage/azure-monitor-search.png
 "Integração do Monitor Azure num serviço de pesquisa")

### <a name="precision-of-reported-numbers"></a>Precisão dos números reportados

As páginas do portal são refrescadas a cada poucos minutos. Como tal, os números relatados no portal são aproximados, destinados a dar-lhe uma noção geral de quão bem o seu sistema está a servir pedidos. As métricas reais, tais como consultas por segundo (QPS) podem ser superiores ou inferiores ao número mostrado na página.

## <a name="activity-logs-and-service-health"></a>Registos de atividade e saúde de serviço

O [**registo de Atividaderecolhe**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) informações do Gestor de Recursos do Azure e reporta sobre alterações à saúde do serviço. Pode monitorizar o registo de atividade para condições críticas, erradas e de alerta relacionadas com a saúde do serviço.

Para tarefas em serviço - como consultas, indexação ou criação de objetos - você verá notificações informativas genéricas como *Obter Chave de Administrador* e Obter chaves de *consulta* para cada pedido, mas não a ação específica em si. Para obter informações sobre este grão, deve configurar o registo de diagnóstico.

Pode aceder ao **registo de atividade** a partir do painel de navegação à esquerda, ou a partir de Notificações na barra de comando da janela superior, ou da página diagnosticar e resolver **problemas.**

## <a name="monitor-storage"></a>Armazenamento de monitores

Páginas com separados incorporadas no relatório da página overview sobre o uso de recursos. Esta informação fica disponível assim que começar a utilizar o serviço, sem necessidade de configuração, e a página é atualizada a cada poucos minutos. 

Se estiver a finalizar decisões sobre [qual o nível a utilizar para cargas](search-sku-tier.md)de trabalho de produção , ou se ajustar [o número de réplicas e divisórias ativas,](search-capacity-planning.md)estas métricas podem ajudá-lo com essas decisões mostrando-lhe a rapidez com que os recursos são consumidos e quão bem a configuração atual lida com a carga existente.

Não estão atualmente disponíveis alertas relacionados com o armazenamento; o consumo de armazenamento não é agregado ou registado na tabela **AzureMetrics** no Monitor Azure. Você precisaria [de construir uma solução personalizada](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) que emita notificações relacionadas com recursos, onde o seu código verifica o tamanho do armazenamento e lida com a resposta. Para obter mais informações sobre métricas de armazenamento, consulte [Obter Estatísticas de Serviço](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

Para monitorização visual no portal, o separador **Utilização** mostra a disponibilidade de recursos em relação aos [limites](search-limits-quotas-capacity.md) atuais impostos pelo nível de serviço. 

A seguinte ilustração é para o serviço gratuito, que está limitado a 3 objetos de cada tipo e 50 MB de armazenamento. Um serviço Básico ou Standard tem limites mais elevados, e se aumentar as contagens de partição, o armazenamento máximo sobe proporcionalmente.

![Estado de utilização relativo aos limites de nível](./media/search-monitor-usage/usage-tab.png
 "Estado de utilização relativo aos limites de nível")

## <a name="monitor-workloads"></a>Monitorizar cargas de trabalho

Os eventos registados incluem os relacionados com indexação e consultas. A tabela **AzureDiagnostics** no Log Analytics recolhe dados operacionais relacionados com consultas e indexação.

A maioria dos dados registados é para operações apenas de leitura. Para outras operações de eliminação de atualizações não capturadas no registo, pode consultar o serviço de pesquisa para obter informações do sistema.

| OperationName | Descrição |
|---------------|-------------|
| Estatísticas de Serviços | Esta operação é uma chamada de rotina para obter estatísticas de [serviços](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), quer seja chamada direta ou implicitamente para preencher uma página geral do portal quando é carregada ou refrescada. |
| Consulta.Pesquisa |  Pedidos de consulta contra um índice Ver [Monitor consultas](search-monitor-queries.md) para informações sobre consultas registadas.|
| Indexação.Índice  | Esta operação é uma chamada para [adicionar, atualizar ou eliminar documentos.](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) |
| índices. Protótipo | Este é um índice criado pelo assistente de dados de importação. |
| Indexers.Create | Crie um indexador explicitamente ou implicitamente através do assistente de dados de importação. |
| Indexers.Get | Devolve o nome de um indexador sempre que o indexante é executado. |
| Indexers.Status | Devolve o estado de um indexante sempre que o indexante é executado. |
| DataSources.Get | Devolve o nome da fonte de dados sempre que um indexante é executado.|
| Índices.Get | Devolve o nome de um índice sempre que um indexante é executado. |

### <a name="kusto-queries-about-workloads"></a>Perguntas de Kusto sobre cargas de trabalho

Se habilitar a exploração madeireira, pode consultar a **AzureDiagnostics** para obter uma lista de operações que foram operadas ao seu serviço e quando. Também pode correlacionar a atividade para investigar alterações no desempenho.

#### <a name="example-list-operations"></a>Exemplo: Operações de lista 

Devolva uma lista de operações e uma contagem de cada uma.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Exemplo: Operações de Correlação

Correlacionar o pedido de consulta com as operações de indexação, e tornar os pontos de dados através de um gráfico de tempo para ver as operações coincidirem.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Use APIs de pesquisa

Tanto a API de Pesquisa Cognitiva Azure como o .NET SDK fornecem acesso programático às métricas de serviço, informações indexadas e indexantes, e contagens de documentos.

+ [Estatísticas de Serviços GET](/rest/api/searchservice/get-service-statistics)
+ [Estatísticas do Índice GET](/rest/api/searchservice/get-index-statistics)
+ [Contagens de documentos get](/rest/api/searchservice/count-documents)
+ [Estatuto do Indexante GET](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Monitorizar o acesso ao utilizador

Como os índices de pesquisa são um componente de uma aplicação de cliente maior, não existe uma metodologia incorporada para controlar ou monitorizar o acesso por utilizador a um índice. Presume-se que os pedidos provêm de um pedido de cliente, para pedidos de administração ou consulta. As operações de leitura de administradores incluem a criação, atualização, a eliminar objetos em todo o serviço. As operações de leitura são consultas contra a recolha de documentos, orientadas para um único índice. 

Como tal, o que você verá nos registos de atividade são referências a chamadas usando chaves de administração ou chaves de consulta. A chave adequada está incluída nos pedidos originários do código do cliente. O serviço não está equipado para lidar com fichas de identidade ou personificação.

Quando existem requisitos de negócio para autorização por utilizador, a recomendação é integração com o Diretório Ativo Azure. Pode utilizar identidades $filter e utilizadores para [aparar resultados](search-security-trimming-for-azure-search-with-aad.md) de pesquisa de documentos que um utilizador não deve ver. 

Não há forma de registar esta informação separadamente da corda de consulta que inclui o parâmetro $filter. Consulte [as consultas do Monitor](search-monitor-queries.md) para obter detalhes sobre as cordas de consulta de reporte.

## <a name="next-steps"></a>Passos seguintes

A fluência com o Monitor Azure é essencial para a supervisão de qualquer serviço Azure, incluindo recursos como a Pesquisa Cognitiva Azure. Se não estiver familiarizado com o Monitor Azure, tenha tempo para rever artigos relacionados com recursos. Além dos tutoriais, o seguinte artigo é um bom lugar para começar.

> [!div class="nextstepaction"]
> [Monitorização de recursos Azure com o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
