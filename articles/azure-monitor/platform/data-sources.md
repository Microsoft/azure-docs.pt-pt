---
title: Fontes de dados em Azure Monitor | Microsoft Docs
description: Descreve os dados disponíveis para monitorar a integridade e o desempenho dos recursos do Azure e os aplicativos em execução neles.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: 8f17a3ffbbee2bc702be1c2c690a1d6c85d792cb
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751052"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Fontes de dados de monitoramento para Azure Monitor
O Azure Monitor se baseia em uma [plataforma de dados de monitoramento comum](data-platform.md) que inclui [logs](data-platform-logs.md) e [métricas](data-platform-metrics.md). Coletar dados nessa plataforma permite que os dados de vários recursos sejam analisados juntos usando um conjunto comum de ferramentas no Azure Monitor. Os dados de monitoramento também podem ser enviados para outros locais para dar suporte a determinados cenários, e alguns recursos podem gravar em outros locais antes que possam ser coletados em logs ou métricas.

Este artigo descreve as diferentes fontes de monitoramento de dados coletadas por Azure Monitor além dos dados de monitoramento criados pelos recursos do Azure. São fornecidos links para informações detalhadas sobre a configuração necessária para coletar esses dados em locais diferentes.

## <a name="application-tiers"></a>Camadas da aplicação

Fontes de dados de monitoramento de aplicativos do Azure podem ser organizadas em camadas, as camadas mais altas sendo seu próprio aplicativo e as camadas inferiores são componentes da plataforma do Azure. O método de acesso a dados de cada camada varia. As camadas de aplicativo são resumidas na tabela abaixo, e as fontes de dados de monitoramento em cada camada são apresentadas nas seções a seguir. Consulte [monitorando locais de dados no Azure](data-locations.md) para obter uma descrição de cada local de dados e como você pode acessar seus dados.


![Camadas de monitoramento](../media/overview/overview.png)


### <a name="azure"></a>Azure
A tabela a seguir descreve brevemente as camadas de aplicativo que são específicas para o Azure. Seguindo o link para obter mais detalhes sobre cada uma das seções abaixo.

| Escalão | Descrição | Método de coleta |
|:---|:---|:---|
| [Locatário do Azure](#azure-tenant) | Dados sobre a operação dos serviços do Azure ao nível do inquilino, como o Azure Active Directory. | Exiba os dados do AAD no portal ou configure a coleção para Azure Monitor usando uma configuração de diagnóstico de locatário. |
| [Subscrição do Azure](#azure-subscription) | Dados relacionados à integridade e ao gerenciamento de serviços entre recursos em sua assinatura do Azure, como o Resource Manager e a integridade do serviço. | Exibir no portal ou configurar a coleta para Azure Monitor usando um perfil de log. |
| [Recursos do Azure](#azure-resources) |  Dados sobre a operação e o desempenho de cada recurso do Azure. | Métricas coletadas automaticamente, exiba em Metrics Explorer.<br>Defina as configurações de diagnóstico para coletar logs em Azure Monitor.<br>Monitoramento de soluções e informações disponíveis para o monitoramento mais detalhado de tipos de recursos específicos. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, outra nuvem ou local 
A tabela a seguir descreve brevemente as camadas de aplicativo que podem estar no Azure, em outra nuvem ou no local. Seguindo o link para obter mais detalhes sobre cada uma das seções abaixo.

| Escalão | Descrição | Método de coleta |
|:---|:---|:---|
| [Sistema operacional (convidado)](#operating-system-guest) | Dados sobre o sistema operacional em recursos de computação. | Instale o agente de Log Analytics para coletar fontes de dados do cliente no Azure Monitor e no agente de dependência para coletar dependências que dão suporte a Azure Monitor para VMs.<br>Para máquinas virtuais do Azure, instale a extensão de diagnóstico do Azure para coletar logs e métricas em Azure Monitor. |
| [Código do aplicativo](#application-code) | Dados sobre o desempenho e a funcionalidade do aplicativo e do código reais, incluindo rastreamentos de desempenho, logs de aplicativos e telemetria de usuários. | Instrumente seu código para coletar dados em Application Insights. |
| [Fontes personalizadas](#custom-sources) | Dados de serviços externos ou outros componentes ou dispositivos. | Coletar dados de log ou métricas em Azure Monitor de qualquer cliente REST. |

## <a name="azure-tenant"></a>Inquilino do Azure
A telemetria relacionada ao seu locatário do Azure é coletada de serviços de todo o locatário, como Azure Active Directory.

![Coleção de locatários do Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory logs de auditoria
[Azure Active Directory relatórios](../../active-directory/reports-monitoring/overview-reports.md) contém o histórico de atividade de entrada e trilha de auditoria de alterações feitas em um locatário específico. 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Configure os logs do Azure AD a serem coletados no Azure Monitor para analisá-los com outros dados de monitoramento. | [Integrar logs do Azure AD com logs de Azure Monitor (versão prévia)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Armazenamento do Azure | Exportar logs do Azure AD para o armazenamento do Azure para arquivamento. | [Tutorial: arquivar logs do Azure AD em uma conta de armazenamento do Azure (versão prévia)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de Eventos | Transmita logs do Azure AD para outros locais usando os hubs de eventos. | [Tutorial: transmitir logs de Azure Active Directory para um hub de eventos do Azure (versão prévia)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Subscrição do Azure
Telemetria relacionada à integridade e operação de sua assinatura do Azure.

![Subscrição do Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Log de atividades do Azure 
O [log de atividades do Azure](platform-logs-overview.md) inclui registros de integridade do serviço juntamente com registros em qualquer alteração de configuração feita aos recursos em sua assinatura do Azure. O log de atividades está disponível para todos os recursos do Azure e representa sua exibição _externa_ .

| Destino | Descrição | Referência |
|:---|:---|
| Registo de atividades | O log de atividades é coletado em seu próprio armazenamento de dados que você pode exibir no menu Azure Monitor ou usar para criar alertas do log de atividades. | [Consultar o log de atividades no portal do Azure](activity-log-view.md#azure-portal) |
| Registos do Azure Monitor | Configure os logs de Azure Monitor para coletar o log de atividades para analisá-lo com outros dados de monitoramento. | [Coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](activity-log-collect.md) |
| Armazenamento do Azure | Exporte o log de atividades para o armazenamento do Azure para arquivamento. | [Log de atividades de arquivamento](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Transmitir o log de atividades para outros locais usando os hubs de eventos | [Transmita o log de atividades para o Hub de eventos](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Service Health
A [integridade do serviço do Azure](../../service-health/service-health-overview.md) fornece informações sobre a integridade dos serviços do Azure em sua assinatura da qual seu aplicativo e recursos dependem.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registo de atividades<br>Registos do Azure Monitor | Os registros de integridade do serviço são armazenados no log de atividades do Azure, para que você possa exibi-los no portal do Azure ou executar outras atividades que podem ser executadas com o log de atividades. | [Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure](service-notifications.md) |


## <a name="azure-resources"></a>Recursos do Azure
Métricas e logs de recursos fornecem informações sobre a operação _interna_ dos recursos do Azure. Eles estão disponíveis para a maioria dos serviços do Azure, e as soluções e informações de monitoramento coletam dados adicionais para serviços específicos.

![Coleção de recursos do Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Métricas de plataforma 
A maioria dos serviços do Azure enviará [métricas de plataforma](data-platform-metrics.md) que refletem seu desempenho e operação diretamente no banco de dados de métricas. As [métricas específicas irão variar para cada tipo de recurso](metrics-supported.md). 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Métricas de Azure Monitor | As métricas de plataforma serão gravadas no banco de dados de métricas Azure Monitor sem configuração. Acesse as métricas de plataforma de Metrics Explorer.  | [Getting started with Azure Metrics Explorer](metrics-getting-started.md) (Introdução ao Explorador de Métricas do Azure)<br>[Métricas com suporte com Azure Monitor](metrics-supported.md) |
| Registos do Azure Monitor | Copie métricas de plataforma para os logs para tendência e outras análises usando Log Analytics. | [O diagnóstico do Azure direto para Log Analytics](resource-logs-collect-workspace.md) |
| Event Hubs | Transmita métricas para outros locais usando os hubs de eventos. |[Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Registos do recurso
[Os logs de recursos](platform-logs-overview.md) fornecem informações sobre a operação _interna_ de um recurso do Azure.  Os logs de recursos são criados automaticamente, mas você deve criar uma configuração de diagnóstico para especificar um destino para que eles sejam coletados para cada recurso.

Os requisitos de configuração e o conteúdo dos logs de recursos variam por tipo de recurso e nem todos os serviços ainda os criam. Consulte [serviços, esquemas e categorias com suporte para logs de recursos do Azure](diagnostic-logs-schema.md) para obter detalhes sobre cada serviço e links para os procedimentos de configuração detalhados. Se o serviço não estiver listado neste artigo, esse serviço não criará logs de recursos no momento.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Envie logs de recursos para Azure Monitor logs para análise com outros dados de log coletados. | [Coletar logs de recursos do Azure no espaço de trabalho Log Analytics no Azure Monitor](resource-logs-collect-storage.md) |
| Armazenamento | Envie logs de recursos para o armazenamento do Azure para arquivamento. | [Arquivar logs de recursos do Azure](resource-logs-collect-workspace.md) |
| Event Hubs | Transmita logs de recursos para outros locais usando os hubs de eventos. |[Transmitir logs de recursos do Azure para um hub de eventos](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Sistema operacional (convidado)
Os recursos de computação no Azure, em outras nuvens, e locais têm um sistema operacional convidado para monitorar. Com a instalação de um ou mais agentes, você pode reunir a telemetria do convidado em Azure Monitor para analisá-lo com as mesmas ferramentas de monitoramento que os próprios serviços do Azure.

![Coleção de recursos de computação do Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico do Azure
Habilitar a extensão de Diagnóstico do Azure para máquinas virtuais do Azure permite que você colete logs e métricas do sistema operacional convidado de recursos de computação do Azure, incluindo funções de trabalho e Web do serviço de nuvem do Azure (clássico), máquinas virtuais, máquina virtual conjuntos de dimensionamento e Service Fabric.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Armazenamento | Quando você habilitar a extensão de diagnóstico, ela será gravada em uma conta de armazenamento por padrão. | [Armazenar e ver dados de diagnósticos no Armazenamento do Azure](diagnostics-extension-to-storage.md) |
| Métricas de Azure Monitor | Quando você configura a extensão de diagnóstico para coletar contadores de desempenho, eles são gravados no banco de dados de métricas Azure Monitor. | [Enviar métricas do sistema operacional convidado para o armazenamento de métrica Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Logs de Application Insights | Colete logs e contadores de desempenho do recurso de computação que dá suporte ao seu aplicativo para ser analisado com outros dados de aplicativo. | [Enviar dados de diagnóstico de serviço de nuvem, máquina virtual ou Service Fabric para Application Insights](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Configure a extensão de diagnóstico para transmitir os dados para outros locais usando os hubs de eventos.  | [Streaming de dados de Diagnóstico do Azure no Hot Path usando hubs de eventos](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Agente de Log Analytics 
Instale o agente de Log Analytics para monitoramento e gerenciamento abrangentes de suas máquinas virtuais Windows ou Linux. A máquina virtual pode ser executada no Azure, em outra nuvem ou no local.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | O agente de Log Analytics se conecta a Azure Monitor diretamente ou por meio do System Center Operations Manager e permite coletar dados de fontes de dados que você configura ou de soluções de monitoramento que fornecem informações adicionais sobre os aplicativos em execução na máquina virtual. | [Fontes de dados do agente no Azure Monitor](agent-data-sources.md)<br>[Conectar Operations Manager ao Azure Monitor](om-agents.md) |
| armazenamento de máquina virtual | Azure Monitor para VMs usa o agente de Log Analytics para armazenar informações de estado integridade em um local personalizado. Veja a secção seguinte para obter mais informações.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs 
O [Azure monitor para VMs](../insights/vminsights-overview.md) fornece uma experiência de monitoramento Personalizada para máquinas virtuais que fornecem recursos além da funcionalidade de Azure monitor principal, incluindo status do serviço e integridade da VM. Ele requer um Dependency Agent em máquinas virtuais Windows e Linux que se integre com o agente Log Analytics para coletar dados descobertos sobre processos em execução na máquina virtual e dependências de processo externas.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Armazena dados sobre processos e dependências no agente. | [Usando o mapa Azure Monitor para VMs (versão prévia) para entender os componentes do aplicativo](../insights/vminsights-maps.md) |
| armazenamento de máquina virtual | Azure Monitor para VMs usa o agente de Log Analytics para armazenar informações de estado integridade em um local personalizado. Isso só está disponível para Azure Monitor para VMs no portal do Azure além da [API REST do Azure Resource Health](/rest/api/resourcehealth/). | [Entender a integridade de suas máquinas virtuais do Azure](../insights/vminsights-health.md)<br>[API REST do Azure Resource Health](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Código do aplicativo
O monitoramento detalhado de aplicativos no Azure Monitor é feito com [Application insights](https://docs.microsoft.com/azure/application-insights/) que coleta dados de aplicativos em execução em uma variedade de plataformas. O aplicativo pode ser executado no Azure, em outra nuvem ou no local.

![Coleta de dados de aplicativo](media/data-sources/applications.png)


### <a name="application-data"></a>Dados da aplicação
Quando você habilita Application Insights para um aplicativo instalando um pacote de instrumentação, ele coleta métricas e logs relacionados ao desempenho e à operação do aplicativo. O Application Insights armazena os dados coletados na mesma plataforma de dados Azure Monitor usada por outras fontes de dados. Ele inclui ferramentas abrangentes para a análise desses dados, mas você também pode analisá-los com dados de outras fontes usando ferramentas como Metrics Explorer e Log Analytics.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Dados operacionais sobre seu aplicativo, incluindo exibições de página, solicitações de aplicativo, exceções e rastreamentos. | [Analisar dados de log em Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informações de dependência entre componentes de aplicativo para dar suporte ao mapa do aplicativo e à correlação de telemetria. | [Correlação de telemetria no Application Insights](../app/correlation.md) <br> [Mapeamento de Aplicações](../app/app-map.md) |
|            | Resultados de testes de disponibilidade que testam a disponibilidade e a capacidade de resposta do seu aplicativo de locais diferentes na Internet pública. | [Monitorizar a disponibilidade e a capacidade de resposta de qualquer site](../app/monitor-web-app-availability.md) |
| Métricas de Azure Monitor | Application Insights coleta métricas que descrevem o desempenho e a operação do aplicativo, além das métricas personalizadas que você define em seu aplicativo no banco de dados de métricas de Azure Monitor. | [Métricas baseadas no registo e pré-agregadas no Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API do Application Insights para métricas e eventos personalizados](../app/api-custom-events-metrics.md) |
| Armazenamento do Azure | Envie dados de aplicativo para o armazenamento do Azure para arquivamento. | [Exportar telemetria a partir do Application Insights](../app/export-telemetry.md) |
|            | Os detalhes dos testes de disponibilidade são armazenados no armazenamento do Azure. Use Application Insights no portal do Azure para baixar para análise local. Os resultados dos testes de disponibilidade são armazenados em logs de Azure Monitor. | [Monitorizar a disponibilidade e a capacidade de resposta de qualquer site](../app/monitor-web-app-availability.md) |
|            | Os dados de rastreamento do profiler são armazenados no armazenamento do Azure. Use Application Insights no portal do Azure para baixar para análise local.  | [Perfil de aplicativos de produção no Azure com Application Insights](../app/profiler-overview.md) 
|            | Os dados de instantâneo de depuração capturados para um subconjunto de exceções são armazenados no armazenamento do Azure. Use Application Insights no portal do Azure para baixar para análise local.  | [Como funcionam os instantâneos](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Soluções e ideias de monitoramento
O [monitoramento de soluções](../insights/solutions.md) e [informações](../insights/insights-overview.md) coleta dados para fornecer informações adicionais sobre a operação de um serviço ou aplicativo específico. Eles podem tratar recursos em diferentes camadas de aplicativo e até mesmo várias camadas.

### <a name="monitoring-solutions"></a>Soluções de monitorização

| Destino | Descrição | Referência
|:---|:---|:---|
| Registos do Azure Monitor | As soluções de monitoramento coletam dados em logs de Azure Monitor onde podem ser analisados usando a linguagem de consulta ou [exibições](view-designer.md) que normalmente são incluídas na solução. | [Detalhes da coleta de dados para soluções de monitoramento no Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores
[Azure monitor para contêineres](../insights/container-insights-overview.md) fornece uma experiência de monitoramento Personalizada para o [AKs (serviço kubernetes do Azure)](/azure/aks/). Ele coleta dados adicionais sobre esses recursos descritos na tabela a seguir.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Registos do Azure Monitor | Armazena dados de monitoramento para AKS, incluindo inventário, logs e eventos. Os dados de métrica também são armazenados em logs a fim de aproveitar sua funcionalidade de análise no Portal. | [Compreender o desempenho do cluster do AKS com o Azure Monitor para contentores](../insights/container-insights-analyze.md) |
| Métricas de Azure Monitor | Os dados de métrica são armazenados no banco de dado de métrica para impulsionar a visualização e os alertas. | [Exibir métricas de contêiner no Metrics Explorer](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Serviço Kubernetes do Azure | Fornece acesso direto aos logs de contêiner do AKS (serviço kubernetes do Azure) (stdout/stderr), eventos e métricas de Pod no Portal. | [Como exibir logs de kubernetes, eventos e métricas de pod em tempo real](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
[Azure monitor para VMs](../insights/vminsights-overview.md) fornece uma experiência personalizada para monitorar máquinas virtuais. Uma descrição dos dados coletados pelo Azure Monitor para VMs está incluída na seção do [sistema operacional (convidado)](#operating-system-guest) acima.

## <a name="custom-sources"></a>Fontes personalizadas
Além das camadas padrão de um aplicativo, talvez seja necessário monitorar outros recursos que têm telemetria que não pode ser coletada com outras fontes de dados. Para esses recursos, grave esses dados em métricas ou logs usando uma API Azure Monitor.

![Coleção personalizada](media/data-sources/custom.png)

| Destino | Método | Descrição | Referência |
|:---|:---|:---|:---|
| Registos do Azure Monitor | API do Recoletor de Dados | Coletar dados de log de qualquer cliente REST e armazenar em Log Analytics espaço de trabalho. | [Enviar dados de log para Azure Monitor com a API do coletor de dados HTTP (visualização pública)](data-collector-api.md) |
| Métricas de Azure Monitor | API de métricas personalizadas | Coletar dados de métrica de qualquer cliente REST e armazenar em Azure Monitor banco de dados de métricas. | [Enviar métricas personalizadas para um recurso do Azure para o repositório de métrica Azure Monitor usando uma API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Outros serviços
Outros serviços no Azure gravam dados na plataforma de dados Azure Monitor. Isso permite que você analise os dados coletados por esses serviços com os dados coletados por Azure Monitor e aproveite as mesmas ferramentas de análise e visualização.

| Serviço | Destino | Descrição | Referência |
|:---|:---|:---|:---|
| [Centro de Segurança do Azure](/azure/security-center/) | Registos do Azure Monitor | A central de segurança do Azure armazena os dados de segurança coletados em um espaço de trabalho Log Analytics que permite que ele seja analisado com outros dados de log coletados pelo Azure Monitor.  | [Data collection in Azure Security Center](../../security-center/security-center-enable-data-collection.md) (Recolha de dados no Centro de Segurança do Azure) |
| [Azure Sentinel](/azure/sentinel/) | Registos do Azure Monitor | O Azure Sentinel armazena os dados coletados de diferentes fontes de dados em um Log Analytics espaço de trabalho que permite que ele seja analisado com outros dados de log coletados pelo Azure Monitor.  | [Conectar fontes de dados](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [tipos de dados de monitoramento coletados por Azure monitor](data-platform.md) e como exibir e analisar esses dados.
- Liste os [diferentes locais em que os recursos do Azure armazenam dados](data-locations.md) e como você pode acessá-los. 
