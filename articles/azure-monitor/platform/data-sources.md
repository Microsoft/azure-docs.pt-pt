---
title: Origens de dados no Azure Monitor | Documentos da Microsoft
description: Descreve os dados disponíveis para monitorizar o estado de funcionamento e desempenho dos seus recursos do Azure e as aplicações em execução nos mesmos.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357530"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Origens de dados de monitorização para o Azure Monitor
Monitor do Azure se baseia numa [plataforma de dados de monitorização comum](data-platform.md) que inclui [registos](data-platform-logs.md) e [métricas](data-platform-metrics.md). Recolher dados para esta plataforma permite que os dados de vários recursos para ser analisados em conjunto através de um conjunto comum de ferramentas no Azure Monitor. Dados de monitorização também podem ser enviados para outras localizações para suportar determinados cenários, e alguns recursos podem escrever para outras localizações antes de poderem ser recolhidas numa registos ou métricas.

Este artigo descreve as diferentes origens de dados recolhidos pelo Monitor do Azure, além dos dados de monitorização criados por recursos do Azure de monitorização. São fornecidas hiperligações para informações detalhadas sobre a configuração necessária para recolher estes dados para localizações diferentes.

## <a name="application-tiers"></a>Camadas da aplicação

Origens de dados de monitorização do aplicações do Azure podem ser organizadas em camadas, as camadas mais altos a ser a própria aplicação e as camadas inferiores a ser componentes da plataforma do Azure. O método de acesso aos dados de cada camada varia. Os escalões de aplicação estão resumidos na tabela abaixo, e as origens de dados em cada escalão de monitorização são apresentadas nas seções a seguir. Ver [monitorização localizações de dados no Azure](data-locations.md) para obter uma descrição de cada localização de dados e como pode acessar seus dados.


![Camadas de monitorização](../media/overview/overview.png)


### <a name="azure"></a>Azure
A tabela seguinte descreve resumidamente as camadas da aplicação que são específicas para o Azure. Seguindo a ligação para obter mais detalhes sobre cada nas secções abaixo.

| Escalão | Descrição | Método de coleção |
|:---|:---|:---|
| [Azure Tenant](#azure-tenant) | Dados sobre o funcionamento dos serviços do Azure ao nível do inquilino, como o Azure Active Directory. | Ver dados do AAD no portal ou configurar a recolha para o Azure Monitor a utilizar uma definição de diagnóstico do inquilino. |
| [Subscrição do Azure](#azure-subscription) | Dados relacionados com o estado de funcionamento e gestão de serviços entre recursos na sua subscrição do Azure, como o Resource Manager e o estado de funcionamento do serviço. | Ver no portal ou configurar a recolha para o Azure Monitor a utilizar um perfil de registo. |
| [Recursos do Azure](#azure-resources) |  Dados sobre a operação e o desempenho de cada recurso do Azure. | Métricas recolhidas automaticamente, ver no Explorador de métricas.<br>Configure definições de diagnóstico para recolher registos no Azure Monitor.<br>Monitorização de soluções e informações disponíveis para monitorização mais detalhada para tipos de recurso específico. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, outra cloud ou no local 
A tabela seguinte descreve resumidamente as camadas da aplicação que possam ser no Azure, noutra cloud ou no local. Seguindo a ligação para obter mais detalhes sobre cada nas secções abaixo.

| Escalão | Descrição | Método de coleção |
|:---|:---|:---|
| [Sistema operativo (convidado)](#operating-system-guest) | Dados sobre o sistema operativo nos recursos de computação. | Instale o agente de Log Analytics para recolher origens de dados do cliente para o agente do Azure Monitor e de dependência para recolher as dependências de suporte do Azure Monitor para as VMs.<br>Para máquinas virtuais do Azure, instale a extensão de diagnóstico do Azure para recolher registos e métricas no Azure Monitor. |
| [Código da aplicação](#application-code) | Dados sobre o desempenho e a funcionalidade da aplicação real e código, incluindo rastreios de desempenho, registos de aplicações e telemetria de utilizador. | Instrumente o seu código para recolher dados para o Application Insights. |
| [Origens personalizadas](#custom-sources) | Dados de serviços externos ou outros componentes ou dispositivos. | Recolha dados de registo ou métricas no Azure Monitor de qualquer cliente REST. |

## <a name="azure-tenant"></a>Inquilino do Azure
Telemetria relacionados com o seu inquilino do Azure é recolhida a partir de serviços de ao nível do inquilino, como o Azure Active Directory.

![Coleção de inquilino do Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Registos de auditoria do Azure Active Directory
[Relatórios do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contém o histórico de início de sessão atividade e auditoria do registo de alterações feitas a um inquilino específico. 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Configure os registos do Azure AD serão recolhidos no Azure Monitor analisá-los com outros dados de monitorização. | [Integrar registos do Azure AD com os registos do Azure Monitor (pré-visualização)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Storage do Azure | Exporte registos do Azure AD para o armazenamento do Azure para arquivamento. | [Tutorial: Arquivar registos do Azure AD para uma conta de armazenamento do Azure (pré-visualização)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de Eventos | Stream registos do Azure AD para outras localizações com os Hubs de eventos. | [Tutorial: Stream registos do Azure Active Directory para um hub de eventos do Azure (pré-visualização)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Subscrição do Azure
Telemetria relacionados com o estado de funcionamento e a operação da sua subscrição do Azure.

![Subscrição do Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Registo de atividades do Azure 
O [registo de atividades do Azure](activity-logs-overview.md) inclui registos de estado de funcionamento do serviço, juntamente com os registos em quaisquer alterações de configuração efetuadas aos recursos na sua subscrição do Azure. O registo de atividades está disponível para todos os recursos do Azure e representa seus _externo_ vista.

| Destino | Descrição | Referência |
|:---|:---|
| Registo de atividades | O registo de atividades é recolhido para seu próprio arquivo de dados que pode ver no menu do Azure Monitor ou utilizar para criar alertas de registo de atividade. | [Consultar o registo de atividades no portal do Azure](activity-log-view.md#azure-portal) |
| Registos do Azure Monitor | Configure registos de Monitor do Azure para recolher o registo de atividades para analisá-los com outros dados de monitorização. | [Recolher e analisar registos de atividades do Azure na área de trabalho do Log Analytics no Azure Monitor](activity-log-collect.md) |
| Storage do Azure | Exporte o registo de atividade ao armazenamento do Azure para arquivamento. | [Arquivar registo de atividades](activity-log-export.md#archive-activity-log)  |
| Hubs de Eventos | Stream o registo de atividades para outras localizações com os Hubs de eventos | [Registo de atividades do Stream para o Hub de eventos](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Service Health
[O Azure Service Health](../../service-health/service-health-overview.md) fornece informações sobre o estado de funcionamento dos serviços do Azure na sua subscrição que a aplicação e os recursos se baseiam em.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registo de atividades<br>Registos do Azure Monitor | Registos de estado de funcionamento do serviço são armazenados no registo de atividades do Azure, para que possa vê-los no portal do Azure ou realizar quaisquer outras atividades que pode realizar com o registo de atividades. | [Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure](service-notifications.md) |


## <a name="azure-resources"></a>Recursos do Azure
Registos de diagnóstico ao nível métricas e recursos fornecem informações sobre o _interno_ operação dos recursos do Azure. Estas estão disponíveis para serviços do Azure mais e soluções de monitorização e insights recolher dados adicionais para serviços específicos.

![Coleção de recursos do Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Métricas de plataforma 
Serviços do Azure mais enviará [métricas de plataforma](data-platform-metrics.md) que refletem o desempenho e operação diretamente para a base de dados de métricas. As específicas [métricas irão variar para cada tipo de recurso](metrics-supported.md). 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Métricas de Monitor do Azure | Escrever métricas de plataforma para a base de dados de métricas de Monitor do Azure sem qualquer configuração. Métricas de plataforma de acesso do Explorador de métricas.  | [Introdução ao Explorador de métricas do Azure](metrics-getting-started.md)<br>[Métricas suportadas com o Azure Monitor](metrics-supported.md) |
| Registos do Azure Monitor | Copie as métricas de plataforma para os registos para análise de tendência e outro com o Log Analytics. | [Direcionar o diagnóstico do Azure para o Log Analytics](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| Hubs de Eventos | Métricas do Stream para outras localizações com os Hubs de eventos. |[Azure Stream a monitorização dos dados para um hub de eventos para consumo por uma ferramenta externa](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>Registos de diagnósticos
[Os registos de diagnóstico](diagnostic-logs-overview.md) fornecer informações sobre o _interno_ operação de um recurso do Azure.  Os registos de diagnóstico não estão ativados por predefinição. Tem de ativá-las e especifique um destino para cada recurso. 

Os requisitos de configuração e o conteúdo dos registos de diagnóstico variam consoante o tipo de recurso e nem todos os serviços ainda criar registos de diagnóstico. Ver [suportada serviços, os esquemas e categorias para os registos de diagnóstico do Azure](diagnostic-logs-schema.md) para obter detalhes sobre cada serviço e hiperligações para procedimentos de configuração detalhadas. Se o serviço não estiver listado neste artigo, em seguida, esse serviço não atualmente escrever os registos de diagnóstico.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Envie registos de diagnóstico para Logs de Monitor do Azure para análise com outros dados recolhidos de registo. Alguns recursos podem escrever diretamente para o Azure Monitor, enquanto outras pessoas a escrever para uma conta de armazenamento antes de a ser importado para uma área de trabalho do Log Analytics. | [Registos de diagnóstico do Azure Stream à área de trabalho do Log Analytics no Azure Monitor](diagnostic-logs-stream-log-store.md)<br>[Utilizar o portal do Azure para recolher registos de armazenamento do Azure](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| Armazenamento | Diagnóstico de enviar registos ao armazenamento do Azure para arquivamento. | [Arquivar registos de diagnóstico do Azure](archive-diagnostic-logs.md) |
| Hubs de Eventos | Registos de diagnóstico de Stream para outras localizações com os Hubs de eventos. |[Registos de diagnóstico do Azure Stream para um hub de eventos](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Sistema operativo (convidado)
Recursos de computação no Azure, noutras clouds e no local têm um sistema de operativo convidado para monitorizar. Com a instalação de um ou mais agentes, pode recolher telemetria do convidado no Azure Monitor para analisá-los com as mesmas ferramentas de monitorização, como os serviços do Azure.

![Coleção de recursos de computação do Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico do Azure
Ativar a extensão de diagnóstico do Azure para máquinas virtuais do Azure permite-lhe para recolher registos e recursos, incluindo serviços Web do serviço de Cloud do Azure (clássico) e funções de trabalho, máquinas virtuais, máquinas virtuais de computação de métricas do sistema operativo convidado do Azure conjuntos de dimensionamento e o Service Fabric.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Armazenamento | Quando ativar a extensão de diagnóstico, irá escrever para uma conta de armazenamento por predefinição. | [Armazenar e ver dados de diagnósticos no Armazenamento do Azure](diagnostics-extension-to-storage.md) |
| Métricas de Monitor do Azure | Ao configurar a extensão de diagnóstico para recolher contadores de desempenho, eles são escritos na base de dados de métricas do Azure Monitor. | [Enviar métricas de SO convidado para a métrica do Azure Monitor armazenam utilizando um modelo do Resource Manager para uma máquina virtual do Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Registos do Application Insights | Recolha registos e contadores de desempenho do recurso de computação que suporta a sua aplicação para ser analisado com outros dados de aplicação. | [Enviar dados de diagnóstico do serviço em nuvem, a Máquina Virtual ou o Service Fabric para o Application Insights](diagnostics-extension-to-application-insights.md) |
| Hubs de Eventos | Configure a extensão de diagnóstico para transmitir os dados para outras localizações com os Hubs de eventos.  | [Transmissão em fluxo de dados de diagnóstico do Azure no caminho de acesso frequente ao utilizar os Hubs de eventos](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Agente do log Analytics 
Instale o agente Log Analytics para monitorização abrangente e a gestão das suas máquinas de virtuais do Windows ou Linux. A máquina virtual pode estar em execução no Azure, noutra cloud ou no local.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | O agente Log Analytics liga-se para o Azure monitorizar diretamente ou através do Microsoft System Center Operations Manager e permite-lhe recolher dados de origens de dados que configurou ou de soluções que fornecem informações adicionais nas aplicações de monitorização em execução na máquina virtual. | [Origens de dados do agente no Azure Monitor](agent-data-sources.md)<br>[Ligar o Operations Manager para o Azure Monitor](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs 
[Monitor do Azure para VMs](../insights/vminsights-overview.md) fornece uma experiência de monitorização personalizada para máquinas virtuais, proporcionando funcionalidades para além das funcionalidades do Azure Monitor principais, incluindo o estado de serviço e o estado de funcionamento da VM. Ele requer um agente de dependência em máquinas virtuais Windows e Linux que se integra com o agente do Log Analytics para recolher os dados detetados sobre processos em execução na máquina virtual e dependências de processo externo.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Armazena os dados sobre processos e as dependências no agente. | [Utilizar o Azure Monitor para VMs (pré-visualização) mapa para compreender os componentes da aplicação](../insights/vminsights-maps.md) |
| Armazenamento de VM | Monitor do Azure para VMs armazena informações de estado de integridade num local personalizado. Isso só está disponível para o Azure Monitor para as VMs no portal do Azure para além da [REST API do Estado de funcionamento do recurso do Azure](/rest/api/resourcehealth/). | [Compreender o estado de funcionamento das suas máquinas virtuais do Azure](../insights/vminsights-health.md)<br>[O Azure Resource health REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Código da aplicação
Monitorização de aplicações detalhadas no Azure Monitor é feito com [Application Insights](https://docs.microsoft.com/azure/application-insights/) que recolhe dados de aplicações em execução num vasto leque de plataformas. A aplicação pode estar em execução no Azure, noutra cloud ou no local.

![Recolha de dados de aplicação](media/data-sources/applications.png)


### <a name="application-data"></a>Dados da aplicação
Quando ativar o Application Insights para uma aplicação ao instalar um pacote de instrumentação, recolhe métricas e registos relacionados com o desempenho e a operação da aplicação. O Application Insights armazena os dados que coleta na mesma plataforma de dados do Azure Monitor utilizada por outras origens de dados. Ele inclui ferramentas abrangentes para analisar estes dados, mas também pode analisá-los com dados de outras origens usando ferramentas como o Explorador de métricas e o Log Analytics.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Dados operacionais sobre a sua aplicação, incluindo vistas de página, pedidos de aplicações, exceções e rastreios. | [Analisar dados de registo no Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informações de dependência entre componentes da aplicação para suportar o mapa da aplicação e a correlação de telemetria. | [Correlação de telemetria no Application Insights](../app/correlation.md) <br> [Mapeamento de Aplicações](../app/app-map.md) |
|            | Resultados de testes de disponibilidade que testar a disponibilidade e capacidade de resposta da sua aplicação a partir de diferentes locais na Internet pública. | [Monitorizar a disponibilidade e a capacidade de resposta de qualquer site](../app/monitor-web-app-availability.md) |
| Métricas de Monitor do Azure | Application Insights recolhe métricas que descrevem o desempenho e a operação da aplicação, além de métricas personalizadas que definir na sua aplicação para a base de dados de métricas do Azure Monitor. | [Métricas com base em log e pré-agregados no Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API do Application Insights para métricas e eventos personalizados](../app/api-custom-events-metrics.md) |
| Storage do Azure | Envie dados de aplicativo ao armazenamento do Azure para arquivamento. | [Exportar telemetria a partir do Application Insights](../app/export-telemetry.md) |
|            | Detalhes de testes de disponibilidade são armazenados no armazenamento do Azure. Utilize o Application Insights no portal do Azure para transferir para análise local. Resultados de testes de disponibilidade são armazenados nos Logs de Monitor do Azure. | [Monitorizar a disponibilidade e a capacidade de resposta de qualquer site](../app/monitor-web-app-availability.md) |
|            | Dados de rastreio do Profiler são armazenados no armazenamento do Azure. Utilize o Application Insights no portal do Azure para transferir para análise local.  | [Aplicações de produção do perfil no Azure com o Application Insights](../app/profiler-overview.md) 
|            | Depure instantâneo dados capturados para um subconjunto de exceções são armazenados no armazenamento do Azure. Utilize o Application Insights no portal do Azure para transferir para análise local.  | [Como funcionam os instantâneos](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Soluções de monitorização e informações
[Soluções de monitorização](../insights/solutions.md) e [Insights](../insights/insights-overview.md) recolher dados para fornecer informações adicionais sobre o funcionamento de um determinado serviço ou aplicação. Eles podem recursos de endereços em camadas da aplicação diferentes e até mesmo várias camadas.

### <a name="monitoring-solutions"></a>Soluções de monitorização

| Destino | Descrição | Referência
|:---|:---|:---|
| Registos do Azure Monitor | Soluções de monitorização recolhem dados sobre os registos do Azure Monitor em que pode ser analisado usando a linguagem de consulta ou [vistas](view-designer.md) que normalmente estão incluídas na solução. | [Detalhes de recolha de dados para a monitorização de soluções no Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Monitor do Azure para contentores
[Monitor do Azure para contentores](../insights/container-insights-overview.md) fornece uma experiência de monitorização personalizada para [Azure Kubernetes Service (AKS)](/azure/aks/). Recolhe dados adicionais sobre esses recursos descritos na tabela seguinte.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Arquivos de dados de monitorização para o AKS, incluindo o inventário, os registos e eventos. Dados métricos também são armazenados nos registos para poder tirar partido de sua funcionalidade de análise no portal. | [Compreender o desempenho do cluster do AKS com o Azure Monitor para contentores](../insights/container-insights-analyze.md) |
| Métricas de Monitor do Azure | Dados de métricos são armazenados na base de dados de métrica para visualização de unidade e alertas. | [Ver métricas de contentor no Explorador de métricas](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Serviço Kubernetes do Azure | Por ordem para uma experiência quase de tempo real, o Azure Monitor para contentores Apresenta os dados diretamente a partir do serviço Kubernetes do Azure no portal do Azure. | [Como ver os registos de contentor em tempo real com o Azure Monitor para contentores (pré-visualização)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
[Monitor do Azure para VMs](../insights/vminsights-overview.md) fornece uma experiência personalizada para monitorizar máquinas virtuais. Uma descrição dos dados recolhidos pelo Monitor do Azure para VMs está incluída nos [sistema operativo (convidado)](#operating-system-guest) secção acima.

## <a name="custom-sources"></a>Origens personalizadas
Além dos escalões padrão de uma aplicação, terá de monitorizar os outros recursos que tenham a telemetria que não pode ser recolhida com as outras origens de dados. Para estes recursos, escreva estes dados para métricas ou registos com uma API de Monitor do Azure.

![Coleção personalizada](media/data-sources/custom.png)

| Destino | Método | Descrição | Referência |
|:---|:---|:---|:---|
| Registos do Azure Monitor | API do Recoletor de Dados | Recolher dados de registo a partir de qualquer cliente REST e armazenar na área de trabalho do Log Analytics. | [Enviar dados de registo para o Azure Monitor com a API de Recoletor de dados HTTP (pré-visualização pública)](data-collector-api.md) |
| Métricas de Monitor do Azure | API de métricas personalizadas | Recolha dados de métrica de qualquer cliente REST e o arquivo na base de dados do Azure Monitor métricas. | [Enviar métricas personalizadas para um recurso do Azure para o arquivo de métrica de Monitor do Azure com uma API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Outros serviços
Outros serviços do Azure escrever dados para a plataforma de dados do Azure Monitor. Isto permite-lhe analisar os dados recolhidos por esses serviços com os dados recolhidos pelo Monitor do Azure e tirar partido da análise do mesmo e ferramentas de visualização.

| Serviço | Destino | Descrição | Referência |
|:---|:---|:---|:---|
| [Centro de Segurança do Azure](/azure/security-center/) | Registos do Azure Monitor | Centro de segurança do Azure armazena os dados de segurança recolhe numa área de trabalho do Log Analytics que lhe permite ser analisados com outros dados de registo recolhidos pelo Azure Monitor.  | [Data collection in Azure Security Center](../../security-center/security-center-enable-data-collection.md) (Recolha de dados no Centro de Segurança do Azure) |
| [Azure Sentinel](/azure/sentinel/) | Registos do Azure Monitor | Sentinel do Azure armazena os dados que coleta a partir de origens de dados diferentes na área de trabalho do Log Analytics que lhe permite ser analisados com outros dados de registo recolhidos pelo Azure Monitor.  | [Ligar a origens de dados](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [tipos de dados recolhidos pelo Azure Monitor](data-platform.md) e como visualizar e analisar estes dados.
- Lista os [diferentes localizações onde os recursos do Azure armazenam dados](data-locations.md) e como pode acessá-lo. 
