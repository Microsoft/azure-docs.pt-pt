---
title: Gestão de extensão VM com servidores ativados Azure Arc
description: Os servidores ativados do Azure Arc podem gerir a implementação de extensões de máquinas virtuais que fornecem tarefas de configuração e automatização pós-implantação com VMs não-Azure.
ms.date: 03/01/2021
ms.topic: conceptual
ms.openlocfilehash: 039c52ccbee03636da0f5acc0fc5844be9b646f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687911"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gestão de extensões de máquinas virtuais com servidores preparados para o Azure Arc

As extensões de máquinas virtuais (VM) são pequenas aplicações que fornecem tarefas de configuração e automatização pós-implantação em VMs Azure. Por exemplo, se uma máquina virtual necessitar de instalação de software, proteção antivírus ou para executar um script nele, pode ser utilizada uma extensão VM.

Os servidores ativados Azure Arc permitem-lhe implementar extensões Azure VM para Windows e VMs não-Azure, simplificando a gestão da sua máquina híbrida através do seu ciclo de vida. As extensões VM podem ser geridas utilizando os seguintes métodos nas suas máquinas híbridas ou servidores geridos por servidores ativados pelo Arc:

- O [portal Azure](manage-vm-extensions-portal.md)
- O [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Modelos de gestor de recursos Azure](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Principais vantagens

O suporte de extensão VM ativado pelo Azure Arc fornece os seguintes benefícios principais:

- Recolher dados de registo para análise com [Logs in Azure Monitor,](../../azure-monitor/logs/data-platform-logs.md) permitindo a extensão VM do agente Log Analytics. Isto é útil para fazer análises complexas através de dados de diferentes tipos de fontes.

- Com [o Azure Monitor para VMs,](../../azure-monitor/vm/vminsights-overview.md)analisa o desempenho dos seus VMs Windows e Linux, e monitoriza os seus processos e dependências de outros recursos e processos externos. Isto é conseguido através da ativação das extensões VM do agente Log Analytics e do agente de dependência.

- Descarregue e execute scripts em máquinas híbridas conectadas utilizando a extensão de script personalizado. Esta extensão é útil para configuração de implementação de posts, instalação de software ou qualquer outra configuração ou tarefas de gestão.

- Atualização automática dos certificados armazenados num [Cofre de Chaves Azure](../../key-vault/general/overview.md).

## <a name="availability"></a>Disponibilidade

A funcionalidade de extensão VM só está disponível na lista de [regiões apoiadas.](overview.md#supported-regions) Certifique-se de que a bordo da sua máquina numa destas regiões.

## <a name="extensions"></a>Extensões

Nesta versão, apoiamos as seguintes extensões VM nas máquinas Windows e Linux.

Para saber mais sobre o pacote do agente Azure Connected Machine e detalhes sobre o componente do agente de extensão, consulte [a visão geral do Agente](agent-overview.md#agent-component-details).

### <a name="windows-extensions"></a>Extensões do Windows

|Extensão |Publisher |Tipo |Informações adicionais |
|----------|----------|-----|-----------------------|
|Scanner integrado de vulnerabilidade do Azure Defender |Qualys |WindowsAgent.AzureSecurityCenter |[Solução integrada de avaliação de vulnerabilidades do Azure Defender para a Azure e máquinas híbridas](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Extensão de Script Personalizado |Microsoft.Compute | CustomScriptExtension |[Extensão de script personalizado do Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|Agente do Log Analytics |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Log Analytics VM extensão para Windows](../../virtual-machines/extensions/oms-windows.md)|
|Monitor azul para VMs (insights) |Microsoft.Azure.Monitoring.DependencyAgent |DependênciaAgentWindows | [Extensão de máquina virtual de agente de dependência para Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Sincronização do certificado de abóbada de chave Azure | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Extensão da máquina virtual key Vault para Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Agente do Azure Monitor |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[Instale o agente Azure Monitor (pré-visualização)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Extensões linux

|Extensão |Publisher |Tipo |Informações adicionais |
|----------|----------|-----|-----------------------|
|Scanner integrado de vulnerabilidade do Azure Defender |Qualys |LinuxAgent.AzureSecurityCenter |[Solução integrada de avaliação de vulnerabilidades do Azure Defender para a Azure e máquinas híbridas](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Extensão de Script Personalizado |Microsoft.Azure.Extensões |PersonalScript |[Extensão de script personalizado Linux Versão 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Agente do Log Analytics |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Log Analytics VM extensão para Linux](../../virtual-machines/extensions/oms-linux.md) |
|Monitor azul para VMs (insights) |Microsoft.Azure.Monitoring.DependencyAgent |DependênciaAgentLinux |[Extensão da máquina virtual do agente de dependência para Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Sincronização do certificado de abóbada de chave Azure | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Extensão da máquina virtual key Vault para Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Agente do Azure Monitor |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[Instale o agente Azure Monitor (pré-visualização)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade depende dos seguintes fornecedores de recursos Azure na sua subscrição:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se ainda não estiverem registados, siga os passos dos [fornecedores de recursos Register Azure](agent-overview.md#register-azure-resource-providers).

Certifique-se de rever a documentação de cada extensão VM referenciada na tabela anterior para entender se tem alguma rede ou requisitos do sistema. Isto pode ajudá-lo a evitar experimentar quaisquer problemas de conectividade com um serviço Azure ou funcionalidade que dependa dessa extensão VM.

### <a name="log-analytics-vm-extension"></a>Extensão VM do Log Analytics

A extensão VM do agente Log Analytics para Linux requer que o Python 2.x seja instalado na máquina-alvo.

### <a name="azure-key-vault-vm-extension-preview"></a>Extensão VM do cofre da chave Azure (pré-visualização)

A extensão VM do Cofre-Chave (pré-visualização) não suporta os seguintes sistemas operativos Linux:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

A implementação da extensão VM do Cofre de Chaves (pré-visualização) só é suportada utilizando:

- A CLI do Azure
- A concha do Azure PowerShell
- Modelo Azure Resource Manager

Antes de implementar a extensão, tem de completar o seguinte:

1. [Criar um cofre e um certificado](../../key-vault/certificates/quick-create-portal.md) (auto-assinado ou importador).

2. Conceder ao Arco Azure o acesso do servidor ao segredo do certificado. Se estiver a utilizar a [pré-visualização do RBAC,](../../key-vault/general/rbac-guide.md)procure o nome do recurso Azure Arc e atribua-lhe a **função Key Vault Secrets User (pré-visualização).** Se estiver a utilizar a política de [acesso ao Cofre de Chaves,](../../key-vault/general/assign-access-policy-portal.md)atribua permissões secretas ao sistema de recursos Azure Arc atribuído. 

### <a name="connected-machine-agent"></a>Agente de máquina conectado

Verifique se a sua máquina corresponde às [versões suportadas](agent-overview.md#supported-operating-systems) do sistema operativo Windows e Linux para o agente Azure Connected Machine.

A versão mínima do agente 'Máquina Conectada' que é suportada com esta funcionalidade no Windows e Linux é a versão 1.0.

Para atualizar a sua máquina para a versão do agente necessário, consulte o [agente de atualização](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Passos seguintes

Pode implementar, gerir e remover extensões VM utilizando os modelos [Azure CLI](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), do [portal Azure](manage-vm-extensions-portal.md), ou [modelos Azure Resource Manager](manage-vm-extensions-template.md).
