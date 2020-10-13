---
title: Fontes de dados no Azure Monitor Microsoft Docs
description: Descreve os dados disponíveis para monitorizar a saúde e o desempenho dos seus recursos Azure e as aplicações que os executam.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: 8ef498a51f25a6b084a0d048661f3d18a5881644
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802073"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Fontes de dados de monitorização do Azure Monitor
O Azure Monitor baseia-se numa [plataforma comum de dados de monitorização](data-platform.md) que inclui [Registos](data-platform-logs.md) e [Métricas.](data-platform-metrics.md) A recolha de dados nesta plataforma permite que os dados de vários recursos sejam analisados em conjunto usando um conjunto comum de ferramentas no Azure Monitor. Os dados de monitorização também podem ser enviados para outros locais para suportar certos cenários, e alguns recursos podem escrever para outros locais antes de poderem ser recolhidos em Registos ou Métricas.

Este artigo descreve as diferentes fontes de dados de monitorização recolhidos pelo Azure Monitor, para além dos dados de monitorização criados pelos recursos da Azure. As ligações são fornecidas a informações detalhadas sobre a configuração necessárias para recolher estes dados em diferentes locais.

## <a name="application-tiers"></a>Camadas da aplicação

As fontes de monitorização de dados das aplicações Azure podem ser organizadas em níveis, sendo os níveis mais altos a sua própria aplicação e os níveis inferiores componentes da plataforma Azure. O método de acesso aos dados de cada nível varia. Os níveis de aplicação são resumidos no quadro abaixo, e as fontes de dados de monitorização em cada nível são apresentadas nas seguintes secções. Consulte [a monitorização das localizações dos dados em Azure](data-locations.md) para obter uma descrição de cada localização de dados e como pode aceder aos seus dados.


![Níveis de monitorização](../media/overview/overview.png)


### <a name="azure"></a>Azure
A tabela seguinte descreve brevemente os níveis de aplicação específicos do Azure. Seguindo o link para mais detalhes sobre cada uma das secções abaixo.

| Escalão de serviço | Descrição | Método de recolha |
|:---|:---|:---|
| [Inquilino Azure](#azure-tenant) | Dados sobre a operação dos serviços do Azure ao nível do inquilino, como o Azure Active Directory. | Ver dados AAD no portal ou configurar a recolha para o Azure Monitor utilizando uma definição de diagnóstico do inquilino. |
| [Assinatura Azure](#azure-subscription) | Dados relacionados com a saúde e gestão de serviços de recursos cruzados na sua subscrição Azure, como Gestor de Recursos e Saúde de Serviço. | Ver no portal ou configurar a recolha para o Azure Monitor utilizando um perfil de registo. |
| [Recursos do Azure](#azure-resources) |  Dados sobre o funcionamento e desempenho de cada recurso Azure. | Métricas recolhidas automaticamente, vista no Metrics Explorer.<br>Configurar definições de diagnóstico para recolher registos no Azure Monitor.<br>Soluções de monitorização e insights disponíveis para uma monitorização mais detalhada para tipos específicos de recursos. |

### <a name="azure-other-cloud-or-on-premises"></a>Azul, outra nuvem, ou no local 
A tabela seguinte descreve brevemente os níveis de aplicação que podem estar em Azure, outra nuvem ou no local. Seguindo o link para mais detalhes sobre cada uma das secções abaixo.

| Escalão de serviço | Descrição | Método de recolha |
|:---|:---|:---|
| [Sistema operativo (convidado)](#operating-system-guest) | Dados sobre o sistema operativo sobre recursos computatórios. | Instale o agente Log Analytics para recolher fontes de dados do cliente no Azure Monitor e agente de Dependência para recolher dependências que suportam o Azure Monitor para VMs.<br>Para máquinas virtuais Azure, instale a Extensão de Diagnóstico Azure para recolher registos e métricas no Azure Monitor. |
| [Código de Aplicação](#application-code) | Dados sobre o desempenho e funcionalidade da aplicação e código reais, incluindo traços de desempenho, registos de aplicações e telemetria do utilizador. | Instrumento o seu código para recolher dados em Insights de Aplicação. |
| [Fontes personalizadas](#custom-sources) | Dados de serviços externos ou outros componentes ou dispositivos. | Recolher dados de registo ou métricas no Azure Monitor de qualquer cliente REST. |

## <a name="azure-tenant"></a>Inquilino do Azure
A telemetria relacionada com o seu inquilino Azure é recolhida de serviços de todo o arrendatário, como o Azure Ative Directory.

![Coleção de inquilinos Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Registos de auditoria do Diretório Ativo Azure
[O relatório do Azure Ative Directory](../../active-directory/reports-monitoring/overview-reports.md) contém o histórico de atividade de inscrição e pista de auditoria de alterações efetuadas dentro de um determinado inquilino. 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Configurar registos Azure AD a serem recolhidos no Azure Monitor para analisá-los com outros dados de monitorização. | [Integre os registos AD do Azure com registos do Azure Monitor (pré-visualização)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Storage do Azure | Exporte os registos Azure AD para o Azure Storage para arquivar. | [Tutorial: Arquivar registos do Azure AD numa conta de armazenamento do Azure (pré-visualização)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de Eventos | Transmita os registos Azure AD para outros locais utilizando os Centros de Eventos. | [Tutorial: Stream Azure Ative Directory registra para um centro de eventos Azure (pré-visualização)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Subscrição do Azure
Telemetria relacionada com a saúde e funcionamento da sua assinatura Azure.

![Subscrição do Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Registo de atividades do Azure 
O [registo de Atividades Azure](platform-logs-overview.md) inclui registos de saúde de serviço, juntamente com registos de quaisquer alterações de configuração es feitas aos recursos na sua subscrição do Azure. O registo de Atividades está disponível para todos os recursos Azure e representa a sua visão _externa._

| Destino | Descrição | Referência |
|:---|:---|
| Registo de atividades | O registo de Atividade é recolhido na sua própria loja de dados que pode ver a partir do menu Azure Monitor ou usar para criar alertas de registo de atividade. | [Consulta do registo de atividades no portal Azure](./activity-log.md#view-the-activity-log) |
| Registos do Azure Monitor | Configure registos de monitores Azure para recolher o registo de Atividade para analisá-lo com outros dados de monitorização. | [Recolher e analisar registos de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](./activity-log.md) |
| Storage do Azure | Exporte o registo de atividades para o Azure Storage para arquivar. | [Registo de Atividades de Arquivo](./resource-logs.md#send-to-azure-storage)  |
| Hubs de Eventos | Transmita o registo de atividades para outros locais usando Centros de Eventos | [Fluxo de registo de atividade para o Centro de Eventos](./resource-logs.md#send-to-azure-event-hubs). |

### <a name="azure-service-health"></a>Azure Service Health
[O Azure Service Health](../../service-health/service-health-overview.md) fornece informações sobre a saúde dos serviços Azure na sua subscrição em que a sua aplicação e recursos dependem.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registo de atividades<br>Registos do Azure Monitor | Os registos de Saúde de Serviço são armazenados no registo de Atividade Azure, para que possa vê-los no portal Azure ou realizar quaisquer outras atividades que possa realizar com o registo de Atividades. | [Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Recursos do Azure
Métricas e registos de recursos fornecem informações sobre o funcionamento _interno_ dos recursos da Azure. Estes estão disponíveis para a maioria dos serviços Azure, e as soluções e insights de monitorização recolhem dados adicionais para determinados serviços.

![Coleção de recursos Azure](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>Métricas de plataforma 
A maioria dos serviços da Azure enviará [métricas de plataforma](data-platform-metrics.md) que refletem o seu desempenho e operação diretamente para a base de dados de métricas. As [métricas específicas variam para cada tipo de recurso.](metrics-supported.md) 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Métricas do Monitor Azure | As métricas da plataforma escreverão para a base de dados de métricas do Azure Monitor sem configuração. Aceder às métricas da plataforma do Metrics Explorer.  | [Getting started with Azure Metrics Explorer](metrics-getting-started.md) (Introdução ao Explorador de Métricas do Azure)<br>[Métricas suportadas com monitor Azure](metrics-supported.md) |
| Registos do Azure Monitor | Copiar métricas da plataforma para Logs para tendências e outras análises usando o Log Analytics. | [Diagnósticos Azure diretos para Log Analytics](./resource-logs.md#send-to-log-analytics-workspace) |
| Hubs de Eventos | Transmita métricas para outros locais usando Centros de Eventos. |[Stream Azure monitorizando dados para um centro de eventos para consumo por uma ferramenta externa](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Registos do recurso
[Os registos de recursos](platform-logs-overview.md) fornecem informações sobre o funcionamento _interno_ de um recurso Azure.  Os registos de recursos são criados automaticamente, mas é necessário criar uma definição de diagnóstico para especificar um destino para que sejam recolhidos para cada recurso.

Os requisitos de configuração e conteúdo dos registos de recursos variam de acordo com o tipo de recurso, e nem todos os serviços ainda os criam. Consulte [serviços, esquemas e categorias de registos de recursos Azure](./resource-logs-schema.md) para obter detalhes em cada serviço e links para procedimentos de configuração detalhados. Se o serviço não estiver listado neste artigo, então esse serviço não cria atualmente registos de recursos.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Envie registos de recursos para Registos do Monitor Azure para análise com outros dados de registo recolhidos. | [Recolher registos de recursos Azure no log analytics espaço de trabalho no Azure Monitor](./resource-logs.md#send-to-azure-storage) |
| Armazenamento | Envie registos de recursos para o Azure Storage para arquivar. | [Registos de recursos do Archive Azure](./resource-logs.md#send-to-log-analytics-workspace) |
| Hubs de Eventos | Transmita os registos de recursos para outros locais utilizando os Centros de Eventos. |[Stream Azure logs de recursos para um centro de eventos](./resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>Sistema operativo (convidado)
Os recursos de cálculo em Azure, em outras nuvens, e no local têm um sistema operativo para monitorizar. Com a instalação de um ou mais agentes, pode recolher telemetria do hóspede para o Azure Monitor para analisá-la com as mesmas ferramentas de monitorização que os próprios serviços Azure.

![Coleção de recursos computacional Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Extensão de Diagnóstico Azure
Permitir a extensão Azure Diagnostics para máquinas Azure Virtual permite-lhe recolher registos e métricas do sistema operativo convidado de recursos de computação Azure, incluindo Azure Cloud Service (clássico) Web and Worker Roles, Máquinas Virtuais, conjuntos de escala de máquinas virtuais e Tecido de Serviço.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Armazenamento | A extensão de diagnóstico Azure escreve sempre para uma conta de Armazenamento Azure. | [Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md)<br>[Using Linux Diagnostic Extension to monitor metrics and logs](../../virtual-machines/extensions/diagnostics-linux.md) (Utilizar a Extensão de Diagnóstico do Linux para monitorizar métricas e registos) |
| Métricas do Monitor Azure | Quando configurar a Extensão de Diagnóstico para recolher contadores de desempenho, eles são escritos na base de dados de métricas do Monitor Azure. | [Envie métricas de OS do Hóspede para a loja métrica do Azure Monitor usando um modelo de Gestor de Recursos para uma máquina virtual Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Hubs de Eventos | Configure a Extensão de Diagnóstico para transmitir os dados para outros locais usando Os Centros de Eventos.  | [Streaming de dados de diagnósticos Azure utilizando centros de eventos](diagnostics-extension-stream-event-hubs.md)<br>[Using Linux Diagnostic Extension to monitor metrics and logs](../../virtual-machines/extensions/diagnostics-linux.md) (Utilizar a Extensão de Diagnóstico do Linux para monitorizar métricas e registos) |
| Registos de Insights de Aplicação | Recolher registos e contadores de desempenho a partir do recurso compute que suporta a sua aplicação para ser analisado com outros dados da aplicação. | [Enviar dados de diagnóstico de Cloud Service, Virtual Machine ou Service Fabric para Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agente do Log Analytics 
Instale o agente Log Analytics para uma monitorização e gestão abrangentes das suas máquinas virtuais Windows ou Linux. A máquina virtual pode estar a funcionar em Azure, outra nuvem ou no local.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | O agente Log Analytics conecta-se ao Azure Monitor diretamente ou através do Gestor de Operações do Centro de Sistema e permite-lhe recolher dados de fontes de dados que configura ou de soluções de monitorização que fornecem informações adicionais sobre aplicações em execução na máquina virtual. | [Fontes de dados de agente no Azure Monitor](agent-data-sources.md)<br>[Connect Operations Manager ao Azure Monitor](om-agents.md) |
| Armazenamento VM | O Azure Monitor para VMs utiliza o agente Log Analytics para armazenar informações do estado de saúde num local personalizado. Veja a secção seguinte para obter mais informações.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs 
[O Azure Monitor para VMs](../insights/vminsights-overview.md) proporciona uma experiência de monitorização personalizada para máquinas virtuais que fornecem funcionalidades para além da funcionalidade core Azure Monitor. Requer um Agente de Dependência em máquinas virtuais Windows e Linux que se integre com o agente Log Analytics para recolher dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Armazena dados sobre processos e dependências do agente. | [Utilizar o Monitor Azure para VMs (pré-visualização) Mapa para entender componentes de aplicações](../insights/vminsights-maps.md) |



## <a name="application-code"></a>Código de Aplicação
A monitorização detalhada da aplicação no Azure Monitor é feita com [o Application Insights](/azure/application-insights/) que recolhe dados de aplicações em execução em várias plataformas. A aplicação pode estar a decorrer em Azure, outra nuvem ou no local.

![Recolha de dados de aplicações](media/data-sources/applications.png)


### <a name="application-data"></a>Dados da aplicação
Quando ativa o Application Insights para uma aplicação através da instalação de um pacote de instrumentação, recolhe métricas e registos relacionados com o desempenho e funcionamento da aplicação. A Application Insights armazena os dados que recolhe na mesma plataforma de dados do Azure Monitor utilizada por outras fontes de dados. Inclui ferramentas extensas para analisar estes dados, mas também pode analisá-los com dados de outras fontes usando ferramentas como Metrics Explorer e Log Analytics.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Dados operacionais sobre a sua aplicação, incluindo visualizações de página, pedidos de aplicações, exceções e vestígios. | [Analise os dados de registo no Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informações de dependência entre componentes de aplicação para apoiar o Mapa de Aplicações e a correlação de telemetria. | [Correlação de telemetria em Insights de Aplicação](../app/correlation.md) <br> [Mapeamento de Aplicações](../app/app-map.md) |
|            | Resultados dos testes de disponibilidade que testam a disponibilidade e capacidade de resposta da sua aplicação a partir de diferentes locais na Internet pública. | [Monitorizar a disponibilidade e a capacidade de resposta de qualquer site](../app/monitor-web-app-availability.md) |
| Métricas do Monitor Azure | A Application Insights recolhe métricas que descrevem o desempenho e o funcionamento da aplicação, para além das métricas personalizadas que define na sua aplicação na base de dados de métricas do Azure Monitor. | [Métricas baseadas no registo e pré-agregadas no Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API do Application Insights para métricas e eventos personalizados](../app/api-custom-events-metrics.md) |
| Storage do Azure | Envie os dados da aplicação para o Azure Storage para arquivar. | [Exportar telemetria a partir do Application Insights](../app/export-telemetry.md) |
|            | Os detalhes dos testes de disponibilidade são armazenados no Azure Storage. Utilize insights de aplicações no portal Azure para descarregar para análise local. Os resultados dos testes de disponibilidade são armazenados em Registos monitores Azure. | [Monitorizar a disponibilidade e a capacidade de resposta de qualquer site](../app/monitor-web-app-availability.md) |
|            | Os dados de rastreio do perfil são armazenados no Azure Storage. Utilize insights de aplicações no portal Azure para descarregar para análise local.  | [Aplicações de produção de perfis em Azure com Insights de Aplicação](../app/profiler-overview.md) 
|            | Os dados de depuração que são capturados para um subconjunto de exceções são armazenados no Azure Storage. Utilize insights de aplicações no portal Azure para descarregar para análise local.  | [Como funcionam os instantâneos](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Soluções e Insights de Monitorização
[As soluções de monitorização](../insights/solutions.md) e [os Insights](../insights/insights-overview.md) recolhem dados para fornecer informações adicionais sobre o funcionamento de um determinado serviço ou aplicação. Podem abordar recursos em diferentes níveis de aplicação e até mesmo em vários níveis.

### <a name="monitoring-solutions"></a>Soluções de monitorização

| Destino | Descrição | Referência
|:---|:---|:---|
| Registos do Azure Monitor | As soluções de monitorização recolhem dados em registos do Azure Monitor onde podem ser analisados utilizando a linguagem de consulta ou [pontos de vista](view-designer.md) que são normalmente incluídos na solução. | [Detalhes da recolha de dados para soluções de monitorização em Azure](../monitor-reference.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores
[O Azure Monitor para contentores](../insights/container-insights-overview.md) proporciona uma experiência de monitorização personalizada para [o Serviço Azure Kubernetes (AKS)](../../aks/index.yml). Recolhe dados adicionais sobre estes recursos descritos no quadro seguinte.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Armazena dados de monitorização de AKS, incluindo inventário, registos e eventos. Os dados métricos também são armazenados em Logs de forma a alavancar a sua funcionalidade de análise no portal. | [Compreender o desempenho do cluster do AKS com o Azure Monitor para contentores](../insights/container-insights-analyze.md) |
| Métricas do Monitor Azure | Os dados métricos são armazenados na base de dados métrica para impulsionar a visualização e os alertas. | [Ver métricas de recipiente no explorador de métricas](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Fornece acesso direto aos seus registos de contentores Azure Kubernetes (AKS) (stdout/stderror), eventos e métricas de pod no portal. | [Como ver registos, eventos e métricas de kubernetes em tempo real ](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
[O Azure Monitor para VMs](../insights/vminsights-overview.md) proporciona uma experiência personalizada para monitorizar máquinas virtuais. Uma descrição dos dados recolhidos pelo Azure Monitor para VMs está incluída na secção [sistema operativo (convidado)](#operating-system-guest) acima.

## <a name="custom-sources"></a>Fontes personalizadas
Além dos níveis padrão de uma aplicação, poderá ser necessário monitorizar outros recursos que tenham telemetria que não possam ser recolhidos com as outras fontes de dados. Para estes recursos, escreva estes dados para Métricas ou Registos utilizando uma API do Monitor Azure.

![Coleção personalizada](media/data-sources/custom.png)

| Destino | Método | Descrição | Referência |
|:---|:---|:---|:---|
| Registos do Azure Monitor | API do Recoletor de Dados | Recolher dados de registo de qualquer cliente REST e armazenar no espaço de trabalho Log Analytics. | [Envie dados de registo para O Monitor de Azure com a API do Colecionador de Dados HTTP (pré-visualização pública)](data-collector-api.md) |
| Métricas do Monitor Azure | Métricas Personalizadas API | Recolher dados métricos de qualquer cliente REST e armazenar na base de dados de métricas Azure Monitor. | [Envie métricas personalizadas para um recurso Azure para a loja métrica Azure Monitor utilizando uma API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Outros serviços
Outros serviços em Azure escrevem dados para a plataforma de dados do Azure Monitor. Isto permite analisar os dados recolhidos por estes serviços com dados recolhidos pelo Azure Monitor e alavancar as mesmas ferramentas de análise e visualização.

| Serviço | Destino | Descrição | Referência |
|:---|:---|:---|:---|
| [Centro de Segurança do Azure](../../security-center/index.yml) | Registos do Azure Monitor | O Azure Security Center armazena os dados de segurança que recolhe num espaço de trabalho do Log Analytics que permite que seja analisado com outros dados de registo recolhidos pelo Azure Monitor.  | [Data collection in Azure Security Center](../../security-center/security-center-enable-data-collection.md) (Recolha de dados no Centro de Segurança do Azure) |
| [Azure Sentinel](../../sentinel/index.yml) | Registos do Azure Monitor | O Azure Sentinel armazena os dados que recolhe de diferentes fontes de dados num espaço de trabalho do Log Analytics que permite que sejam analisados com outros dados de registo recolhidos pelo Azure Monitor.  | [Ligar a origens de dados](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [tipos de dados de monitorização recolhidos pelo Azure Monitor](data-platform.md) e como visualizar e analisar estes dados.
- Liste os [diferentes locais onde os recursos Azure armazenam dados](data-locations.md) e como pode acessá-lo. 
