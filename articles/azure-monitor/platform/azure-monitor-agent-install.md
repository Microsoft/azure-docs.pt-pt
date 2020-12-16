---
title: Instale o agente Azure Monitor
description: Opções para instalar o Azure Monitor Agent (AMA) em máquinas virtuais Azure e servidores ativados Azure Arc.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 893a04f5acd48cf1e6f34033c06a758492e70abf
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516722"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Instale o agente Azure Monitor (pré-visualização)
Este artigo fornece as diferentes opções atualmente disponíveis para instalar o [agente Azure Monitor](azure-monitor-agent-overview.md) em máquinas virtuais Azure e servidores azure Arc habilitados e também as opções para criar [associações com regras de recolha de dados](data-collection-rule-azure-monitor-agent.md) que definem quais os dados que o agente deve recolher.

## <a name="prerequisites"></a>Pré-requisitos
São necessários os seguintes pré-requisitos antes da instalação do agente Azure Monitor.

- [A identidade do sistema gerida](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) deve ser ativada em máquinas virtuais Azure. Isto não é necessário para servidores ativados Azure Arc. A identidade do sistema será ativada automaticamente se o agente for instalado como parte do processo de criação e atribuição de uma regra de recolha de [dados utilizando o portal Azure](#install-with-azure-portal).
- A [etiqueta de serviço AzureResourceManager](../../virtual-network/service-tags-overview.md) deve ser ativada na rede virtual para a máquina virtual.

## <a name="virtual-machine-extension-details"></a>Detalhes da extensão da máquina virtual
O Agente monitor Azure é implementado como uma [extensão Azure VM](../../virtual-machines/extensions/overview.md) com os detalhes na tabela seguinte. Pode ser instalado utilizando qualquer um dos métodos para instalar extensões de máquinas virtuais, incluindo as descritas neste artigo.

| Propriedade | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| Tipo      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1,0 | 1.5 |


## <a name="install-with-azure-portal"></a>Instalar com portal Azure
Para instalar o agente Azure Monitor utilizando o portal Azure, siga o processo para [criar uma regra de recolha de dados](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) no portal Azure. Isto permite-lhe associar a regra de recolha de dados a uma ou mais máquinas virtuais Azure ou servidores ativados a Azure Arc. O agente será instalado em qualquer uma destas máquinas virtuais que ainda não a tenham.


## <a name="install-with-resource-manager-template"></a>Instalar com o modelo de gestor de recursos
Pode utilizar modelos de Gestor de Recursos para instalar o agente Azure Monitor em máquinas virtuais Azure e em servidores ativados aZure Arc e criar uma associação com as regras de recolha de dados. Deve criar qualquer regra de recolha de dados antes de criar a associação.

Obtenha modelos de amostra para instalar o agente e criar a associação a partir do seguinte: 

- [Modelo para instalar o agente Azure Monitor (Azure e Azure Arc)](../samples/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Modelo para criar associação com regra de recolha de dados](../samples/resource-manager-data-collection-rules.md)

Instale os modelos utilizando [qualquer método de implementação para modelos de Gestor de Recursos,](../../azure-resource-manager/templates/deploy-powershell.md) tais como os seguintes comandos.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Instalação com PowerShell
Pode instalar o agente Azure Monitor em máquinas virtuais Azure e em servidores ativados Azure Arc utilizando o comando PowerShell para adicionar uma extensão de máquina virtual. 

### <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure
Utilize os seguintes comandos PowerShell para instalar o agente Azure Monitor em máquinas virtuais Azure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---

### <a name="azure-arc-enabled-servers"></a>Servidores preparados para o Azure Arc
Utilize os seguintes comandos PowerShell para instalar o agente Azure Monitor nos servidores ativados do Arco azul.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>CLI do Azure
Pode instalar o agente Azure Monitor em máquinas virtuais Azure e em servidores ativados Azure Arc utilizando o comando Azure CLI para adicionar uma extensão de máquina virtual. 

### <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure
Utilize os seguintes comandos CLI para instalar o agente Azure Monitor em máquinas virtuais Azure.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Servidores preparados para o Azure Arc
Utilize os seguintes comandos CLI para instalar o agente Azure Monitor nos servidores ativados do Arco azul.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Passos seguintes

- [Crie uma regra de recolha](data-collection-rule-azure-monitor-agent.md) de dados para recolher dados do agente e enviá-lo para o Azure Monitor.
