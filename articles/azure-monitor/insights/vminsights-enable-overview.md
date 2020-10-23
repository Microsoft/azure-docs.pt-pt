---
title: Ativar o Monitor Azure para visão geral dos VMs
description: Aprenda a implementar e configurar o Monitor Azure para VMs. Descubra os requisitos do sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: b903fda9a64233b4906941167ca7fd5819a0316b
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164487"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Ativar o Monitor Azure para visão geral dos VMs

Este artigo fornece uma visão geral das opções disponíveis para permitir ao Monitor Azure para os VM monitorizar a saúde e o desempenho dos seguintes:

- Máquinas virtuais do Azure 
- Conjuntos de dimensionamento de máquinas virtuais do Azure
- Máquinas virtuais híbridas ligadas ao Arco Azure
- Máquinas virtuais no local
- Máquinas virtuais hospedadas em outro ambiente de nuvem.  

Para configurar o Azure Monitor para VMs:

* Ativar uma única máquina Azure VM, Azure VMSS ou Azure Arc selecionando **Insights** diretamente do seu menu no portal Azure.
* Ativar várias máquinas Azure VMSS, Azure VMSS ou Azure Arc utilizando a Política Azure. Este método garante que, nos conjuntos de VM e de escala existentes e novos, as dependências necessárias sejam instaladas e devidamente configuradas. VMs e conjuntos de escala não conformes são relatados, para que você possa decidir se ativar e remediar.
* Ativar várias VMs Azure, VMs Azure Arc, Azure VMSS ou Azure Arc através de uma subscrição ou grupo de recursos especificados utilizando o PowerShell.
* Ativar o Azure Monitor para VMs para monitorizar VMs ou computadores físicos alojados na sua rede corporativa ou noutro ambiente em nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende a informação nas seguintes secções. 

>[!NOTE]
>As seguintes informações descritas nesta secção também são aplicáveis à [solução Mapa de Serviço.](service-map.md)  

### <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics

O Azure Monitor for VMs suporta um espaço de trabalho Log Analytics nas seguintes regiões:

- África
  - Norte da África do Sul
- Ásia-Pacífico
  - Ásia Leste
  - Sudeste Asiático
- Austrália
  - Leste da Austrália
  - Austrália Sudeste
- Azure Government
  - Gov Az dos EUA
  - Gov Va dos EUA
- Canadá
  - Canadá Central
- Europa
  - Europa do Norte
  - Europa Ocidental
- Índia
  - Índia Central
- Japão
  - Leste do Japão
- Reino Unido
  - Sul do Reino Unido
- Estados Unidos
  - E.U.A. Central
  - E.U.A. Leste
  - E.U.A. Leste 2
  - E.U.A. Centro-Norte
  - E.U.A. Centro-Sul
  - E.U.A. Centro-Oeste
  - E.U.A. Oeste
  - E.U.A. Oeste 2


>[!NOTE]
>Pode monitorizar os VMs do Azure em qualquer região. Os VMs em si não se limitam às regiões apoiadas pelo espaço de trabalho Log Analytics.
>

Se não tiver um espaço de trabalho log analytics, pode criar um utilizando um dos recursos:
* [CLI do Azure](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Portal do Azure](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

- Máquina virtual do Azure
- Conjunto de escala de máquina virtual Azure
- Máquina virtual híbrida ligada ao Arco Azure

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

O Azure Monitor para VMs suporta qualquer sistema operativo que suporte o agente Log Analytics e o agente De dependência. Consulte [a visão geral dos agentes do Monitor Azure ](../platform/agents-overview.md#supported-operating-systems) para obter uma lista completa.

Consulte a seguinte lista de considerações sobre o apoio do Linux ao agente de dependência que suporta o Monitor Azure para VMs:

- Apenas as versões de kernel padrão e SMP Linux são suportadas.
- As versões não padrão do kernel, tais como a Extensão de Endereço Físico (PAE) e xen, não são suportadas para qualquer distribuição do Linux. Por exemplo, um sistema com a cadeia de desbloqueio de *2.6.16.21-0.8-xen* não é suportado.
- Os núcleos personalizados, incluindo as reparações de núcleos padrão, não são suportados.
- Para distros de Debian que não a versão 9.4, a funcionalidade do mapa não é suportada, e a funcionalidade Performance está disponível apenas a partir do menu Azure Monitor. Não está disponível diretamente a partir do painel esquerdo do Azure VM.
- O núcleo CentOSPlus é suportado.
- O núcleo Linux deve ser remendado para a vulnerabilidade do Espectro. Consulte o seu fornecedor de distribuição Linux para mais detalhes.



## <a name="supported-azure-arc-machines"></a>Máquinas Azure Arc suportadas
O Azure Monitor para VMs está disponível para servidores ativados aZure Arc em regiões onde o serviço de extensão Arc está disponível. Deve estar a executar a versão 0.9 ou superior ao do Agente Arc.

| Origem ligada | Suportado | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Juntamente com o [agente Log Analytics para windows,](../platform/log-analytics-agent.md)os agentes windows precisam do agente Dependency. Para obter mais informações, consulte [os sistemas operativos suportados.](../platform/agents-overview.md#supported-operating-systems) |
| Agentes do Linux | Sim | Juntamente com o [agente Log Analytics para o Linux,](../platform/log-analytics-agent.md)os agentes Linux precisam do agente Desadependição. Para obter mais informações, consulte [os sistemas operativos suportados.](#supported-operating-systems) |
| Grupo de gestão do System Center Operations Manager | Não | |

## <a name="agents"></a>Agentes
O Azure Monitor para VMs requer que os dois agentes seguintes sejam instalados em cada máquina virtual ou escala de máquina virtual definida para ser monitorizada. Instalar estes agentes e ligá-los ao espaço de trabalho é o único requisito para embarcar no recurso.

- [Agente Log Analytics](../platform/log-analytics-agent.md). Recolhe eventos e dados de desempenho a partir da máquina virtual ou conjunto de escala de máquina virtual e entrega-os no espaço de trabalho Log Analytics. Os métodos de implementação do agente Log Analytics nos recursos Azure utilizam a extensão VM para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md).
- Agente de dependência. Recolhe dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos, que é usado pela [funcionalidade Mapa no Monitor Azure para VMs](vminsights-maps.md). O agente de dependência conta com o agente Log Analytics para entregar os seus dados ao Azure Monitor. Os métodos de implantação do agente de dependência dos recursos Azure utilizam a extensão VM para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> O agente Log Analytics é o mesmo agente utilizado pelo Gestor de Operações do Centro de Sistema. O Azure Monitor para VMs pode monitorizar agentes que também são monitorizados pelo Gestor de Operações se estiverem diretamente ligados, e instala o agente Dependency neles. Os agentes ligados ao Azure Monitor através de uma [ligação de grupo de gestão](../tform/../platform/om-agents.md) não podem ser monitorizados pelo Azure Monitor para VMs.

Seguem-se múltiplos métodos para a implantação destes agentes. 

| Método | Descrição |
|:---|:---|
| [Portal do Azure](./vminsights-enable-portal.md) | Instale ambos os agentes numa única máquina virtual, num conjunto de balanças de máquinas virtuais ou em máquinas virtuais híbridas ligadas ao Arco Azure. |
| [Modelos do Resource Manager](vminsights-enable-powershell.md) | Instale ambos os agentes utilizando qualquer um dos métodos suportados para implementar um modelo de Gestor de Recursos, incluindo CLI e PowerShell. |
| [Azure Policy](./vminsights-enable-policy.md) | Atribua a iniciativa Azure Policy para instalar automaticamente os agentes quando for criada uma máquina virtual ou um conjunto de escala de máquina virtual. |
| [Instalação manual](./vminsights-enable-hybrid.md) | Instale os agentes no sistema operativo dos hóspedes em computadores alojados fora do Azure, incluindo no seu datacenter ou noutros ambientes em nuvem. |




## <a name="management-packs"></a>Pacotes de gestão
Quando um espaço de trabalho do Log Analytics é configurado para O Monitor Azure para VMs, dois pacotes de gestão são encaminhados para todos os computadores Windows ligados a esse espaço de trabalho. Os pacotes de gestão são *nomeados Microsoft.IntelligencePacks.ApplicationDependencyMonitor* e *Microsoft.IntelligencePacks.VMInsights* e são escritos para *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs \* . 

A fonte de dados utilizada pelo pacote de gestão *ApplicationDependencyMonitor* é **%Ficheiros do Programa%\Microsoft Monitoring Agent\Agente\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. A fonte de dados utilizada pelo pacote de gestão *VMInsights* é *de ficheiros %\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Dados de diagnóstico e utilização

A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço Azure Monitor. A Microsoft utiliza estes dados para melhorar a qualidade, segurança e integridade do serviço. 

Para fornecer capacidades precisas e eficientes de resolução de problemas, a funcionalidade Mapa inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operativo e versão, endereço IP, nome DNS e nome da estação de trabalho. A Microsoft não recolhe nomes, endereços ou outras informações de contacto.

Para obter mais informações sobre a recolha e utilização de dados, consulte a [Declaração de Privacidade dos Serviços Online da Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos seguintes

Para aprender a utilizar a função de monitorização de desempenho, consulte [o View Azure Monitor para o desempenho dos VMs](vminsights-performance.md). Para ver as dependências de aplicações descobertas, consulte [o Monitor do Azure para o Mapa de VMs](vminsights-maps.md).
