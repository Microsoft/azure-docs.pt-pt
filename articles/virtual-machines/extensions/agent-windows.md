---
title: Visão geral do agente de máquina virtual do Azure
description: Visão geral do agente de máquina virtual do Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: MicahMcKittrick-MSFT
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
ms.openlocfilehash: 24369ed547b811b212518193a2ae2f76ed197754
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264632"
---
# <a name="azure-virtual-machine-agent-overview"></a>Visão geral do agente de máquina virtual do Azure
O Microsoft Azure agente de máquina virtual (agente de VM) é um processo leve e seguro que gerencia a interação de VM (máquina virtual) com o controlador de malha do Azure. O agente de VM tem uma função primária na habilitação e execução de extensões de máquina virtual do Azure. As extensões de VM habilitam a configuração pós-implantação da VM, como instalar e configurar o software. As extensões de VM também habilitam recursos de recuperação, como redefinir a senha administrativa de uma VM. Sem o agente de VM do Azure, as extensões de VM não podem ser executadas.

Este artigo detalha a instalação e a detecção do agente de máquina virtual do Azure.

## <a name="install-the-vm-agent"></a>Instalar o agente de VM

### <a name="azure-marketplace-image"></a>Imagem do Azure Marketplace

O agente de VM do Azure é instalado por padrão em qualquer VM do Windows implantada de uma imagem do Azure Marketplace. Quando você implanta uma imagem do Azure Marketplace do portal, do PowerShell, da interface de linha de comando ou de um modelo de Azure Resource Manager, o agente de VM do Azure também é instalado.

O pacote do agente convidado do Windows é dividido em duas partes:

- Agente de provisionamento (PA)
- Agente convidado do Windows (ASAA)

Para inicializar uma VM, você deve ter o PA instalado na VM, no entanto, a ASAA não precisa ser instalada. Em tempo de implantação da VM, você pode optar por não instalar o wingman. O exemplo a seguir mostra como selecionar a opção *provisionVmAgent* com um modelo de Azure Resource Manager:

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

Se você não tiver os agentes instalados, não poderá usar alguns serviços do Azure, como o backup do Azure ou a segurança do Azure. Esses serviços exigem a instalação de uma extensão. Se você tiver implantado uma VM sem a asa, poderá instalar a versão mais recente do agente mais tarde.

### <a name="manual-installation"></a>Instalação manual
O agente de VM do Windows pode ser instalado manualmente com um pacote do Windows Installer. A instalação manual pode ser necessária quando você cria uma imagem de VM personalizada que é implantada no Azure. Para instalar manualmente o agente de VM do Windows, [Baixe o instalador do agente de VM](https://go.microsoft.com/fwlink/?LinkID=394789). O agente de VM tem suporte no Windows Server 2008 R2 e posterior.

> [!NOTE]
> É importante atualizar a opção AllowExtensionOperations depois de instalar manualmente o VMAgent em uma VM que foi implantada a partir da imagem sem o ProvisionVMAgent Enable.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Pré-requisitos
- O agente de VM do Windows precisa de pelo menos o Windows Server 2008 R2 (64 bits) para ser executado com o .NET Framework 4,0. Consulte [suporte mínimo de versão para agentes de máquina virtual no Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Verifique se sua VM tem acesso ao endereço IP 168.63.129.16. Para obter mais informações, consulte [o que é o endereço IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>Detectar o agente de VM

### <a name="powershell"></a>PowerShell

O módulo Azure Resource Manager PowerShell pode ser usado para recuperar informações sobre VMs do Azure. Para ver informações sobre uma VM, como o estado de provisionamento para o agente de VM do Azure, use [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

A saída de exemplo condensada a seguir mostra a propriedade *ProvisionVMAgent* aninhada dentro de *OSProfile*. Essa propriedade pode ser usada para determinar se o agente de VM foi implantado na VM:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

O script a seguir pode ser usado para retornar uma lista concisa de nomes de VM e o estado do agente de VM:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Detecção manual

Quando conectado a uma VM do Windows, o Gerenciador de tarefas pode ser usado para examinar os processos em execução. Para verificar o agente de VM do Azure, abra o Gerenciador de tarefas, clique na guia *detalhes* e procure um nome de processo **WindowsAzureGuestAgent. exe**. A presença desse processo indica que o agente de VM está instalado.


## <a name="upgrade-the-vm-agent"></a>Atualizar o agente de VM
O agente de VM do Azure para Windows é atualizado automaticamente. À medida que novas VMs são implantadas no Azure, elas recebem o agente de VM mais recente no tempo de provisionamento da VM. As imagens de VM personalizadas devem ser atualizadas manualmente para incluir o novo agente de VM no momento da criação da imagem.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Coleção de logs automáticos do agente convidado do Windows
O agente convidado do Windows tem um recurso para coletar automaticamente alguns logs. Esse recurso é Controller pelo processo CollectGuestLogs. exe. Ele existe para os serviços de nuvem PaaS e para máquinas virtuais IaaS e seu objetivo é & rapidamente coletar automaticamente alguns logs de diagnóstico de uma VM, para que eles possam ser usados para análise offline. Os logs coletados são logs de eventos, logs do sistema operacional, logs do Azure e algumas chaves do registro. Ele produz um arquivo ZIP que é transferido para o host da VM. Esse arquivo ZIP pode então ser examinado por equipes de engenharia e profissionais de suporte para investigar problemas na solicitação do cliente que possui a VM.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões de VM, consulte [visão geral de extensões e recursos de máquinas virtuais do Azure](overview.md).
