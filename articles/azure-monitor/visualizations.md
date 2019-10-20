---
title: Visualizando dados de Azure Monitor | Microsoft Docs
description: Fornece um resumo dos métodos disponíveis para visualizar dados de métrica e de log armazenados no Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/24/2018
ms.openlocfilehash: c350cb1f8e7343f6d8e7a416aa0bba71388eaa86
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597842"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualizando dados de Azure Monitor
Este artigo fornece um resumo dos métodos disponíveis para visualizar o log e os dados de métrica armazenados no Azure Monitor.

Visualizações como gráficos e grafos podem ajudá-lo a analisar seus dados de monitoramento para fazer uma busca detalhada sobre problemas e identificar padrões. Dependendo da ferramenta usada, você também pode ter a opção de compartilhar visualizações com outros usuários dentro e fora da sua organização.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-dashboards"></a>Painéis do Azure
Os [painéis do Azure](../azure-portal/azure-portal-dashboards.md) são a principal tecnologia de painel para o Azure. Eles são particularmente úteis para fornecer um único painel de vidro em sua infraestrutura e serviços do Azure, permitindo que você identifique rapidamente problemas importantes.

![Dashboard](media/visualizations/dashboard.png)

### <a name="advantages"></a>Principais
- Integração profunda com o Azure. As visualizações podem ser fixadas em painéis de várias páginas do Azure, incluindo Metrics Explorer, Log Analytics e Application Insights.
- Dá suporte a métricas e logs.
- Combine dados de várias fontes, incluindo a saída de [métricas Explorer](platform/metrics-charts.md), [consultas de log](log-query/log-query-overview.md)e [mapas](app/app-map.md) e disponibilidade no Application insights.
- Opção para painéis pessoais ou compartilhados. Integrado com o [RBAC (autenticação baseada em função](../role-based-access-control/overview.md)do Azure).
- Atualização automática. A atualização de métricas depende do intervalo de tempo com mínimo de cinco minutos. Os logs são atualizados a cada hora, com uma opção de atualização manual sob demanda clicando no ícone "atualizar" em uma determinada visualização ou atualizando o painel completo.
- Os painéis de métricas do parametrizadas com carimbo de data/hora e parâmetros personalizados.
- Opções de layout flexíveis.
- Modo de tela inteira.


### <a name="limitations"></a>Limitações
- Controle limitado sobre visualizações de log sem suporte para tabelas de dados. O número total de séries de dados é limitado a 10 com mais séries de dados agrupadas em _outro_ Bucket.
- Não há suporte para parâmetros personalizados para gráficos de log.
- Os gráficos de log são limitados aos últimos 30 dias.
- Os gráficos de log só podem ser fixados em painéis compartilhados.
- Não há interatividade com os dados do painel.
- Detalhamento contextual limitado.

## <a name="azure-monitor-views"></a>Azure Monitor exibições
As [exibições no Azure monitor](platform/view-designer.md) permitem que você crie visualizações personalizadas com dados de log. Eles são usados pelas [soluções de monitoramento](insights/solutions.md) para apresentar os dados coletados.

![Ver](media/visualizations/view.png)

### <a name="advantages"></a>Principais
- Visualizações avançadas para dados de log.
- Exportar e importar modos de exibição para transferi-los para outros grupos de recursos e assinaturas.
- Integra-se ao modelo de gerenciamento de Azure Monitor com espaços de trabalho e soluções de monitoramento.
- [Filtros](platform/view-designer-filters.md) para parâmetros personalizados.
- Interativo, dá suporte ao Drill-in de vários níveis (exibição que faz a busca em outra exibição)

### <a name="limitations"></a>Limitações
- Dá suporte a logs, mas não a métricas.
- Não há exibições pessoais. Disponível para todos os usuários com acesso ao espaço de trabalho.
- Nenhuma atualização automática.
- Opções de layout limitadas.
- Não há suporte para consultas em vários espaços de trabalho ou Application Insights aplicativos.
- As consultas são limitadas no tamanho da resposta para 8MB e tempo de execução de consulta de 110 segundos.


## <a name="workbooks"></a>Livros
As [pastas de trabalho](../azure-monitor/app/usage-workbooks.md) são documentos interativos que fornecem informações aprofundadas sobre seus dados, investigação e colaboração dentro da equipe. Exemplos específicos em que as pastas de trabalho são úteis são guias de solução de problemas e postmortem de incidentes.

![Pasta](media/visualizations/workbook.png)

### <a name="advantages"></a>Principais
- Dá suporte a métricas e logs.
- Dá suporte a parâmetros que habilitam relatórios interativos em que selecionar um elemento em uma tabela atualizará dinamicamente gráficos e visualizações associados.
- Fluxo do tipo documento.
- Opção para pastas de trabalho pessoais ou compartilhadas.
- Experiência de criação fácil e colaborativa.
- Os modelos dão suporte à galeria de modelos pública com base no GitHub.

### <a name="limitations"></a>Limitações
- Nenhuma atualização automática.
- Não há layout denso como painéis, o que torna as pastas de trabalho menos úteis como um único painel. Pretendeu mais o fornecimento de informações mais aprofundadas.


## <a name="power-bi"></a>Power BI
[Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é particularmente útil para criar relatórios e Dashboards centrados nos negócios, bem como relatórios analisando tendências de KPI de longo prazo. Você pode [importar os resultados de uma consulta de log](platform/powerbi.md) em um conjunto de dados Power bi para poder aproveitar seus recursos, como combinar dados de fontes diferentes e compartilhar relatórios na Web e em dispositivos móveis.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Principais
- Visualizações avançadas.
- Interatividade extensiva, incluindo zoom e filtragem cruzada.
- Fácil de compartilhar em toda a sua organização.
- Integração com outros dados de várias fontes de dados.
- Melhor desempenho com resultados armazenados em cache em um cubo.


### <a name="limitations"></a>Limitações
- Dá suporte a logs, mas não a métricas.
- Não há integração com o Azure. Não é possível gerenciar painéis e modelos por meio de Azure Resource Manager.
- Os resultados da consulta precisam ser importados para o modelo Power BI para configurar. Limitação no tamanho e na atualização do resultado.
- Atualização de dados limitada de oito vezes por dia.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) é uma plataforma aberta que o Excel em painéis operacionais. É particularmente útil para detectar e isolar e despreparar incidentes operacionais. Você pode adicionar o [plug-in de fonte de dados Grafana Azure monitor](platform/grafana-plugin.md) à sua assinatura do Azure para que ele visualize os dados de métrica do Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Principais
- Visualizações avançadas.
- Ecossistema avançado de fontes de fonte.
- Interatividade de dados, incluindo ampliar.
- Dá suporte a parâmetros.

### <a name="limitations"></a>Limitações
- Não há integração com o Azure. Não é possível gerenciar painéis e modelos por meio de Azure Resource Manager.
- Custo para dar suporte à infraestrutura Grafana adicional ou custo adicional para o Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Crie seu próprio aplicativo personalizado
Você pode acessar dados em log e dados de métrica em Azure Monitor por meio de sua API usando qualquer cliente REST, que permite que você crie seus próprios sites e aplicativos personalizados.

### <a name="advantages"></a>Principais
- Flexibilidade total na interface do usuário, visualização, interatividade e recursos.
- Combine dados de log e métricas com outras fontes de dados.

### <a name="disadvantages"></a>Desvantagens
- Esforço de engenharia significativo necessário.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os [dados coletados pelo Azure monitor](platform/data-platform.md).
- Saiba mais sobre os [painéis do Azure](../azure-portal/azure-portal-dashboards.md).
- Saiba mais sobre [modos de exibição no Azure monitor](platform/view-designer.md).
- Saiba mais sobre [pastas de trabalho](../azure-monitor/app/usage-workbooks.md).
- Saiba mais sobre como [importar dados de log para o Power bi](../azure-monitor/platform/powerbi.md).
- Saiba mais sobre o [plug-in de fonte de dados Grafana Azure monitor](../azure-monitor/platform/grafana-plugin.md).

