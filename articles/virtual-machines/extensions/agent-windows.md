---
title: Descrição Geral do Agente da Máquina Virtual do Azure
description: Descrição Geral do Agente da Máquina Virtual do Azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 07/20/2019
ms.openlocfilehash: 33db214013111b0dd4540a1b1d2947b7d1854db9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607488"
---
# <a name="azure-virtual-machine-agent-overview"></a>Descrição geral do Agente da Máquina Virtual do Azure
O Agente da Máquina Virtual do Microsoft Azure (Agente da VM) é um processo leve e seguro que gere a interação da máquina virtual (VM) com o Controlador de Recursos de Infraestrutura do Microsoft Azure. O Agente da VM tem uma função primária na ativação e na execução de extensões de máquina virtual do Azure. As extensões de VM permitem a configuração pós-implementação da VM, por exemplo, instalar e configurar o software. As extensões de VM também permitem funcionalidades de recuperação, como a reposição da palavra-passe administrativa de uma VM. Sem o Agente da VM do Azure, as extensões de VM não podem ser executadas.

Este artigo detalha a instalação e a deteção do Agente da Máquina Virtual do Azure.

## <a name="install-the-vm-agent"></a>Instalar o Agente da VM

### <a name="azure-marketplace-image"></a>Imagem do Azure Marketplace

O Agente da VM do Azure é instalado por predefinição em qualquer VM do Windows implementada a partir da imagem do Azure Marketplace. Quando implementa uma imagem do Azure Marketplace a partir do portal, do PowerShell, da Interface de Linha de Comandos ou de um modelo do Azure Resource Manager, o Agente da VM do Azure também é instalado.

O Pacote do Agente Convidado do Windows é dividido em duas partes:

- Agente de Aprovisionamento (PA)
- Agente Convidado do Windows (WinGA)

Para iniciar uma VM, deve ter o PA instalado na VM. No entanto, o WinGA não precisa de ser instalado. Na altura da implementação da VM, pode selecionar não instalar o WinGA. Os seguintes exemplos mostram como selecionar a opção *provisionVmAgent* com um modelo do Azure Resource Manager:

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

Se não tiver os Agentes instalados, não poderá utilizar alguns serviços do Azure, como o Azure Backup ou o Azure Security. Estes serviços precisam de uma extensão para serem instalados. Se tiver implementado uma VM sem o WinGA, poderá instalar a versão mais recente do agente posteriormente.

### <a name="manual-installation"></a>Instalação manual
O agente da VM do Windows pode ser instalado manualmente com um pacote do Windows Installer. Pode ser necessária a instalação manual quando cria uma imagem da VM personalizada que é implementada no Azure. Para instalar manualmente o Agente da VM do Windows, [transfira o programa de instalação do Agente da VM](https://go.microsoft.com/fwlink/?LinkID=394789). Também pode pesquisar uma versão específica nas [versões do Agente VM Do GitHub Windows Iaa.](https://github.com/Azure/WindowsVMAgent/releases) O Agente VM é suportado no Windows Server 2008 (64 bit) e mais tarde.

> [!NOTE]
> É importante atualizar a opção AllowExtensionOperations após a instalação manual do VMAgent numa VM que foi implementada a partir da imagem sem ativar o ProvisionVMAgent.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Pré-requisitos

- O Windows VM Agent precisa de pelo menos o Windows Server 2008 SP2 (64-bit) para ser executado, com o Quadro .NET 4.0. Consulte [o suporte mínimo da versão para agentes de máquinas virtuais em Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

- Confirme que a VM tem acesso ao endereço IP 168.63.129.16. Para mais informações, consulte [o endereço IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

- Certifique-se de que o DHCP está ativado dentro do VM convidado. Isto é necessário para obter o endereço de anfitrião ou tecido da DHCP para o Agente VM IaaS e extensões para funcionar. Se precisar de um IP estático privado, deve configurá-lo através do portal Azure ou PowerShell, e certifique-se de que a opção DHCP dentro do VM está ativada. [Saiba mais](../../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre a configuração de um endereço IP estático com o PowerShell.


## <a name="detect-the-vm-agent"></a>Detetar o Agente da VM

### <a name="powershell"></a>PowerShell

O módulo do PowerShell do Azure Resource Manager pode ser utilizado para recuperar informações sobre as VMs do Azure. Para ver informações sobre uma VM, como o estado de aprovisionamento do Agente da VM do Azure, utilize [Get-AzVM](/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

A seguinte produção de exemplo condensado mostra a propriedade *ProvisionVMAgent* aninhada no interior `OSProfile` . Esta propriedade poderá ser utilizada para determinar se o agente da VM foi implementado na VM:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

O seguinte script pode ser utilizado para devolver uma lista concisa de nomes de VM e o estado do Agente da VM:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Deteção Manual

Quando inicia sessão numa VM do Windows, pode utilizar o Gestor de Tarefas para examinar os processos de execução. Para verificar o Agente da VM do Azure, abra o Gestor de Tarefas, clique no separador *Detalhes* e procure o nome de processo **WindowsAzureGuestAgent.exe**. A presença deste processo indica que o agente da VM está instalado.


## <a name="upgrade-the-vm-agent"></a>Atualizar o Agente da VM
O Agente VM Azure para Windows é automaticamente atualizado em imagens implementadas a partir do Mercado Azure. À medida que as novas VMs são implementadas no Azure, recebem o agente da VM mais recente no momento do aprovisionamento da VM. Se instalou o agente manualmente ou está a implementar imagens VM personalizadas, terá de atualizar manualmente para incluir o novo agente VM na hora da criação de imagem.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Recolha de Registos Automática do Agente Convidado do Windows
O Agente Convidado do Windows possui uma funcionalidade para recolher automaticamente alguns registos. Esta funcionalidade é controlada pelo processo CollectGuestLogs.exe. Existe para os Serviços Cloud PaaS e para Máquinas Virtuais IaaS e o seu objetivo é recolher rápida e automaticamente alguns registos de diagnóstico de uma VM, para poderem ser utilizados na análise offline. Os registos recolhidos são Registos de Eventos, Registos de SO, Registos do Azure e algumas chaves de registo. Produzem um ficheiro ZIP que é transferido para o Anfitrião da VM. Em seguida, este ficheiro ZIP pode ser analisado pelas Equipas de Engenharia e profissionais de Suporte para investigar problemas a pedido do cliente proprietário da VM.

## <a name="guest-agent-and-osprofile-certificates"></a>Agente Convidado e certificados OSProfile
O Agente VM Azure é responsável pela instalação dos certificados referenciados no `OSProfile` conjunto de escala de VM ou de máquina virtual. Se retirar manualmente estes certificados da consola MMC dos certificados dentro do VM convidado, espera-se que o agente convidado os adicione de volta.
Para remover permanentemente um certificado, terá de o retirar do `OSProfile` sistema operativo , e depois removê-lo do sistema operativo do hóspede.

Para uma máquina virtual, utilize o [Remove-AzVMSecret]() para remover os certificados do `OSProfile` .

Para obter mais informações sobre certificados de conjunto de escala de máquina virtual, consulte [conjuntos de escala de máquina virtual - Como remover certificados precários?](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-remove-deprecated-certificates)


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre as extensões de VM, veja [Descrição geral das funcionalidades e extensões de máquinas virtuais do Azure](overview.md).
