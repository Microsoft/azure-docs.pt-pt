---
title: Novidades na documentação do Monitor Azure
description: Atualizações significativas para a documentação do Monitor Do Azure atualizadas todos os meses.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/05/2020
ms.openlocfilehash: ecee13850e735f827a5465e0f49039f514afe233
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657746"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Quais as novidades na documentação do Monitor Azure?
Este artigo fornece listas de artigos do Monitor Do Azure que são novos ou foram significativamente atualizados. Será atualizado na primeira semana de cada mês para incluir atualizações de artigos do mês anterior.

## <a name="january-2020"></a>Janeiro de 2020

### <a name="general"></a>Geral
- [O que é monitorizado pelo Monitor Azure?](monitor-reference.md) - Novo artigo.

### <a name="agents"></a>Agentes
- [Recolher dados de registo com o agente Azure Log Analytics](platform/log-analytics-agent.md) - Tabela atualizada de requisitos de firewall de rede.


### <a name="alerts"></a>Alertas
- [Criar e gerir grupos](platform/action-groups.md) de ação no portal Azure - Definição removida para funções v2 que já não são necessárias.
- [Crie um alerta métrico com um modelo de Gestor de Recursos](platform/alerts-metric-create-templates.md) - Exemplo adicionado para o parâmetro *ignorantes dataantes.*  Restrições adicionais sobre regras de múltiplos critérios.
- [Utilização de log analytics alerta REST API](platform/api-alerts.md) - Exemplo JSON corrigido.


### <a name="application-insights"></a>Application Insights
- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Atualizou a secção de teste de disponibilidade com a forma de adicionar uma regra de porta de entrada para permitir o tráfego utilizando grupos de segurança da rede Azure.
- [Problemas de resolução de problemas com o Perfil de Insights de Aplicação Azure](app/profiler-troubleshooting.md) - Resolução geral atualizada de problemas.
- [Amostragem de telemetria em Insights de Aplicação Azure](app/sampling.md) - Atualizado e reestruturado para melhorar a legibilidade com base no feedback do cliente.


### <a name="data-security"></a>Segurança de dados
- [Configuração da chave gerida pelo cliente do Azure Monitor](platform/customer-managed-keys.md) - Novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções

#### <a name="azure-monitor-for-containers"></a>Azure Monitor para Contentores
- [Configure Azure Monitor para recolha](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config) de dados de agentes de contentores - Detalhes adicionados para o agente de upgrade no Azure Red Hat OpenShift, e acrescentou informações adicionais para distinguir os métodos de atualização do agente.
- [Criar alertas de desempenho para o Monitor Azure para contentores](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-alerts) - Informação revista e etapas atualizadas para criar um alerta sobre os dados de desempenho armazenados no espaço de trabalho usando alertas de contexto de espaço de trabalho.
- [Monitorização da Kubernetes com o Monitor Azure para contentores](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze) - Atualizou tanto o artigo de visão geral como o artigo de análise relativo ao suporte dos clusters windows Kubernetes.
- [Configure Azure Red Hat OpenShift clusters com Monitor Azure para recipientes](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat-setup) - Detalhes adicionados para o agente de upgrade no Azure Red Hat OpenShift, e acrescentou informações adicionais para distinguir os métodos para atualizar o agente.
- [Configure clusters Híbridos Kubernetes com Monitor Azure para contentores](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-hybrid-setup) - Atualizado para refletir suporte adicional para porta segura:10250 com o cAdvisor do Kubelet.
- [Como gerir o Monitor Azure para agente](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-manage-agent) de contentores - Detalhes atualizados relacionados com o comportamento e config de raspagem métrica com o Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.
- [Configure Azure Monitor para recipientes Integração Prometheus](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration) - Detalhes atualizados relacionados com o comportamento e config de raspagem métrica com o Chapéu Vermelho Azure OpenShift em comparação com outros tipos de clusters Kubernetes.
- [Como atualizar o Monitor Azure para recipientes para métricas](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-update-metrics) - Detalhes atualizados relacionados com o comportamento e config de raspagem métrica com o Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
- [O Monitor Azure para VMs (GA) fez frequentemente perguntas](insights/vminsights-ga-release-faq.md) - Acrescentou informações sobre a modernização do espaço de trabalho e agentes para uma nova versão.

#### <a name="office-365"></a>Office 365
- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Detalhes adicionados e FAQ sobre migração para a solução Office 365 em Azure Sentinel. Secção de embarque removida.



### <a name="logs"></a>Registos
- Gerir espaços de [trabalho de Log Analytics no Monitor Azure](platform/manage-access.md) - Atualizações para Não ações.
- [Gerir a utilização e os custos dos Registos do Monitor Do Azure](platform/manage-cost-storage.md) - Esclarecimento adicional sobre o cálculo do volume de dados na secção Modelo de Preços.
- Utilize modelos do Gestor de [Recursos Azure para criar e configurar um espaço](platform/template-workspace-configuration.md) de trabalho de log analytics - modelo atualizado com novos níveis de preços.


### <a name="platform-logs"></a>Registos da plataforma
- [Colete o registo da Atividade Azure com configurações de diagnóstico- Monitor Azure](platform/diagnostic-settings-legacy.md) - Informações adicionais sobre propriedades alteradas.
- [Exportar o Registo de Atividades do Azure](platform/activity-log-export.md) - Atualizado para alterações de UI. 





## <a name="december-2019"></a>Dezembro de 2019

### <a name="agents"></a>Agentes
- [Ligue os computadores Linux ao Azure Monitor](platform/agent-linux.md) - Novo artigo.

### <a name="alerts"></a>Alertas
- [Crie um alerta métrico com um modelo de Gestor de Recursos](platform/alerts-metric-create-templates.md) - Exemplo adicionado para métrica personalizada.
- [Criação de Alertas com Limiares Dinâmicos no Monitor Azure](platform/alerts-dynamic-thresholds.md) - Secção adicional sobre a interpretação de gráficos dinâmicos de limiares.
- [Visão geral de alerta e monitorização de notificação em Azure](platform/alerts-overview.md) - Consulta de Gráfico de Recursos Atualizado.
- [Recursos suportados para alertas métricos no Monitor Do Azure](platform/alerts-metric-near-real-time.md) - Atualização para métricas e dimensões suportadas.
- [Mude de legacy Log Analytics alerta API para novo Azure Alerts API](platform/alerts-log-api-switch.md) - Nota adicionada sobre o nome de alerta modificado.
- [Entenda como os alertas métricos funcionam no Monitor Azure.](platform/alerts-metric-overview.md) - Adicionou tipos de recursos suportados para monitorização em escala.

### <a name="application-insights"></a>Application Insights
- [Application Insights for Worker Service apps (aplicações não HTTP)](app/worker-service.md) - Nível de registo padrão adicionado ao C# código. Versão atualizada de referência do pacote.
- [ApplicationInsights.config reference - Azure](app/configuration-with-applicationinsights-config.md) - Código de amostra atualizado.
- [Automatizar insights de aplicação Azure com powerShell](app/powershell.md) - Modelo de atualização para gestor de recursos.
- [Pacotes NuGet](app/nuget.md) de Insights de Aplicação Do Monitor Azure - Versões de pacoteatualizadas.
- [Crie um novo recurso Azure Application Insights](app/create-new-resource.md) - Nota adicionada ao nome globalmente único.
- [Diagnosticar com Live Metrics Stream - Insights de aplicação Azure](app/live-stream.md) - Atualizado ASP.NET requisito de versão Core SDK.
- [Contadores de eventos em Insights](app/eventcounters.md) de Aplicação - Categoria e tabela atualizadas para métricas personalizadas.
- Explore os registos de [rastreio de Java em Insights de Aplicação Azure](app/java-trace-logs.md) - Configuração adicionada para o limiar de registo do agente Java.
- [Endereços IP utilizados por Application Insights e Log Analytics](app/ip-addresses.md) - Endereços IP atualizados para live metrics Stream.
- Monitor Desempenho de serviços de [aplicações Do Monitor Azure](app/azure-web-apps.md) - Suporte adicional para ASP.NET Core 3.0. 
- [Monitor Python aplicações com Azure Monitor (pré-visualização)](app/opencensus-python.md) - Esclarecimento adicional para o mapeamento de esquemas OpenCensus Python para o Azure . Monitorizar esquema
- [Notas de lançamento para Insights de Aplicação Azure](app/release-notes.md) - notas adicionadas para lançamentos mais antigos.
- [Canais de telemetria em Insights de Aplicação Azure](app/telemetry-channels.md) - Duração atualizada para dados descartados durante um período prolongado de ligação perdida.
- [Amostragem de telemetria em Insights de Aplicação Azure](app/sampling.md) - código corrigido para TelemettryInitializer personalizado.
- [Troubleshoot Application Insights em um projeto web Java](app/java-troubleshoot.md) - Declaração removida sobre não apoiar a coleção de dependência em JDK 9.

### <a name="insights-and-solutions"></a>Insights e soluções
- [Monitor Azure para recipientes frequentemente questionado](insights/container-insights-faq.md) - Pergunta adicional sobre campos de imagem e nome.
- [Solução Azure SQL Analytics no Monitor Azure](insights/azure-sql.md) - Base de Dados Atualizada aguarda suporte de Instância Gerida.
- [Configure O Monitor Azure para recolha](insights/container-insights-agent-config.md) de dados do agente de contentores - Definição adicionada para enrich_container_logs.
- [Configure aglomerados Híbridos Kubernetes com Monitor Azure para recipientes](insights/container-insights-hybrid-setup.md) - Secção de resolução de problemas adicionada.
- Monitor ativo estatuto de [replicação do Diretório com o Monitor Azure](insights/ad-replication-status.md) - .NET Quadro pré-requisito atualizado.
- [Solução de Monitor de Desempenho de Rede em Azure](insights/network-performance-monitor.md) - Regiões apoiadas adicionadas.
- [Otimize o seu ambiente de Diretório Ativo com o Monitor Azure](insights/ad-assessment.md) - .NET Framework pré-requisito atualizado.
- [Otimize o ambiente do SQL Server com o Monitor Azure](insights/sql-assessment.md) - .NET Framework pré-requisito atualizado.
- [Otimize o ambiente do Gestor de Operações do Centro de Sistemacom a Azure Log Analytics](insights/scom-assessment.md) - .NET Framework pré-requisito atualizado.
- [Ligações suportadas com o Conector de Gestão de Serviços de TI no Azure Log Analytics](platform/itsmc-connections.md) - Adicionado nova-iorquino ao id do cliente pré-requisito e segredo de cliente.

### <a name="logs"></a>Registos
- Eliminar e recuperar o espaço de [trabalho do Azure Log Analytics](platform/delete-workspace.md) - Método PowerShell adicionado.
- [Conceber a sua implementação de Registos De MonitorEs Azure](platform/design-logs-deployment.md) - A taxa de ingestão de um espaço de trabalho aumentou.

### <a name="metrics"></a>Métricas
- [Métricas da plataforma Azure Monitor exportáveis através de Definições](platform/metrics-supported-export-diagnostic-settings.md) de Diagnóstico - Novo artigo.

### <a name="platform-logs"></a>Registos da plataforma
Vários artigos atualizados como parte da reestruturação do conteúdo para registos de plataformas com base em novas funcionalidades para configurar o registo de atividade utilizando definições de diagnóstico.

- [Registos de recursos do Archive Azure para conta de armazenamento](platform/resource-logs-collect-storage.md)
- [Esquema de evento de log de atividade azure](platform/activity-log-schema.md)
- [Limites de serviço do Azure Monitor](service-limits.md)
- [Recolher e analisar registos de atividade do Azure no espaço de trabalho do Log Analytics](platform/activity-log-collect.md)
- [Recolher registo de atividade do Azure com definições de diagnóstico (pré-visualização) - Monitor Azure](platform/diagnostic-settings-legacy.md)
- [Colete logs da Atividade Azure em um espaço de trabalho Log Analytics através de inquilinos do Azure](platform/activity-log-collect-tenants.md)
- [Recolher registos de recursos Azure no espaço de trabalho do Log Analytics](platform/resource-logs-collect-workspace.md)
- [Criar definição de diagnóstico em Azure usando o modelo de Gestor de Recursos](platform/diagnostic-settings-template.md)
- [Criar definição de diagnóstico para recolher registos e métricas em Azure](platform/diagnostic-settings.md)
- [Exportar o Registo de Atividades Do Azure](platform/activity-log-export.md)
- [Visão geral dos registos da plataforma Azure](platform/platform-logs-overview.md)
- [Stream Azure monitoriza os dados para o centro de eventos](platform/stream-monitoring-data-event-hubs.md)
- [Stream Azure logs plataforma para um hub de eventos](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Inícios rápidos e tutoriais

- [Crie um gráfico de métricas no Azure Monitor](learn/tutorial-metrics-explorer.md) - Novo artigo.
- [Recolher registos de recursos de um Recurso Azure e analisar com o Azure Monitor](learn/tutorial-resource-logs.md) - Novo artigo.
- [Monitorize um recurso Azure com o Azure Monitor](learn/quick-monitor-azure-resource.md) - Novo artigo.
   
## <a name="next-steps"></a>Passos seguintes

- Se quiser contribuir para a documentação do Monitor Azure, consulte o Guia de [Contribuinte do Docs](https://docs.microsoft.com/contribute/).