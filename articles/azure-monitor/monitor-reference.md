---
title: O que é monitorizado pelo Monitor Azure
description: Referência de todos os serviços e outros recursos monitorizados pelo Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 3cd330e9c4ceba2feeb7a74cafe9f094fd03d690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249168"
---
# <a name="what-is-monitored-by-azure-monitor"></a>O que é monitorizado pelo Monitor Azure?
Este artigo descreve as diferentes aplicações e serviços que são monitorizados pelo Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Insights e soluções fundamentais
Os insights e soluções fundamentais são considerados parte do Azure Monitor e seguem os acordos de apoio e nível de serviço para o Azure. São apoiados em todas as regiões do Azure onde o Azure Monitor está disponível.

### <a name="insights"></a>Informações

Insights fornecem uma experiência de monitorização personalizada para aplicações e serviços particulares. Recolhem e analisam troncos e métricas.

| Informações | Descrição |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Serviço extensível de Gestão de Desempenho de Aplicações (APM) para monitorizar a sua aplicação web ao vivo em qualquer plataforma. |
| [Azure Monitor para Contentores](insights/container-insights-overview.md) | Monitoriza o desempenho das cargas de trabalho dos contentores implantadas em instâncias de contentores Azure ou em clusters Kubernetes geridos hospedados no Serviço Azure Kubernetes (AKS). |
| [Monitor Azure para Cosmos DB (pré-visualização)](insights/cosmosdb-insights-overview.md) | Proporciona uma visão do desempenho global, falhas, capacidade e saúde operacional de todos os seus recursos Azure Cosmos DB numa experiência interativa unificada. |
| [Monitor Azure para Redes (pré-visualização)](insights/network-insights-overview.md) | Proporciona uma visão abrangente da saúde e das métricas para todos os recursos da sua rede. A capacidade avançada de pesquisa ajuda-o a identificar dependências de recursos, permitindo cenários como identificar recursos que estão hospedando o seu website, simplesmente procurando o nome do seu site. |
[Monitor Azure para Grupos de Recursos (pré-visualização)](insights/resource-group-insights.md) |  Triagem e diagnosticar quaisquer problemas que os seus recursos individuais encontrem, ao mesmo tempo que oferece contexto sobre a saúde e desempenho do grupo de recursos como um todo. |
| [Monitor Azure para Armazenamento (pré-visualização)](insights/storage-insights-overview.md) | Fornece uma monitorização abrangente das suas contas de Armazenamento Azure, proporcionando uma visão unificada do desempenho, capacidade e disponibilidade dos seus serviços de Armazenamento Azure. |
| [Monitor Azure para VMs (pré-visualização)](insights/container-insights-overview.md) | Monitoriza as suas máquinas virtuais Azure (VM) e conjuntos de escala de máquinas virtuais à escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos. |

### <a name="core-solutions"></a>Soluções centrais

As soluções baseiam-se em consultas de registo e vistas personalizadas para uma determinada aplicação ou serviço. Recolhem e analisam apenas os registos e estão a ser depreciados ao longo do tempo em favor de insights.

| Solução | Descrição |
|:---|:---|
| [Saúde do agente](insights/solution-agenthealth.md) | Analise a saúde e configuração dos agentes Log Analytics. |
| [Gestão de alertas](platform/alert-management-solution.md) | Analise os alertas recolhidos do System Center Operations Manager, Nagios ou Zabbix. |
| [Mapa de Serviços](insights/service-map.md) | Descobre automaticamente componentes de aplicações nos sistemas Windows e Linux e mapeia a comunicação entre serviços. |



## <a name="azure-services"></a>Serviços do Azure
A tabela seguinte lista os serviços Azure e os dados que recolhem no Monitor Azure. 

- Métricas - O serviço recolhe automaticamente métricas em Métricas de Monitor Azure. 
- Registos - O serviço suporta configurações de diagnóstico que podem recolher registos e métricas da plataforma para registos do Monitor Azure.
- Insight - Existe uma visão disponível para o serviço que proporciona uma experiência de monitorização personalizada para o serviço.

| Serviço | Métricas | Registos | Informações | Notas |
|:---|:---|:---|:---|:---|
|Active Directory | Não | Sim | [Sim](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Não | Não | Não |  |
|Active Directory Domain Services | Não | Sim | Não |  |
|Registo de atividades | Não | Sim | Não | |
|Proteção Avançada Contra Ameaças | Não | Não | Não |  |
|Assistente | Não | Não | Não |  |
|AI Builder | Não | Não | Não |  |
|Analysis Services | Sim | Sim | Não |  |
|API para FHIR | Não | Não | Não |  |
|Gestão de API | Sim | Sim | Não |  |
|Serviço de Aplicações | Sim | Sim | Não |  |
|AppConfig | Não | Não | Não |  |
|Gateway de Aplicação | Sim | Sim | Não |  |
|Serviço de Attestation | Não | Não | Não |  |
|Automatização | Sim | Sim | Não |  |
|Gestor de Serviços Azure (RDFE) | Não | Não | Não |  |
|Cópia de segurança | Não | Sim | Não |  |
|Bastion | Não | Não | Não |  |
|Batch | Sim | Sim | Não |  |
|Batch AI | Não | Não | Não |  |
|Blockchain Service | Não | Sim | Não |  |
|Esquemas | Não | Não | Não |  |
|Serviço de Bot | Não | Não | Não |  |
|Serviços Cloud | Sim | Sim | Não | Agente necessário para monitorizar o sistema operativo dos hóspedes e fluxos de trabalho.  |
|Cloud Shell, | Não | Não | Não |  |
|Serviços Cognitivos | Sim | Sim | Não |  |
|Container Instances | Sim | Não | Não |  |
|Registo de Contentor | Sim | Sim | Não |  |
|Rede de Entrega de Conteúdos (CDN) | Não | Sim | Não |  |
|Cosmos DB | Sim | Sim | [Sim](insights/cosmosdb-insights-overview.md) |  |
|Gestão de Custos | Não | Não | Não |  |
|Data Box | Não | Não | Não |  |
|Catálogo de Dados Gen2 | Não | Não | Não |  |
|Data Explorer | Sim | Sim | Não |  |
|Data Factory | Sim | Sim | Não |  |
|Fábrica de Dados v2 | Não | Sim | Não |  |
|Data Share | Não | Não | Não |  |
|Database for MariaDB | Sim | Sim | Não |  |
|Base de Dados para MySQL | Sim | Sim | Não |  |
|Base de Dados para PostgreSQL | Sim | Sim | Não |  |
|Serviço de Migração de Bases de Dados | Não | Não | Não |  |
|Databricks | Não | Sim | Não |  |
|Proteção contra DDOS | Sim | Sim | Não |  |
|DevOps | Não | Não | Não |  |
|DNS | Sim | Não | Não |  |
|Nomes de domínio | Não | Não | Não |  |
|DPS | Não | Não | Não |  |
|Participação do Cliente da Dinâmica 365 | Não | Não | Não |  |
|Dinâmica 365 Finanças e Operações | Não | Não | Não |  |
|Event Grid | Sim | Não | Não |  |
|Event Hubs | Sim | Sim | Não |  |
|ExpressRoute | Sim | Sim | Não |  |
|Firewall | Sim | Sim | Não |  |
|Front Door | Sim | Sim | Não |  |
|Funções | Sim | Sim | Não |  |
|HDInsight | Não | Sim | Não |  |
|HPC Cache | Não | Não | Não |  |
|Proteção de Informações | Não | Sim | Não |  |
|Intune | Não | Sim | Não |  |
|Centro de IoT | Não | Não | Não |  |
|IoT Hub | Sim | Sim | Não |  |
|Cofre de Chaves | Sim | Sim | Não |  |
|Serviço do Kubernetes (AKS) | Não | Não | [Sim](insights/container-insights-overview.md)  |  |
|Load balancer | Sim | Sim | Não |  |
|Aplicações Lógicas | Sim | Sim | Não |  |
|Serviço do Machine Learning | Não | Não | Não |  |
|Aplicações Geridas  | Não | Não | Não |  |
|Maps  | Não | Não | Não |  |
|Serviços de Multimédia | Sim | Sim | Não |  |
|Microsoft Flow | Não | Não | Não |  |
|Ambiente de Trabalho Gerido da Microsoft | Não | Não | Não |  |
|Microsoft PowerApps | Não | Não | Não |  |
|Microsoft Social Engagement | Não | Não | Não |  |
|Microsoft Stream | Sim | Sim | Não |  |
|Migrar | Não | Não | Não |  |
|Multi-Factor Authentication | Não | Sim | Não |  |
|Observador de Rede | Sim | Sim | Não |  |
|Hubs de Notificação | Sim | Não | Não |  |
|Conjuntos de Dados Abertos | Não | Não | Não |  |
|Política | Não | Não | Não |  |
|Power BI | Sim | Sim | Não |  |
|Power BI Embedded | Não | Não | Não |  |
|Ligação Privada | Não | Não | Não |  |
|Plataforma de Comunicação project spool | Não | Não | Não |  |
|Red Hat OpenShift | Não | Não | Não |  |
|Cache de Redis | Sim | Sim | Não |  |
|Gráfico de Recursos | Não | Não | Não |  |
|Resource Manager | Não | Não | Não |  |
|Pesquisa de Retalho – por Bing | Não | Não | Não |  |
|Pesquisa | Sim | Sim | Não |  |
|Service Bus | Sim | Sim | Não |  |
|Service Fabric | Não | Sim | Não | Agente necessário para monitorizar o sistema operativo dos hóspedes e fluxos de trabalho.  |
|Portal de Inscrição | Não | Não | Não |  |
|Site Recovery | Não | Sim | Não |  |
|Serviço de Nuvem de primavera | Não | Não | Não |  |
|SQL Data Warehouse | Sim | Sim | Não |  |
|SQL Database | Sim | Sim | Não |  |
|SQL Server Stretch Database | Sim | Sim | Não |  |
|Pilha | Não | Não | Não |  |
|Storage | Sim | Não | [Sim](insights/storage-insights-overview.md) |  |
|Cache de armazenamento | Não | Não | Não |  |
|Serviços de Sincronização de Armazenamento | Não | Não | Não |  |
|Stream Analytics | Sim | Sim | Não |  |
|Time Series Insights | Sim | Sim | Não |  |
|TINA | Não | Não | Não |  |
|Gestor de Tráfego | Sim | Sim | Não |  |
|Impressão Universal | Não | Não | Não |  |
|Conjuntos de Dimensionamento de Máquinas Virtuais | Não | Sim | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorizar o sistema operativo dos hóspedes e fluxos de trabalho. |
|Máquinas Virtuais | Sim | Sim | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorizar o sistema operativo dos hóspedes e fluxos de trabalho. |
|Rede Virtual | Sim | Sim | [Sim](insights/network-insights-overview.md) |  |
|Rede Virtual - Registos de Fluxo NSG | Não | Sim | Não |  |
|Gateway de VPN | Sim | Sim | Não |  |
|Windows Virtual Desktop | Não | Não | Não |  |


## <a name="product-integrations"></a>Integrações de produtos
Os serviços e soluções na tabela seguinte armazenam os seus dados num espaço de trabalho do Log Analytics para que possam ser analisados com outros dados de registo recolhidos pelo Azure Monitor.

| Produto/Serviço | Descrição |
|:---|:---|
| [Automatização do Azure](/azure/automation/) | Gerir as atualizações do sistema operativo e rastrear alterações nos computadores Windows e Linux. Ver Gestão de Rastreio e [Atualização](../automation/automation-update-management.md)de [Alterações](../automation/change-tracking.md) . |
| [Proteção de Informação Azure](https://docs.microsoft.com/azure/information-protection/) | Classifique e proteja opcionalmente documentos e e-mails. Consulte o [relatório central para a Proteção de Informação do Azure](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Centro de Segurança do Azure](/azure/security-center/) | Colete e analise eventos de segurança e realize análise de ameaças. Ver [recolha de dados no Centro de Segurança Azure](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Conecta-se a diferentes fontes, incluindo o Office 365 e o Amazon Web Services Cloud Trail. Ver [Ligar fontes](/azure/sentinel/connect-data-sources)de dados . |
| [Análise do Cofre de Chaves](insights/azure-key-vault.md) | Analise os registos de auditoria do cofre de chaves Azure. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Crie uma definição de diagnóstico para enviar registos para o Monitor Azure. Consulte Enviar dados de registo para armazenamento, centros de [eventos ou análises de registo em Intune (pré-visualização)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Rede  | [Monitor](insights/network-performance-monitor.md) de Desempenho da Rede - Monitor de conectividade e desempenho da rede para os pontos finais de serviço e aplicação.<br>[Gateway de aplicação Azure](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - Analise registos e métricas do Portal de Aplicação Azure.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) - Analisa os registos de fluxo do grupo de segurança da rede Desfluxo (NSG) para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. |
| [Office 365](insights/solution-office-365.md) | Monitorize o ambiente do Office 365. Versão atualizada com melhor embarque disponível através do Azure Sentinel. |
| [SQL Analytics](insights/azure-sql.md) | Monitorize o desempenho das bases de dados Azure SQL, piscinas elásticas e instâncias geridas em escala e em várias subscrições. |
| [Surface Hub](insights/surface-hubs.md) | Acompanhe a saúde e o uso dos dispositivos Surface Hub. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Recolha dados dos agentes do Gestor de Operações ligando o seu grupo de gestão ao Azure Monitor. Ver [Connect Operations Manager to Azure Monitor](platform/om-agents.md)<br> Avalie o risco e a saúde do seu grupo de gestão de Gestão de Operações do Centro de Sistema com solução de Avaliação do Gestor de [Operações.](insights/scom-assessment.md) |
| [Quartos de equipas da Microsoft](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Gestão integrada e de ponta a ponta dos dispositivos Microsoft Teams Rooms. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Construir, testar e distribuir aplicações e, em seguida, monitorizar o seu estado e utilização. Consulte [Comece a analisar a sua aplicação móvel com App Center e Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Conformidade com a atualização do Windows](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) - Avalie as atualizações do seu ambiente de trabalho do Windows.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) - Integra-se com o Gestor de Configuração para fornecer informações e inteligência para tomar decisões mais informadas sobre a prontidão da atualização dos seus clientes Windows. |



## <a name="other-solutions"></a>Outras soluções
Outras soluções estão disponíveis para monitorizar diferentes aplicações e serviços, mas o desenvolvimento ativo parou e podem não estar disponíveis em todas as regiões. Estão abrangidos pelo acordo de nível de serviço de ingestão de dados Azure Log Analytics.

| Solução | Descrição |
|:---|:---|
| [Verificação de saúde do Diretório Ativo](insights/ad-assessment.md) | Avalie o risco e a saúde dos seus ambientes de Diretório Ativo. |
| [Estado de replicação do Diretório Ativo](insights/ad-replication-status.md) | Monitoriza regularmente o seu ambiente de Diretório Ativo para eventuais falhas de replicação. |
| [Análise de registo de atividade](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | Analisar entradas de registo de atividade utilizando consultas e vistas predefinidas. |
| [DNS Analytics (pré-visualização)](insights/dns-analytics.md) | Recolhe, analisa e correlaciona registos analíticos e de auditoria do Windows DNS e outros dados relacionados dos seus servidores DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Colete, veja e analise as métricas de saúde e desempenho do seu sistema de memória de nuvem, através de múltiplas implementações. |
| [Contentores](insights/containers.md) | Veja e gere os anfitriões de contentores Docker e Windows. |
| [Avaliações a pedido](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Avalie e otimize a disponibilidade, segurança e desempenho dos seus ambientes tecnológicos no local, híbridos e cloud Microsoft. |
| [Verificação de saúde SQL](insights/sql-assessment.md) | Avalie o risco e a saúde dos ambientes do Seu Servidor SQL.  |
| [Ligar Dados](insights/wire-data.md) | Dados consolidados de rede e desempenho recolhidos a partir de computadores ligados ao Windows e ligados ao Linux com o agente Log Analytics. |


## <a name="third-party-integration"></a>Integração de terceiros

| Solução | Descrição |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | O Conector de Gestão de Serviços de TI (ITSMC) permite ligar o Azure e um produto/serviço de gestão de serviços de TI suportado (ITSM).  |


## <a name="resources-outside-of-azure"></a>Recursos fora de Azure
O Azure Monitor pode recolher dados de recursos fora do Azure utilizando os métodos listados na tabela seguinte.

| Recurso | Método |
|:---|:---|
| Aplicações | Monitorize aplicações web fora do Azure utilizando insights de aplicação. Ver [o que é Informação de Aplicação?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) |
| Virtual Machines | Utilize o agente Log Analytics para recolher dados do sistema operativo de máquinas virtuais de hóspedes em outros ambientes de nuvem ou no local. Consulte [recolher dados de registo com o agente Log Analytics](platform/log-analytics-agent.md). |
| Cliente rest API | ApIs separados estão disponíveis para escrever dados para Registos e Métricas do Monitor Azure de qualquer cliente da API REST. Consulte enviar dados de [registo para o Monitor Azure com a API de Coletor de Dados HTTP](platform/data-collector-api.md) para Registos e enviar [métricas personalizadas para um recurso Azure para a loja métrica Do Monitor Azure utilizando uma API REST](platform/metrics-store-custom-rest-api.md) para Métricas. |



## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre a plataforma de [dados Azure Monitor que armazena os registos e métricas recolhidos por insights e soluções.](platform/data-platform.md)
- Complete um [tutorial sobre monitorização de um recurso Azure.](learn/tutorial-resource-logs.md)
- Complete um tutorial sobre a escrita de uma consulta de [registo para analisar dados em Registos do Monitor do Azure](learn/tutorial-resource-logs.md).
- Complete um tutorial sobre a criação de [um gráfico de métricas para analisar dados em Métricas de Monitor De Azure](learn/tutorial-metrics-explorer.md).

 
