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
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: 4ae8ae43d8c245270b64611bed0ac4930aa0ecc3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763432"
---
# <a name="sources-of-data-in-azure-monitor"></a>Origens de dados no Azure Monitor
Este artigo descreve as origens de dados recolhidos pelo Azure Monitor para monitorizar o estado de funcionamento e desempenho dos seus recursos e as aplicações em execução nos mesmos. Esses recursos podem ser no Azure, no noutra cloud ou no local.  Ver [dados recolhidos pelo Azure Monitor](data-collection.md) para obter detalhes sobre como estes dados são armazenados e como pode vê-lo.

A monitorização dos dados no Azure provém de uma variedade de origens que podem ser organizadas em camadas, as camadas mais altos a ser a sua aplicação e todos os sistemas operativos e as camadas inferiores a ser componentes da plataforma do Azure. Isso está ilustrado no diagrama seguinte com cada camada descrita detalhadamente nas seções a seguir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

![Camadas de dados de monitorização](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Azure Tenant
Telemetria relacionados com o seu inquilino do Azure é recolhida a partir de serviços de ao nível do inquilino, como o Azure Active Directory.

![Coleção de inquilino do Azure](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Registos de auditoria do Azure Active Directory
[Relatórios do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contém o histórico de início de sessão atividade e auditoria do registo de alterações feitas a um inquilino específico. Estes registos de auditoria pode ser escrito nos registos do Azure Monitor analisá-los com outros dados de registo.


## <a name="azure-platform"></a>Plataforma Azure
Telemetria relacionados com o estado de funcionamento e a operação do Azure em si inclui dados sobre a operação e a gestão da sua subscrição do Azure. Ele inclui dados de estado de funcionamento do serviço armazenados no registo de atividades do Azure e os registos de auditoria do Azure Active Directory.

![Coleção de subscrição do Azure](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[O Azure Service Health](service-notifications.md) fornece informações sobre o estado de funcionamento dos serviços do Azure na sua subscrição que a aplicação e os recursos se baseiam em. Pode criar alertas para serem notificadas do atuais e esperados problemas críticos que possam afetar a sua aplicação. Estado de funcionamento do serviço de registos é armazenados na [registo de atividades do Azure](activity-logs-overview.md), para que possa vê-los no Gerenciador de registo de atividade e copiá-los sobre os registos do Azure Monitor.

### <a name="azure-activity-log"></a>Registo de atividades do Azure
O [registo de atividades do Azure](activity-logs-overview.md) inclui registos de estado de funcionamento do serviço, juntamente com os registos em quaisquer alterações de configuração feitas aos seus recursos do Azure. O registo de atividades está disponível para todos os recursos do Azure e representa seus _externo_ vista. Os tipos específicos de registos no registo de Atividades são descritos nas [esquema de eventos de registo de atividades do Azure](activity-log-schema.md).

Pode ver o registo de atividades para um recurso específico na sua página nos registos do Azure de portal ou a vista de vários recursos na [atividade Log Explorer](activity-logs-overview.md). É particularmente útil copiar as entradas de registo para o Azure Monitor para combiná-los com outros dados de monitorização. Também pode enviar-lhes para outras localizações usando [os Hubs de eventos](activity-logs-stream-event-hubs.md).



## <a name="azure-services"></a>Serviços do Azure
Registos de diagnóstico ao nível métricas e recursos fornecem informações sobre o _interno_ operação dos recursos do Azure. Estas estão disponíveis para serviços do Azure mais e soluções de gerenciamento fornecem informações adicionais sobre os serviços específicos.

![Coleção de recursos do Azure](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Métricas
Serviços do Azure mais irão gerar [métricas de plataforma](data-collection.md#metrics) que refletem o desempenho e operação. As específicas [métricas irão variar para cada tipo de recurso](metrics-supported.md).  Eles estão acessíveis a partir de análise de métricas e podem ser copiados para os registos para análise de tendência e outro com o Log Analytics.


### <a name="resource-diagnostic-logs"></a>Registos de diagnóstico de recursos
Embora o registo de atividades fornece informações sobre as operações executadas num recursos do Azure, ao nível de recursos [registos de diagnóstico](diagnostic-logs-overview.md) fornecem informações sobre o funcionamento do recurso em si.   Os requisitos de configuração e conteúdo estes registos [varia consoante o tipo de recurso](diagnostic-logs-schema.md).

Não pode ver os registos de diagnóstico diretamente no portal do Azure, mas pode [enviá-los para o armazenamento do Azure para arquivar](archive-diagnostic-logs.md) e exportá-las para [Hub de eventos](../../event-hubs/event-hubs-about.md) para o redirecionamento para outros serviços, ou [para O Azure Monitor](diagnostic-logs-stream-log-store.md) para análise. Alguns recursos podem escrever diretamente para o Azure Monitor, enquanto outras pessoas a escrever para uma conta de armazenamento antes de ser [importados para o Log Analytics](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Soluções de monitorização
 [Soluções de monitorização](../../azure-monitor/insights/solutions.md) recolher dados para fornecer informações adicionais sobre o funcionamento de um determinado serviço ou aplicação. Eles recolhem dados sobre os registos do Azure Monitor em que pode ser analisado com o [linguagem de consulta](../../azure-monitor/log-query/log-query-overview.md) ou [vistas](view-designer.md) que normalmente estão incluídas na solução.


## <a name="guest-operating-system"></a>Sistema operativo convidado
Recursos de computação no Azure, noutras clouds e no local têm um sistema de operativo convidado para monitorizar. Com a instalação de um ou mais agentes, pode recolher telemetria do convidado para as mesmas ferramentas de monitorização, como os serviços do Azure.

![Coleção de recursos de computação do Azure](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico do Azure
Com a extensão de diagnóstico do Azure, ele fornece um nível básico de monitorização através da recolha de registos e recursos de computação de dados de desempenho do sistema operativo de cliente do Azure.   

### <a name="log-analytics-agent"></a>Agente do log Analytics
Monitorização abrangente e a gestão das suas máquinas virtuais Windows ou Linux ou o computador físico é entregue com o agente do Log Analytics. A máquina virtual pode estar em execução no Azure, noutra cloud ou no local e o agente se liga para o Azure monitorizar diretamente ou através do Microsoft System Center Operations Manager e permite-lhe recolher dados a partir [origens de dados](agent-data-sources.md) que configurar ou a partir [soluções de monitorização](../../azure-monitor/insights/solutions.md) que fornecem informações adicionais sobre as aplicações em execução na máquina virtual.


### <a name="dependency-agent"></a>Agente de Dependência
[Mapa de serviço](../insights/service-map.md) e [do Azure Monitor para VMs](../../azure-monitor/insights/vminsights-overview.md) requer um agente de dependência em máquinas virtuais Windows e Linux. Isso se integra com o agente do Log Analytics recolhe os dados detetados sobre processos em execução na máquina virtual e dependências de processo externo. Ele armazena estes dados no Azure Monitor e visualiza os componentes interconectados detetados.  

Para compreender melhor as diferenças entre os agentes e que a utilizar consoante os requisitos de monitorização, consulte [descrição geral de agentes da monitorização](agents-overview.md).

## <a name="applications"></a>Aplicações
Além de telemetria que seu aplicativo pode gravar o sistema operativo convidado, monitorização detalhada de aplicações é feita com [Application Insights](https://docs.microsoft.com/azure/application-insights/). O Application Insights pode recolher dados de aplicações em execução num vasto leque de plataformas. A aplicação pode estar em execução no Azure, noutra cloud ou no local.

![Recolha de dados de aplicação](media/data-sources/application-collection.png)


### <a name="application-data"></a>Dados da aplicação
Quando ativar o Application Insights para uma aplicação ao instalar um pacote de instrumentação, recolhe métricas e registos relacionados com o desempenho e a operação da aplicação. Isto inclui informações detalhadas sobre exceções, pedidos de aplicações e vistas de página. O Application Insights armazena os dados que coleta no Azure Monitor. Ele inclui ferramentas abrangentes para analisar estes dados, mas também pode analisá-los com dados de outras origens usando ferramentas como a análise de métrica e o log analytics.

Também pode utilizar o Application Insights para [criar uma métrica personalizada](../../application-insights/app-insights-api-custom-events-metrics.md).  Isto permite-lhe definir sua própria lógica para calcular um valor numérico e, em seguida, armazenamos esse valor com outras métricas que podem ser acedidas a partir de análise de métrica e utilizadas para [dimensionamento automático](autoscale-custom-metric.md) e alertas de métricas.


### <a name="dependencies"></a>Dependências
Para monitorizar operações lógicas diferentes de uma aplicação, tem [recolher telemetria em vários componentes](../../application-insights/app-insights-transaction-diagnostics.md). O Application Insights suporta [distribuído a correlação de telemetria](../../application-insights/application-insights-correlation.md) que identifica as dependências entre os componentes que permite analisá-los em conjunto.

### <a name="availability-tests"></a>Testes de disponibilidade
[Testes de disponibilidade](../../application-insights/app-insights-monitor-web-app-availability.md) no Application Insights lhe permitem testar a disponibilidade e capacidade de resposta da sua aplicação a partir de diferentes locais na Internet pública. Pode fazer um teste de ping simples para verificar se o aplicativo está ativo ou utilizar o Visual Studio para criar um teste da web que simula um cenário de utilizador.  Testes de disponibilidade não requerem qualquer instrumentação no aplicativo.

## <a name="custom-sources"></a>Origens personalizadas
Além dos escalões padrão de uma aplicação, terá de monitorizar os outros recursos que tenham a telemetria que não pode ser recolhida com as outras origens de dados. Para estes recursos, terá de escrever estes dados utilizando uma API de Monitor do Azure.

![Recolha de dados personalizados](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>API do Recoletor de Dados
O Azure Monitor pode recolher dados de registo de qualquer cliente REST utilizando o [API do Recoletor de dados](../../azure-monitor/platform/data-collector-api.md). Isto permite-lhe criar cenários de monitorização personalizados e expande a monitorização para recursos que não expõem a telemetria por meio de outras origens.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [tipos de dados recolhidos pelo Azure Monitor](data-collection.md) e como visualizar e analisar estes dados.
