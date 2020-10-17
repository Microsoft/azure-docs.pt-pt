---
title: Novidades na documentação do Azure Monitor
description: Atualizações significativas da documentação do Azure Monitor atualizadas todos os meses.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 07/08/2020
ms.openlocfilehash: c9679fec1d9c5d501ead03bcfbf28132d9e3b90a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151809"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>O que há de novo na documentação do Monitor Azure?

Este artigo fornece listas de artigos do Azure Monitor que são novos ou foram significativamente atualizados. Será atualizado na primeira semana de cada mês para incluir atualizações de artigos do mês anterior.

## <a name="september-2020"></a>Setembro de 2020

### <a name="general"></a>Geral
- [Azure Monitor FAQ](faq.md) - Secção adicionada na OpenTelemetry.

### <a name="agents"></a>Agentes
- [Visão geral do agente do Azure Monitor](platform/azure-monitor-agent-overview.md) - Fatores de decisão adicionados para mudar para novo agente.
- [Visão geral dos agentes de monitorização Azure](platform/agents-overview.md) - Suporte adicionado para o Windows 10.

### <a name="alerts"></a>Alertas
- [Crie um alerta de registo com o modelo Azure Resource Manager](platform/alerts-log-create-templates.md) - Novo artigo.
- [Alertas métricos Azure de resolução de problemas](platform/alerts-troubleshoot-metric.md) - Secção adicionada sobre o modelo ARM de exportação para uma regra de alerta métrico.

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
- [Configure Azure Arc permitiu que kubernetes cluster com Azure Monitor para recipientes](insights/container-insights-enable-arc-enabled-clusters.md) - Orientação adicional para permitir a monitorização usando o principal do serviço.
- [Implantação & métricas HPA com monitor Azure para contentores](insights/container-insights-deployment-hpa-metrics.md) - Novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções
- [Monitor Azure para Cache Azure para Redis](insights/redis-cache-insights-overview.md) - Designação de pré-visualização removida.
- [Monitor Azure para Redes (Pré-visualização)](insights/network-insights-overview.md) - Secções de Conectividade e Tráfego Adicionados.
- [Conector de Gestão de Serviços de TI - Exportação Segura em Monitor Azure](platform/it-service-management-connector-secure-webhook-connections.md) - Novo artigo.
- [Conector de Gestão de Serviços de TI em Azure Monitor](platform/itsmc-connections.md) - Nota sobre as integrações cherwell e provance ITSM.
- [Monitor Key Vault com Monitor Azure para Cofre de Chave](insights/key-vault-insights-overview.md) - Designação de pré-visualização removida.

### <a name="logs"></a>Registos
- Consultas de auditoria em consultas de registo do [Azure Monitor](log-query/query-audit.md) - Novo artigo.
- [Chave gerida pelo cliente Azure Monitor](platform/customer-managed-keys.md) - Lockbox de cliente adicionado.
- [Azure Monitor Logs Agrupamentos Dedicados](log-query/logs-dedicated-clusters.md) - Novo artigo.
- [Conceber a sua implementação de Registos de Monitor Azure](platform/design-logs-deployment.md) - Secção de limite de taxa de volume de escala e ingestão atualizada.
- [Log âmbito de consulta em Azure Monitor Log Analytics](log-query/scope.md) - Atualizações para incluir aplicações baseadas no espaço de trabalho.
- [Registos no Azure Monitor](platform/data-platform-logs.md) - Atualizações para incluir aplicações baseadas no espaço de trabalho.
- [Colunas padrão nos registos de registos do Monitor Azure](platform/log-standard-columns.md) - Atualizações para incluir aplicações baseadas em espaço de trabalho.
- [Limites de serviço do Azure Monitor](service-limits.md) - Limites atualizados para a aceleração da consulta do utilizador.
- [Utilização de contas de armazenamento geridas pelo cliente no Azure Monitor Log Analytics](platform/private-storage.md) - Artigo reescrito.
- [Visualização e análise de dados em Azure Log Analytics](./platform/data-platform-logs.md) - Atualizações para incluir aplicações baseadas no espaço de trabalho.


### <a name="platform-logs"></a>Registos da plataforma
- [Esquema de evento de registo de atividade azure - Monitor Azure](platform/activity-log-schema.md) - níveis de gravidade adicionados.
- [Amostras de modelo do gestor de recursos para definições de diagnóstico](samples/resource-manager-diagnostic-settings.md) - Amostra adicionada para conta de armazenamento Azure.

### <a name="visualizations"></a>Visualizações
- [Visualizações de gráficos de livros do Azure Monitor](platform/workbooks-chart-visualizations.md) - Novo artigo.
- [Renderejador de barras compósitos Azure Monitor](platform/workbooks-composite-bar.md) - Novo artigo.
- [Visualizações de gráficos de livro do Azure Monitor](platform/workbooks-graph-visualizations.md) - Novo artigo.
- [Visualizações da grelha de trabalho do Azure Monitor](platform/workbooks-grid-visualizations.md) - Novo artigo.
- Visualizações do pente de mel do [Azure Monitor](platform/workbooks-honey-comb.md) - Novo artigo.
- [Visualizações de texto de livro do Azure Monitor](platform/workbooks-text-visualizations.md) - Novo artigo.
- Visualizações de azulejos do [Azure Monitor](platform/workbooks-tile-visualizations.md) - Novo artigo.
- [Visualizações de árvores de livro do Azure Monitor](platform/workbooks-tree-visualizations.md) - Novo artigo.




## <a name="august-2020"></a>Agosto de 2020

### <a name="general"></a>Geral

- [O que é monitorizado pelo Azure Monitor](monitor-reference.md) - Atualizado para incluir o agente Azure Monitor.


### <a name="agents"></a>Agentes
- [Visão geral do agente do Azure Monitor](platform/azure-monitor-agent-overview.md) - Novo artigo.
- [Ativar o Azure Monitor para um ambiente híbrido](insights/vminsights-enable-hybrid.md) - versão atualizada do agente de dependência.
- [Visão geral dos agentes de monitorização Azure](platform/agents-overview.md) - Agente Azure Monitor adicionado e tabela de suporte de OS consolidado.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Artigos novos e atualizados da reestruturação do conteúdo do agente
- [Ativar o Monitor Azure para visão geral dos VMs](insights/vminsights-enable-overview.md)
- [Instale o agente Log Analytics nos computadores Linux](platform/agent-linux.md)
- [Instale o agente Log Analytics nos computadores Windows](platform/agent-windows.md)
- [Visão geral do agente do Log Analytics](platform/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights para aplicações web JavaScript](app/javascript.md) - Secção adicionada clarificando a correlação e configuração do servidor do cliente para a correlação CORS.
- [Criar um novo recurso baseado no espaço de trabalho Azure Monitor Insights](app/create-workspace-resource.md) - Capacidades adicionais fornecidas por aplicações baseadas no espaço de trabalho.
- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Endereços IP atualizados para transmissão de métricas ao vivo.
- [Monitorize as aplicações java em qualquer ambiente - Azure Monitor Application Insights](app/java-in-process-agent.md) - Tabela adicionada para telemetria personalizada suportada.
- [Plugin De Reação Nativa para Informações JavaScript SDK](app/javascript-react-native-plugin.md) - Novo artigo.
- [Reagir plugin para Informações JavaScript SDK](app/javascript-react-plugin.md) - Novo artigo.
- [Amostra de modelo do Gestor de Recursos para criar aplicações de função Azure com monitorização de Insights de Aplicação](samples/resource-manager-function-app.md) - Novo artigo.
- [Amostras de modelo de gestor de recursos para criar aplicações web Azure App Services com monitorização de Apps Insights](samples/resource-manager-web-app.md) - Novo artigo.
- [Análise de utilização com Azure Application Insights](app/usage-overview.md) - Vídeo adicionado.

### <a name="autoscale"></a>Dimensionamento Automático
- [Começar com autoescala em Azure](platform/autoscale-get-started.md) - Secção adicionada no encaminhamento para instâncias saudáveis para o Serviço de Aplicações.

### <a name="data-collection"></a>Recolha de dados
- [Configure a recolha de dados para o agente Azure Monitor (pré-visualização)](platform/data-collection-rule-azure-monitor-agent.md) - Novo artigo.
- [Regras de recolha de dados no Monitor Azure (pré-visualização)](platform/data-collection-rule-overview.md) - Novo artigo.


### <a name="containers"></a>Contentores
- [Implantação & métricas HPA com monitor Azure para contentores](insights/container-insights-deployment-hpa-metrics.md) - Novo artigo.

### <a name="insights"></a>Informações
- [Soluções de monitorização no Azure Monitor](insights/solutions.md) - Atualizado para nova UI.
- [Solução monitor de desempenho de rede em Azure](insights/network-performance-monitor.md) - Regiões de espaço de trabalho apoiados.


### <a name="logs"></a>Registos
- [Azure Monitor FAQ](faq.md) - Entrada adicionada para apagar dados de um espaço de trabalho. Entrada acrescida em 502 e 503 respostas.
  - [Conceber a sua implementação de Registos de Monitor Azure](platform/design-logs-deployment.md) - Atualizações para a secção limite de taxa de volume de Ingestion.
- [Gerir a utilização e os custos dos Registos do Monitor Azure](platform/manage-cost-storage.md) - Consultas de utilização atualizadas para um formato de consulta mais eficiente.
- [Otimize as consultas de registo no Azure Monitor](log-query/query-optimization.md) - Adicione valores específicos aos indicadores de desempenho.
- [Amostras de modelo do Gestor de Recursos para definições de diagnóstico](samples/resource-manager-diagnostic-settings.md) - Amostra adicionada para registos de auditoria de consulta de registo.


### <a name="platform-logs"></a>Registos da plataforma
- [Criar definições de diagnóstico para enviar registos e métricas de plataforma para diferentes destinos](platform/diagnostic-settings.md) - Requisito regional adicionado para configurações de diagnóstico.

### <a name="visualizations"></a>Visualizações
- Visão geral dos livros de trabalho do [Monitor Azure](platform/workbooks-overview.md) - Vídeo adicionado.
- [Mova um modelo de livro Azure para outra região](platform/workbook-templates-move-region.md) - Novo artigo.
- [Mover um Livro de Azure para outra região](platform/workbooks-move-region.md) - Novo artigo.



## <a name="july-2020"></a>Julho de 2020

### <a name="general"></a>Geral
- [Implementar Monitor Azure](deploy-scale.md) - Reestruturação do Monitor Azure para o conteúdo de bordo de VMs.
- [Utilize o Link Privado Azure para ligar de forma segura as redes ao Azure Monitor](platform/private-link-security.md) - Secção adicionada nos limites.

### <a name="alerts"></a>Alertas
- [Regras de ação para alertas do Azure Monitor](platform/alerts-action-rules.md) - Processos CLI adicionados.
- [Criar e gerir grupos de ação no portal Azure](platform/action-groups.md) - Atualizado para refletir alterações na UI.
- [Consultas guardadas no Azure Monitor Log Analytics](log-query/saved-queries.md) - Novo artigo.
- [Alertas de registo de resolução de problemas no Azure Monitor](platform/alerts-troubleshoot-log.md) - Secção adicionada na quota de regra de alerta.
- [Resolução de problemas Alertas métricos Azure](platform/alerts-troubleshoot-metric.md) - Secção adicionada sobre regra de alerta sobre uma métrica personalizada que ainda não é emitida.
- [Entenda como os alertas métricos funcionam no Azure Monitor.](platform/alerts-metric-overview.md) - Recomendação adicional para a seleção da granularidade agregação.

### <a name="application-insights"></a>Application Insights
- [Notas de lançamento para extensão de aplicativo web Azure - Application Insights](app/web-app-extension-release-notes.md) - Novo artigo.
- [Amostras de modelo de gestor de recursos para Recursos de Insights de Aplicação](samples/resource-manager-app-resource.md) - Novo artigo.
- [Problemas de resolução de problemas com O Perfil de Insights de Aplicação Azure](app/profiler-troubleshooting.md) - Nota adicional no perfil de execução de bugs para aplicações ASP.NET Core no Azure App Service. 

### <a name="containers"></a>Contentores
- [Registar alertas do Azure Monitor para contentores](insights/container-insights-log-alerts.md) - Novo artigo.
- [Alertas métricos do Monitor Azure para contentores](insights/container-insights-metric-alerts.md) - Novo artigo.

### <a name="logs"></a>Registos
- [Chave gerida pelo cliente Azure Monitor](platform/customer-managed-keys.md) - Mensagem de erro adicionada e configuração cmk da secção para consultas.
- [Azure Monitor HTTP Data Collector API](platform/data-collector-api.md) - Amostra de Python 3 adicionada.
- [Otimize as consultas de registo no Azure Monitor](log-query/query-optimization.md) - Secção adicionada para evitar múltiplas verificações de dados ao utilizar subqueries.
- [Tutorial: Começa com consultas de Log Analytics](log-query/get-started-portal.md) - Vídeo adicionado.

### <a name="platform-logs"></a>Registos da plataforma
- [Crie definições de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos](platform/diagnostic-settings.md) - Vídeo adicionado.
- [Amostras de modelo de gestor de recursos para monitor Azure](samples/resource-manager-samples.md) - amostra DE ARM adicionada usando o tipo de destino Logs. 

### <a name="solutions"></a>Soluções
- [Soluções de monitorização no Azure Monitor](insights/solutions.md) - Processos CLI adicionados.
- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Alteração da data de reforma.

### <a name="virtual-machines"></a>Máquinas virtuais

Artigos novos e atualizados da reestruturação do Azure Monitor para conteúdos de VMs

- [O que é Azure Monitor para VMs?](insights/vminsights-overview.md)
- [Configurar log analytics espaço de trabalho para monitor azure para VMs](insights/vminsights-configure-workspace.md)
- [Ligue os computadores Linux ao Azure Monitor](platform/agent-linux.md)
- [Ativar o Azure Monitor para um ambiente híbrido](insights/vminsights-enable-hybrid.md)
- [Ativar o Azure Monitor para uma única máquina virtual ou escala de máquina virtual definida no portal Azure](insights/vminsights-enable-portal.md)
- [Ativar o Monitor Azure para VMs utilizando a política do Azure](./insights/vminsights-enable-policy.md)
- [Ativar o Monitor Azure para visão geral dos VMs](insights/vminsights-enable-overview.md)
- [Ativar o Monitor Azure para VMs utilizando PowerShell](insights/vminsights-enable-powershell.md)
- [Ativar o Monitor Azure para VMs utilizando modelos de gestor de recursos](insights/vminsights-enable-resource-manager.md)
- [Ativar o Monitor Azure para VMs com PowerShell ou modelos](./insights/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Visualizações
- [Atualizar as visualizações do Painel de Registos -](log-query/dashboard-upgrade.md) Taxa de atualização atualizada.
- [Visualização de dados do Azure Monitor](visualizations.md) - Vídeo adicionado.


## <a name="june-2020"></a>Junho de 2020

### <a name="general"></a>Geral
- [Implementar Monitor Azure](deploy-scale.md) - Novo artigo.
- [Chave gerida pelo cliente Azure Monitor](platform/customer-managed-keys.md) - Propriedade de tipo de faturação atualizada. Adicionou comandos PowerShell.

### <a name="agents"></a>Agentes
- [Visão geral do agente Log Analytics](platform/log-analytics-agent.md) - Requisito de Python 2 adicionado.

### <a name="alerts"></a>Alertas
- [Como atualizar regras de alerta ou regras de ação quando o seu recurso-alvo se desloca para uma região Azure diferente](platform/alerts-resource-move.md) - Novo artigo.
- [Resolução de problemas Alertas métricos Azure](platform/alerts-troubleshoot-metric.md) - Novo artigo.
- [Alertas de registo de resolução de problemas no Azure Monitor](platform/alerts-troubleshoot-metric.md) - Novo artigo.
  
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
- [Como parar de monitorizar o seu cluster híbrido Kubernetes](insights/container-insights-optout-hybrid.md) - Secção adicionada para o Arco ativado kubernetes.
- [Configure Azure Arc permitiu que kubernetes cluster com Azure Monitor para recipientes](insights/container-insights-enable-arc-enabled-clusters.md) - Novo artigo.
- [Configure Azure Red Hat OpenShift v4.x com Azure Monitor para recipientes](insights/container-insights-azure-redhat4-setup.md) - Pré-requisitos atualizados.
- [Configurar o Azure Monitor para contentores Live Data (pré-visualização)](insights/container-insights-livedata-setup.md) - Nota removida sobre a funcionalidade que não está disponível no Governo dos EUA.

### <a name="insights"></a>Informações
- [FAQs - Solução monitor de desempenho de rede em Azure](insights/network-performance-monitor-faq.md) - FAQ adicionado para ExpressRoute Monitor.

### <a name="logs"></a>Registos
- [Eliminar e recuperar o espaço de trabalho Azure Log Analytics](platform/delete-workspace.md) - Comando PowerShell adicionado. Resolução de problemas atualizada.
- [Gerir os espaços de trabalho do Log Analytics no Azure Monitor](platform/manage-access.md) - Exemplo adicional para tabelas não permitidas na secção RBAC.
- [Gerir a utilização e os custos dos Registos monitores Azure](platform/manage-cost-storage.md) - Detalhes adicionais no cálculo do tamanho dos dados. Alertas de volume de dados atualizados. Detalhes sobre os dados de segurança recolhidos pelo Azure Sentinel. Esclarecimento sobre a tampa de dados.
- [Utilize registos do Monitor Azure com aplicações lógicas Azure e limites de](platform/logicapp-flow-connector.md) automatização de energia.

### <a name="metrics"></a>Métricas
- [Azure Monitor suportado métricas por tipo de recurso](platform/metrics-supported.md) - métricas atualizadas do SQL Server.


### <a name="platform-logs"></a>Registos da plataforma

- [Amostras de modelo do Gestor de Recursos para definições de diagnóstico](samples/resource-manager-diagnostic-settings.md) - Correção para a definição de diagnóstico de registo de atividade.
- Enviar registo de [atividades Azure para log Analytics espaço de trabalho usando portal Azure](learn/quick-collect-activity-log-portal.md) - Novo artigo.
- [Envie um registo de atividade azure para log analytics espaço de trabalho usando o modelo de Gestor de Recursos Azure](learn/quick-collect-activity-log-arm.md) - Novo artigo.

Artigos novos e atualizados da reestruturação e consolidação de conteúdos de registo de plataforma

- [Registos de recursos do Archive Azure para a conta de armazenamento](./platform/resource-logs.md#send-to-azure-storage)
- [Esquema de evento de registo de atividade azure](platform/activity-log-schema.md)
- [Log de atividades Azure](platform/activity-log.md)
- [Amostras de CLI monitor Azure](samples/cli-samples.md)
- [Amostras de PowerShell monitor Azure Monitor](samples/powershell-samples.md)
- [Azure Monitoring REST API walkthrough](platform/rest-api-walkthrough.md)
- [Azure Resource Logs suportados serviços e esquemas](./platform/resource-logs-schema.md)
- [Registos de recursos do Azure](platform/resource-logs.md)
- [Recolher e analisar o registo de atividades do Azure no Azure Monitor](./platform/activity-log.md)
- [Recolher registos de recursos Azure no espaço de trabalho Log Analytics](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Criar definições de diagnóstico para enviar registos e métricas da plataforma para destinos diferentes](platform/diagnostic-settings.md)
- [Exportar o Registo de Atividades Azure](./platform/activity-log.md#legacy-collection-methods)
- [Descrição geral dos registos de plataforma Azure](platform/platform-logs-overview.md)
- [Stream Azure plataforma registra para um centro de eventos](./platform/resource-logs.md#send-to-azure-event-hubs)
- [Ver eventos de registo de atividades do Azure no Azure Monitor](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Máquinas virtuais
- [Ativar o Monitor Azure para VMs no portal Azure](./insights/vminsights-enable-portal.md) - Atualizado para incluir O Arco de Azure.
- [Ativar o Azure Monitor para visão geral dos VMs](insights/vminsights-enable-overview.md) - Atualizado para incluir O Arco de Azure.
- [O que é Azure Monitor para VMs?](insights/vminsights-overview.md) - Atualizado para incluir Azure Arc.


### <a name="visualizations"></a>Visualizações
- Fontes de dados de [livros do Azure Monitor](platform/workbooks-data-sources.md) - Alertas Adicionados e Secções de Endpoints Personalizados.
- [Resolução de problemas Azure Monitor insights baseados em livros](insights/troubleshoot-workbooks.md) - Novo artigo.
- [Atualizar as visualizações do Painel de Registos](log-query/dashboard-upgrade.md) - Novo artigo.



## <a name="may-2020"></a>Maio de 2020

### <a name="general"></a>Geral

- [Azure Monitor FAQ](faq.md) - Secção adicionada para métricas.
- [Chave gerida pelo cliente Azure Monitor](platform/customer-managed-keys.md) - Várias alterações na preparação para a disponibilidade geral.
- [Definições políticas incorporadas para O Monitor Azure](./samples/policy-reference.md) - Novo artigo.
- [Contas de armazenamento do cliente para ingestão de registos](platform/private-storage.md) - Novo artigo.
- [Gerir a utilização e os custos dos registos do Monitor Azure](platform/manage-cost-storage.md) - Faturação proporcional de cluster adicionada.
- [Utilize o Link Privado Azure para ligar de forma segura as redes ao Azure Monitor](platform/private-link-security.md) - Novo artigo.


#### <a name="new-resource-manager-template-samples"></a>Novas amostras de modelo do gestor de recursos 
- [Amostras de modelo de gestor de recursos para monitor Azure](samples/resource-manager-samples.md)
- [Amostras de modelo de gestor de recursos para grupos de ação](samples/resource-manager-action-groups.md)
- [Amostras de modelo de gestor de recursos para agentes](samples/resource-manager-agent.md)
- [Amostras de modelo do gestor de recursos para monitor Azure para contentores](samples/resource-manager-container-insights.md)
- [Amostras de modelo de gestor de recursos para monitor Azure para VMs](samples/resource-manager-vminsights.md)
- [Amostras de modelo do gestor de recursos para definições de diagnóstico](samples/resource-manager-diagnostic-settings.md)
- [Amostras de modelo de gestor de recursos para espaços de trabalho do Log Analytics](samples/resource-manager-workspace.md)
- [Amostras de modelo de gestor de recursos para consultas de registo](samples/resource-manager-log-queries.md)
- [Amostras de modelo do gestor de recursos para regras de alerta de consulta de registo](samples/resource-manager-alerts-log.md)
- [Amostras de modelo de gestor de recursos para regras de alerta métrico](samples/resource-manager-alerts-metric.md)
- [Amostras de modelo de gestor de recursos para livros](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Agentes
- [Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](platform/diagnostics-extension-windows-install.md) - Detalhe adicional na configuração dos diagnósticos.
- [Visão geral do agente Log Analytics](platform/log-analytics-agent.md) - Adicione versões Linux suportadas.

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
- [() expressão em consultas de log do Azure Monitor](log-query/app-expression.md)
- [Âmbito de consulta de registo no Azure Monitor Log Analytics](log-query/scope.md)
- [Consulta através de recursos com o Azure Monitor](log-query/cross-workspace-query.md)
- [Propriedades padrão em registos de registos do Azure Monitor](./platform/log-standard-columns.md)
- [Estrutura de Registos monitores Azure](./platform/data-platform-logs.md)





### <a name="containers"></a>Contentores
- [Como ativar o Azure Monitor para contentores](insights/container-insights-onboard.md) - Tabela de configuração de firewall atualizada.
- [Como atualizar o Azure Monitor para contentores para métricas](insights/container-insights-update-metrics.md) - Atualização para utilizar identidades geridas para recolher métricas.
- [Custo de monitorização do Monitor Azure para contentores](insights/container-insights-cost.md) - Novo artigo.
- [Configurar o Azure Monitor para contentores Live Data (pré-visualização)](insights/container-insights-livedata-setup.md) - Suporte para nova ligação de funções de cluster.

### <a name="insights"></a>Informações
- [Monitor Azure para Azure Cache para Redis (pré-visualização)](insights/redis-cache-insights-overview.md) - Novo artigo.
- [Monitor Key Vault com Monitor Azure para Cofre de Chaves (pré-visualização)](./insights/key-vault-insights-overview.md) - Novo artigo.

### <a name="logs"></a>Registos
- [Crie & configurar o Log Analytics com PowerShell](platform/powershell-workspace-configuration.md) - Secção de resolução de problemas adicionada.
- [Crie um espaço de trabalho Log Analytics no portal Azure](learn/quick-create-workspace.md) - Secção de resolução de problemas adicionada.
- [Crie um espaço de trabalho Log Analytics utilizando a secção Azure CLI](learn/quick-create-workspace-cli.md) - Secção de resolução de problemas adicionada.
- [Eliminar e recuperar o espaço de trabalho do Azure Log Analytics](platform/delete-workspace.md) - Informações atualizadas sobre a recuperação de um espaço de trabalho eliminado.
- [Funções em consultas de registo do Monitor Azure](log-query/functions.md) - Nota removida sobre funções que não contenham outras funções.
- [Estrutura de Registos monitores Azure](./platform/data-platform-logs.md) - Descrições de propriedade esclarecidas para tabela de Insights de Aplicação.
- [Utilize registos de monitores Azure com aplicações lógicas Azure e automatização](platform/logicapp-flow-connector.md) de energia - Secção de limites adicionados.
- [Utilize o PowerShell para criar e configurar um espaço de trabalho de análise de log](platform/powershell-workspace-configuration.md) - secção de resolução de problemas adicionada.


### <a name="metrics"></a>Métricas
- [Azure Monitor suportado métricas por tipo de recurso](platform/metrics-supported.md) - métricas esclarecidas dos hóspedes e encaminhamento de métricas. 

### <a name="solutions"></a>Soluções
- [Otimize o seu ambiente de Diretório Ativo com o Azure Monitor](insights/ad-assessment.md) - Adicionado Windows Server 2019 para versões suportadas.
- [Otimize o seu ambiente de Servidor SQL com monitor Azure](insights/sql-assessment.md) - Adicionado às versões suportadas do SQL Server.


### <a name="virtual-machines"></a>Máquinas virtuais
- [Ativar o Azure Monitor para visão geral dos VMs](insights/vminsights-enable-overview.md) - Adicionado às versões suportadas do Ubuntu Server. Regiões apoiadas adicionadas para o espaço de trabalho Log Analytics.
- [Como traçar o desempenho com o Azure Monitor para VMs](insights/vminsights-performance.md) - Secção de limitações adicionadas para métricas indisponíveis.

### <a name="visualizations"></a>Visualizações
- [Azure Monitor Workbooks e Azure Resource Manager Templates](platform/workbooks-automate.md) - Atualização do Gestor de Recursos Adicionado para implementar um modelo de livro.
- [Grupos de livros de trabalho do Azure Monitor](platform/workbooks-groups.md) - Novo artigo.
- [Azure Monitor Workbooks - Transforme os dados da JSON com o JSONPath](platform/workbooks-jsonpath.md) - Novo artigo.


## <a name="april-2020"></a>Abril de 2020

### <a name="general"></a>Geral

- [Chave gerida pelo cliente do Azure Monitor](platform/customer-managed-keys.md) - Secção adicionada em operações assíncronas
- [Gerir os espaços de trabalho do Log Analytics no Azure Monitor](platform/manage-access.md) - Secções de registos personalizados atualizados.

### <a name="alerts"></a>Alertas

- [Regras de ação para alertas do Azure Monitor](platform/alerts-action-rules.md) - Vídeo adicionado.
- [Visão geral do alerta e monitorização da notificação em Azure](platform/alerts-overview.md) - Vídeo adicionado.

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

- [Configure Azure Red Hat OpenShift v4.x com Monitor Azure para recipientes](insights/container-insights-azure-redhat4-setup.md) - Novo artigo.
- [Como corrigir manualmente problemas de sincronização do ServiceNow](platform/itsmc-resync-servicenow.md) - Novo artigo.
- [Como parar de monitorizar o seu cluster V4 Azure e Red Hat OpenShift](insights/container-insights-optout-openshift-v4.md) - Novo artigo.
- [Como parar de monitorizar o seu cluster Azure Red Hat OpenShift v3](insights/container-insights-optout-openshift-v3.md) - Novo artigo.
- [Como parar de monitorizar o seu cluster híbrido Kubernetes](insights/container-insights-optout-hybrid.md) - Novo artigo.

### <a name="insights"></a>Informações

- [Monitor Azure Key Vaults com Monitor Azure para Cofres chave (pré-visualização)](insights/key-vault-insights-overview.md) - Novo artigo.

### <a name="logs"></a>Registos

- [Limites de serviço do Azure Monitor](service-limits.md) - Aceleração de consulta do utilizador adicionada.
- [Gerir a utilização e os custos dos registos do Monitor Azure](platform/manage-cost-storage.md) - Faturação adicionada para clusters de Logs. A consulta de Kusto adicionada para permitir que os clientes com o nível de preços por nó legado para determinar se devem mudar para um nível de Reserva por Gb ou Capacidade.

### <a name="metrics"></a>Métricas

- [Características avançadas do Azure Metrics Explorer](platform/metrics-charts.md) - Secção de agregação adicionada.

### <a name="workbooks"></a>Livros

- [Azure Monitor Workbooks e Azure Resource Manager Modelos](platform/workbooks-automate.md) - Modelo de gestor de recursos adicionado para implantar um modelo de livro.

## <a name="march-2020"></a>Março de 2020

### <a name="general"></a>Geral

- [Visão geral do Azure Monitor](overview.md) - Vídeo geral do Monitor Azure Adicionado.
- [Configuração de chave gerida pelo cliente Azure Monitor](platform/customer-managed-keys.md) - Atualizações gerais.
- [Referência de dados do Azure Monitor](/azure/azure-monitor/reference/) - Novo site.

### <a name="alerts"></a>Alertas

- [Criar, visualizar e gerir alertas de registo de atividade no Azure Monitor](platform/alerts-activity-log.md) - Explicação adicional do modelo do Gestor de Recursos.
- [Entenda como os alertas métricos funcionam no Azure Monitor.](platform/alerts-metric-overview.md) - Atualizado para apoio do governo.
- [Resolução de problemas Alertas e notificações do Azure Monitor](platform/alerts-troubleshoot.md) - Novo artigo.

### <a name="application-insights"></a>Application Insights

- [Automatizar insights de aplicação Azure com PowerShell](app/powershell.md) - Exemplos ARMClient adicionados.
- [Exportação contínua de telemetria a partir de Insights de Aplicação](app/export-telemetry.md) - Adicione tabela com detalhes da estrutura de exportação.
- [Ative o Snapshot Debugger para aplicações .NET no Azure App Service](app/snapshot-debugger-appservice.md) - Exemplo do modelo do Gestor de Recursos Adicionado.
- [Gerir a utilização e os custos para a Azure Application Insights](app/pricing.md) - Informação adicional sobre o alerta da tampa de dados.
- [Monitorar aplicações Python com Monitor Azure (pré-visualização)](app/opencensus-python.md) - Métricas padrão adicionadas.
- [Suporte de mapa de origem para aplicações JavaScript - Azure Monitor Application Insights](app/source-map-support.md) - Novo artigo.

### <a name="containers"></a>Contentores

- [Azure Monitor FAQ](faq.md) - Atualização para monitor Azure para contentores.
- [Configure a monitorização da GPU com o Monitor Azure para contentores](insights/container-insights-gpu-monitoring.md) - Novo artigo.

### <a name="insights"></a>Informações

- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Data de depreciação atualizada.

### <a name="logs"></a>Registos

- [Otimize as consultas de log no Azure Monitor](log-query/query-optimization.md) - Condições de CPU adicionadas para análise de XML e JSON.
- [Eliminar e recuperar o espaço de trabalho do Azure Log Analytics](platform/delete-workspace.md) - Sessão de problemas adicionada.
- [Utilize registos do Monitor Azure com aplicações lógicas Azure e automatização de energia](platform/logicapp-flow-connector.md) - atualizado para o novo conector Azure Monitor.

### <a name="metrics"></a>Métricas

- [Depreciação das métricas do disco no portal Azure](platform/portal-disk-metrics-deprecation.md) - Novo artigo.
- [Tutorial - Criar um gráfico de métricas no Azure Monitor](learn/tutorial-metrics-explorer.md) - Vídeo adicionado.

### <a name="platform-logs"></a>Registos da plataforma

- [Recolher e analisar o registo de atividades do Azure no Azure Monitor](./platform/activity-log.md) - Reescreva melhor a recolha do registo de atividade com definições de diagnóstico.

### <a name="virtual-machines"></a>Máquinas virtuais

- [Monitor Azure máquinas virtuais com Azure Monitor](insights/monitor-vm-azure.md) - Novo artigo.
- [Quickstart: Monitor Azure virtual machines with Azure Monitor](learn/quick-monitor-azure-vm.md) - Atualizado para adicionar Azure Monitor para VMs.
- [Alertas do Monitor Azure para VMs](insights/vminsights-alerts.md) - Novo artigo.
- [Ativar o Azure Monitor para visão geral dos VMs](insights/vminsights-enable-overview.md) - Links de descarregamento de agentes atualizados.

Atualizações gerais para a disponibilidade geral do Monitor Azure para VMs

- [O que é Azure Monitor para VMs?](insights/vminsights-overview.md)
- [Monitor Azure para VMs (GA) frequentemente feito perguntas](insights/vminsights-ga-release-faq.md) 
- [Ativar o Monitor Azure para VMs utilizando a política do Azure](./insights/vminsights-enable-policy.md) 
- [Como traçar o desempenho com o Azure Monitor para VMs](insights/vminsights-performance.md)
- [Como consultar registos do Azure Monitor para VMs](insights/vminsights-log-search.md)
- [Ver dependências de aplicativos com monitor Azure para VMs](insights/vminsights-maps.md) 

### <a name="visualizations"></a>Visualizações

- [Visualização de dados do Azure Monitor](visualizations.md) - Atualizado para notar a depreciação planeada do View Designer.

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="agents"></a>Agentes

Múltiplas atualizações como parte da reescrita do conteúdo de extensão de diagnóstico.

- [Visão geral dos agentes de monitorização Azure](platform/agents-overview.md) - Tabelas reestruturadas para clarificar melhor as características únicas de cada agente.
- [Visão geral da extensão do Azure Diagnostics](platform/diagnostics-extension-overview.md) - Reescrita completa.
- [Utilize o armazenamento de bolhas para iIS e armazenamento de mesa para eventos no Azure Monitor](platform/diagnostics-extension-logs.md) - Reescrita geral para atualização e clareza.
- [Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](platform/diagnostics-extension-windows-install.md) - Novo artigo. 
- [Esquema de extensão de diagnóstico do Windows](platform/diagnostics-extension-schema-windows.md) - Reorganizado.
- [Enviar dados da extensão de diagnóstico do Windows Azure para Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) - Completamente reescrito e atualizado.
- [Armazenar e ver dados de diagnóstico no Azure Storage](../cloud-services/diagnostics-extension-to-storage.md) - Completamente reescrito e atualizado.
- [Log Analytics extensão de máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md) - Melhor esclarece a relação com o agente Log Analytics.
- [Extensão da máquina virtual Azure Monitor para Linux](../virtual-machines/extensions/oms-linux.md) - Melhor esclarece a relação com o agente Log Analytics.

### <a name="application-insights"></a>Application Insights

- [Cadeias de ligação em Azure Application Insights](app/sdk-connection-string.md) - Novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções

#### <a name="azure-monitor-for-containers"></a>Azure Monitor para Contentores

- [Integre o Azure Ative Directory com o Serviço Azure Kubernetes](../aks/azure-ad-integration-cli.md) - Nota adicionada para criar uma aplicação de cliente para apoiar o cluster ativado pelo RBAC para apoiar o Azure Monitor para contentores.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs

- [Azure Monitor for VMs (GA) perguntas frequentes](insights/vminsights-ga-release-faq.md) - Alterar a forma como os dados de desempenho são armazenados.

#### <a name="office-365"></a>Office 365

- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Data de depreciação atualizada.


### <a name="logs"></a>Registos

- [Otimize consultas de registo no Azure Monitor](log-query/query-optimization.md) - Novo artigo.
- [Gerir o uso e os custos para registos Azure Monitor](platform/manage-cost-storage.md) - Consultas melhoradas de amostras para ajudar a entender o seu uso.

### <a name="metrics"></a>Métricas

- [Métricas da plataforma Azure Monitor exportáveis através de Definições de Diagnóstico](platform/metrics-supported-export-diagnostic-settings.md) - Secção adicionada sobre alteração ao comportamento de nulos e valores zero.

### <a name="visualizations"></a>Visualizações

Vários novos artigos para visualizar designer para guia de conversão de livros de trabalho.

- [Azure Monitor ver designer para guia de transição de livros](platform/view-designer-conversion-overview.md) - Novo artigo.
- [Azure Monitor ver designer para opções de conversão de livros](platform/view-designer-conversion-options.md) de trabalho - Novo artigo.
- [Azure Monitor vê designer para conversões de azulejos](platform/view-designer-conversion-tiles.md) de livros - Novo artigo.
- [Azure Monitor ver designer para resumo de conversão de livros e acesso](platform/view-designer-conversion-access.md) - Novo artigo.
- [Azure Monitor vê designer para trabalhos de conversão de tarefas comuns](platform/view-designer-conversion-tasks.md) - Novo artigo.
- [Azure Monitor vê designer para exemplos de conversão de livros](platform/view-designer-conversion-examples.md) - Novo artigo.

## <a name="january-2020"></a>Janeiro de 2020

### <a name="general"></a>Geral

- [O que é monitorizado pelo Azure Monitor?](monitor-reference.md) - Novo artigo.

### <a name="agents"></a>Agentes

- [Recolher dados de registo com o agente Azure Log Analytics](platform/log-analytics-agent.md) - Tabela atualizada de requisitos de firewall de rede.

### <a name="alerts"></a>Alertas

- [Criar e gerir grupos de ação no portal Azure](platform/action-groups.md) - Definição removida para funções v2 que já não são necessárias.
- [Crie um alerta métrico com um modelo de Gestor de Recursos](platform/alerts-metric-create-templates.md) - Adicione exemplo para o parâmetro *IgnoreDataBefore.*  Constrangimentos adicionais sobre regras de vários critérios.
- [Utilização do alerta de log analytics REST API](platform/api-alerts.md) - exemplo JSON corrigido.

### <a name="application-insights"></a>Application Insights

- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Atualize a secção de teste de Disponibilidade com a forma de adicionar uma regra de entrada portuária para permitir o tráfego utilizando grupos de segurança da rede Azure.
- [Problemas de resolução de problemas com O Perfil de Insights de Aplicação Azure](app/profiler-troubleshooting.md) - Resolução de problemas geral atualizado.
- [Amostragem de telemetria em Azure Application Insights](app/sampling.md) - Atualizado e reestruturado para melhorar a legibilidade com base no feedback do cliente.

### <a name="data-security"></a>Segurança de dados

- [Configuração de chave gerida pelo cliente Azure Monitor](platform/customer-managed-keys.md) - Novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções

#### <a name="azure-monitor-for-containers"></a>Azure Monitor para Contentores

- [Configure Azure Monitor para recolha de dados de agente de contentores](insights/container-insights-agent-config.md) - Adicione detalhes para atualizar o agente no Azure Red Hat OpenShift e adicione informações adicionais para distinguir os métodos de atualização do agente.
- [Criar alertas de desempenho para o Azure Monitor para contentores](./insights/container-insights-log-alerts.md) - Informações revistas e passos atualizados para criar um alerta sobre os dados de desempenho armazenados no espaço de trabalho utilizando alertas de contexto de espaço de trabalho.
- [Kubernetes monitorando com o Azure Monitor para contentores](insights/container-insights-analyze.md) - Atualize tanto o artigo de visão geral como o artigo de análise relativo ao suporte de clusters Do Windows Kubernetes.
- [Configure Azure Red Hat OpenShift clusters com Azure Monitor para contentores](insights/container-insights-azure-redhat-setup.md) - Adicione detalhes para atualizar o agente no Azure Red Hat OpenShift, e adicione informações adicionais para distinguir os métodos de upgrade do agente.
- [Configure os agrupamentos Hybrid Kubernetes com O Monitor Azure para contentores](insights/container-insights-hybrid-setup.md) - Atualizado para refletir um suporte adicional para porta segura:10250 com o cAdvisor do Kubelet.
- [Como gerir o Azure Monitor para o agente de contentores](insights/container-insights-manage-agent.md) - Detalhes atualizados relacionados com o comportamento e configuração de raspagem métrica com Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.
- [Configure Monitor Azure para recipientes Integração Prometheus](insights/container-insights-prometheus-integration.md) - Detalhes atualizados relacionados com o comportamento e configuração de raspagem métrica com Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.
- [Como atualizar o Azure Monitor para contentores para métricas](insights/container-insights-update-metrics.md) - Detalhes atualizados relacionados com o comportamento e configuração de raspagem métrica com Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs

- [Azure Monitor for VMs (GA) frequentemente fez perguntas](insights/vminsights-ga-release-faq.md) - Acrescentou informações sobre a atualização do espaço de trabalho e agentes para a nova versão.

#### <a name="office-365"></a>Office 365

- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Detalhes adicionais e FAQ sobre migração para a solução do Office 365 em Azure Sentinel. Secção de embarque removida.

### <a name="logs"></a>Registos

- [Gerir os espaços de trabalho do Log Analytics no Azure Monitor](platform/manage-access.md) - Atualizações para não ações.
- [Gerir a utilização e os custos dos Registos monitores Azure](platform/manage-cost-storage.md) - Esclarecimento adicional sobre o cálculo do volume de dados na secção Modelo de Preços.
- [Utilize modelos de Gestor de Recursos Azure para criar e configurar um espaço de trabalho de Log Analytics](./samples/resource-manager-workspace.md) - Modelo atualizado com novos níveis de preços.

### <a name="platform-logs"></a>Registos da plataforma

- [Recolher registo de atividade azure com definições de diagnóstico- Monitor Azure](./platform/activity-log.md) - Informações adicionais sobre propriedades alteradas.
- [Exportar o Registo de Atividades Azure](./platform/activity-log.md#legacy-collection-methods) - Atualizado para alterações de UI. 

## <a name="december-2019"></a>Dezembro de 2019

### <a name="agents"></a>Agentes

- [Ligue os computadores Linux ao Azure Monitor](platform/agent-linux.md) - Novo artigo.

### <a name="alerts"></a>Alertas

- [Crie um alerta métrico com um modelo de Gestor de Recursos](platform/alerts-metric-create-templates.md) - Exemplo adicionado para métrica personalizada.
- [Criação de Alertas com Limiares Dinâmicos no Monitor Azure](platform/alerts-dynamic-thresholds.md) - Secção adicionada na interpretação de gráficos de limiares dinâmicos.
- [Visão geral do alerta e monitorização da notificação em Azure](platform/alerts-overview.md) - Consulta de gráfico de recurso atualizado.
- [Recursos suportados para alertas métricos no Azure Monitor](platform/alerts-metric-near-real-time.md) - Atualização para métricas e dimensões suportadas.
- [Switch from legacy Log Analytics alerta API para novo Azure Alerts API](platform/alerts-log-api-switch.md) - Nota adicionada no nome de alerta modificado.
- [Entenda como os alertas métricos funcionam no Azure Monitor.](platform/alerts-metric-overview.md) - Tipos de recursos suportados adicionados para monitorização em escala.

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

- [Monitor Azure para contentores Perguntas Frequentes](./faq.md) - Pergunta adicionada nos campos de imagem e nome.
- [Solução Azure SQL Analytics em Azure Monitor](insights/azure-sql.md) - Base de Dados Atualizada aguarda suporte de instância gerida.
- [Configure Monitor Azure para recolha de dados de agente de contentores](insights/container-insights-agent-config.md) - Regulação adicional para enrich_container_logs.
- [Configure os agrupamentos Híbridos Kubernetes com monitor Azure para contentores](insights/container-insights-hybrid-setup.md) - Secção de resolução de problemas adicionada.
- [Monitore o estado de replicação do Diretório Ativo com o Azure Monitor](insights/ad-replication-status.md) - .NET Framework pré-requisito atualizado.
- [Solução monitor de desempenho de rede em Azure](insights/network-performance-monitor.md) - Regiões apoiadas adicionadas.
- [Otimize o seu ambiente de Diretório Ativo com o Azure Monitor](insights/ad-assessment.md) - .NET Framework pré-requisito atualizado.
- [Otimize o seu ambiente de Servidor SQL com monitor Azure](insights/sql-assessment.md) Monitor - .NET Framework pré-requisito atualizado.
- [Otimize o ambiente de Gerente de Operações do System Center com Azure Log Analytics](insights/scom-assessment.md) - .NET Framework pré-requisito atualizado.
- [Conexões suportadas com o IT Service Management Connector em Azure Log Analytics](platform/itsmc-connections.md) - Acrescentou Nova Iorque para pré-requisito de identificação do cliente e segredo de cliente.

### <a name="logs"></a>Registos

- [Eliminar e recuperar o espaço de trabalho Azure Log Analytics](platform/delete-workspace.md) - método PowerShell adicionado.
- [Conceber a sua implementação de Registos de Monitor Azure](platform/design-logs-deployment.md) - A taxa de ingestão para um espaço de trabalho aumentou.

### <a name="metrics"></a>Métricas

- [Métricas da plataforma Azure Monitor exportáveis através de Definições de Diagnóstico](platform/metrics-supported-export-diagnostic-settings.md) - Novo artigo.

### <a name="platform-logs"></a>Registos da plataforma

Vários artigos atualizados como parte da reestruturação de conteúdos para registos de plataformas com base em nova funcionalidade para configurar o registo de atividades utilizando definições de diagnóstico.

- [Registos de recursos do Archive Azure para a conta de armazenamento](./platform/resource-logs.md#send-to-azure-storage)
- [Esquema de evento de registo de atividade azure](platform/activity-log-schema.md)
- [Limites de serviço do Azure Monitor](service-limits.md)
- [Recolher e analisar registos de atividades do Azure no espaço de trabalho log Analytics](./platform/activity-log.md)
- [Recolher registo de atividade do Azure com definições de diagnóstico (pré-visualização) - Monitor Azure](./platform/activity-log.md)
- [Colete registos de atividades Azure em um espaço de trabalho Log Analytics através de inquilinos Azure](./platform/activity-log.md)
- [Recolher registos de recursos Azure no espaço de trabalho Log Analytics](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Crie uma definição de diagnóstico em Azure utilizando o modelo do Gestor de Recursos](./samples/resource-manager-diagnostic-settings.md)
- [Criar definição de diagnóstico para recolher registos e métricas em Azure](platform/diagnostic-settings.md)
- [Exportar o Registo de Atividades Azure](./platform/activity-log.md#legacy-collection-methods)
- [Descrição geral dos registos de plataforma Azure](platform/platform-logs-overview.md)
- [Stream Azure monitorizando dados para o centro de eventos](platform/stream-monitoring-data-event-hubs.md)
- [Stream Azure plataforma registra para um centro de eventos](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Inícios rápidos e tutoriais

- [Criar um gráfico de métricas no Azure Monitor](learn/tutorial-metrics-explorer.md) - Novo artigo.
- [Colete registos de recursos a partir de um Recurso Azure e analise com o Azure Monitor](learn/tutorial-resource-logs.md) - Novo artigo.
- [Monitorize um recurso Azure com Azure Monitor](learn/quick-monitor-azure-resource.md) - Novo artigo.
   
## <a name="next-steps"></a>Passos seguintes

- Se quiser contribuir para a documentação do Azure Monitor, consulte o [Guia de Contribuintes docs](/contribute/).