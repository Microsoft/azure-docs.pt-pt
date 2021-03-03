---
title: Novidades na documentação do Azure Monitor
description: Atualizações significativas da documentação do Azure Monitor atualizadas todos os meses.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: b21e5ea8e25844e3e025915b0b9f15162c642f25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713444"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>O que há de novo na documentação do Monitor Azure?

Este artigo fornece listas de artigos do Azure Monitor que são novos ou foram significativamente atualizados. Será atualizado na primeira semana de cada mês para incluir atualizações de artigos do mês anterior.

## <a name="january-2021"></a>Janeiro de 2021 

### <a name="general"></a>Geral 
- [Azure Monitor FAQ](faq.md) - Entrada adicionada nas informações do dispositivo para Insights de Aplicação.
### <a name="agents"></a>Agentes  
- [Recolha de eventos para windows (ETW) Eventos para análise Registos monitores Azure](./agents/data-sources-event-tracing-windows.md) Monitor - Novo artigo.
- [Regras de recolha de dados em Azure Monitor (pré-visualização)](./agents/data-collection-rule-overview.md) - Ligações adicionadas às amostras PowerShell e CLI.

### <a name="alerts"></a>Alertas  
- [Configure Azure para ligar ferramentas ITSM utilizando a Secure Export](./alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) - Novo artigo.
- [Erros de estado do conector no painel ITSMC](./alerts/itsmc-dashboard-errors.md) - Novo artigo.
- [Investigar erros utilizando o dashboard ITSMC](./alerts/itsmc-dashboard.md) - Novo artigo.
- [Resolução de problemas Alertas métricos Azure](./alerts/alerts-troubleshoot-metric.md) - Secções adicionadas para limiares dinâmicos.
- [Problemas de resolução de problemas no Connector de Gestão de Serviços de TI](./alerts/itsmc-troubleshoot-overview.md) - Novo artigo.

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights correlação de telemetria](app/correlation.md) - Correlação de vestígios adicionado quando um módulo chama outro em OpenCensus Python.
- [Aplicações Insights para páginas web](app/javascript.md) - Novo artigo.
- [Clique no plugin de recolha automática analytics para Insights de Aplicação JavaScript SDK](app/javascript-click-analytics-plugin.md) - Novo artigo.
- [Monitorize as suas aplicações sem alterações de código - auto-instrumentação para Azure Monitor Application Insights](app/codeless-overview.md) - Coluna Python adicionada.
- [Reagir plugin para Informações JavaScript SDK](app/javascript-react-plugin.md) - Novo artigo.
- [Processadores de telemetria (pré-visualização) - Azure Monitor Application Insights for Java](app/java-standalone-telemetry-processors.md) - Rewritten.
- [Análise de utilização com Azure Application Insights](app/usage-overview.md) - Novo artigo.
- [Utilize a Análise de Alteração de Aplicações no Azure Monitor para encontrar problemas com aplicações web](app/change-analysis.md) - Confusões de erro adicionadas.


### <a name="insights"></a>Informações    
- [Monitor Azure para Azure Data Explorer (pré-visualização)](insights/data-explorer.md) - Novo artigo.

### <a name="logs"></a>Registos    
- [Chave gerida pelo cliente Azure Monitor](./logs/customer-managed-keys.md) - Introduza a identidade gerida pelo utilizador.
- [Azure Monitor Logs Clusters Dedicados](./logs/logs-dedicated-clusters.md) - Código de repouso atualizado.
- [Consulta de serviço cross - Azure Monitor e Azure Data Explorer (Preview)](/azure/azure-monitor/platform/azure-data-explorer-monitor-cross-service-query) - Novo artigo.

### <a name="metrics"></a>Métricas
- [Azure Monitor Métricas agregação e exibição explicadas](./essentials/metrics-aggregation-explained.md) - Novo artigo.

### <a name="platform-logs"></a>Registos de plataformas
- [Azure Monitor Registos de Recursos suportados serviços e categorias](./essentials/resource-logs-categories.md) - Novo artigo.

### <a name="visualizations"></a>Visualizações
- [Fontes de dados de livros do Azure Monitor](./visualize/workbooks-data-sources.md) - Adicione a fusão e a alteração da análise.


## <a name="december-2020"></a>Dezembro de 2020

### <a name="general"></a>Geral
- [Chave gerida pelo cliente Azure Monitor](logs/customer-managed-keys.md) - Mensagens de erro adicionadas.
- [Parceiros que se integram com o Azure Monitor](partners.md) - Secção adicionada sobre integração do Event Hub.

### <a name="agents"></a>Agentes
- [Consulta de recursos cruzados Azure Data Explorer usando Azure Monitor](logs/azure-monitor-data-explorer-proxy.md) - Novo artigo.
- [Visão geral dos agentes de monitorização Azure](agents/agents-overview.md) - Apoio ao Oráculo 8 adicionado.

### <a name="alerts"></a>Alertas
- [Resolução de problemas Alertas métricos Azure](alerts/alerts-troubleshoot-metric.md) - Adição de resolução de problemas para limiares dinâmicos.
- [Conector de gestão de serviços de TI em Log Analytics](alerts/itsmc-definition.md) - Novo artigo.
- [Visão geral do Conector de Gestão de Serviços de TI](alerts/itsmc-overview.md) - Informações reestruturadas de resolução de problemas.
- [Ligue Cherwell ao It Service Management Connector](alerts/itsmc-connections-cherwell.md) - Novo artigo.
- [Conecte provance com it service management connector](alerts/itsmc-connections-provance.md) - Novo artigo.
- [Ligue o SCSM ao Conector de Gestão de Serviços de TI](alerts/itsmc-connections-scsm.md) - Novo artigo.
- [Conecte o ServiceNow com o Conector de Gestão de Serviços de TI](alerts/itsmc-connections-servicenow.md) - Novo artigo.
- [Como corrigir manualmente problemas de sincronização do ServiceNow](alerts/itsmc-resync-servicenow.md) - Informações de resolução de problemas reestruturadas.




### <a name="application-insights"></a>Application Insights
- [Azure Application Insights para aplicações web JavaScript](app/javascript.md) - Configuração de cadeia de ligação adicionada.
- [Azure Application Insights métricas padrão](app/standard-metrics.md) - Novo artigo.
- [Azure Monitor Application Insights Java](app/java-in-process-agent.md) - Informações adicionais sobre o envio de telemetria personalizada a partir da sua aplicação.
- [Exportação contínua de telemetria a partir de Application Insights](app/export-telemetry.md) - Definições de diagnóstico adicionadas à exportação.
- [Ativar o Snapshot Debugger para aplicações .NET e .NET Core em Funções Azure](app/snapshot-debugger-function-app.md) - Novo artigo.
- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Endereços IP adicionados para O Governo Azure.
- [Problemas de resolução de problemas com O Perfil de Insights de Aplicação Azure](app/profiler-troubleshooting.md) - Informação adicionada na página de estado do site dos Serviços de Diagnóstico.
- [Resolução de problemas nos testes de disponibilidade do Azure Application Insights](app/troubleshoot-availability.md) - Atualizações para resolução de problemas para testes de ping.
- [Resolução de problemas Azure Monitor Application Insights for Java](app/java-standalone-troubleshoot.md) - Novo artigo.

### <a name="containers"></a>Contentores
- [Relatórios em informações sobre contentores](insights/container-insights-reports.md) - Novo artigo.

### <a name="logs"></a>Registos
- [Azure Monitor Logs Clusters Dedicados](logs/logs-dedicated-clusters.md) - Comandos automatizados adicionados, métodos para desvincular e remover, e resolução de problemas.
- [Consulta de serviço cruzada entre Azure Monitor e Azure Data Explorer (pré-visualização)](logs/azure-data-explorer-monitor-cross-service-query.md) - Novo artigo.
- [Log Analytics exportação de dados do espaço de trabalho em Azure Monitor (pré-visualização)](logs/logs-data-export.md) - Modelos ARM adicionados.

### <a name="metrics"></a>Métricas
- [Funcionalidades avançadas do Azure Metrics Explorer](essentials/metrics-charts.md) - Informação adicional no selecionador de âmbito de recursos.
- [Visualização de múltiplos recursos no Metrics Explorer](essentials/metrics-dynamic-scope.md) - Novo artigo.

### <a name="networks"></a>Redes
- [Solução Azure Networking Analytics no Azure Monitor](insights/azure-networking-analytics.md) - Informação adicional no livro de web Insights.

### <a name="virtual-machines"></a>Máquinas Virtuais
- [Ativar o Azure Monitor para um ambiente híbrido](vm/vminsights-enable-hybrid.md) - Nova versão do agente de dependência.


### <a name="visualizations"></a>Visualizações
- [Visualizações de mapas de mapas do Azure Monitor](visualize/workbooks-map-visualizations.md) - Novo artigo.
- [Os livros de trabalho do Azure Monitor trazem o seu próprio armazenamento](visualize/workbooks-bring-your-own-storage.md) - Novo artigo.


## <a name="november-2020"></a>Novembro de 2020

### <a name="general"></a>Geral
- [Limites de serviço do Monitor Azure](service-limits.md) - Atualizado para suporte Azure Arc.

### <a name="agents"></a>Agentes
- [Visão geral dos agentes de monitorização Azure](agents/agents-overview.md) - Atualizado para suporte Azure Arc.
- [Instale o agente Azure Monitor](agents/azure-monitor-agent-install.md) - Novo artigo.
- [Visão geral do agente do Monitor Azure](agents/azure-monitor-agent-overview.md) - Atualizado para suporte Azure Arc.
- [Amostras de modelo do Gestor de Recursos para agentes](agents/resource-manager-agent.md) - Atualizado para suporte Azure Arc.

### <a name="alerts"></a>Alertas
- [Criar e gerir grupos de ação no portal Azure](alerts/action-groups.md) - Endereços IP de origem adicionada para webhooks.

### <a name="application-insights"></a>Application Insights
- [Aplicação sem código Java monitorizando Azure Monitor Application Insights](app/java-in-process-agent.md) - Exemplo de configuração adicionada.
- [Reagir plugin para Informações JavaScript SDK](app/javascript-react-plugin.md) - Secção adicionada na utilização de ganchos React.
- [Upgrade de Application Insights Java 2.x SDK](app/java-standalone-upgrade-from-2x.md) - Novo artigo.
- [Notas de lançamento para Microsoft.ApplicationInsights.SnapshotCollector](app/snapshot-collector-release-notes.md) - Novo artigo.

### <a name="autoscale"></a>Dimensionamento Automático
- [Começar com autoescala em Azure](autoscale/autoscale-get-started.md) - Secção adicionada sobre a mudança da Autoscale para uma região diferente.

### <a name="data-collection"></a>Recolha de dados
- [Configure a recolha de dados para o agente Azure Monitor (pré-visualização)](agents/data-collection-rule-azure-monitor-agent.md) - Atualizado para suporte Azure Arc.
- [Regras de recolha de dados em Azure Monitor (pré-visualização)](agents/data-collection-rule-overview.md) - Atualizado para suporte Azure Arc.
- [Amostras de modelo do Gestor de Recursos para regras de recolha de dados](agents/resource-manager-data-collection-rules.md) - Novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções
- [Ligue o Azure às ferramentas ITSM utilizando a secção Secure Export](alerts/it-service-management-connector-secure-webhook-connections.md) - Secção adicionada na ligação ao ServiceNow.

### <a name="logs"></a>Registos
- [Integrar Log Analytics e Excel](logs/log-excel.md) - Novo artigo.
- [Log Analytics segurança de dados](logs/data-security.md) - Secção adicionada em funcionalidades de segurança adicionais.
- [Log Analytics integração com Power BI](logs/log-powerbi.md) - Novo artigo.
- [Colunas padrão nos registos de registos do Monitor Azure](logs/log-standard-columns.md) - Coluna _SubscriptionId adicionada.

Artigos novos e atualizados da reestruturação do conteúdo da consulta de registo.

- [Tutorial do Log Analytics](logs/log-analytics-tutorial.md)
- [Consultas de registo no Azure Monitor](logs/log-query-overview.md)
- [Visão geral do Log Analytics no Azure Monitor](logs/log-analytics-overview.md)
- [Amostras para consultas para Azure Data Explorer e Azure Monitor](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Tutorial: Use consultas kusto no Azure Data Explorer e Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Máquinas virtuais

- [Ativar a visão geral dos conhecimentos de VM](vm/vminsights-enable-overview.md) - Regiões apoiadas adicionadas.

Novos artigos para saúde dos hóspedes (pré-visualização)

- [VM insights saúde dos hóspedes (pré-visualização)](vm/vminsights-health-overview.md)
- [VM insights alertas de saúde dos hóspedes (pré-visualização)](vm/vminsights-health-alerts.md)
- [Configure a monitorização em Saúde dos hóspedes (pré-visualização)](vm/vminsights-health-configure.md)
- [Configure a monitorização em VM insights saúde dos hóspedes usando regras de recolha de dados (pré-visualização)](vm/vminsights-health-configure-dcr.md)
- [Ativar a saúde dos hóspedes (pré-visualização)](vm/vminsights-health-enable.md)
- [Resolução de problemas VM insights saúde dos hóspedes (pré-visualização)](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Outubro de 2020

### <a name="general"></a>Geral
- [Aposentadoria Azure Monitor API](logs/operationalinsights-api-retirement.md) - Novo artigo.

### <a name="agents"></a>Agentes
- [O que é monitorizado pelo Azure Monitor](monitor-reference.md) - Secção adicionada aos agentes.

### <a name="alerts"></a>Alertas
- [Criar e gerir grupos de ação no portal Azure](alerts/action-groups.md) - Secção adicionada na etiqueta de serviço.
- [Amostras de modelo do gestor de recursos para alertas métricos](alerts/resource-manager-alerts-metric.md) - Parâmetros de correspondência de conteúdo adicionados e locais de teste.
- [Resolução de problemas Alertas métricos Azure](alerts/alerts-troubleshoot-metric.md) - Adicionar as melhores práticas para a configuração da regra.

### <a name="application-insights"></a>Application Insights
- [Plugin angular para Informações JavaScript SDK](app/javascript-angular-plugin.md) - Novo artigo.
- [Azure Application Insights for ASP.NET Aplicações Core](app/asp-net-core.md) - Adicionado FAQ sobre registos ILogger.
- [Configure a monitorização para ASP.NET com Azure Application Insights](app/asp-net.md) - Artigo reescrito.
- [Métricas baseadas em registos e pré-agregadas em Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) - Tabelas adicionadas com métricas pré-agregadas.
- [Monitorizar a disponibilidade e a capacidade de resposta de qualquer web site](app/monitor-web-app-availability.md) - Secção adicionada nas etiquetas da população da localização.
- [Monitorize as aplicações java em qualquer lugar - Azure Monitor Application Insights](app/java-standalone-config.md) - Exemplo de configuração adicionada.
- [Monitorize as aplicações java em qualquer lugar - Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) - Novo artigo.
- [Use a Análise de Mudança de Aplicação no Azure Monitor para encontrar problemas com aplicações web](app/change-analysis.md) - Secções adicionadas em máquinas virtuais e registo de atividade.
  
### <a name="autoscale"></a>Dimensionamento Automático
- [Começar com autoescala em Azure](autoscale/autoscale-get-started.md) - Secção adicionada sobre a mudança da Autoscale para diferentes regiões.

### <a name="containers"></a>Contentores
- [Configure a monitorização do PV com insights do Contentor](containers/container-insights-persistent-volumes.md) - Novo artigo.
- [Como gerir o agente de insights do contentor](containers/container-insights-manage-agent.md) - Suporte adicionado para Azure Arc ativou o cluster Kubernetes.
- [Alertas métricos de insights de contentores](containers/container-insights-metric-alerts.md) - Suporte adicionado para Azure Arc permitiu o cluster Kubernetes.

### <a name="insights-and-solutions"></a>Insights e soluções
- [Conector de gestão de serviços de TI - Exportação Segura em Monitor Azure](alerts/it-service-management-connector-secure-webhook-connections.md) - Secção adicionada no ServiceNow.

### <a name="logs"></a>Registos
- [Arquivar dados do log analytics espaço de trabalho para o armazenamento Azure usando a Logic App](logs/logs-export-logic-app.md) - Novo artigo.
- [Log Analytics workspace data export in Azure Monitor (preview)](logs/logs-data-export.md) - Corpo de amostra adicionado para pedido de REST para o centro de eventos.
- [Gerir a utilização e os custos dos registos do Monitor Azure](logs/manage-cost-storage.md) - Informação adicional sobre a relação entre os Registos do Monitor Azure e a faturação do Azure Security Center. Consulta adicionada para contagem de nó se utilizar o nível de preços per nó. 
- [Monitorar a saúde do log analytics espaço de trabalho no Azure Monitor](logs/monitor-workspace.md) - Novo artigo.
- [Dados de consulta no Azure Monitor utilizando o Azure Data Explorer (pré-visualização)](logs/azure-data-explorer-monitor-proxy.md) - Novo artigo.
- [Consulta dados exportados do Azure Monitor usando Azure Data Explorer (pré-visualização)](logs/azure-data-explorer-query-storage.md) - Novo artigo.

### <a name="networks"></a>Redes
- [Azure Monitor for Networks Preview](insights/network-insights-overview.md) - Secção de resolução de problemas adicionada. Secção adicionada sobre conectividade.

### <a name="platform-logs"></a>Registos da plataforma
- [Esquema de evento de registo de atividade azure](essentials/activity-log-schema.md) - Descrição adicional dos níveis de gravidade.

### <a name="virtual-machines"></a>Máquinas virtuais
- [Alterar análise no Monitor Azure para VMs](vm/vminsights-change-analysis.md) - Novo artigo.
- [Ativar o Monitor Azure para visão geral dos VMs](vm/vminsights-enable-overview.md) - Regiões apoiadas adicionadas.
- [Como atualizar insights de contentores para métricas](containers/container-insights-update-metrics.md) - Suporte adicionado para Azure Arc permitiu o cluster Kubernetes.



## <a name="september-2020"></a>Setembro de 2020

### <a name="general"></a>Geral
- [Azure Monitor FAQ](faq.md) - Secção adicionada na OpenTelemetry.

### <a name="agents"></a>Agentes
- [Visão geral do agente do Azure Monitor](agents/azure-monitor-agent-overview.md) - Fatores de decisão adicionados para mudar para novo agente.
- [Visão geral dos agentes de monitorização Azure](agents/agents-overview.md) - Suporte adicionado para o Windows 10.

### <a name="alerts"></a>Alertas
- [Crie um alerta de registo com o modelo Azure Resource Manager](alerts/alerts-log-create-templates.md) - Novo artigo.
- [Alertas métricos Azure de resolução de problemas](alerts/alerts-troubleshoot-metric.md) - Secção adicionada sobre o modelo ARM de exportação para uma regra de alerta métrico.

### <a name="application-insights"></a>Application Insights
- [Crie um novo recurso baseado no espaço de trabalho Azure Monitor Insights](app/create-workspace-resource.md) - Designação de pré-visualização removida.
- [Retenção de dados e armazenamento em Azure Application Insights](app/data-retention-privacy.md) - Adicionamos detalhes para novos suportes para a proteção de perda de dados Mac e Linux.
- [Contadores de eventos em Application Insights](app/eventcounters.md) - Nota adicionada nos balcões recolhidos por predefinição.
- [Métricas baseadas em registos e pré-agregadas em Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) - Designação de pré-visualização removida.
- [Migrar um recurso clássico da aplicação Azure Monitor Insights para um recurso baseado no espaço de trabalho](app/convert-classic-resource.md) - Novo artigo.
- [Monitorize as aplicações java em qualquer ambiente - Azure Monitor Application Insights](app/java-in-process-agent.md) - Atualizado para nova versão de pré-visualização do agente.
- [Crie análises de aplicativos web para ASP.NET com Azure Application Insights](app/asp-net.md) - Artigo reescrito.
- [Canais de telemetria em Azure Application Insights](app/telemetry-channels.md) - Adicionamos detalhes para novos suportes para a proteção de perda de dados Mac e Linux.
- [Resolução de problemas Azure Application Insights Snapshot Debugger](app/snapshot-debugger-troubleshoot.md) - Secção SSL adicionada à resolução de problemas do Snapshot Debugger.
- [Use a análise de mudança de aplicação no Azure Monitor para encontrar problemas com aplicações web](app/change-analysis.md) - Máquina Virtual adicionada e Log de atividade.


### <a name="containers"></a>Contentores
- [Configure Azure Arc permitiu o agrupamento kubernetes com insights de contentores](containers/container-insights-enable-arc-enabled-clusters.md) - Orientação adicional para permitir a monitorização usando o principal do serviço.
- [Implantação & métricas de HPA com insights de contentores](containers/container-insights-deployment-hpa-metrics.md) - Novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções
- [Monitor Azure para Cache Azure para Redis](insights/redis-cache-insights-overview.md) - Designação de pré-visualização removida.
- [Monitor Azure para Redes (Pré-visualização)](insights/network-insights-overview.md) - Secções de Conectividade e Tráfego Adicionados.
- [Conector de Gestão de Serviços de TI - Exportação Segura em Monitor Azure](alerts/it-service-management-connector-secure-webhook-connections.md) - Novo artigo.
- [Conector de Gestão de Serviços de TI em Azure Monitor](alerts/itsmc-connections.md) - Nota sobre as integrações cherwell e provance ITSM.
- [Monitor Key Vault com Monitor Azure para Cofre de Chave](insights/key-vault-insights-overview.md) - Designação de pré-visualização removida.

### <a name="logs"></a>Registos
- Consultas de auditoria em consultas de registo do [Azure Monitor](logs/query-audit.md) - Novo artigo.
- [Chave gerida pelo cliente Azure Monitor](logs/customer-managed-keys.md) - Lockbox de cliente adicionado.
- [Azure Monitor Logs Agrupamentos Dedicados](logs/logs-dedicated-clusters.md) - Novo artigo.
- [Conceber a sua implementação de Registos de Monitor Azure](logs/design-logs-deployment.md) - Secção de limite de taxa de volume de escala e ingestão atualizada.
- [Log âmbito de consulta em Azure Monitor Log Analytics](logs/scope.md) - Atualizações para incluir aplicações baseadas no espaço de trabalho.
- [Registos no Azure Monitor](logs/data-platform-logs.md) - Atualizações para incluir aplicações baseadas no espaço de trabalho.
- [Colunas padrão nos registos de registos do Monitor Azure](logs/log-standard-columns.md) - Atualizações para incluir aplicações baseadas em espaço de trabalho.
- [Limites de serviço do Azure Monitor](service-limits.md) - Limites atualizados para a aceleração da consulta do utilizador.
- [Utilização de contas de armazenamento geridas pelo cliente no Azure Monitor Log Analytics](logs/private-storage.md) - Artigo reescrito.
- [Visualização e análise de dados em Azure Log Analytics](./logs/data-platform-logs.md) - Atualizações para incluir aplicações baseadas no espaço de trabalho.


### <a name="platform-logs"></a>Registos da plataforma
- [Esquema de evento de registo de atividade azure - Monitor Azure](essentials/activity-log-schema.md) - níveis de gravidade adicionados.
- [Amostras de modelo do gestor de recursos para definições de diagnóstico](essentials/resource-manager-diagnostic-settings.md) - Amostra adicionada para conta de armazenamento Azure.

### <a name="visualizations"></a>Visualizações
- [Visualizações de gráficos de livros do Azure Monitor](visualize/workbooks-chart-visualizations.md) - Novo artigo.
- [Renderejador de barras compósitos Azure Monitor](visualize/workbooks-composite-bar.md) - Novo artigo.
- [Visualizações de gráficos de livro do Azure Monitor](visualize/workbooks-graph-visualizations.md) - Novo artigo.
- [Visualizações da grelha de trabalho do Azure Monitor](visualize/workbooks-grid-visualizations.md) - Novo artigo.
- Visualizações do pente de mel do [Azure Monitor](visualize/workbooks-honey-comb.md) - Novo artigo.
- [Visualizações de texto de livro do Azure Monitor](visualize/workbooks-text-visualizations.md) - Novo artigo.
- Visualizações de azulejos do [Azure Monitor](visualize/workbooks-tile-visualizations.md) - Novo artigo.
- [Visualizações de árvores de livro do Azure Monitor](visualize/workbooks-tree-visualizations.md) - Novo artigo.




## <a name="august-2020"></a>Agosto de 2020

### <a name="general"></a>Geral

- [O que é monitorizado pelo Azure Monitor](monitor-reference.md) - Atualizado para incluir o agente Azure Monitor.


### <a name="agents"></a>Agentes
- [Visão geral do agente do Azure Monitor](agents/azure-monitor-agent-overview.md) - Novo artigo.
- [Ativar o Azure Monitor para um ambiente híbrido](vm/vminsights-enable-hybrid.md) - versão atualizada do agente de dependência.
- [Visão geral dos agentes de monitorização Azure](agents/agents-overview.md) - Agente Azure Monitor adicionado e tabela de suporte de OS consolidado.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Artigos novos e atualizados da reestruturação do conteúdo do agente
- [Ativar a visão geral dos conhecimentos do VM](vm/vminsights-enable-overview.md)
- [Instalar o agente do Log Analytics em computadores Linux](agents/agent-linux.md)
- [Instalar o agente do Log Analytics em computadores Windows](agents/agent-windows.md)
- [Visão geral do agente do Log Analytics](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights para aplicações web JavaScript](app/javascript.md) - Secção adicionada clarificando a correlação e configuração do servidor do cliente para a correlação CORS.
- [Criar um novo recurso baseado no espaço de trabalho Azure Monitor Insights](app/create-workspace-resource.md) - Capacidades adicionais fornecidas por aplicações baseadas no espaço de trabalho.
- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Endereços IP atualizados para transmissão de métricas ao vivo.
- [Monitorize as aplicações java em qualquer ambiente - Azure Monitor Application Insights](app/java-in-process-agent.md) - Tabela adicionada para telemetria personalizada suportada.
- [Plugin De Reação Nativa para Informações JavaScript SDK](app/javascript-react-native-plugin.md) - Novo artigo.
- [Reagir plugin para Informações JavaScript SDK](app/javascript-react-plugin.md) - Novo artigo.
- [Amostra de modelo do Gestor de Recursos para criar aplicações de função Azure com monitorização de Insights de Aplicação](app/resource-manager-function-app.md) - Novo artigo.
- [Amostras de modelo de gestor de recursos para criar aplicações web Azure App Services com monitorização de Apps Insights](app/resource-manager-web-app.md) - Novo artigo.
- [Análise de utilização com Azure Application Insights](app/usage-overview.md) - Vídeo adicionado.

### <a name="autoscale"></a>Dimensionamento Automático
- [Começar com autoescala em Azure](autoscale/autoscale-get-started.md) - Secção adicionada no encaminhamento para instâncias saudáveis para o Serviço de Aplicações.

### <a name="data-collection"></a>Recolha de dados
- [Configure a recolha de dados para o agente Azure Monitor (pré-visualização)](agents/data-collection-rule-azure-monitor-agent.md) - Novo artigo.
- [Regras de recolha de dados no Monitor Azure (pré-visualização)](agents/data-collection-rule-overview.md) - Novo artigo.


### <a name="containers"></a>Contentores
- [Implantação & métricas de HPA com insights de contentores](containers/container-insights-deployment-hpa-metrics.md) - Novo artigo.

### <a name="insights"></a>Informações
- [Soluções de monitorização no Azure Monitor](insights/solutions.md) - Atualizado para nova UI.
- [Solução monitor de desempenho de rede em Azure](insights/network-performance-monitor.md) - Regiões de espaço de trabalho apoiados.


### <a name="logs"></a>Registos
- [Azure Monitor FAQ](faq.md) - Entrada adicionada para apagar dados de um espaço de trabalho. Entrada acrescida em 502 e 503 respostas.
  - [Conceber a sua implementação de Registos de Monitor Azure](logs/design-logs-deployment.md) - Atualizações para a secção limite de taxa de volume de Ingestion.
- [Gerir a utilização e os custos dos Registos do Monitor Azure](logs/manage-cost-storage.md) - Consultas de utilização atualizadas para um formato de consulta mais eficiente.
- [Otimize as consultas de registo no Azure Monitor](logs/query-optimization.md) - Adicione valores específicos aos indicadores de desempenho.
- [Amostras de modelo do Gestor de Recursos para definições de diagnóstico](essentials/resource-manager-diagnostic-settings.md) - Amostra adicionada para registos de auditoria de consulta de registo.


### <a name="platform-logs"></a>Registos da plataforma
- [Criar definições de diagnóstico para enviar registos e métricas de plataforma para diferentes destinos](essentials/diagnostic-settings.md) - Requisito regional adicionado para configurações de diagnóstico.

### <a name="visualizations"></a>Visualizações
- Visão geral dos livros de trabalho do [Monitor Azure](visualize/workbooks-overview.md) - Vídeo adicionado.
- [Mova um modelo de livro Azure para outra região](visualize/workbook-templates-move-region.md) - Novo artigo.
- [Mover um Livro de Azure para outra região](visualize/workbooks-move-region.md) - Novo artigo.



## <a name="july-2020"></a>Julho de 2020

### <a name="general"></a>Geral
- [Implementar Monitor Azure](deploy-scale.md) - Reestruturação dos insights VM no conteúdo de embarque.
- [Utilize o Link Privado Azure para ligar de forma segura as redes ao Azure Monitor](logs/private-link-security.md) - Secção adicionada nos limites.

### <a name="alerts"></a>Alertas
- [Regras de ação para alertas do Azure Monitor](alerts/alerts-action-rules.md) - Processos CLI adicionados.
- [Criar e gerir grupos de ação no portal Azure](alerts/action-groups.md) - Atualizado para refletir alterações na UI.
- [Exemplo de consultas no Azure Monitor Log Analytics](logs/example-queries.md) - Novo artigo.
- [Alertas de registo de resolução de problemas no Azure Monitor](alerts/alerts-troubleshoot-log.md) - Secção adicionada na quota de regra de alerta.
- [Resolução de problemas Alertas métricos Azure](alerts/alerts-troubleshoot-metric.md) - Secção adicionada sobre regra de alerta sobre uma métrica personalizada que ainda não é emitida.
- [Entenda como os alertas métricos funcionam no Azure Monitor.](alerts/alerts-metric-overview.md) - Recomendação adicional para a seleção da granularidade agregação.

### <a name="application-insights"></a>Application Insights
- [Notas de lançamento para extensão de aplicativo web Azure - Application Insights](app/web-app-extension-release-notes.md) - Novo artigo.
- [Amostras de modelo de gestor de recursos para Recursos de Insights de Aplicação](app/resource-manager-app-resource.md) - Novo artigo.
- [Problemas de resolução de problemas com O Perfil de Insights de Aplicação Azure](app/profiler-troubleshooting.md) - Nota adicional no perfil de execução de bugs para aplicações ASP.NET Core no Azure App Service. 

### <a name="containers"></a>Contentores
- [Registar alertas a partir de insights do Contentor](containers/container-insights-log-alerts.md) - Novo artigo.
- [Alertas métricos dos insights do contentor](containers/container-insights-metric-alerts.md) - Novo artigo.

### <a name="logs"></a>Registos
- [Chave gerida pelo cliente Azure Monitor](logs/customer-managed-keys.md) - Mensagem de erro adicionada e configuração cmk da secção para consultas.
- [Azure Monitor HTTP Data Collector API](logs/data-collector-api.md) - Amostra de Python 3 adicionada.
- [Otimize as consultas de registo no Azure Monitor](logs/query-optimization.md) - Secção adicionada para evitar múltiplas verificações de dados ao utilizar subqueries.
- [Tutorial: Começa com consultas de Log Analytics](./logs/log-analytics-tutorial.md) - Vídeo adicionado.

### <a name="platform-logs"></a>Registos da plataforma
- [Crie definições de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos](essentials/diagnostic-settings.md) - Vídeo adicionado.
- [Amostras de modelo de gestor de recursos para monitor Azure](/resource-manager-samples.md) - amostra DE ARM adicionada usando o tipo de destino Logs. 

### <a name="solutions"></a>Soluções
- [Soluções de monitorização no Azure Monitor](insights/solutions.md) - Processos CLI adicionados.
- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Alteração da data de reforma.

### <a name="virtual-machines"></a>Máquinas virtuais

Artigos novos e atualizados da reestruturação do conteúdo de insights de VM

- [O que são os conhecimentos em VM?](vm/vminsights-overview.md)
- [Configurar log analytics espaço de trabalho para insights VM](vm/vminsights-configure-workspace.md)
- [Ligue os computadores Linux ao Azure Monitor](agents/agent-linux.md)
- [Ativar o Azure Monitor para um ambiente híbrido](vm/vminsights-enable-hybrid.md)
- [Ativar o Azure Monitor para uma única máquina virtual ou escala de máquina virtual definida no portal Azure](vm/vminsights-enable-portal.md)
- [Ativar os conhecimentos de VM utilizando a Política Azure](./vm/vminsights-enable-policy.md)
- [Ativar a visão geral dos conhecimentos do VM](vm/vminsights-enable-overview.md)
- [Ativar insights VM utilizando PowerShell](vm/vminsights-enable-powershell.md)
- [Ativar insights VM usando modelos de Gestor de Recursos](vm/vminsights-enable-resource-manager.md)
- [Ativar insights em VM com PowerShell ou modelos](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Visualizações
- [Atualizar as visualizações do Painel de Registos -](logs/dashboard-upgrade.md) Taxa de atualização atualizada.
- [Visualização de dados do Azure Monitor](visualizations.md) - Vídeo adicionado.


## <a name="june-2020"></a>Junho de 2020

### <a name="general"></a>Geral
- [Implementar Monitor Azure](deploy-scale.md) - Novo artigo.
- [Chave gerida pelo cliente Azure Monitor](logs/customer-managed-keys.md) - Propriedade de tipo de faturação atualizada. Adicionou comandos PowerShell.

### <a name="agents"></a>Agentes
- [Visão geral do agente Log Analytics](agents/log-analytics-agent.md) - Requisito de Python 2 adicionado.

### <a name="alerts"></a>Alertas
- [Como atualizar regras de alerta ou regras de ação quando o seu recurso-alvo se desloca para uma região Azure diferente](alerts/alerts-resource-move.md) - Novo artigo.
- [Resolução de problemas Alertas métricos Azure](alerts/alerts-troubleshoot-metric.md) - Novo artigo.
- [Alertas de registo de resolução de problemas no Azure Monitor](alerts/alerts-troubleshoot-metric.md) - Novo artigo.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights para aplicações web JavaScript](app/javascript.md) - Atualização para a secção SDK JavaScript. Corte atualizado para reportar falhas de carga.
- [Configure BYOS (Bring Your Own Storage) para Profiler & Snapshot Debugger](app/profiler-bring-your-own-storage.md) - Novo artigo.
- [Rastreio de pedido de entrada em Azure Application Insights com OpenCensus Python](app/opencensus-python-request.md) - Registo e configuração atualizados para OpenCensus.
- [Monitorize uma aplicação web de ASP.NET ao vivo com Azure Application Insights](app/monitor-performance-live-website-now.md) - Data de depreciação atualizada para Status Monitor v1.
- [Monitorize Node.js serviços com Insights de Aplicações Azure](app/nodejs.md) - Múltiplas atualizações, incluindo migração de versões perviosas e Configuração SDK
- [Monitorar as aplicações Python com Azure Monitor (pré-visualização)](app/opencensus-python.md) - Secção adicionada sobre a configuração dos exportadores do Monitor Azure.
- [Monitorize as suas apps sem alterações de código - auto-instrumentação para Azure Monitor Application Insights](app/codeless-overview.md) - Novo artigo.
- [Falha de carga SDK de resolução de problemas para aplicações web JavaScript](app/javascript-sdk-load-failure.md) - Novo artigo.

### <a name="containers"></a>Contentores
- [Como parar de monitorizar o seu cluster híbrido Kubernetes](containers/container-insights-optout-hybrid.md) - Secção adicionada para o Arco ativado kubernetes.
- [Configure Azure Arc permitiu que Kubernetes cluster com insights de contentores](containers/container-insights-enable-arc-enabled-clusters.md) - Novo artigo.
- [Configure Azure Red Hat OpenShift v4.x com insights do recipiente](containers/container-insights-azure-redhat4-setup.md) - Pré-requisitos atualizados.
- [Configurar informações de contentores Live Data (pré-visualização)](containers/container-insights-livedata-setup.md) - Nota removida sobre a funcionalidade que não está disponível no Governo dos EUA.

### <a name="insights"></a>Informações
- [FAQs - Solução monitor de desempenho de rede em Azure](insights/network-performance-monitor-faq.md) - FAQ adicionado para ExpressRoute Monitor.

### <a name="logs"></a>Registos
- [Eliminar e recuperar o espaço de trabalho Azure Log Analytics](logs/delete-workspace.md) - Comando PowerShell adicionado. Resolução de problemas atualizada.
- [Gerir os espaços de trabalho do Log Analytics no Azure Monitor](logs/manage-access.md) - Exemplo adicional para tabelas não permitidas na secção Azure RBAC.
- [Gerir a utilização e os custos dos Registos monitores Azure](logs/manage-cost-storage.md) - Detalhes adicionais no cálculo do tamanho dos dados. Alertas de volume de dados atualizados. Detalhes sobre os dados de segurança recolhidos pelo Azure Sentinel. Esclarecimento sobre a tampa de dados.
- [Utilize registos do Monitor Azure com aplicações lógicas Azure e limites de](logs/logicapp-flow-connector.md) automatização de energia.

### <a name="metrics"></a>Métricas
- [Azure Monitor suportado métricas por tipo de recurso](essentials/metrics-supported.md) - métricas atualizadas do SQL Server.


### <a name="platform-logs"></a>Registos da plataforma

- [Amostras de modelo do Gestor de Recursos para definições de diagnóstico](essentials/resource-manager-diagnostic-settings.md) - Correção para a definição de diagnóstico de registo de atividade.
- Enviar registo de [atividades Azure para log Analytics espaço de trabalho usando portal Azure](essentials/quick-collect-activity-log-portal.md) - Novo artigo.
- [Envie um registo de atividade azure para log analytics espaço de trabalho usando o modelo de Gestor de Recursos Azure](essentials/quick-collect-activity-log-arm.md) - Novo artigo.

Artigos novos e atualizados da reestruturação e consolidação de conteúdos de registo de plataforma

- [Registos de recursos do Archive Azure para a conta de armazenamento](./essentials/resource-logs.md#send-to-azure-storage)
- [Esquema de evento de registo de atividade azure](essentials/activity-log-schema.md)
- [Log de atividades Azure](essentials/activity-log.md)
- [Amostras de CLI monitor Azure](/cli-samples.md)
- [Amostras de PowerShell monitor Azure Monitor](/powershell-samples.md)
- [Instruções da API REST de Monitorização do Azure](essentials/rest-api-walkthrough.md)
- [Azure Resource Logs suportados serviços e esquemas](./essentials/resource-logs-schema.md)
- [Registos de recursos do Azure](essentials/resource-logs.md)
- [Recolher e analisar o registo de atividades do Azure no Azure Monitor](./essentials/activity-log.md)
- [Recolher registos de recursos Azure no espaço de trabalho Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Criar definições de diagnóstico para enviar registos e métricas da plataforma para destinos diferentes](essentials/diagnostic-settings.md)
- [Exportar o Registo de Atividades Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Descrição geral dos registos de plataforma Azure](essentials/platform-logs-overview.md)
- [Stream Azure plataforma registra para um centro de eventos](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [Ver eventos de registo de atividades do Azure no Azure Monitor](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Máquinas virtuais
- [Ativar informações em VM no portal Azure](./vm/vminsights-enable-portal.md) - Atualizado para incluir O Arco de Azure.
- [Ativar a visão geral dos insights de VM](vm/vminsights-enable-overview.md) - Atualizado para incluir O Arco de Azure.
- [O que são os conhecimentos em VM?](vm/vminsights-overview.md) - Atualizado para incluir Azure Arc.


### <a name="visualizations"></a>Visualizações
- Fontes de dados de [livros do Azure Monitor](visualize/workbooks-data-sources.md) - Alertas Adicionados e Secções de Endpoints Personalizados.
- [Resolução de problemas Azure Monitor insights baseados em livros](insights/troubleshoot-workbooks.md) - Novo artigo.
- [Atualizar as visualizações do Painel de Registos](logs/dashboard-upgrade.md) - Novo artigo.



## <a name="may-2020"></a>Maio de 2020

### <a name="general"></a>Geral

- [Azure Monitor FAQ](faq.md) - Secção adicionada para métricas.
- [Chave gerida pelo cliente Azure Monitor](logs/customer-managed-keys.md) - Várias alterações na preparação para a disponibilidade geral.
- [Definições políticas incorporadas para O Monitor Azure](./policy-reference.md) - Novo artigo.
- [Contas de armazenamento do cliente para ingestão de registos](logs/private-storage.md) - Novo artigo.
- [Gerir a utilização e os custos dos registos do Monitor Azure](logs/manage-cost-storage.md) - Faturação proporcional de cluster adicionada.
- [Utilize o Link Privado Azure para ligar de forma segura as redes ao Azure Monitor](logs/private-link-security.md) - Novo artigo.


#### <a name="new-resource-manager-template-samples"></a>Novas amostras de modelo do gestor de recursos 
- [Amostras de modelo de gestor de recursos para monitor Azure](/resource-manager-samples.md)
- [Amostras de modelo de gestor de recursos para grupos de ação](alerts/resource-manager-action-groups.md)
- [Amostras de modelo de gestor de recursos para agentes](agents/resource-manager-agent.md)
- [Amostras de modelo de gestor de recursos para insights de contentores](containers/resource-manager-container-insights.md)
- [Amostras de modelo de gestor de recursos para insights de VM](vm/resource-manager-vminsights.md)
- [Amostras de modelo do gestor de recursos para definições de diagnóstico](essentials/resource-manager-diagnostic-settings.md)
- [Amostras de modelo de gestor de recursos para espaços de trabalho do Log Analytics](logs/resource-manager-workspace.md)
- [Amostras de modelo de gestor de recursos para consultas de registo](logs/resource-manager-log-queries.md)
- [Amostras de modelo do gestor de recursos para regras de alerta de consulta de registo](alerts/resource-manager-alerts-log.md)
- [Amostras de modelo de gestor de recursos para regras de alerta métrico](alerts/resource-manager-alerts-metric.md)
- [Amostras de modelo de gestor de recursos para livros](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>Agentes
- [Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](agents/diagnostics-extension-windows-install.md) - Detalhe adicional na configuração dos diagnósticos.
- [Visão geral do agente Log Analytics](agents/log-analytics-agent.md) - Adicione versões Linux suportadas.

### <a name="application-insights"></a>Application Insights

- [Monitorar aplicações em execução em Funções Azure com Insights de Aplicação - Monitor Azure](app/monitor-functions.md) - Novo artigo.
- [Monitorize Node.js serviços com Azure Application Insights](app/nodejs.md) - Atualizações gerais incluindo nova secção sobre migração a partir de versões anteriores.
- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Endereços IP adicionados para webhooks e Governo dos EUA.
- [Monitorize as aplicações no Serviço Azure Kubernetes (AKS) com Insights de Aplicação - Azure Monitor](app/kubernetes-codeless.md) - Novo artigo.
- [Resolução de problemas sem dados - Insights de Aplicação para .NET](app/asp-net-troubleshoot-no-data.md) - Secção adicionada na recolha de registos com traços de dotnet.
- [Utilize a Análise de Alteração de Aplicações no Monitor Azure para encontrar problemas com aplicações web](app/change-analysis.md) - Várias atualizações na funcionalidade De Análise de Alterações.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Artigos novos e atualizados para pré-visualização de aplicações baseadas no espaço de trabalho
- [Azure Monitor Application Insights esquema de recursos baseado no espaço de trabalho](app/apm-tables.md)
- [Criar um novo recurso baseado no espaço de trabalho Azure Monitor Insights](app/create-workspace-resource.md)
- [() expressão em consultas de log do Azure Monitor](logs/app-expression.md)
- [Âmbito de consulta de registo no Azure Monitor Log Analytics](logs/scope.md)
- [Consulta através de recursos com o Azure Monitor](logs/cross-workspace-query.md)
- [Propriedades padrão em registos de registos do Azure Monitor](./logs/log-standard-columns.md)
- [Estrutura de Registos monitores Azure](./logs/data-platform-logs.md)





### <a name="containers"></a>Contentores
- [Como ativar as introspeções do Contentor](containers/container-insights-onboard.md) - Tabela de configuração de firewall atualizada.
- [Como atualizar insights do Contentor para métricas](containers/container-insights-update-metrics.md) - Atualização para usar identidades geridas para recolher métricas.
- [Custo de monitorização para insights de contentores](containers/container-insights-cost.md) - Novo artigo.
- [Configurar informações de recipientes Live Data (pré-visualização)](containers/container-insights-livedata-setup.md) - Suporte para nova ligação de funções de cluster.

### <a name="insights"></a>Informações
- [Monitor Azure para Azure Cache para Redis (pré-visualização)](insights/redis-cache-insights-overview.md) - Novo artigo.
- [Monitor Key Vault com Monitor Azure para Cofre de Chaves (pré-visualização)](./insights/key-vault-insights-overview.md) - Novo artigo.

### <a name="logs"></a>Registos
- [Crie & configurar o Log Analytics com PowerShell](logs/powershell-workspace-configuration.md) - Secção de resolução de problemas adicionada.
- [Crie um espaço de trabalho Log Analytics no portal Azure](logs/quick-create-workspace.md) - Secção de resolução de problemas adicionada.
- [Crie um espaço de trabalho Log Analytics utilizando a secção Azure CLI](logs/quick-create-workspace-cli.md) - Secção de resolução de problemas adicionada.
- [Eliminar e recuperar o espaço de trabalho do Azure Log Analytics](logs/delete-workspace.md) - Informações atualizadas sobre a recuperação de um espaço de trabalho eliminado.
- [Funções em consultas de registo do Monitor Azure](logs/functions.md) - Nota removida sobre funções que não contenham outras funções.
- [Estrutura de Registos monitores Azure](./logs/data-platform-logs.md) - Descrições de propriedade esclarecidas para tabela de Insights de Aplicação.
- [Utilize registos de monitores Azure com aplicações lógicas Azure e automatização](logs/logicapp-flow-connector.md) de energia - Secção de limites adicionados.
- [Utilize o PowerShell para criar e configurar um espaço de trabalho de análise de log](logs/powershell-workspace-configuration.md) - secção de resolução de problemas adicionada.


### <a name="metrics"></a>Métricas
- [Azure Monitor suportado métricas por tipo de recurso](essentials/metrics-supported.md) - métricas esclarecidas dos hóspedes e encaminhamento de métricas. 

### <a name="solutions"></a>Soluções
- [Otimize o seu ambiente de Diretório Ativo com o Azure Monitor](insights/ad-assessment.md) - Adicionado Windows Server 2019 para versões suportadas.
- [Otimize o seu ambiente de Servidor SQL com monitor Azure](insights/sql-assessment.md) - Adicionado às versões suportadas do SQL Server.


### <a name="virtual-machines"></a>Máquinas virtuais
- [Ativar a visão geral dos insights VM](vm/vminsights-enable-overview.md) - Adicionado às versões suportadas do Ubuntu Server. Regiões apoiadas adicionadas para o espaço de trabalho Log Analytics.
- [Como traçar o desempenho com insights VM](vm/vminsights-performance.md) - Secção de limitações adicionadas para métricas indisponíveis.

### <a name="visualizations"></a>Visualizações
- [Azure Monitor Workbooks e Azure Resource Manager Templates](visualize/workbooks-automate.md) - Atualização do Gestor de Recursos Adicionado para implementar um modelo de livro.
- [Grupos de livros de trabalho do Azure Monitor](./visualize/workbooks-groups.md) - Novo artigo.
- [Azure Monitor Workbooks - Transforme os dados da JSON com o JSONPath](visualize/workbooks-jsonpath.md) - Novo artigo.


## <a name="april-2020"></a>Abril de 2020

### <a name="general"></a>Geral

- [Chave gerida pelo cliente do Azure Monitor](logs/customer-managed-keys.md) - Secção adicionada em operações assíncronas
- [Gerir os espaços de trabalho do Log Analytics no Azure Monitor](logs/manage-access.md) - Secções de registos personalizados atualizados.

### <a name="alerts"></a>Alertas

- [Regras de ação para alertas do Azure Monitor](alerts/alerts-action-rules.md) - Vídeo adicionado.
- [Visão geral do alerta e monitorização da notificação em Azure](alerts/alerts-overview.md) - Vídeo adicionado.

### <a name="application-insights"></a>Application Insights

- [Application Map in Azure Application Insights](app/app-map.md) - Adicionados nomes de funções em nuvem config para java agente.
- [Azure Application Insights .NET Agent API reference](app/status-monitor-v2-api-reference.md) - Referência API Consolidada.
- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Endereços IP atualizados para App Insights e ACIs de Analítica de Registo, webhooks do grupo action, Governo Azure US.
- [Monitorize aplicações java em qualquer lugar](app/java-standalone-config.md) - Novo artigo.
- [Monitorize as aplicações de Java em qualquer ambiente](app/java-in-process-agent.md) - Novo artigo.
- [Monitorize as aplicações java em qualquer ambiente](app/java-standalone-arguments.md) - Novo artigo.
- [Monitorar aplicações java em execução no local](app/java-on-premises.md) - Novo artigo.
- [Remover Informações sobre aplicações no Estúdio Visual](app/remove-application-insights.md) - Novo artigo.
- [Amostragem de telemetria em Azure Application Insights](app/sampling.md) - Fixar em amostra de taxa fixa em Python.

### <a name="containers"></a>Contentores

- [Configure Azure Red Hat OpenShift v4.x com insights de recipiente](containers/container-insights-azure-redhat4-setup.md) - Novo artigo.
- [Como corrigir manualmente problemas de sincronização do ServiceNow](alerts/itsmc-resync-servicenow.md) - Novo artigo.
- [Como parar de monitorizar o seu cluster V4 Azure e Red Hat OpenShift](containers/container-insights-optout-openshift-v4.md) - Novo artigo.
- [Como parar de monitorizar o seu cluster Azure Red Hat OpenShift v3](containers/container-insights-optout-openshift-v3.md) - Novo artigo.
- [Como parar de monitorizar o seu cluster híbrido Kubernetes](containers/container-insights-optout-hybrid.md) - Novo artigo.

### <a name="insights"></a>Informações

- [Monitor Azure Key Vaults com Monitor Azure para Cofres chave (pré-visualização)](insights/key-vault-insights-overview.md) - Novo artigo.

### <a name="logs"></a>Registos

- [Limites de serviço do Azure Monitor](service-limits.md) - Aceleração de consulta do utilizador adicionada.
- [Gerir a utilização e os custos dos registos do Monitor Azure](logs/manage-cost-storage.md) - Faturação adicionada para clusters de Logs. A consulta de Kusto adicionada para permitir que os clientes com o nível de preços por nó legado para determinar se devem mudar para um nível de Reserva por Gb ou Capacidade.

### <a name="metrics"></a>Métricas

- [Características avançadas do Azure Metrics Explorer](essentials/metrics-charts.md) - Secção de agregação adicionada.

### <a name="workbooks"></a>Livros

- [Azure Monitor Workbooks e Azure Resource Manager Modelos](visualize/workbooks-automate.md) - Modelo de gestor de recursos adicionado para implantar um modelo de livro.

## <a name="march-2020"></a>Março de 2020

### <a name="general"></a>Geral

- [Visão geral do Azure Monitor](overview.md) - Vídeo geral do Monitor Azure Adicionado.
- [Configuração de chave gerida pelo cliente Azure Monitor](logs/customer-managed-keys.md) - Atualizações gerais.
- [Referência de dados do Azure Monitor](/azure/azure-monitor/reference/) - Novo site.

### <a name="alerts"></a>Alertas

- [Criar, visualizar e gerir alertas de registo de atividade no Azure Monitor](alerts/alerts-activity-log.md) - Explicação adicional do modelo do Gestor de Recursos.
- [Entenda como os alertas métricos funcionam no Azure Monitor.](alerts/alerts-metric-overview.md) - Atualizado para apoio do governo.
- [Resolução de problemas Alertas e notificações do Azure Monitor](alerts/alerts-troubleshoot.md) - Novo artigo.

### <a name="application-insights"></a>Application Insights

- [Automatizar insights de aplicação Azure com PowerShell](app/powershell.md) - Exemplos ARMClient adicionados.
- [Exportação contínua de telemetria a partir de Insights de Aplicação](app/export-telemetry.md) - Adicione tabela com detalhes da estrutura de exportação.
- [Ative o Snapshot Debugger para aplicações .NET no Azure App Service](app/snapshot-debugger-appservice.md) - Exemplo do modelo do Gestor de Recursos Adicionado.
- [Gerir a utilização e os custos para a Azure Application Insights](app/pricing.md) - Informação adicional sobre o alerta da tampa de dados.
- [Monitorar aplicações Python com Monitor Azure (pré-visualização)](app/opencensus-python.md) - Métricas padrão adicionadas.
- [Suporte de mapa de origem para aplicações JavaScript - Azure Monitor Application Insights](app/source-map-support.md) - Novo artigo.

### <a name="containers"></a>Contentores

- [Azure Monitor FAQ](faq.md) - Atualização para informações sobre o contentor.
- [Configure a monitorização da GPU com insights de contentores](containers/container-insights-gpu-monitoring.md) - Novo artigo.

### <a name="insights"></a>Informações

- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Data de depreciação atualizada.

### <a name="logs"></a>Registos

- [Otimize as consultas de log no Azure Monitor](logs/query-optimization.md) - Condições de CPU adicionadas para análise de XML e JSON.
- [Eliminar e recuperar o espaço de trabalho do Azure Log Analytics](logs/delete-workspace.md) - Sessão de problemas adicionada.
- [Utilize registos do Monitor Azure com aplicações lógicas Azure e automatização de energia](logs/logicapp-flow-connector.md) - atualizado para o novo conector Azure Monitor.

### <a name="metrics"></a>Métricas

- [Depreciação das métricas do disco no portal Azure](essentials/portal-disk-metrics-deprecation.md) - Novo artigo.
- [Tutorial - Criar um gráfico de métricas no Azure Monitor](essentials/tutorial-metrics-explorer.md) - Vídeo adicionado.

### <a name="platform-logs"></a>Registos da plataforma

- [Recolher e analisar o registo de atividades do Azure no Azure Monitor](./essentials/activity-log.md) - Reescreva melhor a recolha do registo de atividade com definições de diagnóstico.

### <a name="virtual-machines"></a>Máquinas virtuais

- [Monitor Azure máquinas virtuais com Azure Monitor](vm/monitor-vm-azure.md) - Novo artigo.
- [Quickstart: Monitor Azure virtual machines with Azure Monitor](vm/quick-monitor-azure-vm.md) - Atualizado para adicionar insights VM.
- [Alertas de introspeções VM](vm/vminsights-alerts.md) - Novo artigo.
- [Ativar a visão geral dos insights de VM](vm/vminsights-enable-overview.md) - Links de descarregamento de agentes atualizados.

Atualizações gerais para a disponibilidade geral de insights de VM

- [O que são os conhecimentos em VM?](vm/vminsights-overview.md)
- [Perguntas vM insights (GA) frequentemente feitas](vm/vminsights-ga-release-faq.md) 
- [Ativar os conhecimentos de VM utilizando a Política Azure](./vm/vminsights-enable-policy.md) 
- [Como traçar o desempenho com insights VM](vm/vminsights-performance.md)
- [Como consultar registos a partir de insights VM](vm/vminsights-log-search.md)
- [Ver dependências de aplicativos com insights VM](vm/vminsights-maps.md) 

### <a name="visualizations"></a>Visualizações

- [Visualização de dados do Azure Monitor](visualizations.md) - Atualizado para notar a depreciação planeada do View Designer.

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="agents"></a>Agentes

Múltiplas atualizações como parte da reescrita do conteúdo de extensão de diagnóstico.

- [Visão geral dos agentes de monitorização Azure](agents/agents-overview.md) - Tabelas reestruturadas para clarificar melhor as características únicas de cada agente.
- [Visão geral da extensão do Azure Diagnostics](agents/diagnostics-extension-overview.md) - Reescrita completa.
- [Utilize o armazenamento de bolhas para iIS e armazenamento de mesa para eventos no Azure Monitor](essentials/diagnostics-extension-logs.md) - Reescrita geral para atualização e clareza.
- [Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](agents/diagnostics-extension-windows-install.md) - Novo artigo. 
- [Esquema de extensão de diagnóstico do Windows](agents/diagnostics-extension-schema-windows.md) - Reorganizado.
- [Enviar dados da extensão de diagnóstico do Windows Azure para Azure Event Hubs](agents/diagnostics-extension-stream-event-hubs.md) - Completamente reescrito e atualizado.
- [Armazenar e ver dados de diagnóstico no Azure Storage](../cloud-services/diagnostics-extension-to-storage.md) - Completamente reescrito e atualizado.
- [Log Analytics extensão de máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md) - Melhor esclarece a relação com o agente Log Analytics.
- [Extensão da máquina virtual Azure Monitor para Linux](../virtual-machines/extensions/oms-linux.md) - Melhor esclarece a relação com o agente Log Analytics.

### <a name="application-insights"></a>Application Insights

- [Cadeias de ligação em Azure Application Insights](app/sdk-connection-string.md) - Novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções

#### <a name="container-insights"></a>Insights de contentores

- [Integre o Azure Ative Directory com o Serviço Azure Kubernetes](../aks/azure-ad-integration-cli.md) - Nota adicionada para criar uma aplicação de cliente para apoiar o cluster ativado por Kubernetes PARA suportar insights de contentores.

#### <a name="vm-insights"></a>Insights VM

- [Perguntas sobre a forma](vm/vminsights-ga-release-faq.md) como os dados de desempenho são armazenados.

#### <a name="office-365"></a>Office 365

- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Data de depreciação atualizada.


### <a name="logs"></a>Registos

- [Otimize consultas de registo no Azure Monitor](logs/query-optimization.md) - Novo artigo.
- [Gerir o uso e os custos para registos Azure Monitor](logs/manage-cost-storage.md) - Consultas melhoradas de amostras para ajudar a entender o seu uso.

### <a name="metrics"></a>Métricas

- [Métricas da plataforma Azure Monitor exportáveis através de Definições de Diagnóstico](essentials/metrics-supported-export-diagnostic-settings.md) - Secção adicionada sobre alteração ao comportamento de nulos e valores zero.

### <a name="visualizations"></a>Visualizações

Vários novos artigos para visualizar designer para guia de conversão de livros de trabalho.

- [Azure Monitor ver designer para guia de transição de livros](visualize/view-designer-conversion-overview.md) - Novo artigo.
- [Azure Monitor ver designer para opções de conversão de livros](visualize/view-designer-conversion-options.md) de trabalho - Novo artigo.
- [Azure Monitor vê designer para conversões de azulejos](visualize/view-designer-conversion-tiles.md) de livros - Novo artigo.
- [Azure Monitor ver designer para resumo de conversão de livros e acesso](visualize/view-designer-conversion-access.md) - Novo artigo.
- [Azure Monitor vê designer para trabalhos de conversão de tarefas comuns](visualize/view-designer-conversion-tasks.md) - Novo artigo.
- [Azure Monitor vê designer para exemplos de conversão de livros](visualize/view-designer-conversion-examples.md) - Novo artigo.

## <a name="january-2020"></a>Janeiro de 2020

### <a name="general"></a>Geral

- [O que é monitorizado pelo Azure Monitor?](monitor-reference.md) - Novo artigo.

### <a name="agents"></a>Agentes

- [Recolher dados de registo com o agente Azure Log Analytics](agents/log-analytics-agent.md) - Tabela atualizada de requisitos de firewall de rede.

### <a name="alerts"></a>Alertas

- [Criar e gerir grupos de ação no portal Azure](alerts/action-groups.md) - Definição removida para funções v2 que já não são necessárias.
- [Crie um alerta métrico com um modelo de Gestor de Recursos](alerts/alerts-metric-create-templates.md) - Adicione exemplo para o parâmetro *IgnoreDataBefore.*  Constrangimentos adicionais sobre regras de vários critérios.
- [Utilização do alerta de log analytics REST API](alerts/api-alerts.md) - exemplo JSON corrigido.

### <a name="application-insights"></a>Application Insights

- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Atualize a secção de teste de Disponibilidade com a forma de adicionar uma regra de entrada portuária para permitir o tráfego utilizando grupos de segurança da rede Azure.
- [Problemas de resolução de problemas com O Perfil de Insights de Aplicação Azure](app/profiler-troubleshooting.md) - Resolução de problemas geral atualizado.
- [Amostragem de telemetria em Azure Application Insights](app/sampling.md) - Atualizado e reestruturado para melhorar a legibilidade com base no feedback do cliente.

### <a name="data-security"></a>Segurança de dados

- [Configuração de chave gerida pelo cliente Azure Monitor](logs/customer-managed-keys.md) - Novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções

#### <a name="container-insights"></a>Insights de contentores

- [Configure a recolha de dados do agente de informações do agente](containers/container-insights-agent-config.md) de informação do contentor - Adicione detalhes adicionais para atualizar o agente no Azure Red Hat OpenShift e adicione informações adicionais para distinguir os métodos de atualização do agente.
- [Criar alertas de desempenho para insights de contentores](./containers/container-insights-log-alerts.md) - Informações revistas e passos atualizados para criar um alerta sobre os dados de desempenho armazenados no espaço de trabalho utilizando alertas de contexto de espaço de trabalho.
- [Monitorização de Kubernetes com insights de contentores](containers/container-insights-analyze.md) - Atualize tanto o artigo de visão geral como o artigo de análise relativo ao suporte de clusters Do Windows Kubernetes.
- [Configure Azure Red Hat OpenShift clusters com insights de contentores](containers/container-insights-azure-redhat-setup.md) - Adicione detalhes para atualizar o agente no Azure Red Hat OpenShift, e adicione informações adicionais para distinguir os métodos de upgrade do agente.
- [Configure os clusters Híbridos Kubernetes com insights de contentores](containers/container-insights-hybrid-setup.md) - Atualizado para refletir um suporte adicional para a porta segura:10250 com o cAdvisor do Kubelet.
- [Como gerir o agente de insights do contentor](containers/container-insights-manage-agent.md) - Detalhes atualizados relacionados com o comportamento e configuração de raspagem métrica com Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.
- [Configure Container insights Prometheus Integration](containers/container-insights-prometheus-integration.md) - Detalhes atualizados relacionados com o comportamento e configuração de raspagem métrica com Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.
- [Como atualizar insights de recipientes para métricas](containers/container-insights-update-metrics.md) - Detalhes atualizados relacionados com o comportamento e configuração de raspagem métrica com Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.

#### <a name="vm-insights"></a>Insights VM

- [Os conhecimentos vM (GA) frequentemente perguntei](vm/vminsights-ga-release-faq.md) - Adicionar informações sobre a atualização do espaço de trabalho e agentes para a nova versão.

#### <a name="office-365"></a>Office 365

- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Detalhes adicionais e FAQ sobre migração para a solução do Office 365 em Azure Sentinel. Secção de embarque removida.

### <a name="logs"></a>Registos

- [Gerir os espaços de trabalho do Log Analytics no Azure Monitor](logs/manage-access.md) - Atualizações para não ações.
- [Gerir a utilização e os custos dos Registos monitores Azure](logs/manage-cost-storage.md) - Esclarecimento adicional sobre o cálculo do volume de dados na secção Modelo de Preços.
- [Utilize modelos de Gestor de Recursos Azure para criar e configurar um espaço de trabalho de Log Analytics](./logs/resource-manager-workspace.md) - Modelo atualizado com novos níveis de preços.

### <a name="platform-logs"></a>Registos da plataforma

- [Recolher registo de atividade azure com definições de diagnóstico- Monitor Azure](./essentials/activity-log.md) - Informações adicionais sobre propriedades alteradas.
- [Exportar o Registo de Atividades Azure](./essentials/activity-log.md#legacy-collection-methods) - Atualizado para alterações de UI. 

## <a name="december-2019"></a>Dezembro de 2019

### <a name="agents"></a>Agentes

- [Ligue os computadores Linux ao Azure Monitor](agents/agent-linux.md) - Novo artigo.

### <a name="alerts"></a>Alertas

- [Crie um alerta métrico com um modelo de Gestor de Recursos](alerts/alerts-metric-create-templates.md) - Exemplo adicionado para métrica personalizada.
- [Criação de Alertas com Limiares Dinâmicos no Monitor Azure](alerts/alerts-dynamic-thresholds.md) - Secção adicionada na interpretação de gráficos de limiares dinâmicos.
- [Visão geral do alerta e monitorização da notificação em Azure](alerts/alerts-overview.md) - Consulta de gráfico de recurso atualizado.
- [Recursos suportados para alertas métricos no Azure Monitor](alerts/alerts-metric-near-real-time.md) - Atualização para métricas e dimensões suportadas.
- [Switch from legacy Log Analytics alerta API para novo Azure Alerts API](alerts/alerts-log-api-switch.md) - Nota adicionada no nome de alerta modificado.
- [Entenda como os alertas métricos funcionam no Azure Monitor.](alerts/alerts-metric-overview.md) - Tipos de recursos suportados adicionados para monitorização em escala.

### <a name="application-insights"></a>Application Insights

- [Application Insights for Worker Service apps (non-HTTP apps)](app/worker-service.md) - Nível de registo predefinido ao código C#. Versão de referência de pacote atualizada.
- [ApplicationInsights.config referência - Azure](app/configuration-with-applicationinsights-config.md) - Código de amostra atualizado.
- [Automatizar insights de aplicação Azure com PowerShell](app/powershell.md) - Atualização para o modelo de Gestor de Recursos.
- [Crie um novo recurso Azure Application Insights](app/create-new-resource.md) - Nota adicionada ao nome globalmente único.
- [Diagnosticar com Live Metrics Stream - Azure Application Insights](app/live-stream.md) - Atualizado ASP.NET requisito da versão Core SDK.
- [Contadores de eventos em Application Insights](app/eventcounters.md) - Categoria atualizada e tabela para personalizadosMetrics.
- [Explore os registos de vestígios de Java em Azure Application Insights](app/java-trace-logs.md) - Configuração adicionada para o limiar de registo do agente Java.
- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Endereços IP atualizados para Live Metrics Stream.
- [Monitor Azure app services performance](app/azure-web-apps.md) - Suporte adicional para ASP.NET Core 3.0. 
- [Monitor python aplicações com Azure Monitor (pré-visualização)](app/opencensus-python.md) - Esclarecimento adicionado para o mapeamento de esquemas De Pitão OpenCensus para Azure . Esquema de monitorização
- [Notas de lançamento para Azure Application Insights](app/release-notes.md) - Notas adicionadas para versões mais antigas.
- [Canais de telemetria em Azure Application Insights](app/telemetry-channels.md) - Duração atualizada para dados descartados durante um período prolongado de ligação perdida.
- [Amostragem de telemetria em Azure Application Insights](app/sampling.md) - Corte de código corrigido para telemetriaInitializador personalizado.
- [Troubleshoot Application Insights em um projeto web java](app/java-troubleshoot.md) - Declaração removida sobre não apoiar a coleção de dependência em JDK 9.

### <a name="insights-and-solutions"></a>Insights e soluções

- [Insights de contentores Perguntas frequentes](./faq.md) - Pergunta adicionada nos campos de imagem e nome.
- [Solução Azure SQL Analytics em Azure Monitor](insights/azure-sql.md) - Base de Dados Atualizada aguarda suporte de instância gerida.
- [Configure a recolha de dados do agente de insights do contentor](containers/container-insights-agent-config.md) - Definição adicional para enrich_container_logs.
- [Configure os agrupamentos Híbridos Kubernetes com insights de contentores](containers/container-insights-hybrid-setup.md) - Secção de resolução de problemas adicionada.
- [Monitore o estado de replicação do Diretório Ativo com o Azure Monitor](insights/ad-replication-status.md) - .NET Framework pré-requisito atualizado.
- [Solução monitor de desempenho de rede em Azure](insights/network-performance-monitor.md) - Regiões apoiadas adicionadas.
- [Otimize o seu ambiente de Diretório Ativo com o Azure Monitor](insights/ad-assessment.md) - .NET Framework pré-requisito atualizado.
- [Otimize o seu ambiente de Servidor SQL com monitor Azure](insights/sql-assessment.md) Monitor - .NET Framework pré-requisito atualizado.
- [Otimize o ambiente de Gerente de Operações do System Center com Azure Log Analytics](insights/scom-assessment.md) - .NET Framework pré-requisito atualizado.
- [Conexões suportadas com o IT Service Management Connector em Azure Log Analytics](alerts/itsmc-connections.md) - Acrescentou Nova Iorque para pré-requisito de identificação do cliente e segredo de cliente.

### <a name="logs"></a>Registos

- [Eliminar e recuperar o espaço de trabalho Azure Log Analytics](logs/delete-workspace.md) - método PowerShell adicionado.
- [Conceber a sua implementação de Registos de Monitor Azure](logs/design-logs-deployment.md) - A taxa de ingestão para um espaço de trabalho aumentou.

### <a name="metrics"></a>Métricas

- [Métricas da plataforma Azure Monitor exportáveis através de Definições de Diagnóstico](essentials/metrics-supported-export-diagnostic-settings.md) - Novo artigo.

### <a name="platform-logs"></a>Registos da plataforma

Vários artigos atualizados como parte da reestruturação de conteúdos para registos de plataformas com base em nova funcionalidade para configurar o registo de atividades utilizando definições de diagnóstico.

- [Registos de recursos do Archive Azure para a conta de armazenamento](./essentials/resource-logs.md#send-to-azure-storage)
- [Esquema de evento de registo de atividade azure](essentials/activity-log-schema.md)
- [Limites de serviço do Azure Monitor](service-limits.md)
- [Recolher e analisar registos de atividades do Azure no espaço de trabalho log Analytics](./essentials/activity-log.md)
- [Recolher registo de atividade do Azure com definições de diagnóstico (pré-visualização) - Monitor Azure](./essentials/activity-log.md)
- [Colete registos de atividades Azure em um espaço de trabalho Log Analytics através de inquilinos Azure](./essentials/activity-log.md)
- [Recolher registos de recursos Azure no espaço de trabalho Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Crie uma definição de diagnóstico em Azure utilizando o modelo do Gestor de Recursos](./essentials/resource-manager-diagnostic-settings.md)
- [Criar definição de diagnóstico para recolher registos e métricas em Azure](essentials/diagnostic-settings.md)
- [Exportar o Registo de Atividades Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Descrição geral dos registos de plataforma Azure](essentials/platform-logs-overview.md)
- [Stream Azure monitorizando dados para o centro de eventos](essentials/stream-monitoring-data-event-hubs.md)
- [Stream Azure plataforma registra para um centro de eventos](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Inícios rápidos e tutoriais

- [Criar um gráfico de métricas no Azure Monitor](essentials/tutorial-metrics-explorer.md) - Novo artigo.
- [Colete registos de recursos a partir de um Recurso Azure e analise com o Azure Monitor](essentials/tutorial-resource-logs.md) - Novo artigo.
- [Monitorize um recurso Azure com Azure Monitor](essentials/quick-monitor-azure-resource.md) - Novo artigo.
   
## <a name="next-steps"></a>Passos seguintes

- Se quiser contribuir para a documentação do Azure Monitor, consulte o [Guia de Contribuintes docs](/contribute/).