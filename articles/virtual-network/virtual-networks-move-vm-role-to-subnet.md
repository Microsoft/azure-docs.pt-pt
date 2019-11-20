---
title: Mover instâncias de função da VM do Azure (clássico) ou dos serviços de nuvem para uma sub-rede diferente
description: Saiba como mover as instâncias de função de VMs (clássicas) e de serviços de nuvem para uma sub-rede diferente usando o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2a92355632ef0d7059c9e5294448de0ca591b82
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196689"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Mover uma VM (clássica) ou uma instância de função de serviços de nuvem para uma sub-rede diferente usando o PowerShell
Você pode usar o PowerShell para mover suas VMs (clássicas) de uma sub-rede para outra na mesma rede virtual (VNet). As instâncias de função podem ser movidas editando o arquivo CSCFG, em vez de usar o PowerShell.

> [!NOTE]
> Este artigo explica como mover VMs implantadas somente por meio do modelo de implantação clássico.
> 
> 

Por que mover VMs para outra sub-rede? A migração de sub-rede é útil quando a sub-rede mais antiga é muito pequena e não pode ser expandida devido a VMs em execução existentes nessa sub-rede. Nesse caso, você pode criar uma sub-rede nova e maior e migrar as VMs para a nova sub-rede e, depois que a migração for concluída, você poderá excluir a sub-rede vazia antiga.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Como mover uma VM para outra sub-rede
Para mover uma VM, execute o cmdlet Set-AzureSubnet do PowerShell, usando o exemplo abaixo como modelo. No exemplo a seguir, estamos movendo TestVM de sua sub-rede atual para Subnet-2. Certifique-se de editar o exemplo para refletir seu ambiente. Observe que sempre que você executar o cmdlet Update-AzureVM como parte de um procedimento, ele reiniciará a VM como parte do processo de atualização.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Se você especificou um IP privado interno estático para sua VM, precisará limpar essa configuração antes de poder mover a VM para uma nova sub-rede. Nesse caso, use o seguinte:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Para mover uma instância de função para outra sub-rede
Para mover uma instância de função, edite o arquivo CSCFG. No exemplo a seguir, estamos movendo "Role0" na rede virtual *VNETName* de sua sub-rede presente para *Subnet-2*. Como a instância de função já foi implantada, você apenas alterará o nome da sub-rede = Subnet-2. Certifique-se de editar o exemplo para refletir seu ambiente.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
