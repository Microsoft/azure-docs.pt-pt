---
title: O que é monitorizado pelo Azure Monitor
description: Referência de todos os serviços e outros recursos monitorizados pelo Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/15/2020
ms.openlocfilehash: 146deba7a0ef1e0dc5ffe03f9ad414e752058274
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945380"
---
# <a name="what-is-monitored-by-azure-monitor"></a>O que é monitorizado pelo Azure Monitor?
Este artigo descreve as diferentes aplicações e serviços que são monitorizados pelo Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Insights e soluções fundamentais
Os principais insights e soluções são considerados parte do Azure Monitor e seguem os acordos de apoio e nível de serviço para o Azure. São apoiados em todas as regiões do Azure onde o Azure Monitor está disponível.

### <a name="insights"></a>Informações

Os insights fornecem uma experiência de monitorização personalizada para aplicações e serviços específicos. Recolhem e analisam registos e métricas.

| Informações | Description |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Serviço extensível de Gestão de Desempenho de Aplicações (APM) para monitorizar a sua aplicação web ao vivo em qualquer plataforma. |
| [Monitor Azure para contentores](insights/container-insights-overview.md) | Monitoriza o desempenho das cargas de trabalho dos contentores implantadas em instâncias de contentores Azure ou em agrupamentos de Kubernetes geridos alojados no Serviço Azure Kubernetes (AKS). |
| [Monitor Azure para Cosmos DB](insights/cosmosdb-insights-overview.md) | Proporciona uma visão do desempenho geral, falhas, capacidade e saúde operacional de todos os seus recursos DB Azure Cosmos numa experiência interativa unificada. |
| [Monitor Azure para redes (pré-visualização)](insights/network-insights-overview.md) | Fornece uma visão abrangente da saúde e métricas para todos os seus recursos de rede. A capacidade avançada de pesquisa ajuda-o a identificar dependências de recursos, permitindo cenários como identificar recursos que estão hospedando o seu website, simplesmente procurando o nome do seu site. |
[Monitor Azure para grupos de recursos (pré-visualização)](insights/resource-group-insights.md) |  Triagem e diagnóstico de quaisquer problemas que os seus recursos individuais encontrem, ao mesmo tempo que oferece contexto quanto à saúde e desempenho do grupo de recursos como um todo. |
| [Monitor Azure para armazenamento](insights/storage-insights-overview.md) | Fornece uma monitorização abrangente das suas contas de Armazenamento Azure, proporcionando uma visão unificada do desempenho, capacidade e disponibilidade dos seus serviços de armazenamento Azure. |
| [Azure Monitor para VMs](insights/vminsights-overview.md) | Monitoriza as suas máquinas virtuais Azure (VM) e conjuntos de escala de máquinas virtuais à escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos. |
| [Monitor Azure para Cofre de Chaves (pré-visualização)](insights/key-vaults-insights-overview.md) | Povides monitoriza completamente os seus cofres chave, fornecendo uma visão unificada dos seus pedidos de Cofre chave, desempenho, falhas e latência. |
| [Monitor Azure para Cache Azure para Redis (pré-visualização)](insights/redis-cache-insights-overview.md) |  Proporciona uma visão unificada e interativa do desempenho global, falhas, capacidade e saúde operacional. |


### <a name="core-solutions"></a>Soluções centrais

As soluções baseiam-se em consultas de registo e vistas personalizadas para uma determinada aplicação ou serviço. Recolhem e analisam apenas registos e estão a ser depreciados ao longo do tempo em favor de insights.

| Solução | Description |
|:---|:---|
| [Saúde do agente](insights/solution-agenthealth.md) | Analise a saúde e configuração dos agentes do Log Analytics. |
| [Gestão de alertas](platform/alert-management-solution.md) | Analise os alertas recolhidos pelo Gestor de Operações do Centro de Sistema, Nagios ou Zabbix. |
| [Mapa de Serviços](insights/service-map.md) | Descobre automaticamente componentes de aplicações nos sistemas Windows e Linux e mapeia a comunicação entre os serviços. |



## <a name="azure-services"></a>Serviços do Azure
A tabela que se segue lista os serviços Azure e os dados que recolhem no Azure Monitor. 

- Métricas - O serviço recolhe automaticamente métricas em Métricas do Monitor Azure. 
- Registos - O serviço suporta definições de diagnóstico que podem recolher registos e métricas da plataforma para registos do Monitor Azure.
- Insight - Existe uma visão disponível para o serviço que proporciona uma experiência de monitorização personalizada para o serviço.

| Serviço | Métricas | Registos | Informações | Notas |
|:---|:---|:---|:---|:---|
|Active Directory | No | Yes | [Sim](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Não | Não | Não |  |
|Active Directory Domain Services | No | Yes | No |  |
|Registo de atividades | No | Yes | No | |
|Proteção Avançada Contra Ameaças | Não | Não | Não |  |
|Assistente | Não | Não | Não |  |
|AI Builder | Não | Não | Não |  |
|Analysis Services | Sim | Sim | No |  |
|API para FHIR | Não | Não | Não |  |
|Gestão de API | Sim | Sim | No |  |
|Serviço de Aplicações | Sim | Sim | No |  |
|AppConfig | Não | Não | Não |  |
|Gateway de Aplicação | Sim | Sim | No |  |
|Serviço de Attestation | Não | Não | Não |  |
|Automatização | Sim | Sim | No |  |
|Gestor de Serviços Azure (RDFE) | Não | Não | Não |  |
|Backup | No | Yes | No |  |
|Bastion | Não | Não | Não |  |
|Batch | Sim | Sim | No |  |
|Batch AI | Não | Não | Não |  |
|Serviço Blockchain | No | Yes | No |  |
|Blueprints | Não | Não | Não |  |
|Serviço de Bot | Não | Não | Não |  |
|Serviços Cloud | Sim | Sim | No | Agente necessário para monitorizar o sistema operativo dos hóspedes e os fluxos de trabalho.  |
|Cloud Shell | Não | Não | Não |  |
|Serviços Cognitivos | Sim | Sim | No |  |
|Container Instances | Yes | Não | Não |  |
|Container Registry | Sim | Sim | No |  |
|Rede de Entrega de Conteúdos (CDN) | No | Yes | No |  |
|Cosmos DB | Sim | Sim | [Sim](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Não | Não | Não |  |
|Data Box | Não | Não | Não |  |
|Catálogo de Dados Gen2 | Não | Não | Não |  |
|Data Explorer | Sim | Sim | No |  |
|Data Factory | Sim | Sim | No |  |
|Fábrica de Dados v2 | No | Yes | No |  |
|Data Share | Não | Não | Não |  |
|Database for MariaDB | Sim | Sim | No |  |
|Base de Dados para MySQL | Sim | Sim | No |  |
|Base de Dados para PostgreSQL | Sim | Sim | No |  |
|Serviço de Migração de Bases de Dados | Não | Não | Não |  |
|Databricks | No | Yes | No |  |
|Proteção contra DDOS | Sim | Sim | No |  |
|DevOps | Não | Não | Não |  |
|DNS | Yes | Não | Não |  |
|Nomes de domínio | Não | Não | Não |  |
|DPS | Não | Não | Não |  |
|Dinâmica 365 Envolvimento do Cliente | Não | Não | Não |  |
|Dinâmica 365 Finanças e Operações | Não | Não | Não |  |
|Event Grid | Yes | Não | Não |  |
|Hubs de Eventos | Sim | Sim | No |  |
|ExpressRoute | Sim | Sim | No |  |
|Firewall | Sim | Sim | No |  |
|Front Door | Sim | Sim | No |  |
|Funções | Sim | Sim | No |  |
|HDInsight | No | Yes | No |  |
|HPC Cache | Não | Não | Não |  |
|Information Protection | No | Yes | No |  |
|Intune | No | Yes | No |  |
|IoT Central | Não | Não | Não |  |
|Hub IoT | Sim | Sim | No |  |
|Key Vault | Sim | Sim | [Sim](insights/key-vaults-insights-overview.md) |  |
|Serviço do Kubernetes (AKS) | No | Não | [Sim](insights/container-insights-overview.md)  |  |
|Balanceador de Carga | Sim | Sim | No |  |
|Logic Apps | Sim | Sim | No |  |
|Serviço Machine Learning | No | Não | Não |  |
|Aplicações Geridas  | No | Não | Não |  |
|Mapas  | No | Não | Não |  |
|Serviços de Multimédia | Sim | Sim | No |  |
|Microsoft Flow | No | Não | Não |  |
|Ambiente de Trabalho Gerido da Microsoft | No | Não | Não |  |
|Microsoft PowerApps | No | Não | Não |  |
|Microsoft Social Engagement | No | Não | Não |  |
|Microsoft Stream | Sim | Sim | No |  |
|Migrar | No | Não | Não |  |
|Multi-Factor Authentication | No | Yes | No |  |
|Observador de Rede | Sim | Sim | No |  |
|Hubs de Notificação | Yes | No | Não |  |
|Open Datasets | No | Não | Não |  |
|Política | No | Não | Não |  |
|Power BI Embedded | Sim | Sim | No |  |
|Ligação Privada | No | Não | Não |  |
|Plataforma de Comunicação de Spool de Projeto | No | Não | Não |  |
|Red Hat OpenShift | No | Não | Não |  |
|Cache de Redis | Sim | Sim | [Sim](insights/redis-cache-insights-overview.md) | |
|Resource Graph | No | Não | Não |  |
|Resource Manager | No | Não | Não |  |
|Pesquisa de Varejo - por Bing | Não | Não | Não |  |
|Pesquisar | Sim | Sim | No |  |
|Service Bus | Sim | Sim | No |  |
|Service Fabric | No | Yes | No | Agente necessário para monitorizar o sistema operativo dos hóspedes e os fluxos de trabalho.  |
|Portal de Inscrição | Não | Não | Não |  |
|Recuperação de sites | No | Yes | No |  |
|Serviço cloud de primavera | Não | Não | Não |  |
|SQL Data Warehouse | Sim | Sim | No |  |
|Base de Dados SQL | Sim | Sim | No |  |
|SQL Server Stretch Database | Sim | Sim | No |  |
|Pilha | Não | Não | Não |  |
|Armazenamento | Yes | No | [Sim](insights/storage-insights-overview.md) |  |
|Cache de armazenamento | Não | Não | Não |  |
|Serviços de Sincronização de Armazenamento | Não | Não | Não |  |
|Stream Analytics | Sim | Sim | No |  |
|Time Series Insights | Sim | Sim | No |  |
|TINA | Não | Não | Não |  |
|Gestor de Tráfego | Sim | Sim | No |  |
|Impressão Universal | Não | Não | Não |  |
|Conjuntos de Dimensionamento de Máquinas Virtuais | No | Yes | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorizar o sistema operativo dos hóspedes e os fluxos de trabalho. |
|Máquinas Virtuais | Sim | Sim | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorizar o sistema operativo dos hóspedes e os fluxos de trabalho. |
|Rede Virtual | Sim | Sim | [Sim](insights/network-insights-overview.md) |  |
|Rede Virtual - Registos de Fluxo NSG | No | Yes | No |  |
|Gateway de VPN | Sim | Sim | No |  |
|Windows Virtual Desktop | Não | Não | Não |  |


## <a name="product-integrations"></a>Integrações de produtos
Os serviços e soluções na tabela seguinte armazenam os seus dados num espaço de trabalho do Log Analytics para que possa ser analisado com outros dados de registo recolhidos pelo Azure Monitor.

| Produto/Serviço | Description |
|:---|:---|
| [Automatização do Azure](/azure/automation/) | Gerir as atualizações do sistema operativo e rastrear as alterações nos computadores Windows e Linux. Ver [Change Tracking](../automation/change-tracking.md) and Update [Management](../automation/automation-update-management.md). |
| [Proteção de Informação Azure](https://docs.microsoft.com/azure/information-protection/) | Classifique e proteja opcionalmente documentos e e-mails. Consulte [a Central de Informação para Proteção de Informações Azure](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Centro de Segurança do Azure](/azure/security-center/) | Recolher e analisar eventos de segurança e realizar análises de ameaças. Ver [recolha de dados no Azure Security Center](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Conecta-se a diferentes fontes, incluindo o Office 365 e o Amazon Web Services Cloud Trail. Consulte [as fontes de dados do Connect](/azure/sentinel/connect-data-sources). |
| [Análise do Key Vault](insights/azure-key-vault.md) | Analise os registos Azure Key Vault AuditEvent. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Crie uma definição de diagnóstico para enviar registos para o Azure Monitor. Consulte [enviar dados de registo para armazenamento, centros de eventos ou analíticos de registo em Intune (pré-visualização)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Rede  | [Monitor de Desempenho da Rede](insights/network-performance-monitor.md) - Monitorizar a conectividade da rede e o desempenho para os pontos finais de serviço e aplicação.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - Analise os registos e métricas do Gateway de Aplicação Azure.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) - Analisa os registos de fluxo do grupo de segurança da rede Network Watcher (NSG) para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. |
| [Office 365](insights/solution-office-365.md) | Monitorize o ambiente do Seu Escritório 365. Versão atualizada com melhor embarque disponível através do Azure Sentinel. |
| [Análise de SQL](insights/azure-sql.md) | Monitorize o desempenho das bases de dados Azure SQL, piscinas elásticas e casos geridos em escala e em várias subscrições. |
| [Surface Hub](insights/surface-hubs.md) | Acompanhe a saúde e a utilização dos dispositivos Surface Hub. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Recolher dados de agentes do Gestor de Operações ligando o seu grupo de gestão ao Azure Monitor. Ver [Gestor de Operações de Ligação ao Monitor Azure](platform/om-agents.md)<br> Avalie o risco e a saúde do seu grupo de gestão de operações do System Center Com a solução [de Avaliação do Gestor de Operações.](insights/scom-assessment.md) |
| [Salas microsoft teams](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Gestão integrada e de ponta a ponta dos dispositivos Microsoft Teams Rooms. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Construa, teste e distribua aplicações e, em seguida, monitorize o seu estado e utilização. Consulte [Começar a analisar a sua aplicação móvel com App Center e Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Conformidade com o Windows Update](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) - Avalie as atualizações do ambiente de trabalho do Windows.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) - Integra-se com o Gestor de Configuração para fornecer informações e informações para tomar decisões mais informadas sobre a prontidão da atualização dos seus clientes Windows. |



## <a name="other-solutions"></a>Outras soluções
Outras soluções estão disponíveis para monitorizar diferentes aplicações e serviços, mas o desenvolvimento ativo parou e podem não estar disponíveis em todas as regiões. Estão abrangidos pelo acordo de nível de serviço de ingestão de dados Azure Log Analytics.

| Solução | Description |
|:---|:---|
| [Verificação de saúde do Diretório Ativo](insights/ad-assessment.md) | Avalie o risco e a saúde dos seus ambientes de Diretório Ativo. |
| [Estado de replicação do Diretório Ativo](insights/ad-replication-status.md) | Monitoriza regularmente o seu ambiente ative directory para eventuais falhas de replicação. |
| [Análise de registo de atividade](platform/activity-log.md#activity-log-analytics-monitoring-solution) | Ver Entradas de Registo de Atividade. |
| [DNS Analytics (pré-visualização)](insights/dns-analytics.md) | Recolhe, analisa e correlaciona registos analíticos e de auditoria do Windows DNS e outros dados relacionados dos seus servidores DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Colete, veja e analise as métricas de saúde e desempenho do seu sistema Cloud Foundry, através de múltiplas implementações. |
| [Contentores](insights/containers.md) | Ver e gerir os anfitriões dos contentores Docker e Windows. |
| [Avaliações a pedido](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Avalie e otimize a disponibilidade, segurança e desempenho dos seus ambientes tecnológicos no local, híbridos e cloud microsoft. |
| [Verificação de saúde SQL](insights/sql-assessment.md) | Avalie o risco e a saúde dos seus ambientes do SQL Server.  |
| [Ligar Dados](insights/wire-data.md) | Dados consolidados de rede e desempenho recolhidos a partir de computadores ligados ao Windows e ligados ao Linux com o agente Log Analytics. |

## <a name="third-party-integration"></a>Integração de terceiros

| Solução | Description |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | O Conector de Gestão de Serviços de TI (ITSMC) permite ligar o Azure a um produto/serviço de Gestão de Serviços de TI (ITSM) suportado.  |


## <a name="resources-outside-of-azure"></a>Recursos fora de Azure
O Azure Monitor pode recolher dados de recursos fora de Azure utilizando os métodos listados na tabela seguinte.

| Recurso | Método |
|:---|:---|
| Aplicações | Monitorize aplicações web fora do Azure usando Insights de Aplicação. Ver [o que é Insights de Aplicação?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). . |
| Máquinas virtuais | Utilize o agente Log Analytics para recolher dados do sistema operativo convidado de máquinas virtuais em outros ambientes de nuvem ou no local. Consulte [recolher dados de registo com o agente Log Analytics](platform/log-analytics-agent.md). |
| CLIENTE API DE DESCANSO | ApIs separados estão disponíveis para escrever dados para Azure Monitor Logs e Métricas de qualquer cliente da API REST. Consulte [Enviar dados de registo para o Azure Monitor com a API de Retorca de Dados HTTP](platform/data-collector-api.md) para Registos e Enviar [métricas personalizadas para um recurso Azure monitor para a loja métrica do Azure Monitor utilizando uma API REST](platform/metrics-store-custom-rest-api.md) para Métricas. |



## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre a [plataforma de dados do Azure Monitor que armazena os registos e métricas recolhidos por insights e soluções.](platform/data-platform.md)
- Preencha um [tutorial sobre a monitorização de um recurso Azure](learn/tutorial-resource-logs.md).
- Preencha um [tutorial sobre a escrita de uma consulta de registo para analisar dados em Registos do Monitor Azure](learn/tutorial-resource-logs.md).
- Preencha um [tutorial sobre a criação de um gráfico de métricas para analisar dados em Azure Monitor Metrics](learn/tutorial-metrics-explorer.md).

 
