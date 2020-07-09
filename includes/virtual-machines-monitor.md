---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: feea9696316723a2750be6fc1e13001224320324
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81865608"
---
Com o crescimento significativo dos VM hospedados no Azure, é importante identificar questões de desempenho e saúde que impactem aplicações e serviços de infraestrutura que apoiam. A monitorização básica é entregue por padrão com o Azure pelos tipos métricos de utilização do CPU, utilização do disco, utilização da memória e tráfego de rede recolhido pelo hipervisor do hospedeiro. Dados métricos e de registo adicionais podem ser recolhidos usando [extensões](../articles/virtual-machines/windows/extensions-features.md) para configurar diagnósticos nos seus VMs do sistema operativo convidado.

Para detetar e ajudar a diagnosticar problemas de desempenho e saúde com o sistema operativo convidado, os componentes de aplicação web .NET ou Java que funcionam dentro do VM, o Azure Monitor fornece monitorização centralizada com funcionalidades abrangentes, como O Monitor Azure para VMs e Insights de Aplicações.

## <a name="diagnostics-and-metrics"></a>Diagnósticos e métricas 

Pode configurar e monitorizar a recolha de [dados](https://docs.microsoft.com/cli/azure/vm/diagnostics) de diagnóstico utilizando [métricas](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) no portal Azure, no Azure CLI, no Azure PowerShell e na programação de Interfaces de Programação de Aplicações (APIs). Pode, por exemplo:

- **Observe as métricas básicas para o VM.** No ecrã geral do portal Azure, as métricas básicas apresentadas incluem a utilização do CPU, a utilização da rede, o total de bytes de disco e as operações de disco por segundo.

- **Ative a recolha de diagnósticos de arranque e vê-lo utilizando o portal Azure.** Ao trazer a sua própria imagem para Azure ou até mesmo iniciar uma das imagens da plataforma, pode haver muitas razões pelas quais um VM entra num estado não-inicial. Pode ativar facilmente os diagnósticos de arranque quando cria um VM clicando **ativado** para diagnósticos de arranque sob a secção de monitorização do ecrã Definições.

    Como arranque de VMs, o agente de diagnóstico de arranque captura a saída de arranque e armazena-a no armazenamento Azure. Estes dados podem ser utilizados para resolver problemas de arranque das VMs. Os diagnósticos de arranque não são ativados automaticamente quando cria um VM a partir de ferramentas de linha de comando. Antes de ativar os diagnósticos de arranque, tem de ser criada uma conta de armazenamento para guardar os registos de arranque. Se ativar o diagnóstico de arranque no portal Azure, é criada automaticamente uma conta de armazenamento para si.

    Se não for ativar os diagnósticos de arranque quando o VM foi criado, pode sempre ative-lo mais tarde utilizando [o Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)ou um [modelo de Gestor de Recursos Azure](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Ativar a recolha de dados de diagnóstico sonoro de hóspedes.** Quando cria um VM, tem a oportunidade no ecrã de definições para ativar os diagnósticos de SO dos hóspedes. Quando ativar a recolha de dados de diagnóstico, a [extensão iaaSDiagnostics para Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou a [extensão iaaSDiagnostics para Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) é adicionada ao VM, que lhe permite recolher dados adicionais de disco, CPU e memória.

    Utilizando os dados de diagnóstico recolhidos, pode configurar a autoscalagem para os seus VMs. Também pode configurar [registos do Monitor Azure](../articles/azure-monitor/platform/data-platform-logs.md) para armazenar os dados e configurar alertas para informá-lo quando o desempenho não está certo.

## <a name="alerts"></a>Alertas

Pode criar [alertas baseados](../articles/azure-monitor/platform/alerts-overview.md) em métricas de desempenho específicas. Exemplos dos problemas sobre os quais pode ser alertado incluem quando a utilização média do CPU excede um determinado limiar, ou o espaço de disco gratuito disponível cai abaixo de uma determinada quantidade. Os alertas podem ser configurados no [portal Azure,](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)utilizando [modelos do Gestor de Recursos Azure,](../articles/azure-monitor/platform/alerts-metric-create-templates.md)ou [Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[O Azure Service Health](../articles/service-health/service-health-overview.md) fornece orientação e suporte personalizados quando problemas nos serviços Azure o afetam, e ajuda-o a preparar-se para a próxima manutenção planeada. O Serviço Azure Health alerta-o a si e às suas equipas utilizando notificações direcionadas e flexíveis.

## <a name="azure-resource-health"></a>Azure Resource Health

[A saúde do Recurso Azure](../articles/service-health/resource-health-overview.md) ajuda-o a diagnosticar e a obter apoio quando um problema do Azure impacta os seus recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

## <a name="azure-activity-log"></a>Registo de Atividades do Azure

O [Azure Activity Log](../articles/azure-monitor/platform/platform-logs-overview.md) é um registo de subscrição que fornece informações sobre eventos de nível de subscrição que ocorreram em Azure. O registo inclui uma gama de dados, desde dados operacionais do Azure Resource Manager até atualizações sobre eventos de Saúde de Serviço. Pode clicar em 'Registar atividade' no portal Azure para visualizar o registo para o seu VM.

Algumas das coisas que pode fazer com o registo de atividades incluem:

- Criar um [alerta num evento de Registo de Atividades.](../articles/azure-monitor/platform/platform-logs-overview.md)
- [Transmita-o para um Centro de Eventos](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o Power BI.
- Analise-o no Power BI utilizando o [pacote de conteúdo power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Guarde-o numa conta de armazenamento](../articles/azure-monitor/platform/archive-activity-log.md) para inspeção de arquivo ou manual. Pode especificar o tempo de retenção (em dias) utilizando o Perfil de Registo.

Também pode aceder aos dados de registo de atividade utilizando [a Azure PowerShell,](https://docs.microsoft.com/powershell/module/azurerm.insights/)o [Azure CLI,](https://docs.microsoft.com/cli/azure/monitor)ou [Monitor REST APIs](https://docs.microsoft.com/rest/api/monitor/).

[Os Registos de Recursos Azure](../articles/azure-monitor/platform/platform-logs-overview.md) são registos emitidos pelo seu VM que fornecem dados ricos e frequentes sobre o seu funcionamento. Os registos de recursos diferem do registo de atividade, fornecendo informações sobre as operações realizadas dentro do VM.

Algumas das coisas que pode fazer com registos de diagnóstico incluem:

- [Guarde-os numa conta de armazenamento](../articles/azure-monitor/platform/archive-diagnostic-logs.md) para auditoria ou inspeção manual. Pode especificar o tempo de retenção (em dias) utilizando definições de diagnóstico de recursos.
- [Transmita-os para Os Centros de Eventos](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o Power BI.
- Analise-os com [Log Analytics.](../articles/log-analytics/log-analytics-azure-storage.md)

## <a name="advanced-monitoring"></a>Monitorização avançada

Para visibilidade da aplicação ou serviço suportado pelo Azure VM e conjuntos de escala de máquinas virtuais, identificação de problemas com o SO convidado ou carga de trabalho em execução no VM para entender se está a afetar a disponibilidade ou desempenho da aplicação, ou se é um problema com a aplicação, ativar tanto o [Monitor Azure para VMs](../articles/azure-monitor/insights/vminsights-overview.md) como [insights de aplicação.](../articles/azure-monitor/app/app-insights-overview.md)

O Azure Monitor for VMs monitoriza as suas máquinas virtuais Azure (VM) em escala, analisando o desempenho e a saúde dos seus VMs Windows e Linux, incluindo os diferentes processos e dependências interligadas de outros recursos e processos externos que descobre. Inclui vários gráficos de desempenho de tendências para ajudar durante a investigação de problemas e avaliar a capacidade dos seus VMs. O mapa de dependência mostra máquinas monitorizadas e não monitorizadas, ligações de rede falhadas e ativas entre processos e estas máquinas, e mostra gráficos de tendências com métricas padrão de ligação de rede. Combinado com o Application Insights, monitoriza a sua aplicação e captura telemetria, como pedidos HTTP, exceções, etc. para que possa correlacionar problemas entre os VMs e a sua aplicação. Configure [Azure Monitor alerta](../articles/azure-monitor/platform/alerts-overview.md) para alertá-lo sobre condições importantes detetadas a partir da monitorização de dados recolhidos pelo Azure Monitor para VMs.

## <a name="next-steps"></a>Próximos passos

- Caminhe pelos degraus no [Monitor de uma Máquina Virtual do Windows com Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) ou [Monitorize uma Máquina Virtual Linux com o Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Saiba mais sobre as melhores práticas em torno [da monitorização e diagnósticos.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)
