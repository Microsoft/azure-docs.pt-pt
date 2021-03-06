---
title: Ativar a visão geral dos conhecimentos do VM
description: Saiba como implementar e configurar insights VM. Descubra os requisitos do sistema.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: bb2e12082b80c397eec27409b1177379a92fdd7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102634163"
---
# <a name="enable-vm-insights-overview"></a>Ativar a visão geral dos conhecimentos do VM

Este artigo fornece uma visão geral das opções disponíveis para permitir que os conhecimentos de VM monitorizem a saúde e o desempenho dos seguintes:

- Máquinas virtuais do Azure 
- Conjuntos de dimensionamento de máquinas virtuais do Azure
- Máquinas virtuais híbridas ligadas ao Arco Azure
- Máquinas virtuais no local
- Máquinas virtuais hospedadas em outro ambiente de nuvem.  

Para configurar insights VM:

* Ativar uma única máquina virtual Azure, conjunto de balanças de máquinas virtuais Azure ou máquina Azure Arc selecionando **Insights** diretamente do seu menu no portal Azure.
* Ativar várias máquinas virtuais Azure, máquinas virtuais Azure ou máquinas Azure Arc utilizando a Azure Policy. Este método garante que, nos conjuntos de VM e de escala existentes e novos, as dependências necessárias sejam instaladas e devidamente configuradas. São reportadas máquinas virtuais e conjuntos de escalas não conformes, para que possa decidir se as permite e as remedia.
* Ativar várias máquinas virtuais Azure, máquinas virtuais Azure Arc, conjuntos de balanças de máquinas virtuais Azure ou máquinas Azure Arc através de uma subscrição ou grupo de recursos especificados utilizando o PowerShell.
* Ative os conhecimentos de VM para monitorizar VMs ou computadores físicos alojados na sua rede corporativa ou em outro ambiente em nuvem.

## <a name="supported-machines"></a>Máquinas suportadas
Os conhecimentos VM suportam as seguintes máquinas:

- Máquina virtual do Azure
- Conjunto de escala de máquina virtual Azure
- Máquina virtual híbrida ligada ao Arco Azure


## <a name="supported-azure-arc-machines"></a>Máquinas Azure Arc suportadas
Os insights VM estão disponíveis para servidores ativados a Azure Arc em regiões onde o serviço de extensão Arc está disponível. Deve estar a executar a versão 0.9 ou superior ao do Agente Arc.

| Origem ligada | Suportado | Description |
|:--|:--|:--|
| Agentes do Windows | Yes | Juntamente com o [agente Log Analytics para windows,](../agents/log-analytics-agent.md)os agentes windows precisam do agente Dependency. Para obter mais informações, consulte [os sistemas operativos suportados.](../agents/agents-overview.md#supported-operating-systems) |
| Agentes do Linux | Yes | Juntamente com o [agente Log Analytics para o Linux,](../agents/log-analytics-agent.md)os agentes Linux precisam do agente Desadependição. Para obter mais informações, consulte [os sistemas operativos suportados.](#supported-operating-systems) |
| Grupo de gestão do System Center Operations Manager | No | |

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Os conhecimentos VM suportam qualquer sistema operativo que suporte o agente Log Analytics e o agente De dependência. Consulte [a visão geral dos agentes do Monitor Azure ](../agents/agents-overview.md#supported-operating-systems) para obter uma lista completa.

> [!IMPORTANT]
> A funcionalidade de saúde dos hóspedes de informações VM tem um suporte mais limitado do sistema operativo enquanto está em pré-visualização pública. Consulte [a Saúde dos hóspedes (pré-visualização) do Enable VM insights para](../vm/vminsights-health-enable.md) obter uma lista detalhada.

### <a name="linux-considerations"></a>Considerações de Linux
Consulte a seguinte lista de considerações sobre o apoio do Linux ao agente de dependência que suporta insights VM:

- Apenas as versões de kernel padrão e SMP Linux são suportadas.
- As versões não padrão do kernel, tais como a Extensão de Endereço Físico (PAE) e xen, não são suportadas para qualquer distribuição do Linux. Por exemplo, um sistema com a cadeia de desbloqueio de *2.6.16.21-0.8-xen* não é suportado.
- Os núcleos personalizados, incluindo as reparações de núcleos padrão, não são suportados.
- Para distros de Debian que não a versão 9.4, a funcionalidade do mapa não é suportada, e a funcionalidade Performance está disponível apenas a partir do menu Azure Monitor. Não está disponível diretamente a partir do painel esquerdo do Azure VM.
- O núcleo CentOSPlus é suportado.

O núcleo Linux deve ser corrigido para as vulnerabilidades spectre e Meltdown. Consulte o seu fornecedor de distribuição Linux para mais detalhes. Executar o seguinte comando para verificar se está disponível se a Spectre/Meltdown foi atenuada:

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

A saída para este comando será semelhante à seguinte e especificará se uma máquina está vulnerável a qualquer um dos problemas. Se estes ficheiros estiverem em falta, a máquina não é remada.

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
As introspeções em VM requerem um espaço de trabalho log analytics. Consulte [o espaço de trabalho Configure Log Analytics para obter informações sobre VM](vminsights-configure-workspace.md) para obter detalhes e requisitos deste espaço de trabalho.
## <a name="agents"></a>Agentes
Os conhecimentos em VM requerem que os dois agentes seguintes sejam instalados em cada máquina virtual ou escala de máquina virtual definida para ser monitorizada. Para embarcar no recurso, instale estes agentes e ligue-os ao espaço de trabalho.  Consulte [os requisitos de rede](../agents/log-analytics-agent.md#network-requirements) para os requisitos de rede para estes agentes.

- [Agente Log Analytics](../agents/log-analytics-agent.md). Recolhe eventos e dados de desempenho a partir da máquina virtual ou conjunto de escala de máquina virtual e entrega-os no espaço de trabalho Log Analytics. Os métodos de implementação do agente Log Analytics nos recursos Azure utilizam a extensão VM para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md).
- Agente de dependência. Recolhe dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos, que são usados pela [funcionalidade Mapa em insights VM](../vm/vminsights-maps.md). O agente de dependência conta com o agente Log Analytics para entregar os seus dados ao Azure Monitor. Os métodos de implantação do agente de dependência dos recursos Azure utilizam a extensão VM para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> O agente Log Analytics é o mesmo agente utilizado pelo Gestor de Operações do Centro de Sistema. Os conhecimentos vM podem monitorizar agentes que também são monitorizados pelo Gestor de Operações se estiverem diretamente ligados, e você instala o agente Desagravado neles. Os agentes ligados ao Azure Monitor através de uma [ligação de grupo de gestão](../tform/../agents/om-agents.md) não podem ser monitorizados por insights VM.

Seguem-se múltiplos métodos para a implantação destes agentes. 

| Método | Descrição |
|:---|:---|
| [Portal do Azure](../vm/vminsights-enable-portal.md) | Instale ambos os agentes numa única máquina virtual, num conjunto de balanças de máquinas virtuais ou em máquinas virtuais híbridas ligadas ao Arco Azure. |
| [Modelos do Resource Manager](../vm/vminsights-enable-resource-manager.md) | Instale ambos os agentes utilizando qualquer um dos métodos suportados para implementar um modelo de Gestor de Recursos, incluindo CLI e PowerShell. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | Atribua a iniciativa Azure Policy para instalar automaticamente os agentes quando for criada uma máquina virtual ou um conjunto de escala de máquina virtual. |
| [Instalação manual](../vm/vminsights-enable-hybrid.md) | Instale os agentes no sistema operativo dos hóspedes em computadores alojados fora do Azure, incluindo no seu datacenter ou noutros ambientes em nuvem. |


## <a name="network-requirements"></a>Requisitos de rede

- Consulte [os requisitos de rede](../agents/log-analytics-agent.md#network-requirements) para os requisitos de rede para o agente Log Analytics.
- O agente de dependência requer uma ligação da máquina virtual ao endereço 169.254.169.254. Este é o ponto final do serviço de metadados Azure. Certifique-se de que as definições de firewall permitem ligações a este ponto final.


## <a name="management-packs"></a>Pacotes de gestão
Quando um espaço de trabalho do Log Analytics é configurado para insights VM, dois pacotes de gestão são encaminhados para todos os computadores Windows ligados a esse espaço de trabalho. Os pacotes de gestão são *nomeados Microsoft.IntelligencePacks.ApplicationDependencyMonitor* e *Microsoft.IntelligencePacks.VMInsights* e são escritos para *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs*. 

A fonte de dados utilizada pelo pacote de gestão *ApplicationDependencyMonitor* é **%Ficheiros do Programa%\Microsoft Monitoring Agent\Agente\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. A fonte de dados utilizada pelo pacote de gestão *VMInsights* é *de ficheiros %\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Dados de diagnóstico e utilização

A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço Azure Monitor. A Microsoft utiliza estes dados para melhorar a qualidade, segurança e integridade do serviço. 

Para fornecer capacidades precisas e eficientes de resolução de problemas, a funcionalidade Mapa inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operativo e versão, endereço IP, nome DNS e nome da estação de trabalho. A Microsoft não recolhe nomes, endereços ou outras informações de contacto.

Para obter mais informações sobre a recolha e utilização de dados, consulte a [Declaração de Privacidade dos Serviços Online da Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos seguintes

Para aprender a utilizar a função de monitorização de desempenho, consulte [ver insights VM Performance](../vm/vminsights-performance.md). Para ver as dependências de aplicações descobertas, consulte [o Mapa de insights VM da Vista.](../vm/vminsights-maps.md)
