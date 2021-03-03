---
title: Visualização de dados do Azure Monitor | Microsoft Docs
description: Fornece um resumo dos métodos disponíveis para visualizar dados métricos e de registo armazenados no Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2021
ms.openlocfilehash: 29164bbb205e9f3ad59ac28200b5c6cd1e47bf39
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707579"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualizar dados do Azure Monitor
Este artigo fornece um resumo dos métodos disponíveis para visualizar os dados de registo e métrica armazenados no Azure Monitor.

Visualizações como gráficos e gráficos podem ajudá-lo a analisar os seus dados de monitorização para aprofundar questões e identificar padrões. Dependendo da ferramenta que utiliza, poderá também ter a opção de partilhar visualizações com outros utilizadores dentro e fora da sua organização.

## <a name="workbooks"></a>Livros
Os livros de trabalho são [documentos](./visualize/workbooks-overview.md) interativos que fornecem informações profundas sobre os seus dados, investigação e colaboração dentro da equipa. Exemplos específicos onde os livros são úteis são guias de resolução de problemas e autópsias de incidentes.

![O diagrama mostra imagens de várias páginas de um livro, incluindo Análise de Visualizações de Página, Utilização e Tempo Gasto na Página.](media/visualizations/workbook.png)

### <a name="advantages"></a>Vantagens
- Suporta métricas e registos.
- Suporta parâmetros que permitem relatórios interativos onde a seleção de um elemento numa tabela irá atualizar dinamicamente gráficos e visualizações associadas.
- Fluxo de documentos.
- Opção para livros pessoais ou partilhados.
- Experiência de autoria fácil e colaborativa.
- Os modelos suportam a galeria de modelos baseada em GitHub.

### <a name="limitations"></a>Limitações
- Sem renovação automática.
- Nenhum layout denso como os dashboards, que tornam os livros menos úteis como uma única vidraça de vidro. Destinava-se mais a fornecer informações mais profundas.


## <a name="azure-dashboards"></a>Dashboards do Azure
[Os dashboards Azure](../azure-portal/azure-portal-dashboards.md) são a principal tecnologia de dashboarding para o Azure. Eles são particularmente úteis para fornecer um único painel de vidro sobre a sua infraestrutura e serviços Azure permitindo-lhe identificar rapidamente questões importantes.

![A screenshot mostra um exemplo de um Painel Azure com informações personalizáveis.](media/visualizations/dashboard.png)

Aqui está um vídeo sobre a criação de dashboards.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

### <a name="advantages"></a>Vantagens
- Profunda integração no Azure. As visualizações podem ser fixadas a dashboards de várias páginas Azure, incluindo [Metrics Explorer,](essentials/metrics-charts.md) [Log Analytics](logs/log-analytics-overview.md)e [Application Insights](app/app-insights-overview.md).
- Suporta métricas e registos.
- Combine dados de várias fontes, incluindo a saída do [Metrics Explorer,](essentials/metrics-charts.md) [consultas de registo,](logs/log-query-overview.md) [mapas](app/app-map.md) e disponibilidade em [Insights de Aplicação.](app/app-insights-overview.md)
- Opção para dashboards pessoais ou partilhados. Integrado com [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md).
- Refrescamento automático. A atualização das métricas depende do intervalo de tempo com um mínimo de cinco minutos. Os registos atualizam-se a cada hora, com uma opção de atualização manual a pedido clicando no ícone "refresh" numa determinada visualização, ou refrescando o painel completo.
- Dashboards de métricas parametrizados com tempotamp e parâmetros personalizados.
- Opções de layout flexíveis.
- Modo de ecrã completo.


### <a name="limitations"></a>Limitações
- Controlo limitado sobre visualizações de registos sem suporte para tabelas de dados. O número total de séries de dados é limitado a 50 com mais séries de dados agrupadas sob _outro_ balde.
- Sem suporte a parâmetros personalizados para gráficos de registo.
- As tabelas de registos estão limitadas a 30 dias.
- As tabelas de registos só podem ser fixadas a dashboards partilhados.
- Sem interatividade com dados do painel de instrumentos.
- Exercício contextual limitado.


## <a name="power-bi"></a>Power BI
[O Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é particularmente útil para a criação de dashboards e relatórios centrados no negócio, bem como relatórios que analisam as tendências de KPI a longo prazo. Pode [importar os resultados de uma consulta de registo](visualize/powerbi.md) num conjunto de dados Power BI para que possa tirar partido das suas funcionalidades, tais como combinar dados de diferentes fontes e partilhar relatórios na web e dispositivos móveis.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Vantagens
- Ricas visualizações.
- Interatividade extensiva, incluindo zoom-in e filtragem cruzada.
- Fácil de partilhar em toda a sua organização.
- Integração com outros dados de várias fontes de dados.
- Melhor desempenho com resultados em cache num cubo.


### <a name="limitations"></a>Limitações
- Suporta registos, mas não métricas.
- Sem integração Azure. Não é possível gerir dashboards e modelos através do Azure Resource Manager.
- Os resultados da consulta devem ser importados para o modelo Power BI para configurar. Limitação no tamanho do resultado e atualização.
- Atualização limitada de dados de oito vezes por dia.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) é uma plataforma aberta que se destaca em dashboards operacionais. É particularmente útil para detetar e isolar e trilar incidentes operacionais. Pode adicionar [plugin de fonte de dados Grafana Azure Monitor](visualize/grafana-plugin.md) à sua assinatura Azure para que ele visualize os seus dados de métricas Azure.

![A imagem mostra visualizações de Grafana.](media/visualizations/grafana.png)

### <a name="advantages"></a>Vantagens
- Ricas visualizações.
- Rico ecossistema de fontes de dados.
- Interatividade de dados, incluindo zoom dentro
- Suporta parâmetros.

### <a name="limitations"></a>Limitações
- Sem integração Azure. Não é possível gerir dashboards e modelos através do Azure Resource Manager.
- Custo para suportar infraestrutura grafana adicional ou custo adicional para Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Construa a sua própria aplicação personalizada
Pode aceder aos dados em registo e dados métricos no Azure Monitor através da sua API utilizando qualquer cliente REST, o que lhe permite construir os seus próprios websites e aplicações personalizadas.

### <a name="advantages"></a>Vantagens
- Flexibilidade completa na UI, visualização, interatividade e características.
- Combine métricas e registar dados com outras fontes de dados.

### <a name="disadvantages"></a>Desvantagens
- Um esforço significativo de engenharia necessário.


## <a name="azure-monitor-views"></a>Vistas do monitor Azure

> [!IMPORTANT]
> As opiniões estão em vias de ser depreciadas. Consulte [o Azure Monitor ver designer para guia de transição de livros](visualize/view-designer-conversion-overview.md) de trabalho para obter orientações sobre a conversão de visualizações em livros de trabalho.

[As vistas no Azure Monitor](visualize/view-designer.md)  permitem-lhe criar visualizações personalizadas com dados de registo. São utilizados [através da monitorização de soluções](insights/solutions.md) para apresentar os dados que recolhem.


![A screenshot mostra um azulejo de solução de monitorização do contentor e a visão detalhada do Monitor Azure que se abre quando o seleciona.](media/visualizations/view.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas para dados de registo.
- Vistas de exportação e importação para transferi-las para outros grupos de recursos e subscrições.
- Integra no modelo de gestão do Azure Monitor com espaços de trabalho e soluções de monitorização.
- [Filtros](visualize/view-designer-filters.md) para parâmetros personalizados.
- Interativo, suporta perfuração de vários níveis (vista que perfura para outra vista)

### <a name="limitations"></a>Limitações
- Suporta registos, mas não métricas.
- Sem opiniões pessoais. Disponível para todos os utilizadores com acesso ao espaço de trabalho.
- Sem renovação automática.
- Opções de layout limitadas.
- Sem suporte para consulta em vários espaços de trabalho ou aplicações application insights.
- As consultas são limitadas no tamanho da resposta a 8MB e o tempo de execução de consulta de 110 segundos.

## <a name="next-steps"></a>Passos seguintes
- Conheça os [dados recolhidos pelo Azure Monitor.](data-platform.md)
- Saiba mais [sobre os dashboards Azure](../azure-portal/azure-portal-dashboards.md).
- Saiba mais [sobre o Explorador de Métricas](essentials/metrics-getting-started.md)
- Conheça [os Livros de Trabalho.](./visualize/workbooks-overview.md)
- Saiba mais sobre [os dados de registo de importação no Power BI](./visualize/powerbi.md).
- Saiba mais sobre o [plugin de fonte de dados do Grafana Azure Monitor](./visualize/grafana-plugin.md).
- Saiba mais [sobre as vistas no Azure Monitor](visualize/view-designer.md).

