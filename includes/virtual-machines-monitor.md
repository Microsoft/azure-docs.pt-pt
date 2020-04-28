---
title: incluir ficheiro
description: incluir ficheiro
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: feea9696316723a2750be6fc1e13001224320324
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865608"
---
Com o crescimento significativo dos VMs alojados no Azure, é importante identificar problemas de desempenho e saúde que impactam aplicações e serviços de infraestrutura que apoiam. A monitorização básica é entregue por padrão com o Azure pelos tipos métricos de utilização do CPU, utilização do disco, utilização da memória e tráfego de rede recolhido pelo hipervisor hospedeiro. Dados métricos e de registo adicionais podem ser recolhidos usando [extensões](../articles/virtual-machines/windows/extensions-features.md) para configurar diagnósticos nos seus VMs a partir do sistema operativo convidado.

Para detetar e ajudar a diagnosticar problemas de desempenho e saúde com o sistema operativo dos hóspedes, os componentes da aplicação web baseados em .NET ou Java que funcionam dentro do VM, o Azure Monitor fornece monitorização centralizada com funcionalidades abrangentes como o Monitor Azure para VMs e Insights de Aplicação.

## <a name="diagnostics-and-metrics"></a>Diagnósticos e métricas 

Pode configurar e monitorizar a recolha de dados de [diagnóstico](https://docs.microsoft.com/cli/azure/vm/diagnostics) utilizando [métricas](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) no portal Azure, no Azure CLI, no Azure PowerShell e na programação de Interfaces de Programação de Aplicações (APIs). Pode, por exemplo:

- **Observe as métricas básicas para o VM.** No ecrã geral do portal Azure, as métricas básicas mostradas incluem a utilização do CPU, o uso da rede, o total de bytes de disco e as operações de disco por segundo.

- **Ative a recolha de diagnósticos de botas e veja-o utilizando o portal Azure.** Ao trazer a sua própria imagem para o Azure ou até mesmo arrancar uma das imagens da plataforma, pode haver muitas razões pelas quais um VM entra num estado não-sabotável. Pode ativar facilmente o diagnóstico da bota quando criar um VM clicando **ativado** para diagnósticos de arranque sob a secção de monitorização do ecrã Definições.

    À medida que os VMs arrancam, o agente de diagnóstico de botas captura a saída de botas e armazena-a no armazenamento Azure. Estes dados podem ser utilizados para resolver problemas de arranque das VMs. Os diagnósticos de arranque não são ativados automaticamente quando cria um VM a partir de ferramentas de linha de comando. Antes de ativar os diagnósticos de arranque, tem de ser criada uma conta de armazenamento para guardar os registos de arranque. Se ativar diagnósticos de arranque no portal Azure, é criada automaticamente uma conta de armazenamento para si.

    Se não tiver ativado o diagnóstico de arranque quando o VM foi criado, pode sempre capacitá-lo mais tarde utilizando [o Azure CLI,](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics) [O Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)ou um modelo de Gestor de [Recursos Azure](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Ativar a recolha de dados de diagnóstico solista sinuoso convidado.** Ao criar um VM, tem a oportunidade no ecrã de definições para ativar os diagnósticos de OS do hóspede. Quando permite a recolha de dados de diagnóstico, a [extensão IaaSDiagnostics para O Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou a [extensão IaaSDiagnostics para Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) é adicionada ao VM, o que lhe permite recolher dados adicionais de disco, CPU e memória.

    Utilizando os dados de diagnóstico recolhidos, pode configurar a autoscalcificação para os seus VMs. Também pode configurar registos do [Monitor Azure](../articles/azure-monitor/platform/data-platform-logs.md) para armazenar os dados e configurar alertas para que saiba quando o desempenho não está certo.

## <a name="alerts"></a>Alertas

Pode criar alertas com base em [métricas](../articles/azure-monitor/platform/alerts-overview.md) de desempenho específicas. Exemplos dos problemas sobre os quais pode ser alertado incluem quando o uso médio de CPU excede um determinado limiar, ou o espaço de disco livre disponível cai abaixo de um determinado valor. Os alertas podem ser configurados no [portal Azure,](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)utilizando modelos de [Gestor de Recursos Azure,](../articles/azure-monitor/platform/alerts-metric-create-templates.md)ou [Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[A Azure Service Health](../articles/service-health/service-health-overview.md) fornece orientação e apoio personalizados quando problemas nos serviços do Azure o afetam, e ajuda-o a preparar-se para a próxima manutenção planeada. A Azure Service Health alerta-o a si e às suas equipas através de notificações direcionadas e flexíveis.

## <a name="azure-resource-health"></a>Azure Resource Health

[A saúde](../articles/service-health/resource-health-overview.md) da Azure Resource ajuda-o a diagnosticar e a obter apoio quando um problema do Azure afeta os seus recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

## <a name="azure-activity-log"></a>Registo de Atividades do Azure

O [Registo de Atividades do Azure](../articles/azure-monitor/platform/platform-logs-overview.md) é um registo de subscrição que fornece informações sobre eventos de nível de subscrição que ocorreram no Azure. O registo inclui uma série de dados, desde dados operacionais do Gestor de Recursos do Azure até atualizações sobre eventos de Saúde de Serviço. Pode clicar em 'Iniciar sessão de atividades' no portal Azure para visualizar o registo do seu VM.

Algumas das coisas que pode fazer com o registo de atividade incluem:

- Criar um [alerta sobre um evento de Registo](../articles/azure-monitor/platform/platform-logs-overview.md)de Atividades.
- [Transmita-o para um Hub](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) de Eventos para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o Power BI.
- Analise-o no Power BI utilizando o pacote de [conteúdo Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Guarde-o para uma conta](../articles/azure-monitor/platform/archive-activity-log.md) de armazenamento para inspeção de arquivo ou manual. Pode especificar o tempo de retenção (em dias) utilizando o Perfil de Registo.

Também pode aceder aos dados de registo de atividade utilizando o [Azure PowerShell,](https://docs.microsoft.com/powershell/module/azurerm.insights/)o [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)ou [o Monitor REST APIs](https://docs.microsoft.com/rest/api/monitor/).

[Os Registos](../articles/azure-monitor/platform/platform-logs-overview.md) de Recursos Azure são registos emitidos pelo seu VM que fornecem dados ricos e frequentes sobre o seu funcionamento. Os registos de recursos diferem do registo de atividade, fornecendo informações sobre as operações que foram realizadas dentro do VM.

Algumas das coisas que pode fazer com registos de diagnóstico incluem:

- [Guarde-os para uma conta](../articles/azure-monitor/platform/archive-diagnostic-logs.md) de armazenamento para auditoria ou inspeção manual. Pode especificar o tempo de retenção (em dias) utilizando as Definições de Diagnóstico de Recursos.
- [Transmita-os para Centros](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) de Eventos para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o Power BI.
- Analise-os com [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitorização avançada

Para visibilidade da aplicação ou serviço suportado pelo VM Azure e conjuntos de escala de máquinavirtual, identificação de problemas com o oss o de hóspedes ou carga de trabalho em execução no VM para perceber se está a afetar a disponibilidade ou o desempenho da aplicação, ou se é um problema com a aplicação, permitir tanto o [Monitor Azure para VMs](../articles/azure-monitor/insights/vminsights-overview.md) como [insights de aplicação.](../articles/azure-monitor/app/app-insights-overview.md)

O Monitor Azure para VMs monitoriza as suas máquinas virtuais Azure (VM) em escala, analisando o desempenho e a saúde dos seus VMs Windows e Linux, incluindo os diferentes processos e dependências interligadas de outros recursos e processos externos que descobre. Inclui vários gráficos de desempenho de tendências para ajudar durante a investigação de problemas e avaliar a capacidade dos seus VMs. O mapa de dependência mostra máquinas monitorizadas e não monitorizadas, ligações de rede falhadas e ativas entre processos e estas máquinas, e mostra gráficos de tendências com métricas padrão de ligação de rede. Combinado com os Insights de Aplicação, monitoriza a sua aplicação e captura telemetria, tais como pedidos HTTP, exceções, etc. para que possa correlacionar problemas entre os VMs e a sua aplicação. O Configure [Azure Monitor alerta](../articles/azure-monitor/platform/alerts-overview.md) para alertá-lo sobre as condições importantes detetadas a partir da monitorização dos dados recolhidos pelo Monitor Azure para VMs.

## <a name="next-steps"></a>Passos seguintes

- Caminhe pelos degraus do [Monitor uma máquina virtual do Windows com Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) ou [Monitorize uma máquina virtual Linux com o Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Saiba mais sobre as melhores práticas em torno da [Monitorização e diagnósticos.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)
