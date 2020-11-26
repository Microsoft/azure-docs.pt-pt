---
title: O que é monitorizado pelo Azure Monitor
description: Referência de todos os serviços e outros recursos monitorizados pelo Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 5be6a2d73be35b3d637df9364364784d373dabb3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186699"
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
| [Monitor Azure para Cofre de Chaves (pré-visualização)](./insights/key-vault-insights-overview.md) | Fornece uma monitorização completa dos seus cofres chave, fornecendo uma visão unificada dos seus pedidos de Cofre chave, desempenho, falhas e latência. |
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
|Active Directory B2C | No | No | No |  |
|Active Directory Domain Services | No | Yes | No |  |
|Registo de atividades | No | Yes | No | |
|Proteção Avançada Contra Ameaças | No | No | No |  |
|Assistente | No | No | No |  |
|AI Builder | No | No | No |  |
|Analysis Services | Yes | Yes | No |  |
|API para FHIR | No | No | No |  |
|Gestão de API | Yes | Yes | No |  |
|Serviço de Aplicações | Yes | Yes | No |  |
|AppConfig | No | No | No |  |
|Gateway de Aplicação | Yes | Yes | No |  |
|Serviço de Attestation | No | No | No |  |
|Automatização | Yes | Yes | No |  |
|Gestor de Serviços Azure (RDFE) | No | No | No |  |
|Backup | No | Yes | No |  |
|Bastion | No | No | No |  |
|Batch | Yes | Yes | No |  |
|Batch AI | No | No | No |  |
|Serviço Blockchain | No | Yes | No |  |
|Blueprints | No | No | No |  |
|Serviço de Bot | No | No | No |  |
|Serviços Cloud | Yes | Yes | No | Agente necessário para monitorizar o sistema operativo dos hóspedes e os fluxos de trabalho.  |
|Cloud Shell | No | No | No |  |
|Serviços Cognitivos | Yes | Yes | No |  |
|Container Instances | Yes | No | No |  |
|Container Registry | Yes | Yes | No |  |
|Rede de Entrega de Conteúdos (CDN) | No | Yes | No |  |
|Cosmos DB | Yes | Yes | [Sim](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | No | No | No |  |
|Data Box | No | No | No |  |
|Catálogo de Dados Gen2 | No | No | No |  |
|Data Explorer | Yes | Yes | No |  |
|Data Factory | Yes | Yes | No |  |
|Fábrica de Dados v2 | No | Yes | No |  |
|Data Share | No | No | No |  |
|Database for MariaDB | Yes | Yes | No |  |
|Base de Dados para MySQL | Yes | Yes | No |  |
|Base de Dados para PostgreSQL | Yes | Yes | No |  |
|Database Migration Service | No | No | No |  |
|Databricks | No | Yes | No |  |
|Proteção contra DDOS | Yes | Yes | No |  |
|DevOps | No | No | No |  |
|DNS | Yes | No | No |  |
|Nomes de domínio | No | No | No |  |
|DPS | No | No | No |  |
|Dinâmica 365 Envolvimento do Cliente | No | No | No |  |
|Dinâmica 365 Finanças e Operações | No | No | No |  |
|Event Grid | Yes | No | No |  |
|Hubs de Eventos | Yes | Yes | No |  |
|ExpressRoute | Yes | Yes | No |  |
|Firewall | Yes | Yes | No |  |
|Front Door | Yes | Yes | No |  |
|Funções | Yes | Yes | No |  |
|HDInsight | No | Yes | No |  |
|HPC Cache | No | No | No |  |
|Information Protection | No | Yes | No |  |
|Intune | No | Yes | No |  |
|IoT Central | No | No | No |  |
|IoT Hub | Yes | Yes | No |  |
|Key Vault | Yes | Yes | [Sim](./insights/key-vault-insights-overview.md) |  |
|Serviço do Kubernetes (AKS) | No | No | [Sim](insights/container-insights-overview.md)  |  |
|Balanceador de Carga | Yes | No | No |  |
|Logic Apps | Yes | Yes | No |  |
|Serviço Machine Learning | No | No | No |  |
|Aplicações Geridas  | No | No | No |  |
|Mapas  | No | No | No |  |
|Serviços de Multimédia | Yes | Yes | No |  |
|Ambiente de Trabalho Gerido da Microsoft | No | No | No |  |
|Microsoft PowerApps | No | No | No |  |
|Microsoft Social Engagement | No | No | No |  |
|Microsoft Stream | Yes | Yes | No |  |
|Migrar | No | No | No |  |
|Multi-Factor Authentication | No | Yes | No |  |
|Observador de Rede | Yes | Yes | No |  |
|Hubs de Notificação | Yes | No | No |  |
|Open Datasets | No | No | No |  |
|Política | No | No | No |  |
|Power Automate | No | No | Não |  |
|Power BI Embedded | Yes | Yes | No |  |
|Ligação Privada | No | No | No |  |
|Plataforma de Comunicação de Spool de Projeto | No | No | No |  |
|Red Hat OpenShift | No | No | No |  |
|Cache de Redis | Yes | Yes | [Sim](insights/redis-cache-insights-overview.md) | |
|Resource Graph | No | No | No |  |
|Resource Manager | No | No | No |  |
|Pesquisa de Varejo - por Bing | No | No | No |  |
|Pesquisar | Yes | Yes | No |  |
|Service Bus | Yes | Yes | No |  |
|Service Fabric | No | Yes | No | Agente necessário para monitorizar o sistema operativo dos hóspedes e os fluxos de trabalho.  |
|Portal de Inscrição | No | No | No |  |
|Recuperação de sites | No | Yes | No |  |
|Serviço cloud de primavera | No | No | No |  |
|Azure Synapse Analytics | Yes | Yes | No |  |
|Base de Dados SQL | Yes | Yes | No |  |
|SQL Server Stretch Database | Yes | Yes | No |  |
|Pilha | No | No | No |  |
|Armazenamento | Yes | No | [Sim](insights/storage-insights-overview.md) |  |
|Cache de armazenamento | No | No | No |  |
|Serviços de Sincronização de Armazenamento | No | No | No |  |
|Stream Analytics | Yes | Yes | No |  |
|Time Series Insights | Yes | Yes | No |  |
|TINA | No | No | No |  |
|Gestor de Tráfego | Yes | Yes | No |  |
|Impressão Universal | No | No | No |  |
|Conjuntos de Dimensionamento de Máquinas Virtuais | No | Yes | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorizar o sistema operativo dos hóspedes e os fluxos de trabalho. |
|Máquinas Virtuais | Yes | Yes | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorizar o sistema operativo dos hóspedes e os fluxos de trabalho. |
|Rede Virtual | Yes | Yes | [Sim](insights/network-insights-overview.md) |  |
|Rede Virtual - Registos de Fluxo NSG | No | Yes | No |  |
|Gateway de VPN | Yes | Yes | No |  |
|Windows Virtual Desktop | No | No | No |  |

## <a name="virtual-machine-agents"></a>Agentes de máquina virtual
A tabela que se segue lista os agentes que podem recolher dados do sistema operativo dos hóspedes das máquinas virtuais e enviar dados para o Monitor. Cada agente pode recolher dados diferentes e enviá-lo para Métricas ou Registos no Azure Monitor. 

Consulte [a visão geral dos agentes do Azure Monitor](platform/agents-overview.md) para obter mais informações sobre os dados que cada agente pode recolher.

| Agente |  Métricas | Registos |
|:---|:---|:---|:---|
| [Agente do Monitor Azure (pré-visualização)](platform/azure-monitor-agent-overview.md) | Yes | Yes |
| [Agente do Log Analytics](platform/log-analytics-agent.md) | No | Yes|
| [Extensão de diagnóstico](platform/diagnostics-extension-overview.md) | Yes | No |
| [Agente telegraf](platform/collect-custom-metrics-linux-telegraf.md) | Yes | No |
| [Agente de Dependência](insights/vminsights-enable-overview.md) | No | Yes |


## <a name="product-integrations"></a>Integrações de produtos
Os serviços e soluções na tabela seguinte armazenam os seus dados num espaço de trabalho do Log Analytics para que possa ser analisado com outros dados de registo recolhidos pelo Azure Monitor.

| Produto/Serviço | Description |
|:---|:---|
| [Automatização do Azure](../automation/index.yml) | Gerir as atualizações do sistema operativo e rastrear as alterações nos computadores Windows e Linux. Ver [Change Tracking](../automation/change-tracking/overview.md) and Update [Management](../automation/update-management/overview.md). |
| [Proteção de Informação Azure ](/azure/information-protection/) | Classifique e proteja opcionalmente documentos e e-mails. Consulte [a Central de Informação para Proteção de Informações Azure](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Centro de Segurança do Azure](../security-center/index.yml) | Recolher e analisar eventos de segurança e realizar análises de ameaças. Ver [recolha de dados no Azure Security Center](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Conecta-se a diferentes fontes, incluindo o Office 365 e o Amazon Web Services Cloud Trail. Consulte [as fontes de dados do Connect](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Crie uma definição de diagnóstico para enviar registos para o Azure Monitor. Consulte [enviar dados de registo para armazenamento, centros de eventos ou analíticos de registo em Intune (pré-visualização)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Rede  | [Monitor de Desempenho da Rede](insights/network-performance-monitor.md) - Monitorizar a conectividade da rede e o desempenho para os pontos finais de serviço e aplicação.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - Analise os registos e métricas do Gateway de Aplicação Azure.<br>[Traffic Analytics](../network-watcher/traffic-analytics.md) - Analisa os registos de fluxo do grupo de segurança da rede Network Watcher (NSG) para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. |
| [Office 365](insights/solution-office-365.md) | Monitorize o ambiente do Seu Escritório 365. Versão atualizada com melhor embarque disponível através do Azure Sentinel. |
| [SQL Analytics](insights/azure-sql.md) | Monitorize o desempenho das bases de dados Azure SQL e das instâncias geridas sql em escala e em várias subscrições. |
| [Surface Hub](insights/surface-hubs.md) | Acompanhe a saúde e a utilização dos dispositivos Surface Hub. |
| [System Center Operations Manager](/system-center/scom) | Recolher dados de agentes do Gestor de Operações ligando o seu grupo de gestão ao Azure Monitor. Ver [Gestor de Operações de Ligação ao Monitor Azure](platform/om-agents.md)<br> Avalie o risco e a saúde do seu grupo de gestão de operações do System Center Com a solução [de Avaliação do Gestor de Operações.](insights/scom-assessment.md) |
| [Salas microsoft teams](/microsoftteams/room-systems/azure-monitor-deploy) | Gestão integrada e de ponta a ponta dos dispositivos Microsoft Teams Rooms. |
| [Visual Studio App Center](/appcenter/) | Construa, teste e distribua aplicações e, em seguida, monitorize o seu estado e utilização. Consulte [Começar a analisar a sua aplicação móvel com App Center e Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Conformidade com o Windows Update](/windows/deployment/update/update-compliance-get-started) - Avalie as atualizações do ambiente de trabalho do Windows.<br>[Desktop Analytics](/configmgr/desktop-analytics/overview) - Integra-se com o Gestor de Configuração para fornecer informações e informações para tomar decisões mais informadas sobre a prontidão da atualização dos seus clientes Windows. |



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
| [Avaliações a pedido](/services-hub/health/getting_started_with_on_demand_assessments) | Avalie e otimize a disponibilidade, segurança e desempenho dos seus ambientes tecnológicos no local, híbridos e cloud microsoft. |
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
| Aplicações | Monitorize aplicações web fora do Azure usando Insights de Aplicação. Ver [o que é Insights de Aplicação?](./app/app-insights-overview.md). . |
| Máquinas virtuais | Utilize agentes para recolher dados do sistema operativo convidado de máquinas virtuais em outros ambientes de nuvem ou no local. Consulte [a visão geral dos agentes do Monitor Azure](platform/agents-overview.md). |
| CLIENTE API DE DESCANSO | ApIs separados estão disponíveis para escrever dados para Azure Monitor Logs e Métricas de qualquer cliente da API REST. Consulte [Enviar dados de registo para o Azure Monitor com a API de Retorca de Dados HTTP](platform/data-collector-api.md) para Registos e Enviar [métricas personalizadas para um recurso Azure monitor para a loja métrica do Azure Monitor utilizando uma API REST](platform/metrics-store-custom-rest-api.md) para Métricas. |



## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre a [plataforma de dados do Azure Monitor que armazena os registos e métricas recolhidos por insights e soluções.](platform/data-platform.md)
- Preencha um [tutorial sobre a monitorização de um recurso Azure](learn/tutorial-resource-logs.md).
- Preencha um [tutorial sobre a escrita de uma consulta de registo para analisar dados em Registos do Monitor Azure](learn/tutorial-resource-logs.md).
- Preencha um [tutorial sobre a criação de um gráfico de métricas para analisar dados em Azure Monitor Metrics](learn/tutorial-metrics-explorer.md).

