---
title: Novidades na documentação do Monitor Azure
description: Atualizações significativas para a documentação do Monitor Do Azure atualizadas todos os meses.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 04/06/2020
ms.openlocfilehash: 8701acc6ac10dd9d31df99bb04dd030d156250e2
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871397"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Quais as novidades na documentação do Monitor Azure?
Este artigo fornece listas de artigos do Monitor Do Azure que são novos ou foram significativamente atualizados. Será atualizado na primeira semana de cada mês para incluir atualizações de artigos do mês anterior.

## <a name="april-2020"></a>Abril de 2020

### <a name="general"></a>Geral
- [Chave gerida pelo cliente do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys.md) - Secção adicionada em operações assíncronas
- Gerir espaços de [trabalho de Log Analytics no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access.md) - secções de registos personalizados atualizados.

### <a name="alerts"></a>Alertas
- [Regras de ação para alertas Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules) - Vídeo adicionado.
- [Visão geral de alerta e monitorização de notificação em Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview.md) - Vídeo adicionado.

### <a name="application-insights"></a>Application Insights
- [Mapa de aplicação em Insights de Aplicação Azure](https://docs.microsoft.com/azure/azure-monitor/app/app-map.md) - nomes de papéis em nuvem adicionados config para agente java.
- [Azure Application Insights .Net Agent API reference](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference.md) - Referência Consolidada da API.
- [Endereços IP utilizados por Application Insights e Log Analytics](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses.md) - Endereços IP atualizados para App Insights e Log Analytics APIs, Webhooks do grupo de ação, Governo dos EUA Azure.
- [Monitorize aplicações java em qualquer lugar](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config.md) - Novo artigo
- [Monitorize aplicações java em qualquer ambiente](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent.md) - Novo artigo
- [Monitor ize aplicações java em execução em qualquer ambiente](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments.md) - Novo artigo
- [Monitor aplicativos Java em execução nas instalações](https://docs.microsoft.com/azure/azure-monitor/app/java-on-premises.md) - Novo artigo
- [Remover insights de aplicação em estúdio visual](https://docs.microsoft.com/azure/azure-monitor/app/remove-application-insights.md) - novo artigo
- [Amostragem de telemetria em Insights de Aplicação Azure](https://docs.microsoft.com/azure/azure-monitor/app/sampling) - Fixar na amostra de taxa fixa em Python.

### <a name="containers"></a>Contentores
- [Configure Azure Red Hat OpenShift v4.x com Monitor Azure para contentores](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat4-setup) - Novo artigo
- [Como corrigir manualmente os problemas de sincronização do ServiceNow](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-resync-servicenow) - Novo artigo
- [Como parar de monitorizar o seu cluster Azure e Red Hat OpenShift v4](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-openshift-v4) - Novo artigo
- [Como parar de monitorizar o seu cluster Azure Red Hat OpenShift v3](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-openshift-v3) - Novo artigo
- [Como parar de monitorizar o seu cluster híbrido Kubernetes](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid) - Novo artigo

### <a name="insights"></a>Informações
- [Monitor Azure Key Vaults com Monitor Azure para cofres chave (pré-visualização)](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview) - Novo artigo

### <a name="logs"></a>Registos
- [Limites](https://docs.microsoft.com/azure/azure-monitor/service-limits) de serviço do Azure Monitor - Aceleração adicional da consulta do utilizador.
- [Gerir o uso e os custos para os registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage) - faturação adicional para clusters de Logs. Adicionou a consulta kusto para permitir aos clientes com um nível de preços por nó legado determinar se devem ou não mudar-se para um nível de Reserva per GB ou de Reserva de Capacidade.

### <a name="metrics"></a>Métricas
- [Características avançadas do Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) - Secção de agregação adicionada.

### <a name="workbooks"></a>Livros
- [Azure Monitor Books e Modelos](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-automate) de Gestor de Recursos Azure - Modelo de gestor de recursos adicionado para implementar um modelo de livro.



## <a name="march-2020"></a>Março de 2020

### <a name="general"></a>Geral
- [Visão geral do Monitor Azure](overview.md) - Vídeo de visão geral do Monitor Azure adicionado.
- [Configuração de chave gerida pelo cliente Do Azure Monitor](platform/customer-managed-keys.md) - Atualizações gerais.
- Referência de [dados do Azure Monitor](/azure/azure-monitor/reference) - Novo site.

### <a name="alerts"></a>Alertas

- [Criar, visualizar e gerir alertas](platform/alerts-activity-log.md) de registo de atividade no Monitor Azure - Explicação adicional do modelo de Gestor de Recursos.
- [Entenda como os alertas métricos funcionam no Monitor Azure.](platform/alerts-metric-overview.md) - Atualizado para apoio do governo.
- [Resolução de problemas Alertas e notificações](platform/alerts-troubleshoot.md) do Azure Monitor - Novo artigo

### <a name="application-insights"></a>Application Insights
- [Automatizar insights de aplicação Azure com powerShell](app/powershell.md) - Exemplos adicionais do ARMClient.
- [Exportação contínua de telemetria a partir de Insights de Aplicação](app/export-telemetry.md) - Adicione tabela com detalhes da estrutura de exportação.
- [Ative o Snapshot Debugger para aplicações .NET no Serviço de Aplicações Azure](app/snapshot-debugger-appservice.md) - exemplo de modelo de Gestor de Recursos Adicionado.
- [Gerir o uso e os custos para o Azure Application Insights](app/pricing.md) - Informações adicionais sobre o alerta da tampa de dados.
- [Monitorize aplicações Python com Monitor Azure (pré-visualização)](app/opencensus-python.md) - Métricas padrão adicionadas.
- [Suporte de mapa de fonte para aplicações JavaScript - Azure Monitor Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/source-map-support) - Novo artigo.


### <a name="containers"></a>Contentores
- [Azure Monitor FAQ](faq.md) - Atualização para o Monitor Azure para contentores.
- [Configure monitorização da GPU com Monitor Azure para contentores](insights/container-insights-gpu-monitoring.md) - Novo artigo

### <a name="insights"></a>Informações
- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Data de depreciação atualizada.

### <a name="logs"></a>Registos
- [Otimizar consultas](log-query/query-optimization.md) de log no Monitor Azure - condição cpu adicionada para a análise de XML e JSON.
- Eliminar e recuperar o espaço de [trabalho do Azure Log Analytics](platform/delete-workspace.md) - Resolução de problemas adicionada.
- [Utilize registos de monitores Azure com aplicações lógicas azure e power automate](platform/logicapp-flow-connector.md) - Atualizado para o novo conector Azure Monitor.

### <a name="metrics"></a>Métricas
- [Depreciação de métricas de disco no portal Azure](https://docs.microsoft.com/azure/azure-monitor/platform/portal-disk-metrics-deprecation) - Novo artigo.
- [Tutorial - Crie um gráfico de métricas no Monitor Azure](learn/tutorial-metrics-explorer.md) - Vídeo adicionado.

### <a name="platform-logs"></a>Registos da plataforma
- Recolher e analisar o log de atividade do [Azure no Azure Monitor](platform/activity-log-collect.md) - Reescrever para explicar melhor o registo de atividade de recolha com configurações de diagnóstico.

### <a name="virtual-machines"></a>Máquinas virtuais
- [Monitor Azure máquinas virtuais com Monitor Azure](insights/monitor-vm-azure.md) - Novo artigo.
- [Quickstart: Monitor Azure máquinas virtuais com Monitor Azure](learn/quick-monitor-azure-vm.md) - Atualizado para adicionar Monitor Azure para VMs.
- [Alertas do Monitor Azure para VMs](insights/vminsights-alerts.md) - Novo artigo
- Ativar o [Monitor Azure para visualização geral dos VMs](insights/vminsights-enable-overview.md) - Links de descarregamento de agentes atualizados.


Atualizações gerais para a disponibilidade geral do Monitor Azure para VMs
- [O que é o Monitor Azure para VMs?](insights/vminsights-overview.md)
- [Monitor Azure para VMs (GA) frequentemente feito perguntas](insights/vminsights-ga-release-faq.md) 
- [Ativar o Monitor Azure para VMs utilizando a Política Azure](insights/vminsights-enable-at-scale-policy.md) 
- [Como traçar o desempenho com o Monitor Azure para VMs](insights/vminsights-performance.md)
- [Como consultar registos do Monitor Azure para VMs](insights/vminsights-log-search.md)
- [Ver dependências de aplicativos com Monitor Azure para VMs](insights/vminsights-maps.md) 


### <a name="visualizations"></a>Visualizações
- [Visualização de dados do Azure Monitor](visualizations.md) - Atualizado para notar a depreciação planeada do View Designer.





## <a name="february-2020"></a>Fevereiro de 2020

### <a name="agents"></a>Agentes
Múltiplas atualizações como parte da reescrita do conteúdo da extensão de diagnósticos.

- Visão geral dos agentes de [monitorização do Azure](platform/agents-overview.md) - Tabelas reestruturadas para clarificar melhor as características únicas de cada agente.
- Visão geral da [extensão do Azure Diagnostics](platform/diagnostics-extension-overview.md) - Complete reescrever.
- [Utilize o armazenamento de blob para IIS e armazenamento de mesa para eventos no Azure Monitor](platform/diagnostics-extension-logs.md) - Reescrever geral para atualização e clareza.
- [Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](platform/diagnostics-extension-windows-install.md) - Novo artigo. 
- Esquema de [extensão de diagnóstico do Windows](platform/diagnostics-extension-schema-windows.md) - Reorganizado.
- [Envie dados da extensão de diagnóstico do Windows Azure para o Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) - Completamente reescrito e atualizado.
- Armazenar e ver dados de [diagnóstico no Armazenamento Azure](/azure/cloud-services/diagnostics-extension-to-storage) - Completamente reescritoe e atualizado.
- [Extensão](../virtual-machines/extensions/oms-windows.md) de máquina virtual Log Analytics para Windows - Melhor clarifica a relação com o agente Log Analytics.
- [Extensão de máquina virtual Azure Monitor para Linux](../virtual-machines/extensions/oms-linux.md) - Melhor esclarece a relação com o agente Log Analytics.




### <a name="application-insights"></a>Application Insights
- [Cordas de ligação em Insights de Aplicação Azure](app/sdk-connection-string.md) - Novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções

#### <a name="azure-monitor-for-containers"></a>Azure Monitor para Contentores
- Integrar o [Diretório Ativo Azure com o Serviço Azure Kubernetes](../aks/azure-ad-integration.md) - Nota adicional para a criação de uma aplicação de cliente para apoiar cluster habilitado para o RBAC para apoiar o Monitor Azure para contentores.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
- [O Monitor Azure para VMs (GA) perguntas frequentemente -](insights/vminsights-ga-release-faq.md) Alterar a forma como os dados de desempenho são armazenados.

#### <a name="office-365"></a>Office 365
- [Solução de gestão do Office 365 em Azure](insights/solution-office-365.md) - Data de depreciação atualizada.


### <a name="logs"></a>Registos
- [Otimizar consultas](log-query/query-optimization.md) de log no Azure Monitor - Novo artigo.
- [Gerir o uso e os custos dos registos do Monitor Azure](platform/manage-cost-storage.md) - Consultas de amostra melhoradas para ajudar a compreender o seu uso.

### <a name="metrics"></a>Métricas
- [Métricas da plataforma Azure Monitor exportáveis através](platform/metrics-supported-export-diagnostic-settings.md) de Definições de Diagnóstico - Secção adicionada sobre a alteração ao comportamento de nulos e valores nulos.


### <a name="visualizations"></a>Visualizações
Vários novos artigos para ver designer para guia de conversão de livros de trabalho.

- [Azure Monitor vê designer para guia](platform/view-designer-conversion-overview.md) de transição de livros de livros - Novo artigo.
- [Azure Monitor vê designer para opções](platform/view-designer-conversion-options.md) de conversão de livros de livros - Novo artigo.
- [Azure Monitor vê designer para conversões](platform/view-designer-conversion-tiles.md) de azulejos de livros - Novo artigo.
- [Azure Monitor vê designer para reconversão de livros de livros e acesso](platform/view-designer-conversion-access.md) - Novo artigo.
- [Azure Monitor vê designer para reconversão](platform/view-designer-conversion-tasks.md) de livros de trabalho tarefas comuns - Novo artigo.
- [Azure Monitor vê designer para exemplos](platform/view-designer-conversion-examples.md) de conversão de livros de livros - Novo artigo.




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
- [Configure Azure Monitor para recolha](insights/container-insights-agent-config.md) de dados de agentes de contentores - Detalhes adicionados para o agente de upgrade no Azure Red Hat OpenShift, e acrescentou informações adicionais para distinguir os métodos de atualização do agente.
- [Criar alertas de desempenho para o Monitor Azure para contentores](insights/container-insights-alerts.md) - Informação revista e etapas atualizadas para criar um alerta sobre os dados de desempenho armazenados no espaço de trabalho usando alertas de contexto de espaço de trabalho.
- [Monitorização da Kubernetes com o Monitor Azure para contentores](insights/container-insights-analyze.md) - Atualizou tanto o artigo de visão geral como o artigo de análise relativo ao suporte dos clusters windows Kubernetes.
- [Configure Azure Red Hat OpenShift clusters com Monitor Azure para recipientes](insights/container-insights-azure-redhat-setup.md) - Detalhes adicionados para o agente de upgrade no Azure Red Hat OpenShift, e acrescentou informações adicionais para distinguir os métodos para atualizar o agente.
- [Configure clusters Híbridos Kubernetes com Monitor Azure para contentores](insights/container-insights-hybrid-setup.md) - Atualizado para refletir suporte adicional para porta segura:10250 com o cAdvisor do Kubelet.
- [Como gerir o Monitor Azure para agente](insights/container-insights-manage-agent.md) de contentores - Detalhes atualizados relacionados com o comportamento e config de raspagem métrica com o Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.
- [Configure Azure Monitor para recipientes Integração Prometheus](insights/container-insights-prometheus-integration.md) - Detalhes atualizados relacionados com o comportamento e config de raspagem métrica com o Chapéu Vermelho Azure OpenShift em comparação com outros tipos de clusters Kubernetes.
- [Como atualizar o Monitor Azure para recipientes para métricas](insights/container-insights-update-metrics.md) - Detalhes atualizados relacionados com o comportamento e config de raspagem métrica com o Azure Red Hat OpenShift em comparação com outros tipos de clusters Kubernetes.


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
- [Application Insights for Worker Service apps (apps não HTTP)](app/worker-service.md) - Adicionou o nível de registo padrão ao código C# . Versão atualizada de referência do pacote.
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