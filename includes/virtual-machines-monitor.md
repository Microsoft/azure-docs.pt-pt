---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: 11c9b2ea3ea054415f25f864651df28288aa0025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266845"
---
Você pode aproveitar muitas oportunidades para monitorar suas VMs coletando, exibindo e analisando dados de diagnóstico e de log. Para fazer o [monitoramento](../articles/azure-monitor/overview.md) simples de sua VM, você pode usar a tela de visão geral para a vm na portal do Azure. Você pode usar [extensões](../articles/virtual-machines/windows/extensions-features.md) para configurar o diagnóstico em suas VMs para coletar dados de métrica adicionais. Você também pode usar opções de monitoramento mais avançadas, como [Application insights](../articles/azure-monitor/app/app-insights-overview.md) e [log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnóstico e métricas 

Você pode configurar e monitorar a coleção de [dados de diagnóstico](https://docs.microsoft.com/cli/azure/vm/diagnostics) usando [métricas](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) no portal do Azure, as interfaces de programação de aplicativos CLI do Azure, Azure PowerShell e programação (APIs). Pode, por exemplo:

- **Observe as métricas básicas para a VM.** Na tela de visão geral do portal do Azure, as métricas básicas mostradas incluem uso de CPU, uso de rede, total de bytes de disco e operações de disco por segundo.

- **Habilite a coleta de diagnóstico de inicialização e exiba-o usando o portal do Azure.** Ao trazer sua própria imagem para o Azure ou até mesmo inicializar uma das imagens da plataforma, pode haver muitas razões pelas quais uma VM entra em um estado não inicializável. Você pode habilitar o diagnóstico de inicialização facilmente ao criar uma VM clicando em **habilitado** para diagnóstico de inicialização na seção monitoramento da tela Configurações.

    À medida que as VMs são inicializadas, o agente de diagnóstico de inicialização captura a saída de inicialização e a armazena no armazenamento do Azure. Estes dados podem ser utilizados para resolver problemas de arranque das VMs. O diagnóstico de inicialização não é habilitado automaticamente quando você cria uma VM com base em ferramentas de linha de comando. Antes de ativar os diagnósticos de arranque, tem de ser criada uma conta de armazenamento para guardar os registos de arranque. Se você habilitar o diagnóstico de inicialização no portal do Azure, uma conta de armazenamento será criada automaticamente para você.

    Se você não habilitou o diagnóstico de inicialização quando a VM foi criada, você sempre poderá habilitá-la mais tarde usando [CLI do Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)ou um [modelo de Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Habilite a coleção de dados de diagnóstico do sistema operacional convidado.** Ao criar uma VM, você tem a oportunidade na tela configurações para habilitar o diagnóstico do sistema operacional convidado. Quando você habilita a coleta de dados de diagnóstico, a [extensão IaaSDiagnostics para Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou a [extensão IaaSDiagnostics para Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) é adicionada à VM, o que permite que você colete dados de disco, CPU e memória adicionais.

    Usando os dados de diagnóstico coletados, você pode configurar o dimensionamento automático para suas VMs. Você também pode configurar logs para armazenar os dados e configurar alertas para que você saiba quando o desempenho não está bem certo.

## <a name="alerts"></a>Alertas

Você pode criar [alertas](../articles/azure-monitor/platform/alerts-overview.md) com base em métricas de desempenho específicas. Exemplos dos problemas sobre os quais você pode ser alertado incluem quando o uso médio da CPU excede um determinado limite ou o espaço livre em disco disponível cai abaixo de um determinado valor. Os alertas podem ser configurados no [portal do Azure](../articles/azure-monitor/platform/alerts-classic-portal.md), usando [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell)ou o [CLI do Azure](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

A [integridade do serviço do Azure](../articles/service-health/service-health-overview.md) fornece orientação e suporte personalizados quando problemas nos serviços do Azure afetam você e ajuda você a se preparar para a próxima manutenção planejada. A integridade do serviço do Azure alerta você e suas equipes usando notificações de destino e flexíveis.

## <a name="azure-resource-health"></a>Azure Resource Health

O [Azure Resource Health](../articles/service-health/resource-health-overview.md) ajuda você a diagnosticar e obter suporte quando um problema do Azure impacta seus recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

## <a name="azure-activity-log"></a>Registo de atividades do Azure

O [log de atividades do Azure](../articles/azure-monitor/platform/activity-logs-overview.md) é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. O log inclui um intervalo de dados, de Azure Resource Manager dados operacionais a atualizações em eventos de integridade do serviço. Você pode clicar em log de atividades no portal do Azure para exibir o log de sua VM.

Algumas das coisas que você pode fazer com o log de atividades incluem:

- Criar um [alerta em um evento do log de atividades](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Transmita-o para um hub de eventos](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o PowerBI.
- Analise-o no PowerBI usando o [pacote de conteúdo do powerbi](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Salve-o em uma conta de armazenamento para a](../articles/azure-monitor/platform/archive-activity-log.md) inspeção manual ou de arquivamento. Você pode especificar o tempo de retenção (em dias) usando o perfil de log.

Você também pode acessar os dados do log de atividades usando [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), o [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor)ou [monitorar as APIs REST](https://docs.microsoft.com/rest/api/monitor/).

[Os logs de recursos do Azure](../articles/azure-monitor/platform/resource-logs-overview.md) são logs emitidos por sua VM que fornecem dados avançados e frequentes sobre sua operação. Os logs de recursos diferem do log de atividades, fornecendo informações sobre as operações que foram executadas na VM.

Algumas das coisas que você pode fazer com os logs de diagnóstico incluem:

- [Salve-os em uma conta de armazenamento](../articles/azure-monitor/platform/archive-diagnostic-logs.md) para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as configurações de diagnóstico de recurso.
- [Transmita-os para os hubs de eventos](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o PowerBI.
- Analise-os com [log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitorização avançada

- [Azure monitor](../articles/azure-monitor/overview.md) é um serviço que monitora seus ambientes locais e de nuvem para manter a disponibilidade e o desempenho. Ele fornece uma solução abrangente para coletar, analisar e agir na telemetria de seus ambientes locais e na nuvem. Ajuda a compreender o desempenho das suas aplicações e identifica proativamente os problemas que as afetam e os recursos de que dependem. Você pode instalar uma extensão em uma [VM do Linux](../articles/virtual-machines/linux/extensions-oms.md) ou em uma [VM do Windows](../articles/virtual-machines/windows/extensions-oms.md) que instala o agente de log Analytics para coletar dados de log e armazenar em um espaço de trabalho log Analytics.

    Para VMs Windows e Linux, o método recomendado para coletar logs é instalar o agente de Log Analytics. A maneira mais fácil de instalar o agente de Log Analytics em uma VM é por meio da [extensão de vm log Analytics](../articles/log-analytics/log-analytics-azure-vm-extension.md). A utilização da extensão simplifica o processo de instalação e configura automaticamente o agente para enviar dados para a área de trabalho do Log Analytics que especificar. O agente é também atualizado automaticamente e assegura que tem as funcionalidades e correções mais recentes.

- O [observador de rede](../articles/network-watcher/network-watcher-monitoring-overview.md) permite que você monitore sua VM e seus recursos associados à medida que eles se relacionam com a rede em que estão. Você pode instalar a extensão do agente do observador de rede em uma [VM do Linux](../articles/virtual-machines/linux/extensions-nwa.md) ou em uma [VM do Windows](../articles/virtual-machines/windows/extensions-nwa.md).

- O [Azure monitor para VMs](../articles/azure-monitor/insights/vminsights-overview.md) monitora suas máquinas virtuais (VM) do Azure em escala analisando o desempenho e a integridade de suas VMs Windows e Linux, incluindo seus processos diferentes e dependências interconectadas em outros recursos e externos processar. 

## <a name="next-steps"></a>Passos seguintes
- Percorra as etapas em [monitorar uma máquina virtual do Windows com Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) ou [monitorar uma máquina virtual Linux com o CLI do Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Saiba mais sobre as práticas recomendadas em relação ao [monitoramento e ao diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
