---
title: Visualização de dados do Monitor Azure [ Monitor De visualização] Microsoft Docs
description: Fornece um resumo dos métodos disponíveis para visualizar dados métricos e de registo armazenados no Monitor Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/24/2018
ms.openlocfilehash: 7bace6ff6eb7dd1c02e4907e338fc0be9d66471c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372287"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualização de dados do Monitor Azure
Este artigo fornece um resumo dos métodos disponíveis para visualizar os dados de registo e métricas armazenados no Monitor Azure.

Visualizações como gráficos e gráficos podem ajudá-lo a analisar os seus dados de monitorização para aprofundar problemas e identificar padrões. Dependendo da ferramenta que utiliza, poderá também ter a opção de partilhar visualizações com outros utilizadores dentro e fora da sua organização.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-dashboards"></a>Tabliers Azure
[Os dashboards Azure](../azure-portal/azure-portal-dashboards.md) são a principal tecnologia de dashboarding para o Azure. Eles são particularmente úteis para fornecer um único painel de vidro sobre a sua infraestrutura e serviços Azure, permitindo-lhe identificar rapidamente questões importantes.

![Dashboard](media/visualizations/dashboard.png)

### <a name="advantages"></a>Vantagens
- Integração profunda em Azure. As visualizações podem ser fixadas em dashboards de várias páginas Azure, incluindo Metrics Explorer, Log Analytics e Application Insights.
- Suporta métricas e registos.
- Combine dados de várias fontes, incluindo saída de [métricas exploradoras,](platform/metrics-charts.md)consultas [de](app/app-map.md) [registo,](log-query/log-query-overview.md)mapas e disponibilidade em Insights de Aplicação.
- Opção para dashboards pessoais ou partilhados. Integrado com autenticação baseada em funções Azure [(RBAC)](../role-based-access-control/overview.md).
- Atualização automática. A atualização das métricas depende do intervalo de tempo com um mínimo de cinco minutos. Os registos atualizam-se a cada hora, com uma opção de atualização manual a pedido clicando no ícone "refresh" numa determinada visualização, ou refrescando o painel completo.
- Painéis de métricas parametrizadas com carimbo de tempo e parâmetros personalizados.
- Opções de layout flexíveis.
- Modo de tela completa.


### <a name="limitations"></a>Limitações
- Controlo limitado sobre visualizações de registo sem suporte para tabelas de dados. O número total de séries de dados está limitado a 10 com outras séries de dados agruparadas sob _outro_ balde.
- Sem suporte de parâmetros personalizados para gráficos de registo.
- Os gráficos de registo estão limitados a 30 dias.
- Os gráficos de registo só podem ser fixados em dashboards partilhados.
- Sem interatividade com dados do painel de instrumentos.
- Exercício contextual limitado.

## <a name="azure-monitor-views"></a>Pontos de vista do Monitor Azure
[As vistas no Monitor Azure](platform/view-designer.md) permitem-lhe criar visualizações personalizadas com dados de registo. São utilizados através da [monitorização de soluções](insights/solutions.md) para apresentar os dados que recolhem.

![Ver](media/visualizations/view.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas para dados de registo.
- Exportação e importância para transferi-los para outros grupos de recursos e subscrições.
- Integra-se no modelo de gestão do Monitor Azure com espaços de trabalho e soluções de monitorização.
- [Filtros](platform/view-designer-filters.md) para parâmetros personalizados.
- Interativo, suporta perfuração de vários níveis (vista que perfura em outra vista)

### <a name="limitations"></a>Limitações
- Suporta troncos, mas não métricas.
- Sem pontos de vista pessoais. Disponível para todos os utilizadores com acesso ao espaço de trabalho.
- Sem refrescamento automático.
- Opções limitadas de layout.
- Nenhum suporte para consulta em vários espaços de trabalho ou aplicações de Application Insights.
- As consultas são limitadas em tamanho de resposta a 8MB e tempo de execução de consulta de 110 segundos.


## <a name="workbooks"></a>Livros
[Os livros](../azure-monitor/app/usage-workbooks.md) de trabalho são documentos interativos que fornecem informações profundas sobre os seus dados, investigação e colaboração dentro da equipa. Exemplos específicos onde os livros são úteis são guias de resolução de problemas e pós-morte de incidentes.

![Livro](media/visualizations/workbook.png)

### <a name="advantages"></a>Vantagens
- Suporta métricas e registos.
- Suporta parâmetros que permitem relatórios interativos onde a seleção de um elemento numa tabela irá atualizar dinamicamente gráficos e visualizações associados.
- Fluxo documental.
- Opção para livros pessoais ou partilhados.
- Experiência de autor fácil e colaborativa.
- Os modelos suportam a galeria de modelos baseada em GitHub pública.

### <a name="limitations"></a>Limitações
- Sem refrescamento automático.
- Nenhum layout denso como dashboards, que tornam os livros de trabalho menos úteis como um único painel de vidro. Destinado mais para fornecer insights mais profundos.


## <a name="power-bi"></a>Power BI
[O Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é particularmente útil para a criação de dashboards e relatórios centrados no negócio, bem como relatórios que analisam as tendências de KPI a longo prazo. Pode [importar os resultados de uma consulta de log](platform/powerbi.md) num conjunto de dados power BI para que possa tirar partido das suas funcionalidades, tais como combinar dados de diferentes fontes e partilhar relatórios na web e dispositivos móveis.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas.
- Interatividade extensiva, incluindo zoom-in e cross-filtering.
- Fácil de partilhar em toda a sua organização.
- Integração com outros dados de múltiplas fontes de dados.
- Melhor desempenho com resultados emcache num cubo.


### <a name="limitations"></a>Limitações
- Suporta troncos, mas não métricas.
- Sem integração Azure. Não é possível gerir dashboards e modelos através do Azure Resource Manager.
- Os resultados da consulta precisam de ser importados para o modelo Power BI para configurar. Limitação no tamanho do resultado e atualização.
- Atualização limitada de dados de oito vezes por dia.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) é uma plataforma aberta que se destaca em dashboards operacionais. É particularmente útil para detetar e isolar e triagem de incidentes operacionais. Pode adicionar o plugin de origem de dados do [Grafana Azure Monitor](platform/grafana-plugin.md) à sua subscrição Azure para que este visualize os dados das métricas do Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas.
- Rico ecossistema de fontes de dados.
- Interatividade de dados, incluindo zoom dentro
- Suporta parâmetros.

### <a name="limitations"></a>Limitações
- Sem integração Azure. Não é possível gerir dashboards e modelos através do Azure Resource Manager.
- Custo para suportar infraestrutura adicional grafana ou custo adicional para Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Construa a sua própria aplicação personalizada
Pode aceder a dados em registo e dados métricos no Monitor Azure através da sua API utilizando qualquer cliente REST, o que lhe permite construir os seus próprios websites e aplicações personalizados.

### <a name="advantages"></a>Vantagens
- Flexibilidade total em UI, visualização, interatividade e características.
- Combine métricas e dados de registo com outras fontes de dados.

### <a name="disadvantages"></a>Desvantagens
- Esforço significativo de engenharia necessário.


## <a name="next-steps"></a>Passos seguintes
- Conheça os [dados recolhidos pelo Azure Monitor.](platform/data-platform.md)
- Saiba mais sobre [os dashboards Azure.](../azure-portal/azure-portal-dashboards.md)
- Saiba mais sobre [as vistas no Monitor Azure.](platform/view-designer.md)
- Saiba mais sobre [livros de trabalho.](../azure-monitor/app/usage-workbooks.md)
- Saiba mais sobre os dados de [registo de importação no Power BI](../azure-monitor/platform/powerbi.md).
- Conheça o plugin de fonte de fonte de dados do [Grafana Azure Monitor](../azure-monitor/platform/grafana-plugin.md).

