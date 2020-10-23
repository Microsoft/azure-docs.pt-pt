---
title: Gestão de extensão VM com servidores ativados Azure Arc
description: Os servidores ativados do Azure Arc podem gerir a implementação de extensões de máquinas virtuais que fornecem tarefas de configuração e automatização pós-implantação com VMs não-Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: e9865761fd3e5897ee3f01cd3d6ca620d5ea2f4b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460891"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gestão de extensão de máquina virtual com servidores ativados do Azure Arc

As extensões de máquinas virtuais (VM) são pequenas aplicações que fornecem tarefas de configuração e automatização pós-implantação em VMs Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, a proteção antivírus ou a execução de um script, poderá ser utilizada uma extensão de VM.

Os servidores ativados pelo Azure Arc permitem-lhe implantar extensões VM Azure para Windows e Linux VMs não-Azure, simplificando a gestão da sua máquina híbrida no local, borda e outros ambientes de nuvem através do seu ciclo de vida. As extensões VM podem ser geridas utilizando os seguintes métodos nas suas máquinas híbridas ou servidores geridos por servidores ativados pelo Arc:

- O [portal Azure](manage-vm-extensions-portal.md)
- O [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Modelos de gestor de recursos Azure](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Principais vantagens

O suporte de extensão VM ativado pelo Azure Arc fornece os seguintes benefícios principais:

- Utilize [a Configuração do Estado da Automação Azure](../../automation/automation-dsc-overview.md) para armazenar centralmente as configurações e manter o estado desejado das máquinas híbridas conectadas ativadas através da extensão DSC VM.

- Recolher dados de registo para análise com [Logs in Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) ativado através da extensão VM do agente Log Analytics. Isto é útil para realizar análises complexas através de dados de várias fontes.

- Com [o Azure Monitor para VMs,](../../azure-monitor/insights/vminsights-overview.md)analisa o desempenho dos seus VMs Windows e Linux, e monitoriza os seus processos e dependências de outros recursos e processos externos. Isto é conseguido através da ativação das extensões VM do agente Log Analytics e do agente de dependência.

- Descarregue e execute scripts em máquinas híbridas conectadas utilizando a extensão de script personalizado. Esta extensão é útil para configuração de implementação de posts, instalação de software ou qualquer outra configuração ou tarefas de gestão.

## <a name="availability"></a>Disponibilidade

A funcionalidade de extensão VM só está disponível na lista de [regiões apoiadas.](overview.md#supported-regions) Certifique-se de que a bordo da sua máquina numa destas regiões.

## <a name="extensions"></a>Extensões

Nesta versão, apoiamos as seguintes extensões VM nas máquinas Windows e Linux.

|Extensão |SO |Publisher |Informações adicionais |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Extensão de script personalizado do Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft.PowerShell|[Extensão DSC do Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agente do Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics VM extensão para Windows](../../virtual-machines/extensions/oms-windows.md)|
|Agente de dependência da Microsoft | Windows |Microsoft.Compute | [Extensão de máquina virtual de agente de dependência para Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|PersonalScript|Linux |Microsoft.Azure.Extension |[Extensão de script personalizado Linux Versão 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Extensão DSC powerShell para Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agente do Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics VM extensão para Linux](../../virtual-machines/extensions/oms-linux.md) |
|Agente de dependência da Microsoft | Linux |Microsoft.Compute | [Extensão da máquina virtual do agente de dependência para Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Para saber mais sobre o pacote do agente Azure Connected Machine e detalhes sobre o componente do agente de extensão, consulte [a visão geral do Agente](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade depende dos seguintes fornecedores de recursos Azure na sua subscrição:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se ainda não estiverem registados, siga os passos dos [fornecedores de recursos Register Azure](agent-overview.md#register-azure-resource-providers).

A extensão VM do agente Log Analytics para Linux requer que o Python 2.x seja instalado na máquina-alvo.

### <a name="connected-machine-agent"></a>Agente de máquina conectado

Verifique se a sua máquina corresponde às [versões suportadas](agent-overview.md#supported-operating-systems) do sistema operativo Windows e Linux para o agente Azure Connected Machine.

A versão mínima do agente 'Máquina Conectada' que é suportada com esta funcionalidade no Windows e Linux é a versão 1.0.

Para atualizar a sua máquina para a versão do agente necessário, consulte o [agente de atualização](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Passos seguintes

Pode implementar, gerir e remover extensões VM utilizando os modelos [Azure CLI,](manage-vm-extensions-cli.md) [PowerShell](manage-vm-extensions-powershell.md), do [portal Azure](manage-vm-extensions-portal.md), ou [modelos Azure Resource Manager](manage-vm-extensions-template.md).
