---
title: Visão geral do agente de máquinas virtuais Azure
description: Visão geral do agente de máquinas virtuais Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: 3d9c178201ab0c22ed4eab9cf65f7d48e59e1359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246129"
---
# <a name="azure-virtual-machine-agent-overview"></a>Visão geral do Agente De MáquinaS Virtuais Azure
O Microsoft Azure Virtual Machine Agent (VM Agent) é um processo seguro e leve que gere a interação da máquina virtual (VM) com o Controlador de Tecido Sino. O Agente VM tem um papel primordial na ativação e execução de extensões de máquinas virtuais Azure. As extensões VM permitem a configuração pós-implantação de VM, tais como a instalação e configuração do software. As extensões VM também permitem funcionalidades de recuperação, tais como a reposição da palavra-passe administrativa de um VM. Sem o Agente Azure VM, as extensões VM não podem ser executadas.

Este artigo detalha a instalação e deteção do Agente Máquina Virtual Azure.

## <a name="install-the-vm-agent"></a>Instalar o Agente VM

### <a name="azure-marketplace-image"></a>Imagem azure marketplace

O Agente Azure VM é instalado por padrão em qualquer VM do Windows implantado a partir de uma imagem do Azure Marketplace. Quando implementa uma imagem do Azure Marketplace a partir do portal, PowerShell, Interface de Linha de Comando ou um modelo de Gestor de Recursos Azure, o Agente Azure VM também está instalado.

O Pacote de Agente Convidado do Windows é dividido em duas partes:

- Agente de Provisionamento (PA)
- Agente convidado do Windows (WinGA)

Para iniciar um VM tem de ter o PA instalado no VM, no entanto o WinGA não precisa de ser instalado. No tempo de implementação da VM, pode selecionar não instalar o WinGA. O exemplo seguinte mostra como selecionar a opção *provisionVmAgent* com um modelo de Gestor de Recursos Azure:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Se não tiver os Agentes instalados, não poderá utilizar alguns serviços Azure, como o Azure Backup ou o Azure Security. Estes serviços requerem uma extensão para ser instalado. Se implementou um VM sem o WinGA, pode instalar a versão mais recente do agente mais tarde.

### <a name="manual-installation"></a>Instalação manual
O agente VM do Windows pode ser instalado manualmente com um pacote de instalação Windows. A instalação manual pode ser necessária quando criar uma imagem VM personalizada que seja implantada no Azure. Para instalar manualmente o Agente VM do Windows, [descarregue o instalador do Agente VM](https://go.microsoft.com/fwlink/?LinkID=394789). O Agente VM é suportado no Windows Server 2008 R2 e posteriormente.

> [!NOTE]
> É importante atualizar a opção AllowExtensionOperations após a instalação manual do VMAgent num VM que foi implantado a partir da imagem sem ativar o ProvisionVMAgent.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Pré-requisitos
- O Agente VM do Windows necessita de pelo menos o Windows Server 2008 R2 (64 bits) para funcionar, com a .Net Framework 4.0. Ver [suporte de versão mínima para agentes de máquinas virtuais em Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Certifique-se de que o seu VM tem acesso ao endereço IP 168.63.129.16. Para mais informações consulte [O endereço IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>Detetar o Agente VM

### <a name="powershell"></a>PowerShell

O módulo PowerShell do Gestor de Recursos Azure pode ser usado para recuperar informações sobre VMs Azure. Para ver informações sobre um VM, como o estado de provisionamento do Agente Azure VM, utilize [o Get-AzVM:](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)

```powershell
Get-AzVM
```

A saída de exemplo condensado seguinte mostra a propriedade *ProvisionVMAgent* aninhada dentro *do OSProfile*. Esta propriedade pode ser usada para determinar se o agente VM foi implantado para o VM:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

O seguinte script pode ser usado para devolver uma lista concisa de nomes VM e o estado do Agente VM:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Deteção Manual

Quando inicia sessão num VM do Windows, o Task Manager pode ser utilizado para examinar os processos de execução. Para verificar se o Agente Azure VM, abra o Task Manager, clique no separador *Detalhes* e procure um nome de processo **WindowsAzureGuestAgent.exe**. A presença deste processo indica que o agente VM está instalado.


## <a name="upgrade-the-vm-agent"></a>Atualizar o Agente VM
O Agente Azure VM para Windows é automaticamente atualizado. À medida que os novos VMs são implantados para o Azure, recebem o mais recente agente VM no tempo de provisionamento vm. As imagens VM personalizadas devem ser atualizadas manualmente para incluir o novo agente VM no momento da criação de imagem.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Coleção de registos automáticos do agente convidado do Windows
O Windows Guest Agent tem uma funcionalidade para recolher automaticamente alguns registos. Esta funcionalidade é controladora pelo processo CollectGuestLogs.exe. Existe tanto para os Serviços paas cloud como para as Máquinas Virtuais IaaS e o seu objetivo é & rapidamente recolher alguns registos de diagnóstico de um VM - para que possam ser usados para análise offline. Os registos recolhidos são Registos de Eventos, Registos DE OS, Registos Azure e algumas chaves de registo. Produz um ficheiro ZIP que é transferido para o Hospedeiro da VM. Este ficheiro ZIP pode então ser analisado por Equipas de Engenharia e profissionais de Suporte para investigar questões a pedido do cliente que detém o VM.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões VM, consulte [extensões de máquinas virtuais Azure e funcionalidades de visão geral](overview.md).
