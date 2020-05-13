---
title: Monitor Azure para Azure Cache para Redis (pré-visualização)/ Microsoft Docs
description: Este artigo descreve a funcionalidade Azure Monitor for Redis Cache que fornece ao Azure Cache para os proprietários da Redis uma compreensão rápida dos problemas de desempenho e utilização.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210978"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Explore o Monitor Azure para Azure Cache para Redis (pré-visualização)

O Azure Monitor para Azure Cache for Redis (pré-visualização) proporciona uma visão do desempenho global, falhas, capacidade e saúde operacional de todos os seus recursos Azure Cache para Redis numa experiência interativa unificada. Este artigo irá ajudá-lo a entender os benefícios desta nova experiência de monitorização, e como pode modificar e adaptar a experiência de acordo com as necessidades únicas da sua organização.

## <a name="introduction"></a>Introdução

Antes de mergulhar na experiência, deve entender como apresenta e visualiza a informação.

Entrega:

* **Na perspetiva** de escala do seu Azure Cache para recursos Redis em todas as suas subscrições num único local, com a capacidade de se adequar seletivamente apenas às subscrições e recursos que está interessado em avaliar.

* **Faça análises** de um determinado azure cache para o recurso Redis para ajudar a diagnosticar problemas ou realizar análises detalhadas por categoria - utilização, falhas, capacidade e operações. A seleção de qualquer uma dessas opções proporciona uma visão aprofundada do relevante.  

* **Personalizável** - Esta experiência é construída em cima de modelos de livro do Azure Monitor, permitindo-lhe alterar quais as métricas apresentadas, modificar ou definir limiares que se alinham com os seus limites e, em seguida, economizar num livro personalizado. Os gráficos nos livros de trabalho podem então ser fixados nos tabliers Azure.  

Esta funcionalidade não requer que ative ou configure nada, estes Azure Cache for Redis são recolhidos por padrão.

>[!NOTE]
>Não existe qualquer custo para aceder a esta funcionalidade e só será cobrado para as funcionalidades essenciais do Monitor Azure que configura ou ativa, conforme descrito na página de detalhes de preços do [Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Ver métricas de utilização e desempenho para Azure Cache para Redis

Para ver a utilização e desempenho das suas contas de armazenamento em todas as suas subscrições, execute os seguintes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure monitor **e** selecione **Monitor**.

    ![Caixa de pesquisa com a palavra "Monitor" e uma queda que diz Serviços "Monitor" com uma imagem de estilo velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecione **Azure Cache para Redis (pré-visualização)**.

Se esta opção não estiver presente clique **mais** e selecione **Azure Cache para Redis**.

### <a name="overview"></a>Descrição geral

Em **resumo,** a tabela apresenta o Azure Cache interativo para as métricas Redis. Pode filtrar os resultados com base nas opções selecionadas a partir das seguintes listas de drop-down:

* **Assinaturas** - apenas estão listadas subscrições que possuam um Azure Cache para o recurso Redis.  

* **Azure Cache for Redis** - Pode selecionar tudo, um subconjunto ou um único Azure Cache para o recurso Redis.

* **Intervalo** de tempo - por padrão, exibe as últimas 4 horas de informação com base nas seleções correspondentes feitas.

O telha de balcão sob as listas de drop-down rolls-up o número total de azure cache para recursos Redis estão nas subscrições selecionadas. Há códigos de cores condicional ou mapas de calor para colunas no livro que reportam métricas de transação. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

A seleção de uma seta de queda ao lado de um dos recursos Azure Cache para os recursos Redis revelará uma desagregação das métricas de desempenho a nível de recursos individuais:

![Screenshot da experiência de visão geral](./media/redis-cache-insights-overview/overview.png)

A seleção do Azure Cache para o nome de recurso Redis realçado em azul irá levá-lo à **visão** geral padrão para a conta associada. Mostra, `Used Memory` `Used Memory Percentage` , , , `Server Load` `Server Load Timeline` . `CPU` `Connected Clients` `Cache Misses` . `Errors (Max)`

### <a name="operations"></a>Operações

Selecione **Operações** no topo da página e abre a parte de **Operações** do modelo do livro. Mostra, `Total Operations` `Total Operations Timeline` , . `Operations Per Second` `Gets` `Sets` .

![Screenshot da experiência de visão geral](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Utilização

Selecione **Utilização** na parte superior da página e abre a parte de **utilização** do modelo do livro. Mostra, `Cache Read` `Cache Read Timeline` , . `CacheWrite` `CacheHits` `Cache Misses` .

![Screenshot da experiência de visão geral](./media/redis-cache-insights-overview/usage.png)

Selecione **Falhas** no topo da página e abre a parte de **Falhas** do modelo do livro. Mostra, `Total Errors` `Failover/Errors` , , , `UnresponsiveClient/Errors` `RDB/Errors` . `AOF/Errors` `Export/Errors` `Dataloss/Errors` . `Import/Errors`

![Screenshot de falhas com avaria por tipo de pedido HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definições métricas

Para obter uma lista completa das definições métricas que compõem estes livros, consulte as métricas disponíveis e os intervalos de [reporte.](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals)

## <a name="pin-export-and-expand"></a>Pin, exportar e expandir

Pode fixar qualquer uma das secções métricas num [Painel De Instrumentos Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) selecionando o ícone do pino na parte superior direita da secção.

![Pino de secção métrica para exemplo do painel de instrumentos](./media/cosmosdb-insights-overview/pin.png)

Para exportar os seus dados para o formato Excel, selecione o ícone de seta para baixo para a esquerda do ícone do pino.

![Ícone do livro de exportação](./media/cosmosdb-insights-overview/export.png)

Para expandir ou colapsar todas as vistas de drop-down no livro, selecione o ícone de expansão à esquerda do ícone de exportação:

![Expandir ícone de livro](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Personalize o Monitor Azure para Azure Cache para Redis (pré-visualização)

Uma vez que esta experiência é construída em cima dos modelos de livro do Azure Monitor, você tem a capacidade de **personalizar**  >  **editar** e **guardar** uma cópia da sua versão modificada em um livro personalizado. 

![Personalizar bar](./media/cosmosdb-insights-overview/customize.png)

Os livros de reprodução são guardados dentro de um grupo de recursos, quer na secção **My Reports** que seja privada para si ou na secção **Relatórios Partilhados** que esteja acessível a todos os que tenham acesso ao grupo de recursos. Depois de guardar o livro personalizado, precisa de ir à galeria do livro para o lançar.

![Lançar galeria de livro saindo do bar de comando](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Passos seguintes

* Configure [alertas métricos](../platform/alerts-metric.md) e notificações de [saúde](../../service-health/alerts-activity-log-service-notifications.md) de serviço para configurar alertaautomatizado para ajudar na deteção de problemas.

* Saiba os cenários que os livros de trabalho são projetados para apoiar, como autor de novos relatórios existentes e mais através da revisão de [relatórios interativos Create com livros de trabalho do Monitor Azure.](../app/usage-workbooks.md)
