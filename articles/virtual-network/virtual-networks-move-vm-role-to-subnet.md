---
title: Mover uma VM (clássico) ou a instância de função de serviços em nuvem a uma sub-rede diferente - Azure PowerShell | Documentos da Microsoft
description: Saiba como mover VMs (clássico) e instâncias de função dos serviços Cloud a uma sub-rede diferente com o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: 787a50a0cbf16089cd15f922b494cd12d680cb43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640400"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Mover uma VM (clássico) ou a instância de função de serviços em nuvem a uma sub-rede diferente com o PowerShell
Pode utilizar o PowerShell para mover as suas VMs (clássico) de uma sub-rede para outra na mesma rede virtual (VNet). Instâncias de função podem ser movidas ao editar o ficheiro CSCFG, em vez de utilizar o PowerShell.

> [!NOTE]
> Este artigo explica como mover VMs implementadas através do modelo de implementação clássica apenas.
> 
> 

Por que mover VMs para outra sub-rede? Migração de sub-rede é útil quando a sub-rede mais antiga é demasiado pequena e não pode ser expandida devido a existentes VMs em execução nessa sub-rede. Nesse caso, pode criar uma sub-rede nova, maior e migrar as VMs para a nova sub-rede, em seguida, após a migração estiver concluída, pode eliminar a sub-rede vazia antiga.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Como mover uma VM para outra sub-rede
Para mover uma VM, execute o cmdlet do PowerShell Set-AzureSubnet, usando o exemplo abaixo como um modelo. No exemplo a seguir, vamos mudar TestVM da sua sub-rede presente, para sub-rede-2. Certifique-se de que editar o exemplo para refletir o seu ambiente. Tenha em atenção que sempre que executar o cmdlet Update-AzureVM como parte de um procedimento, ele será reiniciado a VM como parte do processo de atualização.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Se tiver especificado um IP estático de privada interno para a sua VM, terá de limpar a configuração antes de pode mover a VM para uma nova sub-rede. Nesse caso, utilize o seguinte:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Para mover uma instância de função para outra sub-rede
Para mover uma instância de função, edite o ficheiro CSCFG. No exemplo a seguir, vamos mudar "Role0" na rede virtual *VNETName* da sua sub-rede presente para *sub-rede 2*. Uma vez que a instância de função já foi implementada, apenas vai alterar o nome da sub-rede = 2 de sub-rede. Certifique-se de que editar o exemplo para refletir o seu ambiente.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
