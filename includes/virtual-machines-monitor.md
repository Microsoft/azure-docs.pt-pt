---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: fbc6889507e58c4721597a1108337fcb1f8756a2
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751753"
---
Com o crescimento significativo das VMs hospedadas no Azure, é importante identificar problemas de desempenho e integridade que afetam os aplicativos e serviços de infraestrutura aos quais eles dão suporte. O monitoramento básico é fornecido por padrão com o Azure pelos tipos de métrica uso de CPU, utilização de disco, utilização de memória e tráfego de rede coletado pelo hipervisor de host. Dados de métrica e de log adicionais podem ser coletados usando [extensões](../articles/virtual-machines/windows/extensions-features.md) para configurar o diagnóstico em suas VMs a partir do sistema operacional convidado.

Para detectar e ajudar a diagnosticar problemas de desempenho e integridade com os componentes do sistema operacional convidado, baseados em .NET ou aplicativos Web Java em execução dentro da VM, Azure Monitor fornece monitoramento centralizado com recursos abrangentes, como Azure Monitor para VMs e Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnóstico e métricas 

Você pode configurar e monitorar a coleção de [dados de diagnóstico](https://docs.microsoft.com/cli/azure/vm/diagnostics) usando [métricas](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) no portal do Azure, as interfaces de programação de aplicativos CLI do Azure, Azure PowerShell e programação (APIs). Por exemplo, pode:

- **Observe as métricas básicas para a VM.** Na tela de visão geral do portal do Azure, as métricas básicas mostradas incluem uso de CPU, uso de rede, total de bytes de disco e operações de disco por segundo.

- **Habilite a coleta de diagnóstico de inicialização e exiba-o usando o portal do Azure.** Ao trazer sua própria imagem para o Azure ou até mesmo inicializar uma das imagens da plataforma, pode haver muitas razões pelas quais uma VM entra em um estado não inicializável. Você pode habilitar o diagnóstico de inicialização facilmente ao criar uma VM clicando em **habilitado** para diagnóstico de inicialização na seção monitoramento da tela Configurações.

    À medida que as VMs são inicializadas, o agente de diagnóstico de inicialização captura a saída de inicialização e a armazena no armazenamento do Azure. Estes dados podem ser utilizados para resolver problemas de arranque das VMs. O diagnóstico de inicialização não é habilitado automaticamente quando você cria uma VM com base em ferramentas de linha de comando. Antes de ativar os diagnósticos de arranque, tem de ser criada uma conta de armazenamento para guardar os registos de arranque. Se você habilitar o diagnóstico de inicialização no portal do Azure, uma conta de armazenamento será criada automaticamente para você.

    Se você não habilitou o diagnóstico de inicialização quando a VM foi criada, você sempre poderá habilitá-la mais tarde usando [CLI do Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)ou um [modelo de Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Habilite a coleção de dados de diagnóstico do sistema operacional convidado.** Ao criar uma VM, você tem a oportunidade na tela configurações para habilitar o diagnóstico do sistema operacional convidado. Quando você habilita a coleta de dados de diagnóstico, a [extensão IaaSDiagnostics para Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou a [extensão IaaSDiagnostics para Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) é adicionada à VM, o que permite que você colete dados de disco, CPU e memória adicionais.

    Usando os dados de diagnóstico coletados, você pode configurar o dimensionamento automático para suas VMs. Você também pode configurar [logs de Azure monitor](../articles/azure-monitor/platform/data-platform-logs.md) para armazenar os dados e configurar alertas para que você saiba quando o desempenho não está certo.

## <a name="alerts"></a>Alertas

Você pode criar [alertas](../articles/azure-monitor/platform/alerts-overview.md) com base em métricas de desempenho específicas. Exemplos dos problemas sobre os quais você pode ser alertado incluem quando o uso médio da CPU excede um determinado limite ou o espaço livre em disco disponível cai abaixo de um determinado valor. Os alertas podem ser configurados no [portal do Azure](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal), usando [modelos de Azure Resource Manager](../articles/azure-monitor/platform/alerts-metric-create-templates.md)ou [CLI do Azure](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

A [integridade do serviço do Azure](../articles/service-health/service-health-overview.md) fornece orientação e suporte personalizados quando problemas nos serviços do Azure afetam você e ajuda você a se preparar para a próxima manutenção planejada. A integridade do serviço do Azure alerta você e suas equipes usando notificações de destino e flexíveis.

## <a name="azure-resource-health"></a>Azure Resource Health

O [Azure Resource Health](../articles/service-health/resource-health-overview.md) ajuda você a diagnosticar e obter suporte quando um problema do Azure impacta seus recursos. Informa-o relativamente ao estado de funcionamento atual e passado dos seus recursos e ajuda-o a atenuar problemas. O Resource Health fornece suporte técnico quando precisar de ajuda com problemas de serviço do Azure.

## <a name="azure-activity-log"></a>Registo de Atividades do Azure

O [log de atividades do Azure](../articles/azure-monitor/platform/platform-logs-overview.md) é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. O log inclui um intervalo de dados, de Azure Resource Manager dados operacionais a atualizações em eventos de integridade do serviço. Você pode clicar em log de atividades no portal do Azure para exibir o log de sua VM.

Algumas das coisas que você pode fazer com o log de atividades incluem:

- Criar um [alerta em um evento do log de atividades](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Transmita-o para um hub de eventos](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como Power bi.
- Analise-o em Power BI usando o [pacote de conteúdo do Power bi](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Salve-o em uma conta de armazenamento para a](../articles/azure-monitor/platform/archive-activity-log.md) inspeção manual ou de arquivamento. Você pode especificar o tempo de retenção (em dias) usando o perfil de log.

Você também pode acessar os dados do log de atividades usando [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), o [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor)ou [monitorar as APIs REST](https://docs.microsoft.com/rest/api/monitor/).

[Os logs de recursos do Azure](../articles/azure-monitor/platform/platform-logs-overview.md) são logs emitidos por sua VM que fornecem dados avançados e frequentes sobre sua operação. Os logs de recursos diferem do log de atividades, fornecendo informações sobre as operações que foram executadas na VM.

Algumas das coisas que você pode fazer com os logs de diagnóstico incluem:

- [Salve-os em uma conta de armazenamento](../articles/azure-monitor/platform/archive-diagnostic-logs.md) para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as configurações de diagnóstico de recurso.
- [Transmita-os para os hubs de eventos](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como Power bi.
- Analise-os com [log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitorização avançada

Para obter visibilidade do aplicativo ou serviço com suporte dos conjuntos de dimensionamento de máquinas virtuais e VM do Azure, identificação de problemas com o sistema operacional convidado ou carga de trabalho em execução na VM para entender se ele está afetando a disponibilidade ou o desempenho do aplicativo, ou é um problema com o aplicativo, habilite [Azure monitor para VMs](../articles/azure-monitor/insights/vminsights-overview.md) e [Application insights](../articles/azure-monitor/app/app-insights-overview.md).

O Azure Monitor para VMs monitora suas máquinas virtuais (VM) do Azure em escala analisando o desempenho e a integridade de suas VMs Windows e Linux, incluindo os diferentes processos e dependências interconectadas em outros recursos e processos externos descobre. Ele inclui vários gráficos de tendências de desempenho para ajudar durante a investigação de problemas e avaliar a capacidade de suas VMs. O mapa de dependências mostra computadores monitorados e não monitorados, conexões de rede com falha e ativas entre processos e esses computadores e mostra gráficos de tendência com métricas de conexão de rede padrão. Combinado com Application Insights, você monitora seu aplicativo e captura a telemetria, como solicitações HTTP, exceções, etc., para que você possa correlacionar os problemas entre as VMs e seu aplicativo. Configure [Azure monitor alertas](../articles/azure-monitor/platform/alerts-overview.md) para alertá-lo sobre condições importantes detectadas por meio de monitoramento de dados coletados pelo Azure monitor para VMs.

## <a name="next-steps"></a>Passos seguintes

- Percorra as etapas em [monitorar uma máquina virtual do Windows com Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) ou [monitorar uma máquina virtual Linux com o CLI do Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Saiba mais sobre as práticas recomendadas em relação ao [monitoramento e ao diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
