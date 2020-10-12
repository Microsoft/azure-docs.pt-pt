---
title: Recolher detalhes sobre todas as VMs numa subscrição com o PowerShell
description: Recolher detalhes sobre todas as VMs numa subscrição com o PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: 8d61da9c3a3d575fcbce0b77e1f8b2684df58106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87171693"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Recolher detalhes sobre todas as VMs numa subscrição com o PowerShell

Este script cria um csv que contém o Nome VM, Nome do Grupo de Recursos, Região, Tamanho Vm, Rede Virtual, Subnet, Endereço IP Privado, Tipo DE SO e Endereço IP Público dos VMs na subscrição fornecida.

Se não tiver uma [subscrição do Azure,](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="sample-script"></a>Script de exemplo

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VmSize, VirtualNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress, NicName, ApplicationSecurityGroup 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VmSize = $vm.HardwareProfile.VmSize
        $info.VirtualNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $info.NicName = $nic.Name 
        $info.ApplicationSecurityGroup = $nic.IpConfigurations.ApplicationSecurityGroups.Id 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VmSize, VirtualNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress, NicName, ApplicationSecurityGroup 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os seguintes comandos para criar uma exportação csv dos detalhes de VMs numa subscrição. Cada comando na tabela liga à documentação específica do comando.

|Comando|Notas|
|-|-|
|[Selecione-AzSubscription](/powershell/module/az.accounts/set-azcontext)|Define o inquilino, subscrição e ambiente para os cmdlets a utilizar na sessão atual.|
|[Get-AzVM](/powershell/module/az.compute/get-azvm)|Obtém as propriedades de uma máquina virtual.|
|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)|Obtém um endereço IP público.|
|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface)|Obtém uma interface de rede.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=/azure/virtual-machines/windows/toc.json).
