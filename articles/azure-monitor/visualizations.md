---
title: Visualização de dados do Azure Monitor | Documentos da Microsoft
description: Fornece um resumo dos métodos disponíveis para visualizar a métrica e registos de dados armazenados no Azure Monitor.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/24/2018
ms.author: bwren
ms.openlocfilehash: 55aed5d1133756789c3ed8979f0a9599f465e5d9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315100"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualização de dados do Azure Monitor
Este artigo fornece um resumo dos métodos disponíveis para visualizar a métrica dados armazenados no Azure Monitor e de registo.

Visualizações, como tabelas e gráficos podem ajudar a analisar os dados de monitorização para desagregação em problemas e identificar padrões. Dependendo da ferramenta utilizada, pode também optar por partilhar visualizações com outros utilizadores dentro e fora da sua organização.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-dashboards"></a>Dashboards do Azure
[Dashboards do Azure](../azure-portal/azure-portal-dashboards.md) são a tecnologia de vistas primário para o Azure. Eles são particularmente úteis no fornecimento de painel único de vidro em sua infraestrutura do Azure e serviços, permitindo-lhe identificar rapidamente problemas importantes.

![Dashboard](media/visualizations/dashboard.png)

### <a name="advantages"></a>Vantagens
- Integração profunda no Azure. Visualizações podem ser afixadas em dashboards de várias páginas do Azure, incluindo o Explorador de métricas, do Log Analytics e Application Insights.
- Oferece suporte a métricas e registos.
- Combinar dados de várias origens, incluindo a saída de [Explorador de métricas](platform/metrics-charts.md), [consultas de registo](log-query/log-query-overview.md), e [mapeia](app/app-map.md) e disponibilidade no Application Insights.
- Opção para dashboards pessoais ou partilhados. Integrado com o Azure [autenticação baseada em funções (RBAC)](../role-based-access-control/overview.md).
- Atualização automática. Atualização de métricas depende do intervalo de tempo com o mínimo de cinco minutos. Registos de atualização num minuto.
- Dashboards de métricas de Parametrized com timestamp e parâmetros personalizados.
- Opções de layout flexível.
- Modo de ecrã inteiro.


### <a name="limitations"></a>Limitações
- Controle limitado sobre visualizações de registo sem suporte para tabelas de dados. Número total de série de dados está limitado a 10 com outras séries de dados agrupados sob um _outros_ bucket.
- Não existem parâmetros personalizados suportam para gráficos de log.
- Gráficos de log estão limitados a últimos 30 dias.
- Gráficos de log só podem ser afixados a dashboards partilhados.
- Sem interatividade com dados de dashboard.
- Desagregação contextual limitada.

## <a name="azure-monitor-views"></a>Modos de exibição de Monitor do Azure
[Modos de exibição no Azure Monitor](platform/view-designer.md) permitem-lhe criar visualizações personalizadas com dados de registo. Elas são usadas pelo [soluções de monitorização](insights/solutions.md) para apresentar os dados que recolher.

![Vista](media/visualizations/view.png)

### <a name="advantages"></a>Vantagens
- Visualizações otimizadas para dados de registo.
- Exportar e importar vistas para transferi-las para outros grupos de recursos e subscrições.
- Integra-se no modelo de gestão do Azure Monitor com áreas de trabalho e soluções de monitorização.
- [Filtros](platform/view-designer-filters.md) para parâmetros personalizados.
- Interativo, oferece suporte a vários níveis de exploração-em (vista que detalha outra vista)

### <a name="limitations"></a>Limitações
- Mas não as métricas de registos de suporte.
- Não existem vistas de pessoas. Disponível para todos os utilizadores com acesso à área de trabalho.
- Nenhuma atualização automática.
- Opções de layout de limitadas.
- Não há suporte para consultas entre várias áreas de trabalho ou aplicações do Application Insights.
- Consultas estão limitadas de tamanho de resposta para o tempo de execução de 8MB e a consulta de 110 segundos.


## <a name="workbooks"></a>Livros
[Pastas de trabalho](../azure-monitor/app/usage-workbooks.md) são documentos interativos que fornecem informações mais detalhadas sobre os seus dados, a investigação e a colaboração dentro da equipe. Exemplos específicos em que as pastas de trabalho são úteis são resolução de problemas de guias e incidente postmortem.

![Livro](media/visualizations/workbook.png)

### <a name="advantages"></a>Vantagens
- Oferece suporte a métricas e registos.
- Parâmetros de suporta ativar relatórios interativos em que selecionar um elemento numa tabela serão dinamicamente atualizar associado de gráficos e visualizações.
- Tipo de documento de fluxo.
- Opção para pastas de trabalho pessoais ou partilhadas.
- Experiência de criação simples e amigável para colaboração.
- Modelos suportam a Galeria de modelos baseados no GitHub público.

### <a name="limitations"></a>Limitações
- Nenhuma atualização automática.
- Não existem esquemas densa, como dashboards, que tornam as pastas de trabalho menos útil como um único painel de vidro. O objetivo mais para fornecer informações mais detalhadas.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é particularmente útil para a criação de dashboards centrado em negócios e relatórios, bem como relatórios de análise de tendências KPI a longo prazo. Pode [importar os resultados de uma consulta de registo](platform/powerbi.md) para um conjunto de dados do Power BI para que possa tirar partido das respetivas funcionalidades, como combinar dados de diferentes origens e partilhar relatórios na web e em dispositivos móveis.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Vantagens
- Visualizações otimizadas.
- Extensas incluindo zoom-in de interatividade e filtragem cruzada.
- Fácil de partilhar em toda a organização.
- Integração com outros dados de várias origens de dados.
- Um melhor desempenho com os resultados em cache num cubo.


### <a name="limitations"></a>Limitações
- Mas não as métricas de registos de suporte.
- Sem integração do Azure. Não é possível gerir dashboards e modelos através do Gestor de recursos do Azure.
- Tem de ser importados para o modelo do Power BI para configurar os resultados da consulta. Limite de tamanho de resultado e a atualização.
- Atualização de dados limitados de oito vezes por dia.


## <a name="grafana"></a>Grafana
[O Grafana](https://grafana.com/) é uma plataforma aberta, que é a opção ideal em dashboards operacionais. É particularmente útil para detetar e o isolamento e separação incidentes operacionais. Pode adicionar [Plug-in de origem de dados do Azure Monitor da Grafana](platform/grafana-plugin.md) à sua subscrição do Azure-lo a visualizar os seus dados de métricas do Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Vantagens
- Visualizações otimizadas.
- Ecossistema avançado de origens de dados.
- Dados, incluindo interatividade ampliar o tempo limite.
- Suporta parâmetros.

### <a name="limitations"></a>Limitações
- Sem integração do Azure. Não é possível gerir dashboards e modelos através do Gestor de recursos do Azure.
- Custo para suportar uma infraestrutura adicional o Grafana ou custos adicionais para o Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Criar a sua aplicação personalizada
Pode aceder a dados no registo e dados de métrica no Azure Monitor através da API através de qualquer cliente REST, que permite que crie suas próprias aplicações e Web sites personalizados.

### <a name="advantages"></a>Vantagens
- Obter flexibilidade total na interface do Usuário, visualização, interatividade e funcionalidades.
- Combine métricas e registos de dados com outras origens de dados.

### <a name="disadvantages"></a>Desvantagens
- Esforço significativo de engenharia necessário.


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o [dados recolhidos pelo Azure Monitor](platform/data-collection.md).
- Saiba mais sobre [dashboards do Azure](../azure-portal/azure-portal-dashboards.md).
- Saiba mais sobre [modos de exibição no Azure Monitor](platform/view-designer.md).
- Saiba mais sobre [livros](../azure-monitor/app/usage-workbooks.md).
- Saiba mais sobre [importar dados de registo no Power BI](../azure-monitor/platform/powerbi.md).
- Saiba mais sobre o [Plug-in de origem de dados do Azure Monitor da Grafana](../azure-monitor/platform/grafana-plugin.md).

