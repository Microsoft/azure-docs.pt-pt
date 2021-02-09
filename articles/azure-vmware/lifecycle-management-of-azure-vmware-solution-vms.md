---
title: Gestão do ciclo de vida da Azure VMware Solution VMs
description: Aprenda a gerir todos os aspetos do ciclo de vida dos VMS da sua Solução VMware Azure com ferramentas nativas do Microsoft Azure.
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d8224a37e46b336ebf889fe1c075930f34f10ca4
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988534"
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
- Implemente facilmente o agente Log Analytics utilizando o suporte de extensão VM dos servidores ativados pelos servidores para novos E VMs existentes. 
- O seu espaço de trabalho Log Analytics no Azure Monitor permite a recolha de registos e a recolha do contador de desempenho utilizando o agente ou extensões do Log Analytics. Recolher dados e registos a um único ponto e apresentar esses dados a diferentes serviços nativos do Azure. 
- Os benefícios adicionais do Azure Monitor incluem: 
    - Monitorização sem emenda 
    - Melhor visibilidade da infraestrutura 
    - Notificações instantâneas 
    - Resolução automática 
    - Eficiência de custos 

## <a name="integrated-azure-monitoring-architecture"></a>Arquitetura integrada de monitorização do Azure

O diagrama seguinte mostra a arquitetura de monitorização integrada para VMs de Solução VMware Azure.

![Arquitetura integrada de monitorização do Azure](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>Antes de começar

Se é novo na Azure ou não conhece qualquer um dos serviços mencionados anteriormente, reveja os seguintes artigos:

- [Descrição geral da autenticação da conta de Automatização](../automation/automation-security-overview.md)
- [Projetando a sua implementação de registos de monitores Azure](../azure-monitor/platform/design-logs-deployment.md) e [monitor Azure](../azure-monitor/overview.md)
- [Plataformas](../security-center/security-center-planning-and-operations-guide.md) [de](../security-center/security-center-os-coverage.md) planeamento e apoio para o Centro de Segurança Azure
- [Ativar o Monitor Azure para visão geral dos VMs](../azure-monitor/insights/vminsights-enable-overview.md)
- [O que é a Azure Arc ativado servidores?](../azure-arc/servers/overview.md) e [o que é que o Arco Azure permitiu kubernetes?](../azure-arc/kubernetes/overview.md)
- [Descrição geral da Gestão de Atualizações](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Integração e implantação de serviços nativos do Azure

### <a name="enable-azure-update-management"></a>Ativar a gestão de atualização do Azure

A Azure Update Management na Azure Automation gere as atualizações do sistema operativo para as suas máquinas Windows e Linux num ambiente híbrido. Monitoriza a conformidade de correção e encaminha os alertas de desvio para o Azure Monitor para reparação. A Azure Update Management deve ligar-se ao seu espaço de trabalho Log Analytics para utilizar dados armazenados para avaliar o estado das atualizações nos seus VMs.

1.  Antes de poder adicionar Log Analytics à Azure Update Management, primeiro tem de [criar uma conta Azure Automation](../automation/automation-create-standalone-account.md). Se preferir criar a sua conta utilizando um modelo, consulte [Criar uma conta de Automação utilizando um modelo de Gestor de Recursos Azure.](../automation/quickstart-create-automation-account-template.md)

2. **Log Analytics workspace** permite a recolha de registos e a recolha do contador de desempenho usando o agente log Analytics ou extensões. Para criar o seu espaço de trabalho Log Analytics, consulte [Criar um espaço de trabalho Log Analytics no portal Azure.](../azure-monitor/learn/quick-create-workspace.md) Se preferir, também pode criar um espaço de trabalho através do [CLI,](../azure-monitor/learn/quick-create-workspace-cli.md) [PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md)ou [modelo Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md).

3. Para ativar a Gestão de Atualizações Azure para os seus VMs, consulte [Enable Update Management a partir de uma conta Dempaça](../automation/update-management/enable-from-automation-account.md). No processo, irá ligar o seu espaço de trabalho Log Analytics à sua conta de automação. 
 
4. Uma vez adicionadoS VMs à Azure Update Management, pode [implementar atualizações em VMs e rever os resultados](../automation/update-management/deploy-updates.md). 

### <a name="enable-azure-security-center"></a>Ativar o Centro de Segurança Azure

O Azure Security Center fornece uma proteção avançada de ameaças através das suas cargas de trabalho híbridas na nuvem e nas instalações. Avaliará a vulnerabilidade dos VMS da Solução VMware Azure e levantará alertas conforme necessário. Estes alertas de segurança podem ser encaminhados para o Azure Monitor para resolução.

O Centro de Segurança Azure não necessita de implantação. Para obter mais informações, consulte uma lista de [funcionalidades suportadas para máquinas virtuais.](../security-center/security-center-services.md)

1. Para adicionar VMs de Solução VMware Azure e VMs não-Azure ao Centro de Segurança, consulte [Quickstart: Configurar o Centro de Segurança Azure](../security-center/security-center-get-started.md). 

2. Depois de adicionar VMs ou VMs de Solução VMure de um ambiente não-Azure, ative o Azure Defender no Centro de Segurança. O Centro de Segurança avaliará os VMs para potenciais problemas de segurança. Também fornece recomendações no separador Visão Geral. Para mais informações, consulte [as recomendações de segurança no Centro de Segurança Azure.](../security-center/security-center-recommendations.md)

3. Pode definir políticas de segurança no Centro de Segurança Azure. Para obter informações sobre a configuração das suas políticas de segurança, consulte [trabalhar com políticas de segurança.](../security-center/tutorial-security-policy.md)

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>VMs a bordo para Azure Arc ativados servidores

A Azure Arc estende a gestão do Azure a qualquer infraestrutura, incluindo a Azure VMware Solution, no local ou outras plataformas em nuvem.

- Para obter informações sobre como ativar os servidores ativados do Azure Arc para vários Windows ou VMs Linux, consulte [as máquinas híbridas Connect para Azure à escala](../azure-arc/servers/onboard-service-principal.md).

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Aglomerados híbridos de Kubernetes a bordo com Arc habilitados Kubernetes

Pode anexar um cluster Kubernetes alojado no seu ambiente Azure VMware Solution utilizando Kubernetes ativados pelo Arco Azure. 

- Para obter mais informações, consulte [Criar um diretor de serviço de bordo ativado pelo Arco Azure.](../azure-arc/kubernetes/create-onboarding-service-principal.md)

### <a name="deploy-the-log-analytics-agent"></a>Implementar o agente Log Analytics

Os VMs de Solução VMware Azure podem ser monitorizados através do agente Log Analytics (também referido como Agente de Monitorização da Microsoft (MMA) ou agente OMS Linux). Já criou um espaço de trabalho Log Analytics, permitindo a Gestão de Atualização da Automação Azure.

- Implemente o agente Log Analytics utilizando [o suporte de extensão VM ativado pelos servidores Azure Arc](../azure-arc/servers/manage-vm-extensions.md).

### <a name="enable-azure-monitor"></a>Ativar o Monitor Azure

O Azure Monitor é uma solução abrangente para recolher, analisar e agir em telemetria a partir dos ambientes de nuvem e no local. Não requer nenhuma implantação. Com o Azure Monitor, pode monitorizar o desempenho do sistema operativo dos hóspedes e descobrir e mapear as dependências de aplicações para Azure VMware Solution ou VMs no local.

- O Azure Monitor permite-lhe recolher dados de diferentes fontes para monitorizar e analisar. Para obter mais informações, consulte [fontes de monitorização dos dados do Azure Monitor](../azure-monitor/platform/data-sources.md).

- Recolher diferentes tipos de dados para análise, visualização e alerta. Para obter mais informações, consulte [a plataforma de dados do Azure Monitor.](../azure-monitor/platform/data-platform.md)

- Para configurar o Azure Monitor com o seu espaço de trabalho Log Analytics, consulte [o espaço de trabalho Configure Log Analytics para O Monitor Azure para VMs](../azure-monitor/insights/vminsights-configure-workspace.md).

- Pode criar regras de alerta para identificar problemas no seu ambiente, como o uso elevado de recursos, patches em falta, espaço em disco baixo e batimentos cardíacos dos seus VMs. Também pode definir uma resposta automatizada a eventos detetados enviando um alerta para as ferramentas de Gestão de Serviços de TI (ITSM). A notificação de deteção de alerta também pode ser enviada por e-mail. Para criar tais regras, consulte:
    - [Criar, visualizar e gerir alertas métricos utilizando o Azure Monitor](../azure-monitor/platform/alerts-metric.md).
    - [Criar, visualizar e gerir alertas de registo utilizando o Azure Monitor](../azure-monitor/platform/alerts-log.md).
    - [Regras de](../azure-monitor/platform/alerts-action-rules.md) ação para definir ações e notificações automatizadas.
    - [Ligue o Azure às ferramentas ITSM utilizando o Conector de Gestão de Serviços de TI](../azure-monitor/platform/itsmc-overview.md).
    
 ## <a name="next-steps"></a>Passos seguintes

Agora que cobriu usando as ferramentas nativas da Azure para gerir os seus VMs de Solução VMware Azure durante todo o seu ciclo de vida, talvez queira saber:

- [Proteger os VMs da solução Azure VMware com o Centro de Segurança Azure](azure-security-integration.md).
- [Configuração do Servidor de Backup Azure para solução VMware Azure](set-up-backup-server-for-azure-vmware-solution.md).
- [Integrando a Azure VMware Solution num hub e arquitetura de fala.](concepts-hub-and-spoke.md)
