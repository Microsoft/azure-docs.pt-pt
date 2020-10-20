---
title: Gestão do ciclo de vida da Azure VMware Solution VMs
description: Aprenda a gerir todos os aspetos do ciclo de vida dos VMS da sua Solução VMware Azure com ferramentas nativas do Microsoft Azure.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 928a632a34dd31272c7c3bf92f6dc6dda97cb6cc
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216254"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Gestão do ciclo de vida da Azure VMware Solution VMs

As ferramentas nativas do Microsoft Azure permitem-lhe monitorizar e gerir as suas máquinas virtuais (VMs) no ambiente Azure. No entanto, também lhe permitem monitorizar e gerir os seus VMs na Azure VMware Solution e nos seus VMs no local. Nesta visão geral, vamos olhar para a arquitetura de monitorização integrada que o Azure oferece, e como você pode usar as suas ferramentas nativas para gerir os seus VMS de Solução VMware Azure durante todo o seu ciclo de vida.

## <a name="benefits"></a>Benefícios

- Os serviços nativos do Azure podem ser utilizados para gerir os seus VMs num ambiente híbrido (Azure, Azure VMware Solution e no local).
- Monitorização integrada e visibilidade do seu Azure, Azure VMware Solution e VMs no local.
- Com a Azure Update Management em Azure Automation, pode gerir as atualizações do sistema operativo tanto para as suas máquinas Windows como Linux. 
- O Centro de Segurança Azure fornece uma proteção avançada de ameaças, incluindo:
    - Monitorização da integridade do ficheiro
    - Alertas de segurança sem ficheiros
    - Avaliação do patch do sistema operativo
    - Avaliação de erros de segurança
    - Avaliação da proteção do ponto final 
- Implemente facilmente o Agente de Monitorização da Microsoft (MMA) utilizando o Azure ARC para novos VMs. 
- O seu espaço de trabalho Log Analytics no Azure Monitor permite a recolha de registos e a recolha de contadores de desempenho utilizando MMA ou extensões. Recolher dados e registos a um único ponto e apresentar esses dados a diferentes serviços nativos do Azure. 
- Os benefícios adicionais do Azure Monitor incluem: 
    - Monitorização sem emenda 
    - Melhor visibilidade da infraestrutura 
    - Notificações instantâneas 
    - Resolução automática 
    - Eficiência de custos 

## <a name="integrated-azure-monitoring-architecture"></a>Arquitetura integrada de monitorização do Azure

O diagrama seguinte mostra a arquitetura de monitorização integrada para VMs de Solução VMware Azure.

![Arquitetura integrada de monitorização do Azure](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Integração e implantação de serviços nativos do Azure

**A Azure ARC** estende a gestão do Azure a qualquer infraestrutura, incluindo a Azure VMware Solution, no local ou outras plataformas em nuvem. O Azure ARC pode ser implantado instalando um cluster Azure Kubernetes Service (AKS) no ambiente Azure VMware Solution. Para obter mais informações, consulte [um cluster Kubernetes ativado pelo Arco Azure.](../azure-arc/kubernetes/connect-cluster.md)

Os VMs de Solução VMware Azure podem ser monitorizados através do MMA (também referido como agente Log Analytics ou agente OMS Linux). O MMA pode ser instalado automaticamente quando os VMs são a provisionados através de fluxos de trabalho do ciclo de vida ARC VM. O MMA também pode ser instalado ao implementar VMs a partir de um modelo em vCenter; novamente, com VMs a provisionados através de fluxos de trabalho ARC. Todos os VMs Azure VMware Solution podem então ter MMA instalado e enviar registos para o espaço de trabalho Azure Log Analytics. Para obter mais informações, consulte [a visão geral do agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

**Log Analytics workspace** permite a recolha de registos e a recolha de contadores de desempenho usando MMA ou extensões. Para criar o seu espaço de trabalho Log Analytics, consulte [Criar um espaço de trabalho Log Analytics no portal Azure.](../azure-monitor/learn/quick-create-workspace.md)
- Para adicionar VMs do Windows ao seu espaço de trabalho de análise de registo, consulte [instalar o agente Log Analytics nos computadores Windows](../azure-monitor/platform/agent-windows.md).
- Para adicionar VMs Linux ao seu espaço de trabalho de análise de registo, consulte [instalar o agente Log Analytics nos computadores Linux](../azure-monitor/platform/agent-linux.md).

**A Azure Update Management** na Azure Automation gere as atualizações do sistema operativo para as suas máquinas Windows e Linux num ambiente híbrido. Monitoriza a conformidade de correção e encaminha os alertas de desvio para o Azure Monitor para reparação. A Azure Update Management deve ligar-se ao seu espaço de trabalho Log Analytics para utilizar dados armazenados para avaliar o estado das atualizações nos seus VMs.
- Para adicionar Log Analytics à Azure Update Management, primeiro precisa [de criar uma conta Azure Automation](../automation/automation-create-standalone-account.md).
- Para ligar o seu espaço de trabalho Log Analytics com a sua conta de automação, consulte [o espaço de trabalho do Log Analytics e a conta Demôm automação.](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)
- Para ativar a Gestão de Atualizações Azure para os seus VMs, consulte [Enable Update Management a partir de uma conta Dempaça](../automation/update-management/enable-from-automation-account.md).
- Uma vez adicionadoS VMs à Azure Update Management, pode [implementar atualizações em VMs e rever os resultados](../automation/update-management/deploy-updates.md). 

**O Azure Security Center** fornece uma proteção avançada de ameaças através das suas cargas de trabalho híbridas na nuvem e nas instalações. Avaliará a vulnerabilidade dos VMS da Solução VMware Azure e levantará alertas conforme necessário. Estes alertas de segurança podem ser encaminhados para o Azure Monitor para resolução.
- O Centro de Segurança Azure não necessita de implantação. Para obter mais informações, consulte uma lista de [funcionalidades suportadas para máquinas virtuais.](../security-center/security-center-services.md)
- Para adicionar VMs de Solução VMware Azure e VMs não-Azure ao Azure Security Center, consulte [computadores Windows a bordo do Azure Security Center](../security-center/quickstart-onboard-machines.md) e [computadores Linux a bordo do Azure Security Center](../security-center/quickstart-onboard-machines.md).
- Depois de adicionar VMs, o Azure Security Center analisa o estado de segurança dos recursos para identificar potenciais vulnerabilidades. Também fornece recomendações no separador Visão Geral. Para mais informações, consulte [as recomendações de segurança no Centro de Segurança Azure.](../security-center/security-center-recommendations.md)
- Pode definir políticas de segurança no Centro de Segurança Azure. Para obter informações sobre a configuração das suas políticas de segurança, consulte [trabalhar com políticas de segurança.](../security-center/tutorial-security-policy.md)

**O Azure Monitor** é uma solução abrangente para recolher, analisar e agir em telemetria a partir dos ambientes de nuvem e no local. Não requer nenhuma implantação.
- O Azure Monitor permite-lhe recolher dados de diferentes fontes para monitorizar e analisar. Para obter mais informações, consulte [fontes de monitorização dos dados do Azure Monitor](../azure-monitor/platform/data-sources.md).
- Também pode recolher diferentes tipos de dados para análise, visualização e alerta. Para obter mais informações, consulte [a plataforma de dados do Azure Monitor.](../azure-monitor/platform/data-platform.md)
- Para configurar o Azure Monitor com o seu espaço de trabalho Log Analytics, consulte [o espaço de trabalho Configure Log Analytics para O Monitor Azure para VMs](../azure-monitor/insights/vminsights-configure-workspace.md).
- Pode criar regras de alerta para identificar problemas no seu ambiente, como o uso elevado de recursos, patches em falta, espaço em disco baixo e batimentos cardíacos dos seus VMs. Também pode definir uma resposta automatizada a eventos detetados enviando um alerta para as ferramentas de Gestão de Serviços de TI (ITSM). A notificação de deteção de alerta também pode ser enviada por e-mail. Para criar tais regras, consulte:
    - [Criar, visualizar e gerir alertas métricos utilizando o Azure Monitor](../azure-monitor/platform/alerts-metric.md).
    - [Criar, visualizar e gerir alertas de registo utilizando o Azure Monitor](../azure-monitor/platform/alerts-log.md).
    - [Regras de](../azure-monitor/platform/alerts-action-rules.md) ação para definir ações e notificações automatizadas.
    - [Ligue o Azure às ferramentas ITSM utilizando o Conector de Gestão de Serviços de TI](../azure-monitor/platform/itsmc-overview.md).

**A plataforma Azure como um serviço (PaaS)** é um ambiente de desenvolvimento e implementação na nuvem, com recursos que lhe permitem fornecer aplicações baseadas na nuvem. Por exemplo, pode integrar a Base de Dados Azure SQL com os VMs da Solução VMware Azure. Os alertas SQL podem então ser correlacionados com os alertas VMware Solution VMware da Azure. Por exemplo, digamos que a perna da Base de Dados SQL da sua aplicação está dentro do Azure PAAS, e o nível de aplicação web da mesma aplicação está hospedado nos VMs da Solução VMware Azure. Os alertas de base de dados podem então ser correlacionados com os alertas de aplicações web. A resolução de problemas é simplificada com uma visibilidade única e integrada da Azure, Azure VMware Solution e VMs no local.