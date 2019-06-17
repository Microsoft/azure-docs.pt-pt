---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: ac400c86af8236ff5d67b8b6fbf99f6f4b1d36c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158230"
---
Pode tirar partido das muitas oportunidades para monitorizar as suas VMs por recolher, visualizar e analisar o diagnóstico e registos de dados. Para fazer simples [monitorização](../articles/azure-monitor/overview.md) da sua VM, pode utilizar o ecrã de descrição geral para a VM no portal do Azure. Pode usar [extensões](../articles/virtual-machines/windows/extensions-features.md) para configurar os diagnósticos nas suas VMs para recolher dados de métricos adicionais. Também pode utilizar as opções de monitorização mais avançadas, como [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) e [do Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnósticos e métricas 

Pode configurar e monitorizar a coleção de [dados de diagnóstico](https://docs.microsoft.com/cli/azure/vm/diagnostics) usando [métricas](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) no portal do Azure, a CLI do Azure, Azure PowerShell e aplicativos de programação Interfaces de programação (APIs). Pode, por exemplo:

- **Observe as métricas básicas para a VM.** No ecrã de descrição geral do portal do Azure, as métricas básicas apresentadas incluem a utilização da CPU, utilização de rede, total de bytes de disco e as operações de disco por segundo.

- **Ativar a recolha de diagnóstico de arranque e vê-la no portal do Azure.** Quando colocar a sua própria imagem para o Azure ou mesmo arrancar uma das imagens da plataforma, podem existir muitos motivos por que uma VM entra num Estado de não arranque. É possível habilitar facilmente o diagnóstico de arranque quando cria uma VM ao clicar em **ativado** para diagnóstico de arranque na secção monitorização do ecrã de definições.

    Como as VMs de arranque, o agente de diagnóstico de arranque captura a saída de arranque e guarda-a no armazenamento do Azure. Estes dados podem ser utilizados para resolver problemas de arranque das VMs. Diagnóstico de arranque não é ativado automaticamente quando cria uma VM a partir das ferramentas da linha de comandos. Antes de ativar os diagnósticos de arranque, tem de ser criada uma conta de armazenamento para guardar os registos de arranque. Se ativar o diagnóstico de arranque no portal do Azure, uma conta de armazenamento é criada automaticamente para.

    Se não tiver ativado o diagnóstico de arranque quando a VM foi criada, pode sempre ativá-la mais tarde, utilizando [CLI do Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic), ou um [modelo Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Ative a recolha de dados de diagnóstico do SO convidado.** Quando cria uma VM, terá a oportunidade no ecrã de definições para ativar o diagnóstico de SO convidado. Quando ativa a recolha de dados de diagnóstico, o [IaaSDiagnostics extensão para Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou o [IaaSDiagnostics extensão para Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) é adicionado à VM, que permite-lhe recolher adicionais dados de disco, da CPU e memória.

    Pode utilizar os dados de diagnóstico recolhidos, para configurar o dimensionamento automático para as suas VMs. Também pode configurar os registos para armazenar os dados e defina alertas para informá-lo quando o desempenho não é muito bom.

## <a name="alerts"></a>Alertas

Pode criar [alertas](../articles/azure-monitor/platform/alerts-overview.md) com base em métricas de desempenho específicos. Os problemas que pode ser alertado sobre exemplos de quando a utilização média da CPU excede um determinado limiar ou espaço em disco livre disponível cai abaixo de um determinado período. Alertas podem ser configurados no [portal do Azure](../articles/azure-monitor/platform/alerts-classic-portal.md), utilizando [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell), ou o [da CLI do Azure](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[O Azure Service Health](../articles/service-health/service-health-overview.md) fornece orientação e suporte personalizados quando é afetado por problemas nos serviços do Azure e ajuda a preparar para a próxima a manutenção planeada. O Azure Service Health alerta-o e as suas equipas através de notificações flexíveis e direcionadas.

## <a name="azure-resource-health"></a>Azure Resource Health

[O Azure Resource health](../articles/service-health/resource-health-overview.md) ajuda-o a diagnosticar e obter suporte quando um problema do Azure afeta os seus recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

## <a name="azure-activity-log"></a>Registo de atividades do Azure

O [registo de atividades do Azure](../articles/azure-monitor/platform/activity-logs-overview.md) é um registo de subscrição que fornece informações sobre os eventos de nível de assinatura que ocorreram no Azure. O registo de inclui uma variedade de dados, a partir de dados operacionais do Azure Resource Manager para as atualizações em eventos de estado de funcionamento do serviço. Pode clicar em registo de atividades no portal do Azure para ver o registo para a sua VM.

Algumas das coisas que pode fazer com o registo de atividades incluem:

- Criar uma [alertas num evento do registo de atividade](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Stream-lo para um Hub de eventos](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou de uma solução de análise personalizada, como o Power BI.
- Analise-o no Power BI com o [pacote de conteúdos do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Guarde-o para uma conta de armazenamento](../articles/azure-monitor/platform/archive-activity-log.md) para inspeção de arquivamento ou manual. Pode especificar o período de retenção (em dias) com o perfil de registo.

Também pode acessar os dados de registo de atividade usando [do Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), o [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor), ou [APIs de REST do Monitor](https://docs.microsoft.com/rest/api/monitor/).

[Registos de diagnóstico do Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md) registos emitidos pela sua VM, que fornecem dados avançados e frequentes sobre o respetivo funcionamento. Os registos de diagnóstico diferem do registo de atividades, fornecendo informações sobre operações executadas dentro da VM.

Algumas das coisas que pode fazer com os registos de diagnóstico incluem:

- [Salvá-los numa conta de armazenamento](../articles/azure-monitor/platform/archive-diagnostic-logs.md) para inspeção de auditoria ou manual. Pode especificar o período de retenção (em dias) com as definições de diagnóstico de recursos.
- [Stream-los para os Hubs de eventos](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou de uma solução de análise personalizada, como o Power BI.
- Analisá-los com [do Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitorização avançada

- [O Azure Monitor](../articles/azure-monitor/overview.md) é um serviço que monitoriza a sua cloud e ambientes para manter a disponibilidade e desempenho no local. Proporciona uma solução abrangente para recolher, analisar e atuar na telemetria a partir da nuvem e ambientes no local. Ajuda a compreender o desempenho das suas aplicações e identifica proativamente os problemas que as afetam e os recursos de que dependem. Pode instalar uma extensão num [VM do Linux](../articles/virtual-machines/linux/extensions-oms.md) ou uma [Windows VM](../articles/virtual-machines/windows/extensions-oms.md) que instala o agente do Log Analytics para recolher dados de registo e armazenar numa área de trabalho do Log Analytics.

    Para Windows e VMs do Linux, o método recomendado para recolher registos está instalando o agente Log Analytics. A forma mais fácil de instalar o agente Log Analytics numa VM é através da [extensão de VM do Log Analytics](../articles/log-analytics/log-analytics-azure-vm-extension.md). A utilização da extensão simplifica o processo de instalação e configura automaticamente o agente para enviar dados para a área de trabalho do Log Analytics que especificar. O agente é também atualizado automaticamente e assegura que tem as funcionalidades e correções mais recentes.

- [Observador de rede](../articles/network-watcher/network-watcher-monitoring-overview.md) permite-lhe monitorizar a sua VM e os respetivos recursos associados, como estão relacionados com a rede que estão. Pode instalar a extensão de agente do observador de rede num [VM do Linux](../articles/virtual-machines/linux/extensions-nwa.md) ou uma [Windows VM](../articles/virtual-machines/windows/extensions-nwa.md).

- [Monitor do Azure para VMs](../articles/azure-monitor/insights/vminsights-overview.md) monitoriza as suas máquinas virtuais do Azure (VM) em escala ao analisar o desempenho e estado de funcionamento do Windows e VMs do Linux, incluindo os respetivos diferentes processos e interconectadas dependências em outros recursos e externo processos. 

## <a name="next-steps"></a>Passos Seguintes
- Siga os passos em [monitorize máquinas virtuais Windows com o Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) ou [monitorizar máquinas virtuais do Linux com a CLI do Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Saiba mais sobre as práticas recomendadas sobre [monitorização e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
