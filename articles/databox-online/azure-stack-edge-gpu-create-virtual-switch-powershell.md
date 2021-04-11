---
title: Criar um novo interruptor virtual em Azure Stack Edge via PowerShell
description: Descreve como criar um interruptor virtual num dispositivo Azure Stack Edge utilizando o PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556256"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>Criar um novo interruptor virtual no Azure Stack Edge Pro GPU via PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como criar um novo interruptor virtual no seu dispositivo GPU Azure Stack Edge Pro. Por exemplo, criaria um novo interruptor virtual se quisesse que as suas máquinas virtuais se ligassem através de uma porta de rede física diferente.

## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação VM

1. Ligue-se à interface PowerShell no seu dispositivo.
2. Consulta de interfaces de rede física disponíveis.
3. Criar um interruptor virtual.
4. Verifique a rede virtual e a sub-rede que são criadas automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

- Tem acesso a uma máquina cliente que pode aceder à interface PowerShell do seu dispositivo. Ver [Ligar à interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    A máquina do cliente deve estar a executar um [SISTEMA Suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)

- Utilize a UI local para ativar o cálculo numa das interfaces de rede física do seu dispositivo, de acordo com as instruções da [rede de computação Enable](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) no seu dispositivo. 


## <a name="connect-to-the-powershell-interface"></a>Ligue-se à interface do PowerShell

[Ligue-se à interface PowerShell do seu dispositivo.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)

## <a name="query-available-network-interfaces"></a>Interfaces de rede disponíveis em consulta

1. Utilize o seguinte comando para apresentar uma lista de interfaces de rede físicas nas quais pode criar um novo interruptor virtual. Irá selecionar uma destas interfaces de rede.

    ```powershell
    Get-NetAdapter -Physical
    ```
    Aqui está uma saída de exemplo:
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Escolha uma interface de rede que seja:

    - No estado **up.** 
    - Não utilizado por nenhum interruptor virtual existente. Atualmente, apenas um vswitch pode ser configurado por interface de rede. 
    
    Para verificar a associação de comutadora virtual e interface de rede existente, execute o `Get-HcsExternalVirtualSwitch` comando.
 
    Aqui está uma saída de exemplo.

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    Neste caso, o Porto 2 está associado a um interruptor virtual existente e não deve ser utilizado.

## <a name="create-a-virtual-switch"></a>Criar um comutador virtual

Utilize o cmdlet seguinte para criar um novo interruptor virtual na interface de rede especificada. Após esta operação estar concluída, as suas instâncias de computação podem utilizar a nova rede virtual.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

Utilize o `Get-HcsExternalVirtualSwitch` comando para identificar o interruptor recém-criado. O novo interruptor que é criado chama-se `vswitch-<InterfaceAlias>` . . 

Aqui está uma saída de exemplo:

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>Verificar rede, sub-rede 

Depois de ter criado o novo switch virtual, o Azure Stack Edge Pro GPU cria automaticamente uma rede virtual e uma sub-rede que correspondem à sua conta. Pode utilizar esta rede virtual ao criar VMs.

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>Passos seguintes

- [Implemente VMs no seu dispositivo GPU Azure Stack Edge Pro via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Implemente VMs no seu dispositivo GPU Azure Stack Edge Pro através do portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
