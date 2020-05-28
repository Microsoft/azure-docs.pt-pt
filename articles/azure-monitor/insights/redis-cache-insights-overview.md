---
title: Monitor Azure para Azure Cache para Redis (pré-visualização)/ Microsoft Docs
description: Este artigo descreve a funcionalidade Azure Monitor para Azure Redis Cache, que proporciona aos proprietários de cache uma compreensão rápida dos problemas de desempenho e utilização.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/21/2020
ms.openlocfilehash: d892c58413803ff65fbd8ca8b25a61bc5ffeb26f
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847366"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Explore o Monitor Azure para Azure Cache para Redis (pré-visualização)

Para todos os seus recursos Azure Cache para Redis, o Azure Monitor para Azure Cache for Redis (pré-visualização) proporciona uma visão unificada e interativa de:

- Desempenho geral
- Falhas
- Capacidade
- Saúde operacional

Este artigo ajuda-o a compreender os benefícios desta nova experiência de monitorização. Também mostra como modificar e adaptar a experiência de acordo com as necessidades únicas da sua organização.

## <a name="introduction"></a>Introdução

Antes de iniciar a experiência, deve entender como o Azure Monitor para Azure Cache for Redis apresenta visualmente informações.

Entrega:

- **Na perspetiva** de escala do seu Azure Cache para recursos Redis num único local em todas as suas subscrições. Pode selecionar apenas as subscrições e recursos que pretende avaliar.

- **Análise de perfuração** de um cache azure particular para recurso Redis. Pode diagnosticar problemas e ver uma análise detalhada da utilização, falhas, capacidade e operações. Selecione qualquer uma destas categorias para ver uma visão aprofundada das informações relevantes.  

- **Personalização** desta experiência, que é construída no topo dos modelos do livro Azure Monitor. A experiência permite alterar as métricas apresentadas e modificar ou definir limiares que se alinham com os seus limites. Pode guardar as alterações num livro personalizado e, em seguida, fixar gráficos de livro para os dashboards Azure.

Esta funcionalidade não requer que ative ou configure nada. A informação azure Cache para redis é recolhida por padrão.

>[!NOTE]
>Não há nenhuma taxa para aceder a esta funcionalidade. É cobrado apenas para as funcionalidades essenciais do Monitor Azure que configura ou ativa, conforme descrito na página de detalhes de preços do [Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Ver métricas de utilização e desempenho para Azure Cache para Redis

Para ver a utilização e desempenho das suas contas de armazenamento em todas as suas subscrições, faça os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Procure o **Monitor**e selecione **Monitor**.

    ![Caixa de pesquisa com a palavra "Monitor" e o resultado de pesquisa de Serviços que mostra "Monitor" com um símbolo de velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

1. Selecione **Azure Cache para Redis (pré-visualização)**. Se esta opção não estiver presente, selecione **Mais**  >  **Cache Azure para Redis**.

### <a name="overview"></a>Descrição geral

Em **resumo,** a tabela apresenta o Azure Cache interativo para as métricas Redis. Pode filtrar os resultados com base nas opções selecionadas a partir das seguintes listas de drop-down:

- **Assinaturas**: Apenas estão listadas subscrições que possuam um Azure Cache para o recurso Redis.  

- **Cache Azure para Redis**: Pode selecionar tudo, um subconjunto ou um único Azure Cache para o recurso Redis.

- **Intervalo**de tempo : Por defeito, a tabela apresenta as últimas quatro horas de informação com base nas seleções correspondentes.

Há um contra-lore debaixo das listas de entrega. O azulejo mostra o número total de azure cache para recursos Redis nas subscrições selecionadas. Códigos de cores condicional ou mapas de calor para colunas de livros reportam métricas de transação. A cor mais profunda representa o maior valor. As cores mais claras representam valores mais baixos.

A seleção de uma seta de lista de lançamento ao lado de um dos recursos Azure Cache para os recursos Redis revela uma desagregação das métricas de desempenho a nível de recursos individuais.

![Screenshot da experiência de visão geral](./media/redis-cache-insights-overview/overview.png)

Ao selecionar o nome de recurso Azure Cache para Redis realçado em azul, consulte a tabela de **visão geral** padrão para a conta associada. Mostra estas colunas:

- **Memória Utilizada**
- **Percentagem de Memória Utilizada**
- **Carga do Servidor**
- **Linha Cronológica da Carga do Servidor**
- **CPU**
- **Clientes Ligados**
- **Falhas de Acerto na Cache**
- **Erros (Máx.)**

### <a name="operations"></a>Operações

Quando selecionar **Operações** no topo da página, abre-se a tabela **de Operações** do modelo do livro. Mostra estas colunas:

- **Total de Operações**
- **Linha Cronológica do Total de Operações**
- **Operações por Segundo**
- **Obtenções**
- **Definições**

![Screenshot da experiência de operações](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Utilização

Quando selecionar **o Uso** no topo da página, abre-se a tabela de **utilização** do modelo do livro. Mostra estas colunas:

- **Leitura da Cache**
- **Linha Cronológica de Leitura da Cache**
- **Escrita na Cache**
- **Acertos na Cache**
- **Falhas de Acerto na Cache**

![Screenshot da experiência de utilização](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Falhas

Ao selecionar **Falhas** no topo da página, abre-se a tabela **Falhas** do modelo do livro. Mostra estas colunas:

- **Total de Erros**
- **Falha/Erros**
- **Cliente/Erros Sem Resposta**
- **RDB/Erros**
- **AOF/Erros**
- **Exportação/Erros**
- **Perda de dados/Erros**
- **Importação/Erros**

![Screenshot de falhas com uma avaria por tipo de pedido HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definições métricas

Para obter uma lista completa das definições métricas que formam estes livros, consulte o [artigo sobre métricas disponíveis e intervalos](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals)de reporte .

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Vista de um Azure Cache para recurso Redis

Para aceder ao Azure Monitor para Azure Cache for Redis diretamente a partir de um recurso individual:

1. No portal Azure, selecione Azure Cache para Redis.

2. Na lista, escolha um Azure Cache individual para o recurso Redis. Na secção de monitorização, escolha Insights (pré-visualização).

    ![Screenshot das opções do Menu com as palavras "Insights(pré-visualização)" realçadas numa caixa vermelha](./media/redis-cache-insights-overview/insights.png)

Estas vistas também são acessíveis selecionando o nome de recurso de um azure cache para recurso Redis do livro de nível Azure Monitor.

### <a name="resource-level-overview"></a>Visão geral do nível dos recursos

No livro **overview** para o Azure Redis Cache, mostra várias métricas de desempenho que lhe dão acesso a:

- Gráficos de desempenho interativos mostrando os detalhes mais essenciais relacionados com O Cache Azure para o desempenho redis.

- Métricas e azulejos de estado destacam o desempenho do fragmento, o número total de clientes conectados e a latência geral.

![Screenshot do painel de instrumentos de visão geral que exibe informações sobre o desempenho do CPU, memória usada, clientes conectados, erros, chaves expiradas e chaves despejadas](./media/redis-cache-insights-overview/resource-overview.png)

A seleção de qualquer um dos outros separadores para **Desempenho** ou **Operações** abre os respetivos livros.

### <a name="resource-level-performance"></a>Desempenho ao nível dos recursos

![Screenshot dos gráficos de desempenho do recurso](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Operações ao nível dos recursos

![Screenshot dos gráficos de operações de recursos](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>Pin, exportar e expandir

Para fixar qualquer secção métrica num [painel azure,](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)selecione o símbolo do pino na parte superior direita da secção.

![Uma secção métrica com o símbolo do pino realçado](./media/cosmosdb-insights-overview/pin.png)

Para exportar os seus dados para um formato Excel, selecione o símbolo da seta para baixo para a esquerda do símbolo do pino.

![Um símbolo de livro de exportação destacado](./media/cosmosdb-insights-overview/export.png)

Para expandir ou colapsar todas as vistas num livro, selecione o símbolo de expansão à esquerda do símbolo de exportação.

![Um símbolo de livro expandido destacado](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Personalize o Monitor Azure para Azure Cache para Redis (pré-visualização)

Como esta experiência é construída no topo dos modelos de livro do Azure Monitor, pode selecionar **Customize**  >  **Edit**  >  **Save** para guardar uma cópia da sua versão modificada num livro personalizado.

![Uma barra de comando com Customize destacado](./media/cosmosdb-insights-overview/customize.png)

Os livros de reprodução são guardados dentro de um grupo de recursos na secção **My Reports** ou na secção **Relatórios Partilhados.** **Os meus relatórios** só estão disponíveis para si. **Os Relatórios Partilhados** estão disponíveis para todos os que têm acesso ao grupo de recursos.

Depois de guardar um livro personalizado, vá à galeria do livro para abri-lo.

![Um bar de comando com Galeria em destaque](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Próximos passos

* Configure [alertas métricos](../platform/alerts-metric.md) e notificações de [saúde](../../service-health/alerts-activity-log-service-notifications.md) de serviço para configurar alertas automatizados que ajudam na deteção de problemas.

* Conheça os cenários que os livros de trabalho suportam, como autor ou personalizar relatórios, e muito mais através da revisão de [relatórios interativos Create com livros de trabalho do Azure Monitor.](../platform/workbooks-overview.md)
