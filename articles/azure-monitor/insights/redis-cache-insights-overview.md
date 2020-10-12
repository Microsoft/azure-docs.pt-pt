---
title: Monitor Azure para Azure Cache para Redis Microsoft Docs
description: Este artigo descreve o recurso Azure Monitor for Azure Redis Cache, que proporciona aos proprietários de cache uma rápida compreensão dos problemas de desempenho e utilização.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/10/2020
ms.openlocfilehash: e7b8a8e131cf7e0aae051f42043abf0640c927e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90887008"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Explore o Monitor Azure para Azure Cache para Redis

Para todos os seus recursos Azure Cache para recursos Redis, o Azure Monitor for Azure Cache for Redis fornece uma visão unificada e interativa de:

- Desempenho geral
- Falhas
- Capacidade
- Saúde operacional

Este artigo ajuda-o a compreender os benefícios desta nova experiência de monitorização. Também mostra como modificar e adaptar a experiência para se adaptar às necessidades únicas da sua organização.

## <a name="introduction"></a>Introdução

Antes de iniciar a experiência, deve entender como o Azure Monitor for Azure Cache para Redis apresenta informações visualmente.

Entrega:

- **Na perspetiva** de escala do seu Azure Cache para recursos Redis em um único local em todas as suas subscrições. Pode selecionar apenas as subscrições e recursos que pretende avaliar.

- **Análise de perfuração** de um cache Azure particular para recurso Redis. Pode diagnosticar problemas e ver análise detalhada da utilização, falhas, capacidade e operações. Selecione qualquer uma destas categorias para ver uma visão aprofundada das informações relevantes.  

- **Personalização** desta experiência, que é construída em cima de modelos de livro do Azure Monitor. A experiência permite alterar as métricas apresentadas e modificar ou definir limiares que se alinham com os seus limites. Pode guardar as alterações num livro personalizado e, em seguida, pin gráficos de livros de trabalho para painéis Azure.

Esta funcionalidade não requer que você ative ou configure nada. A azure Cache para informações redis é recolhida por padrão.

>[!NOTE]
>Não há qualquer custo para aceder a esta funcionalidade. É cobrado apenas para as funcionalidades essenciais do Azure Monitor que configura ou ativa, conforme descrito na página de detalhes de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Ver métricas de utilização e desempenho para Azure Cache para Redis

Para visualizar a utilização e desempenho das suas contas de armazenamento em todas as suas subscrições, faça os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Procure por **Monitor**e selecione **Monitor**.

    ![Caixa de pesquisa com a palavra "Monitor" e o resultado da pesquisa de Serviços que mostra "Monitor" com um símbolo do velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

1. Selecione **Azure Cache para Redis**. Se esta opção não estiver presente, selecione **Mais**  >  **Cache Azure para Redis**.

### <a name="overview"></a>Descrição geral

Em **visão geral,** a tabela apresenta cache Azure interativo para métricas Redis. Pode filtrar os resultados com base nas opções selecionadas nas seguintes listas de entrega:

- **Subscrições**: Apenas estão listadas as subscrições que tenham uma Cache Azure para recurso Redis.  

- **Cache azul para Redis**: Pode selecionar tudo, um subconjunto ou uma única Cache Azure para recurso Redis.

- **Intervalo de tempo**: Por predefinição, a tabela apresenta as últimas quatro horas de informação com base nas seleções correspondentes.

Há um contra azulejo sob as listas de espera. O azulejo mostra o número total de Cache Azure para recursos Redis nas subscrições selecionadas. Códigos de cores condicional ou mapas de calor para colunas de livros reportam métricas de transação. A cor mais profunda representa o valor mais alto. Cores mais claras representam valores mais baixos.

Selecionar uma seta de lista de recuo ao lado de uma das caches Azure para recursos Redis revela uma desagregação das métricas de desempenho ao nível de recursos individuais.

![Screenshot da experiência de visão geral](./media/redis-cache-insights-overview/overview.png)

Quando selecionar o cache Azure para o nome de recurso Redis realçado em azul, vê a tabela **de visão geral** predefinida para a conta associada. Mostra estas colunas:

- **Memória Utilizada**
- **Percentagem de Memória Utilizada**
- **Carga do Servidor**
- **Linha Cronológica da Carga do Servidor**
- **CPU**
- **Clientes Ligados**
- **Falhas de Acerto na Cache**
- **Erros (Máx.)**

### <a name="operations"></a>Operações

Quando seleciona **Operações** no topo da página, abre-se a tabela **operações** do modelo de livro. Mostra estas colunas:

- **Total de Operações**
- **Linha Cronológica do Total de Operações**
- **Operações por Segundo**
- **Obtenções**
- **Definições**

![Screenshot da experiência de operações](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Utilização

Quando seleciona **Utilização** no topo da página, abre-se a tabela de **utilização** do modelo do livro. Mostra estas colunas:

- **Leitura da Cache**
- **Linha Cronológica de Leitura da Cache**
- **Escrita na Cache**
- **Acertos na Cache**
- **Falhas de Acerto na Cache**

![Screenshot da experiência de utilização](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Falhas

Quando seleciona **Falhas** no topo da página, abre-se a tabela **Falhas** do modelo do livro. Mostra estas colunas:

- **Total de Erros**
- **Falha/Erros**
- **Sem Resposta, Estamoss tão bem tempo/erros**
- **RDB/Erros**
- **AOF/Erros**
- **Exportação/Erros**
- **Perda de dados/Erros**
- **Importação/Erros**

![Screenshot de falhas com uma avaria por tipo de pedido HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definições métricas

Para obter uma lista completa das definições métricas que formam estes livros, consulte o [artigo sobre as métricas disponíveis e intervalos de reporte](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Vista de uma Cache Azure para recurso Redis

Para aceder ao Azure Monitor para Azure Cache para Redis diretamente a partir de um recurso individual:

1. No portal Azure, selecione Azure Cache para Redis.

2. Da lista, escolha um Azure Cache individual para recurso Redis. Na secção de monitorização, escolha Insights.

    ![Screenshot das opções do Menu com as palavras "Insights" destacadas numa caixa vermelha](./media/redis-cache-insights-overview/insights.png)

Estas vistas também são acessíveis selecionando o nome de recurso de um recurso Azure Cache para Redis a partir do livro de trabalho de nível Azure Monitor.

### <a name="resource-level-overview"></a>Visão geral do nível de recursos

No **livro de visão** geral para a Cache Azure Redis, mostra várias métricas de desempenho que lhe dão acesso a:

- Gráficos de desempenho interativos mostrando os detalhes mais essenciais relacionados com Azure Cache para o desempenho de Redis.

- Métricas e telhas de estado que destacam o desempenho de fragmentos, o número total de clientes conectados e a latência geral.

![Screenshot do painel de visão geral exibindo informações sobre o desempenho do CPU, memória usada, clientes conectados, erros, teclas expiradas e teclas despejadas](./media/redis-cache-insights-overview/resource-overview.png)

A seleção de qualquer um dos outros separadores para **Performance** ou **Operações** abre os respetivos livros de trabalho.

### <a name="resource-level-performance"></a>Desempenho ao nível dos recursos

![Screenshot dos gráficos de desempenho do recurso](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Operações ao nível dos recursos

![Screenshot dos gráficos de operações de recursos](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>Pin, exportação e expansão

Para fixar qualquer secção métrica num [painel Azure,](../../azure-portal/azure-portal-dashboards.md)selecione o símbolo do pino na parte superior direita da secção.

![Uma secção métrica com o símbolo do pino realçado](./media/cosmosdb-insights-overview/pin.png)

Para exportar os seus dados para um formato Excel, selecione o símbolo da seta para baixo à esquerda do símbolo do pino.

![Um símbolo de livro de exportação em destaque](./media/cosmosdb-insights-overview/export.png)

Para expandir ou colapsar todas as vistas num livro, selecione o símbolo de expansão à esquerda do símbolo de exportação.

![Um símbolo de livro de expansão em destaque](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Personalize o Monitor Azure para Azure Cache para Redis

Uma vez que esta experiência é construída em cima dos modelos de livros do Azure Monitor, pode **selecionar Customize**  >  **Edit**  >  **Save** para guardar uma cópia da sua versão modificada num livro de trabalho personalizado.

![Uma barra de comando com Customize em destaque](./media/cosmosdb-insights-overview/customize.png)

Os livros de trabalho são guardados dentro de um grupo de recursos na secção **My Reports** ou na secção **Relatórios Partilhados.** **Os meus relatórios** só estão disponíveis para si. **O Shared Reports** está disponível para todos os que tenham acesso ao grupo de recursos.

Depois de guardar um livro personalizado, vá à galeria do livro para abri-lo.

![Um bar de comando com Galeria em destaque](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Resolução de problemas

Para obter orientações sobre resolução de problemas, consulte o artigo dedicado baseado em insights baseados em [livros](troubleshoot-workbooks.md)de trabalho .

## <a name="next-steps"></a>Passos seguintes

* Configure [alertas métricos](../platform/alerts-metric.md) e [notificações de saúde](../../service-health/alerts-activity-log-service-notifications-portal.md) de serviço para configurar alertas automatizados que ajudam na deteção de problemas.

* Conheça os cenários que os livros suportam, como autor ou personalizar relatórios, e mais através da revisão [Criar relatórios interativos com os livros de trabalho do Azure Monitor.](../platform/workbooks-overview.md)
