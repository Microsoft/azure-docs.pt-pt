---
title: Fontes de dados no Monitor Do Azure Microsoft Docs
description: Descreve os dados disponíveis para monitorizar a saúde e o desempenho dos seus recursos Azure e as aplicações que os executam.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: 2a5d1178bd6dbd6f7cfdd2ec2af17b78836a38d7
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096729"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Fontes de dados de monitorização do Monitor Azure
O Azure Monitor baseia-se numa [plataforma comum](data-platform.md) de dados de monitorização que inclui [Registos](data-platform-logs.md) e [Métricas.](data-platform-metrics.md) A recolha de dados nesta plataforma permite que dados de vários recursos sejam analisados em conjunto utilizando um conjunto comum de ferramentas no Monitor Azure. Os dados de monitorização também podem ser enviados para outros locais para suportar determinados cenários, e alguns recursos podem escrever para outros locais antes de poderem ser recolhidos em Registos ou Métricas.

Este artigo descreve as diferentes fontes de monitorização dos dados recolhidos pelo Azure Monitor, para além dos dados de monitorização criados pelos recursos do Azure. Os links são fornecidos a informações detalhadas sobre a configuração necessária para recolher estes dados para diferentes locais.

## <a name="application-tiers"></a>Camadas da aplicação

As fontes de monitorização dos dados das aplicações do Azure podem ser organizadas em níveis, sendo os níveis mais elevados a sua própria aplicação e os níveis inferiores sendo componentes da plataforma Azure. O método de acesso aos dados de cada nível varia. Os níveis de aplicação são resumidos na tabela abaixo, e as fontes de dados de monitorização em cada nível são apresentadas nas seguintes secções. Consulte a localização dos [dados de monitorização em Azure](data-locations.md) para obter uma descrição de cada localização de dados e como pode aceder aos seus dados.


![Níveis de monitorização](../media/overview/overview.png)


### <a name="azure"></a>Azure
A tabela seguinte descreve brevemente os níveis de aplicação específicos do Azure. Seguindo o link para mais detalhes sobre cada uma das secções abaixo.

| Escalão | Descrição | Método de recolha |
|:---|:---|:---|
| [Inquilino Azure](#azure-tenant) | Dados sobre o funcionamento dos serviços Azure ao nível dos inquilinos, como o Azure Ative Directory. | Ver dados aAD no portal ou configurar a recolha para o Monitor Azure utilizando uma definição de diagnóstico de inquilino. |
| [Subscrição do Azure](#azure-subscription) | Dados relacionados com a saúde e gestão de serviços de cross-resource na sua subscrição Azure, como Gestor de Recursos e Saúde de Serviço. | Ver no portal ou configurar a recolha para o Monitor Azure utilizando um perfil de registo. |
| [Recursos do Azure](#azure-resources) |  Dados sobre o funcionamento e desempenho de cada recurso Azure. | Métricas recolhidas automaticamente, vista no Metrics Explorer.<br>Configure as definições de diagnóstico para recolher registos no Monitor Azure.<br>Soluções de monitorização e insights disponíveis para uma monitorização mais detalhada para tipos específicos de recursos. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, outra nuvem, ou no local 
A tabela seguinte descreve brevemente os níveis de aplicação que podem estar em Azure, outra nuvem ou no local. Seguindo o link para mais detalhes sobre cada uma das secções abaixo.

| Escalão | Descrição | Método de recolha |
|:---|:---|:---|
| [Sistema operativo (convidado)](#operating-system-guest) | Dados sobre o sistema operativo sobre os recursos da computação. | Instale o agente Log Analytics para recolher fontes de dados dos clientes no Agente Azure Monitor e Dependência para recolher dependências que suportam o Monitor Azure para VMs.<br>Para máquinas virtuais Azure, instale a Extensão de Diagnóstico Azure para recolher registos e métricas no Monitor Azure. |
| [Código de aplicação](#application-code) | Dados sobre o desempenho e funcionalidade da aplicação e código reais, incluindo rastreios de desempenho, registos de aplicações e telemetria do utilizador. | Instrumente o seu código para recolher dados em Insights de Aplicação. |
| [Fontes personalizadas](#custom-sources) | Dados de serviços externos ou outros componentes ou dispositivos. | Recolher dados de registo ou métricas no Monitor Azure de qualquer cliente REST. |

## <a name="azure-tenant"></a>Inquilino do Azure
A telemetria relacionada com o seu inquilino Azure é recolhida de serviços de arrendamento, como o Azure Ative Directory.

![Coleção de inquilinos azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Registos de Auditoria de Diretório Ativo Azure
O [relatório azure Ative Directory](../../active-directory/reports-monitoring/overview-reports.md) contém o histórico de atividade de entrada e de auditoria das alterações efetuadas dentro de um determinado inquilino. 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Configure os registos adatos azure a recolher no Monitor Azure para analisá-los com outros dados de monitorização. | [Integrar registos de AD Azure com registos do Monitor Azure (pré-visualização)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Storage do Azure | Exportar registos ad ad para armazenamento azure para arquivamento. | [Tutorial: Registos de Anúncios De Arquivo Azure numa conta de armazenamento Azure (pré-visualização)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de Eventos | Stream Azure AD regista para outros locais usando Hubs de eventos. | [Tutorial: Stream Azure Ative Directory logy to a Azure event hub (pré-visualização)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Subscrição do Azure
Telemetria relacionada com a saúde e operação da sua assinatura Azure.

![Subscrição do Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Registo de Atividade sintetizar 
O [registo da Atividade Azure](platform-logs-overview.md) inclui registos de saúde de serviço, juntamente com registos de quaisquer alterações de configuração feitas aos recursos na sua subscrição Azure. O registo de Atividades está disponível para todos os recursos do Azure e representa a sua visão _externa._

| Destino | Descrição | Referência |
|:---|:---|
| Registo de atividades | O registo de Atividades é recolhido na sua própria loja de dados que pode visualizar a partir do menu Do Monitor Do Azure ou usar para criar alertas de registo de Atividade. | [Consulta do log de atividade no portal Azure](activity-log-view.md#azure-portal) |
| Registos do Azure Monitor | Configure os Registos do Monitor Azure para recolher o registo de atividade para analisá-lo com outros dados de monitorização. | [Recolher e analisar registos de atividade do Azure no espaço de trabalho do Log Analytics no Monitor Azure](activity-log-collect.md) |
| Storage do Azure | Exportar o registo de atividade para o Armazenamento Azure para arquivamento. | [Registo de Atividade seleção de Arquivo](resource-logs-collect-storage.md)  |
| Hubs de Eventos | Transmita o registo de atividade para outros locais usando Hubs de Eventos | [Registo de atividade de fluxo para O Centro](resource-logs-stream-event-hubs.md)de Eventos . |

### <a name="azure-service-health"></a>Azure Service Health
A [Azure Service Health](../../service-health/service-health-overview.md) fornece informações sobre a saúde dos serviços Azure na sua subscrição em que a sua aplicação e recursos dependem.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registo de atividades<br>Registos do Azure Monitor | Os registos de saúde de serviço são armazenados no registo da Atividade Azure, para que possa vê-los no portal Azure ou realizar quaisquer outras atividades que possa realizar com o registo de Atividades. | [Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure](service-notifications.md) |


## <a name="azure-resources"></a>Recursos do Azure
Métricas e registos de recursos fornecem informações sobre o funcionamento _interno_ dos recursos Azure. Estes estão disponíveis para a maioria dos serviços Azure, e soluções de monitorização e insights recolhem dados adicionais para determinados serviços.

![Coleção de recursos Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Métricas da plataforma 
A maioria dos serviços azure enviará [métricas](data-platform-metrics.md) da plataforma que refletem o seu desempenho e operação diretamente para a base de dados de métricas. As [métricas específicas variarão para cada tipo de recurso](metrics-supported.md). 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Métricas do Monitor Azure | As métricas da plataforma escreverão para a base de dados de métricas do Monitor Azure sem configuração. Aceda às métricas da plataforma de acesso do Metrics Explorer.  | [Getting started with Azure Metrics Explorer](metrics-getting-started.md) (Introdução ao Explorador de Métricas do Azure)<br>[Métricas suportadas com monitor Azure](metrics-supported.md) |
| Registos do Azure Monitor | Copie as métricas da plataforma para Registos para tendências e outras análises utilizando log Analytics. | [Diagnósticos Azure direto para Log Analytics](resource-logs-collect-workspace.md) |
| Hubs de Eventos | Transmita métricas para outros locais usando Hubs de Eventos. |[Stream Azure monitorizaos dados para um centro de eventos para consumo por uma ferramenta externa](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Registos do recurso
[Os registos](platform-logs-overview.md) de recursos fornecem informações sobre o funcionamento _interno_ de um recurso Azure.  Os registos de recursos são criados automaticamente, mas é necessário criar uma definição de diagnóstico para especificar um destino para os mesmos recolhidos para cada recurso.

Os requisitos de configuração e o conteúdo dos registos de recursos variam por tipo de recurso, e nem todos os serviços ainda os criam. Consulte [serviços suportados, schemas e categorias para registos](diagnostic-logs-schema.md) de recursos Azure para obter detalhes sobre cada serviço e links para procedimentos de configuração detalhados. Se o serviço não estiver listado neste artigo, então esse serviço não cria atualmente registos de recursos.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Envie registos de recursos para registos do Monitor Azure para análise com outros dados de registo recolhidos. | [Recolher registos de recursos azure no espaço de trabalho do Log Analytics no Monitor Azure](resource-logs-collect-storage.md) |
| Armazenamento | Envie registos de recursos para o Armazenamento Azure para arquivamento. | [Registos de recursos do Archive Azure](resource-logs-collect-workspace.md) |
| Hubs de Eventos | Transmita registos de recursos para outros locais usando Centros de Eventos. |[Stream Azure regista recursos para um centro de eventos](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Sistema operativo (convidado)
Os recursos computacionais em Azure, noutras nuvens, e no local têm um sistema operativo convidado para monitorizar. Com a instalação de um ou mais agentes, pode recolher telemetria do hóspede para o Monitor Azure para analisá-la com as mesmas ferramentas de monitorização que os próprios serviços Do Azure.

![Coleção de recursos de computação Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico azure
Ativar a extensão de Diagnóstico Sino Azul para máquinas Azure Virtual permite-lhe recolher registos e métricas do sistema operativo convidado de recursos computacionais Azure, incluindo Azure Cloud Service (clássico) Funções Web e Trabalhadores, Máquinas Virtuais, Máquinavirtual conjuntos de escala, e Tecido de Serviço.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Armazenamento | A extensão de diagnóstico azure escreve sempre para uma conta de Armazenamento Azure. | [Instalar e configurar extensão de diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md)<br>[Utilize a extensão de diagnóstico do Linux para monitorizar métricas e registos](../../virtual-machines/extensions/diagnostics-linux.md) |
| Métricas do Monitor Azure | Quando configura a Extensão de Diagnóstico para recolher contadores de desempenho, são escritos na base de dados de métricas do Monitor Azure. | [Envie métricas de OS do Hóspede para a loja métrica Do Monitor Azure usando um modelo de Gestor de Recursos para uma máquina virtual do Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Hubs de Eventos | Configure a Extensão de Diagnóstico para transmitir os dados para outros locais usando centros de eventos.  | [Streaming de dados de diagnóstico sintetizar através de Hubs de Eventos](diagnostics-extension-stream-event-hubs.md)<br>[Utilize a extensão de diagnóstico do Linux para monitorizar métricas e registos](../../virtual-machines/extensions/diagnostics-linux.md) |
| Registos de Insights de Aplicação | Colete registos e contadores de desempenho do recurso computacional que suporta a sua aplicação para ser analisado com outros dados da aplicação. | [Envie dados de diagnóstico de serviço em nuvem, máquina virtual ou tecido de serviço para Insights de Aplicação](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agente do Log Analytics 
Instale o agente Log Analytics para uma monitorização e gestão abrangentes das suas máquinas virtuais Windows ou Linux. A máquina virtual pode estar a funcionar em Azure, outra nuvem ou no local.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | O agente Log Analytics conecta-se ao Monitor Azure diretamente ou através do System Center Operations Manager e permite-lhe recolher dados de fontes de dados que configura ou de monitorizar soluções que fornecem informações adicionais sobre as aplicações funcionando na máquina virtual. | [Fontes de dados do agente no Monitor Azure](agent-data-sources.md)<br>[Conectar Gerente de Operações ao Monitor Azure](om-agents.md) |
| Armazenamento VM | O Monitor Azure para VMs utiliza o agente Log Analytics para armazenar informações sobre o estado de calor num local personalizado. Consulte a secção seguinte para mais informações.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs 
[O Azure Monitor para VMs](../insights/vminsights-overview.md) proporciona uma experiência de monitorização personalizada para máquinas virtuais que fornecem funcionalidades para além da funcionalidade Core Azure Monitor, incluindo o estado do serviço e a saúde VM. Requer um Agente de Dependência em máquinas virtuais Windows e Linux que se integre com o agente Log Analytics para recolher dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Armazena dados sobre processos e dependências do agente. | [Utilizar o Monitor Azure para VMs (pré-visualização) Mapa para compreender os componentes da aplicação](../insights/vminsights-maps.md) |
| Armazenamento VM | O Monitor Azure para VMs utiliza o agente Log Analytics para armazenar informações sobre o estado de calor num local personalizado. Isto só está disponível para o Monitor Azure para VMs no portal Azure, além da API de saúde de [recursos Azure.](/rest/api/resourcehealth/) | [Compreenda a saúde das suas máquinas virtuais Azure](../insights/vminsights-health.md)<br>[API DE Saúde de Recursos Azure](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Código de aplicação
A monitorização detalhada das aplicações no Monitor Do Azure é feita com insights de [aplicação](https://docs.microsoft.com/azure/application-insights/) que recolhem dados de aplicações em execução em várias plataformas. A aplicação pode estar em execução em Azure, outra nuvem, ou no local.

![Recolha de dados de aplicação](media/data-sources/applications.png)


### <a name="application-data"></a>Dados da aplicação
Quando ativa os Insights de Aplicação para uma aplicação instalando um pacote de instrumentação, recolhe métricas e registos relacionados com o desempenho e o funcionamento da aplicação. A Aplicação Insights armazena os dados que recolhe na mesma plataforma de dados Do Monitor Azure utilizada por outras fontes de dados. Inclui ferramentas extensas para analisar estes dados, mas também pode analisá-los com dados de outras fontes usando ferramentas como O Explorador de Métricas e Log Analytics.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Dados operacionais sobre a sua aplicação, incluindo visualizações de páginas, pedidos de aplicação, exceções e vestígios. | [Analisar dados de registo no Monitor Azure](../log-query/log-query-overview.md) |
|                    | Informação de dependência entre componentes de aplicação para apoiar o Mapa de Aplicações e a correlação de telemetria. | [Correlação de telemetria em Insights de Aplicação](../app/correlation.md) <br> [Mapeamento de Aplicações](../app/app-map.md) |
|            | Resultados de testes de disponibilidade que testam a disponibilidade e capacidade de resposta da sua aplicação a partir de diferentes locais na Internet pública. | [Monitorizar a disponibilidade e a capacidade de resposta de qualquer site](../app/monitor-web-app-availability.md) |
| Métricas do Monitor Azure | Application Insights recolhe métricas que descrevem o desempenho e o funcionamento da aplicação, além de métricas personalizadas que define na sua aplicação na base de dados de métricas do Monitor Azure. | [Métricas baseadas em log e pré-agregadas em Insights de Aplicação](../app/pre-aggregated-metrics-log-metrics.md)<br>[API insights de aplicação para eventos e métricas personalizados](../app/api-custom-events-metrics.md) |
| Storage do Azure | Envie dados de aplicação para o Armazenamento Azure para arquivamento. | [Exportar telemetria a partir do Application Insights](../app/export-telemetry.md) |
|            | Os detalhes dos testes de disponibilidade são armazenados no Armazenamento Azure. Utilize insights de aplicação no portal Azure para descarregar para análise local. Os resultados dos testes de disponibilidade são armazenados em Registos do Monitor Azure. | [Monitorizar a disponibilidade e a capacidade de resposta de qualquer site](../app/monitor-web-app-availability.md) |
|            | Os dados de rastreio do perfil são armazenados no Armazenamento Azure. Utilize insights de aplicação no portal Azure para descarregar para análise local.  | [Aplicações de produção de perfil em Azure com Insights de Aplicação](../app/profiler-overview.md) 
|            | Os dados instantâneos de depurados que são capturados para um subconjunto de exceções são armazenados no Armazenamento Azure. Utilize insights de aplicação no portal Azure para descarregar para análise local.  | [Como as fotos funcionam](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Soluções e Insights de Monitorização
[As soluções](../insights/solutions.md) [de](../insights/insights-overview.md) monitorização e insights recolhem dados para fornecer informações adicionais sobre o funcionamento de um determinado serviço ou aplicação. Podem abordar recursos em diferentes níveis de aplicação e até em vários níveis.

### <a name="monitoring-solutions"></a>Soluções de monitorização

| Destino | Descrição | Referência
|:---|:---|:---|
| Registos do Azure Monitor | As soluções de monitorização recolhem dados em registos do Monitor Do Azure onde podem ser analisados utilizando a linguagem ou [pontos](view-designer.md) de vista que normalmente estão incluídos na solução. | [Detalhes da recolha de dados para soluções de monitorização em Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores
[O Azure Monitor para contentores](../insights/container-insights-overview.md) proporciona uma experiência de monitorização personalizada para o [Serviço Azure Kubernetes (AKS)](/azure/aks/). Recolhe dados adicionais sobre estes recursos descritos na tabela seguinte.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Lojas que monitorizam dados de AKS, incluindo inventário, registos e eventos. Os dados métricos também são armazenados em Registos de forma a alavancar a sua funcionalidade de análise no portal. | [Compreender o desempenho do cluster do AKS com o Azure Monitor para contentores](../insights/container-insights-analyze.md) |
| Métricas do Monitor Azure | Os dados métricos são armazenados na base de dados métrica para impulsionar a visualização e alertas. | [Ver métricas de contentores no explorador de métricas](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Fornece acesso direto aos seus registos de contentores do Serviço Azure Kubernetes (AKS) (stdout/stderror), eventos e métricas de pod no portal. | [Como ver registos, eventos e métricas de cápsulas kubernetes em tempo real](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
[O Azure Monitor para VMs](../insights/vminsights-overview.md) proporciona uma experiência personalizada para monitorizar máquinas virtuais. Uma descrição dos dados recolhidos pelo Azure Monitor para VMs está incluída na secção [sistema operativo (convidado)](#operating-system-guest) acima.

## <a name="custom-sources"></a>Fontes personalizadas
Além dos níveis padrão de uma aplicação, poderá ser necessário monitorizar outros recursos que tenham telemetria que não possam ser recolhidos com as outras fontes de dados. Para estes recursos, escreva estes dados para métricas ou registos utilizando uma API do Monitor Azure.

![Coleção personalizada](media/data-sources/custom.png)

| Destino | Método | Descrição | Referência |
|:---|:---|:---|:---|
| Registos do Azure Monitor | API do Recoletor de Dados | Recolher dados de registo de qualquer cliente REST e armazenar no espaço de trabalho do Log Analytics. | [Envie dados de registo para o Monitor Azure com a Http Data Collector API (pré-visualização pública)](data-collector-api.md) |
| Métricas do Monitor Azure | API de Métricas Personalizadas | Recolher dados métricos de qualquer cliente REST e armazenar na base de dados de métricas do Monitor Do Azure. | [Envie métricas personalizadas para um recurso Azure para a loja métrica Do Monitor Azure utilizando uma API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Outros serviços
Outros serviços no Azure escrevem dados para a plataforma de dados Azure Monitor. Isto permite-lhe analisar os dados recolhidos por estes serviços com dados recolhidos pelo Azure Monitor e alavancar as mesmas ferramentas de análise e visualização.

| Serviço | Destino | Descrição | Referência |
|:---|:---|:---|:---|
| [Centro de Segurança do Azure](/azure/security-center/) | Registos do Azure Monitor | O Azure Security Center armazena os dados de segurança que recolhe num espaço de trabalho do Log Analytics que permite ser analisado com outros dados de registo recolhidos pelo Azure Monitor.  | [Data collection in Azure Security Center](../../security-center/security-center-enable-data-collection.md) (Recolha de dados no Centro de Segurança do Azure) |
| [Sentinela-azul](/azure/sentinel/) | Registos do Azure Monitor | O Azure Sentinel armazena os dados que recolhe de diferentes fontes de dados num espaço de trabalho do Log Analytics que permite ser analisado com outros dados de registo recolhidos pelo Azure Monitor.  | [Ligar fontes de dados](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [tipos de dados de monitorização recolhidos pelo Azure Monitor](data-platform.md) e como visualizar e analisar estes dados.
- Enumere os [diferentes locais onde os recursos do Azure armazenam dados](data-locations.md) e como pode aceder aos mesmos. 
